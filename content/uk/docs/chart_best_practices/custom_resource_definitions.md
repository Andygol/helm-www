---
title: "Custom Resource Definitions"
description: "Як створювати та використовувати CRD (Custom Resource Definitions)."
weight: 7
---

Цей розділ посібника з найкращих практик розглядає створення та використання обʼєктів Custom Resource Definition (CRD).

При роботі з CRD важливо розрізняти два різні аспекти:

- Існує декларація CRD. Це YAML файл, який має `kind: CustomResourceDefinition`.
- Потім є ресурси, які _використовують_ CRD. Наприклад, якщо CRD визначає `foo.example.com/v1`, будь-який ресурс, який має `apiVersion: example.com/v1` і `kind: Foo`, є ресурсом, що використовує цей CRD.

## Встановлення декларації CRD перед використанням ресурсу {#install-a-crd-declaration-before-using-the-resource}

Helm оптимізований для швидкого завантаження якомога більшої кількості ресурсів у Kubernetes. За дизайном Kubernetes може обробити цілий набір маніфестів і активувати їх усі (це називається циклом узгодження).

Але є різниця з CRD.

Для CRD декларація повинна бути зареєстрована до того, як будь-які ресурси цього типу CRD можуть бути використані. Процес реєстрації іноді займає кілька секунд.

### Метод 1: Нехай `helm` зробить це за вас {#method-1-let-helm-do-it-for-you}

З приходом Helm 3 ми видалили старі `crd-install` хуки на користь простішої методології. Тепер існує спеціальна тека `crds`, яку ви можете створити у вашому чарті для зберігання CRD. Ці CRD не підлягають шаблонізації, але будуть стандартно встановлені під час виконання `helm install` для чарту. Якщо CRD вже існує, його буде пропущена з попередженням. Якщо ви бажаєте пропустити крок встановлення CRD, ви можете використовувати прапорець `--skip-crds`.

#### Декілька застережень (і пояснень) {#some-caveats-and-explanations}

Зараз не підтримується оновлення або видалення CRD за допомогою Helm. Це було зроблено після тривалого обговорення у спільноті через небезпеку випадкової втрати даних. Крім того, наразі немає консенсусу в спільноті щодо того, як управляти CRD та їх життєвим циклом. Як це буде розвиватися, Helm додасть підтримку для цих випадків.

Прапорець `--dry-run` для `helm install` і `helm upgrade` наразі не підтримується для CRD. Мета "Dry Run" полягає в перевірці того, що вивід чарту дійсно працюватиме, якщо буде надіслано на сервер. Але CRD є модифікацією поведінки сервера. Helm не може встановити CRD під час сухого запуску, тому клієнт виявлення не дізнається про цей Custom Resource (CR), і перевірка не пройде. Ви можете перемістити CRD до окремого чарту або використовувати `helm template` замість цього.

Ще один важливий момент, який слід врахувати в обговоренні підтримки CRD, — це те, як обробляється рендеринг шаблонів. Одним з відмінних недоліків методу `crd-install`, що використовувався в Helm 2, була неспроможність правильно перевіряти чарти через зміну доступності API (CRD фактично додає ще один доступний API до вашого кластера Kubernetes). Якщо чарт встановлював CRD, `helm` більше не мав дійсного набору версій API, з якими можна було працювати. Це також є причиною видалення підтримки шаблонізації для CRD. З новим методом `crds` для встановлення CRD ми тепер забезпечуємо, що `helm` має абсолютно достовірну інформацію про поточний стан кластера.

### Метод 2: Окремі чарти {#method-2-separate-charts}

Ще один спосіб зробити це — помістити визначення CRD в один чарт, а потім розмістити будь-які ресурси, які використовують цей CRD, в _іншому_ чарті.

В цьому методі кожен чарт потрібно встановлювати окремо. Однак цей робочий процес може бути більш корисним для адміністраторів кластерів, які мають права адміністратора на кластер.