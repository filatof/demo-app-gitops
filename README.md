# Demo App GitOps

В этом репозитории **демо-приложение в микросервисной архитектуре**, основанное на открытом проекте [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo).  
Я использую его как пример для демонстрации принципов GitOps и практики развертывания микросервисов в Kubernetes-кластере.  

Репозиторий показывает:
- как структурировать манифесты по окружениям (`base`, `stage`, `prod`);
- как управлять приложениями и инфраструктурой через [FluxCD](https://fluxcd.io/);
- как автоматизировать обновления Docker-образов с помощью Image Automation;
- как использовать Kustomize для переиспользования и наложения конфигураций.

Таким образом, проект служит базой для **отработки CI/CD-практик в Kubernetes** и может быть использован как шаблон для более сложных production-сценариев.

---


## Структура репозитория

```text
.
├── apps/                  # Манифесты приложений
│   ├── base/              # Базовые шаблоны для всех окружений
│   ├── prod/              # Конфигурация приложений для production
│   └── stage/             # Конфигурация приложений для staging
│
├── clusters/              # Описание кластеров и FluxCD
│   ├── prod/              # Конфигурация production-кластера
│   └── staging/           # Конфигурация staging-кластера
│
├── infrastructure/        # Инфраструктурные компоненты (Helm-чарты, namespace, ingress и т.п.)
│   ├── base/              # Базовые инфраструктурные компоненты
│   ├── prod/              # Инфраструктура production-кластера
│   └── stage/             # Инфраструктура staging-кластера
│
└── README.md
```

---

## Приложения (`apps/`)

В директории `apps/base/` описаны все сервисы демо-приложения:

- `adservice.yaml`  
- `cartservice.yaml`  
- `checkoutservice.yaml`  
- `currencyservice.yaml`  
- `emailservice.yaml`  
- `frontend.yaml`  
- `loadgenerator.yaml`  
- `paymentservice.yaml`  
- `productcatalogservice.yaml`  
- `recommendationservice.yaml`  
- `redis.yaml`  
- `shippingservice.yaml`  

Все сервисы объединяются с помощью `kustomization.yaml` и затем подключаются в окружениях `stage` и `prod`.

---

## Инфраструктура (`infrastructure/`)

В `infrastructure/base/` описаны базовые компоненты инфраструктуры (ingress, prometheus, loki и т.д.):

- **nginx ingress** (каталог `nginx/`)  
  - `chart.yaml` – информация о чарте  
  - `namespace.yaml` – описание namespace  
  - `release.yaml` – HelmRelease манифест  
  - `repository.yaml` – HelmRepository манифест  
  - `kustomization.yaml` – сборка манифестов  

Окружения `stage` и `prod` используют `kustomization.yaml` для подключения базовой инфраструктуры.

---

##  Кластеры (`clusters/`)

Каждое окружение имеет собственную директорию с конфигурацией:

- `clusters/staging/`  
  - `app-demo.yaml` – подключение приложений  
  - `flux-system/` – манифесты FluxCD (gotk-components, gotk-sync, kustomization)  
  - `image_policy.yaml` / `image_repository.yaml` / `image_update_automation.yaml` – настройка автоматического обновления образов  
  - `infrastructure.yaml` – подключение инфраструктуры  

- `clusters/prod/`  
  - `app.yaml` – подключение приложений  
  - `flux-system/` – манифесты FluxCD  
  - `infrastructure.yaml` – подключение инфраструктуры  

---

## GitOps-подход

FluxCD следит за изменениями в этом репозитории и автоматически синхронизирует состояние Kubernetes-кластера с описанными манифестами:

1. **Base** — базовые манифесты, общие для всех окружений.  
2. **Stage / Prod** — накладывают поверх базовых манифестов конфигурацию окружения.  
3. FluxCD применяет изменения автоматически при изменении репозитория.  

---

## Как использовать

1. Настроить FluxCD в кластере с помощью `flux bootstrap`.  
2. Указать путь к директории соответствующего окружения (`clusters/staging` или `clusters/prod`).  
3. Flux автоматически подтянет:
   - инфраструктуру из `infrastructure/`  
   - приложения из `apps/`  
   - настройки Image Automation (для staging)  

---

## Полезные ссылки

- [FluxCD документация](https://fluxcd.io/docs/)  
- [Kustomize документация](https://kubectl.docs.kubernetes.io/references/kustomize/)  
- [Helm документация](https://helm.sh/docs/)  

