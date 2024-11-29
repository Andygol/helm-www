---
title: "Helm Dependency Update"
---

## helm dependency update

оновлення charts/ на основі вмісту Chart.yaml

### Опис {#synopsis}

Оновлює залежності на диску, щоб відобразити стан, зазначений у Chart.yaml.

Ця команда перевіряє, що необхідні чарти, зазначені в `Chart.yaml`, присутні в `charts/` і відповідають прийнятній версії. Вона завантажує найновіші чарти, що задовольняють залежності, та очищає застарілі залежності.

У разі успішного оновлення буде згенеровано файл блокування, який можна використовувати для відновлення залежностей до точної версії.

Залежності не обовʼязково повинні бути представлені в `Chart.yaml`. З цієї причини команда оновлення не видалить чарти, якщо вони (а) присутні у файлі `Chart.yaml`, але (б) мають неправильну версію.

```none
helm dependency update CHART [flags]
```

### Параметри {#options}

```none
  -h, --help             довідка для update
      --keyring string   вʼязка ключів, що містить публічні ключі (стандартно "~/.gnupg/pubring.gpg")
      --skip-refresh     не оновлювати локальний кеш репозиторіїв
      --verify           перевірити пакети на відповідність підписам
```

### Параметри, успадковані від батьківських команд {#options-inherited-from-parent-commands}

```none
      --burst-limit int                 стандартні обмеження на стороні клієнта (стандартно 100)
      --debug                           увімкнути розширений вивід
      --kube-apiserver string           адреса та порт сервера API Kubernetes
      --kube-as-group stringArray       група для імперсонації під час операції, цей прапор може бути повторений для вказання кількох груп.
      --kube-as-user string             імʼя користувача для імперсонації під час операції
      --kube-ca-file string             файл центру сертифікаці СА для підключення до сервера API Kubernetes
      --kube-context string             імʼя контексту kubeconfig для використання
      --kube-insecure-skip-tls-verify   якщо встановлено true, сертифікат сервера API Kubernetes не буде перевірятися на дійсність. Це робить ваші HTTPS-зʼєднання небезпечними
      --kube-tls-server-name string     імʼя сервера для перевірки сертифіката сервера API Kubernetes. Якщо не вказано, використовується імʼя хоста, що використовується для підключення до сервера
      --kube-token string               токен на предʼявника, який використовується для автентифікації
      --kubeconfig string               шлях до файлу kubeconfig
  -n, --namespace string                простір імен для цього запиту
      --qps float32                     кількість запитів в секунду під час взаємодії з API Kubernetes, не включаючи сплески
      --registry-config string          шлях до файлу конфігурації реєстру (стандартно "~/.config/helm/registry/config.json")
      --repository-cache string         шлях до теки, що містить кешовані індекси репозиторіїв (стандартно "~/.cache/helm/repository")
      --repository-config string        шлях до файлу, що містить імена та URL репозиторіїв (стандартно "~/.config/helm/repositories.yaml")
```

### Дивіться також {#see-also}

* [helm dependency](helm_dependency.md) — керувати залежностями чарту

###### Автоматично згенеровано spf13/cobra 11 вересня 2024