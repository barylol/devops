## Terraform

1. Что содержит код Terraform?

    <details>
      <summary>Ответ</summary>

    Ресурсы облачного провайдера, описанные на языке HCA, а также провижининг для создаваемых ресурсов.

    </details>

---

2. В чём отличие между Terraform и Ansible?

    <details>
      <summary>Ответ</summary>

    Оба решения используются для описания инфраструктуры кодом, однако Terraform используется для описание ресурсов (хостов, сетей и т.д.), а Ansible - для настройки хостов (установки пакетов, создания юзеров и т.д.). При помощи Terraform можно провижнить хосты, через вызов bash-скриптов, но это не так удобно как с использованием Ansible. При помощи Ansible можно описывать ресурсы, используя модули для разных поставщиков ресурсов, но это, опять же, не так удобно как с использованием Terraform. Оба инструмента дополняют друг друга и зачастую используются вместе.

    </details>

---

3. Перечислите основные абстракции Terraform

    <details>
      <summary>Ответ</summary>

    * Provider - внутренний интерфейс, представляющий собой провайдер определённого сервиса. Может быть как удалённым, так и локальным.
    * State - текущее состояние инфраструктуры.
    * Statefile - файл с текущим состоянием инфраструктуры.
    * Backend - блок кода, определяющий, где именно хранить состояние инфораструктуры.
    * Resource - блок кода, определяющий ресурс, предоставляемый провайдером.
    * Data source (data) - блок кода, который позволяет получить информацию о существующем ресурсе в системе провайдера.
    * Variable - переменная которая может быть задана в процессе исполнения процесса конфигурации.
    * Locals - локальные динамические или статические значения, к которым можно обращаться из кода.
    * Output - блок кода, определяющий значение, которое будет передано в вывод программы после исполнения конфигурации.
    * Module - обособленный код, который может быть вызван в других конфигурациях.

    </details>

---

4. Как Terraform обновляет ресурсы?

    <details>
      <summary>Ответ</summary>

    Terraform, также как и Ansible, использует декларативный подход к описанию инфраструктуры. То есть, в файлах конфигурации указывается желаемое состояние инфраструктуры, а не конкретные команды для выполнения. Terraform хранит текущее состояние инфраструктуры в файле tfstate. При первом использовании Terraform происходит инициализация проекта — каталога, в котором находятся файлы конфигурации. Для этого используется команда ```terraform init```. Эта команда позволяет загрузить необходимые версии провайдеров и прочих зависимостей, используемых в конфигурации инфраструктуры. Во время применения команды ```terraform apply```, Terraform создаст описанные ресурсы на хостинг-платформе и внесёт изменения в специальный файл с расширением .tfstate в каталоге проекта. Именно этот файл используется Terraform для сравнения текущего состояния инфраструктуры с новой версией конфигурации всякий раз при выполнении ```terraform apply```. При выполнении этой команды, Terraform создаёт план обновления, включающий в себя требуемые изменения инфраструктуры. Он выглядит как список изменений в файл .tfstate. В процессе применения плана указанные изменения вносятся как в инфраструктуру, так и в файл .tfstate.

    </details>

---

5. Как применить изменения в Terraform, минуя шаг подтверждения?

    <details>
      <summary>Ответ</summary>

    ```terraform apply --auto-approve```

    </details>

---

6. Как проверить корректность конфигурационных файлов Terraform?

    <details>
      <summary>Ответ</summary>

    При помощи команды ```terraform validate```

    </details>

---

7. Как хранить состояние инфраструктуры в Terraform?

    <details>
      <summary>Ответ</summary>

    Например, можно хранить tfstate в git-репозитории команды. Так как tfstate содержит логины и пароли в незашифрованном виде, файл нужно зашифровать и расшифровывать перед каждым использованием из git-репозитория. Другой вариант - хранить в специализированном Terraform backend.

    </details>

---

8. Какой Terraform backend лучше?

    <details>
      <summary>Ответ</summary>

    Зависит от требований к хранению состояния.

    * AWS S3 — Standard (с блокировкой через DynamoDB). Сохраняет состояние в виде заданного ключа в заданном сегменте на Amazon S3. Этот бэкэнд также поддерживает блокировку состояния и проверку согласованности через DynamoDB.
    * terraform enterprise — Standard (без блокировки).
    * etcd — Standard (без лока). Сохраняет состояние в etcd 2.x по заданному пути.
    * etcdv3 — Standard (с блокировкой). Сохраняет состояние в хранилище etcd в виде K/V с заданным префиксом.
    * gcs — Standard (с локом). Сохраняет состояние как объект в настраиваемом префиксе в заданном сегменте в Google Cloud Storage (GCS). Этот бэкэнд также поддерживает блокировку состояния.

    Существуют также и другие бэкенды для Terraform.

    </details>

---

9. Как добавить имеющиеся ресурсы в tfstate?

    <details>
      <summary>Ответ</summary>

    ```bash
    terraform import [options] ADDRESS ID
    ```

    1. Например, создаем директорию и инициализируем будущую инфраструктуру:

    ```bash
    mkdir terraform-test
    cd terraform-test
    terraform init
    nano main.tf
    ```

    2. Добавляем в файл main.tf следующий код:

    ```
    provider "aws" {
      region = "us-west-1"
      profile = "tyx-local"
    }
    resource "aws_s3_bucket" "sample_bucket" {
      bucket = "tyx-local-bucket"
      acl = "public"
    }
    ```

    3. Выполняем импорт ресурса:

    ```bash
    terraform import aws_s3_bucket.sample_bucket tyx-local-bucket
    ```

    </details>

---

10. Зачем нужен ```terraform taint```?

    <details>
      <summary>Ответ</summary>

    Команда ```terraform taint``` пометит ресурс инфраструктуры, который будет удален и заново создан при следующем применении команды ```terraform apply```. 

    </details>

---

11. Как удалить инфраструктуру, описанную Terraform?

    <details>
      <summary>Ответ</summary>

    При помощи команды ```terraform destroy```

    </details>

---

12. Как проводить тестирование terraform?

    <details>
      <summary>Ответ</summary>

    * tflint для проверки кода на соответствие стилю.
    * ```terraform validate``` для проверки кода на наличие ошибок.
    * ```terraforn plan``` для проверки корректности вносимых изменений.

    </details>

---

13. Что такое модуль в Terraform? Для чего он нужен?

    <details>
      <summary>Ответ</summary>

    Модуль это пакет конфигурации Terraform, который можно использовать при повторной конфигурации компонентов инфраструктуры, а также базовой организации кода Terraform в директориях. При подключения модуля ему даётся имя.

    </details>

---

14. Как хранить переменные в Terraform?

    <details>
      <summary>Ответ</summary>

    * main.tf - основной конфигурационный файл, описывающий какие инстансы необходимо создать.
    * variables.tf - конфигурация с описанием переменных и значениями по умолчанию.
    * terraform.tfvars - конфигурация со значениями переменных. Этот файл нужно добавить в .gitignore и очень внимательно следить за тем, чтобы его не пушить.
    * outputs.tf - описание выходных переменных. Необязательный файл, но очень удобно выделять нужные параметры созданного инстанса, например IP-адрес хоста, созданного в облаке.

    </details>

---

15. Как конвертировать Kubernetes yaml-манифест в HCL средствами Linux и terraform?

    <details>
      <summary>Ответ</summary>

    ```bash
    echo 'yamldecode(file("filename.yaml"))' | terraform console
    ```

    </details>

---

16. Как должен выглядеть .gitignore-файл для Terraform?

    <details>
      <summary>Ответ</summary>

    ```
    # Локальные директории Terraform
    **/.terraform/*

    # Файлы с состоянием Terraform (если не настроен бэкэнд и они хранятся непосредственно в репозитории)
    *.tfstate
    *.tfstate.*

    # Логи ошибок
    crash.log
    crash.*.log

    # Файлы с переменными
    *.tfvars
    *.tfvars.json

    # Файлы с переопределением значений
    override.tf
    override.tf.json
    *_override.tf
    *_override.tf.json

    # Файлы с планом выполнения, полученные командой terraform plan -out=tfplan
    *tfplan*

    # Файлы с конфигурацией интерфейса командной строки
    .terraformrc
    terraform.rc
    ```

    </details>

---

17. Как осуществляется блокировка .tfstate-файла?

    <details>
      <summary>Ответ</summary>
    Расписать подробно инфу из этого видео <https://youtu.be/R9so36Uob8c>

    </details>