## Git

1. Что такое GitFlow?

    <details>
      <summary>Ответ</summary>

    GitFlow - модель ветвления Git.

    *Ключевые идеи*:
    1. Данная модель отлично подходит для организации рабочего процесса на основе релизов,
    2. Gitflow предлагает создание отдельной ветки для исправлений ошибок в продуктовой среде.

    *Последовательность работы при использовании модели Gitflow*:

    1. Из *master* создается ветка *develop*.
    2. Из *develop* создаются ветки *feature*.
    3. Когда разработка новой функциональности завершена, она объединяется с веткой *develop*.
    4. Из *develop* создается ветка *release*.
    5. Когда ветка релиза готова, она объединяется с *develop* и *master*.
    6. Если в *master* обнаружена проблема, из нее создается ветка *hotfix*.
    7. Как только исправление на ветке *hotfix* завершено, она объединяется с *develop* и *master*.

    </details>

---

2. Чем `merge` отличается от `rebase`?

    <details>
      <summary>Ответ</summary>

    - `git merge` - выполняет слияние коммитов из одной ветки в другую. В этом процессе изменяется только целевая ветка. История исходных веток остается неизменной.

      ![git-merge](images/git-merge.png)

      *Преимущества*:
        1. Простота,
        2. Сохраняет полную историю и хронологический порядок,
        3. Поддерживает контекст ветки.

      *Недостатки*:
        1. История коммитов может быть заполнена (загрязнена) множеством коммитов,
        2. Отладка с использованием git bisect может стать сложнее.


    - `git rebase` - сжимает все изменения в один патч. Затем интегрирует патч в целевую ветку. В отличии от *merge*, *rebase* перезаписывает историю, потому что она передаётся завершенную работу из одной ветки в другую. В процессе устраняется нежелательная история.

      ![git-rebase](images/git-rebase.png)

      *Преимущества*:
        1. Упрощает потенциально сложную историю,
        2. Упрощение манипуляций с единственным коммитом,
        3. Избежание слияния коммитов в занятых репозиториях и ветках,
        4. Очищает промежуточные коммиты, делая их одним коммитом, что полезно для DevOps команд.

        *Недостатки*:
        1. Сжатие фич до нескольких коммитов может скрыть контекст
        2. Перемещение публичных репозиториев может быть опасным при     работе в команде,
        3. Появляется больше работы,
        4. Для восстановления с удаленными ветками требуется     принудительный пуш. Это приводит к обновлению всех веток, имеющих одно и то же имя, как локально, так и удаленно.

    </details>

---

3. Чем `tag` отличается от `branch`?

    <details>
      <summary>Ответ</summary>

    И *tag* и *branch* представляют собой указатели на коммиты.
    - Ветка представляет собой отдельный поток разработки, который может выполняться одновременно с другими разработками в той же кодовой базе. Коммит в ветке указывает на изменения, которые добавляются в новых коммитах
    - Тег представляет собой версию определенной ветки в определенный момент времени.

    *Tag* представляет собой версию той или иной ветки в определенный момент времени. *Branch* представляет собой отдельный поток разработки, который может выполнятся одновременно с другими разработками в той же кодовой базе.

    </details>

---

4. Можно ли запушить или закоммитить в тег?

    <details>
      <summary>Ответ</summary>

    По умолчанию, команда git push не отправляет теги на удалённые сервера. После создания теги нужно отправлять явно на удалённый сервер. Процесс аналогичен отправке веток, достаточно выполнить команду git push origin tagname. Если у вас много тегов, и вам хотелось бы отправить все за один раз, то можно использовать опцию --tags для команды git push. В таком случае все ваши теги отправятся на удалённый сервер (если только их уже там нет).

    </details>

---

5. В ветке *develop* есть коммит с изменениями, которые нужно перенести в ветку *master*. Как это сделать?

    <details>
      <summary>Ответ</summary>

    Необходимо найти хеш этого коммита и выполнить следующую комманду в ветке, в которую нужно перенести коммит:

    ```bash
    git cherry-pick <commit_hash>
    ```

    </details>

---

6. Для чего нужна команда `git commit --amend`?

    <details>
      <summary>Ответ</summary>

    `commit --amend` используется для исправления сообщения последнего коммита. Также возможно использовать, чтобы добавить файлы в индекс (`git add`), после добавить файлы в коммит `git commit --amend`.

    </details>

---

7. Что такое Trunk-based Development?

    <details>
      <summary>Ответ</summary>

    Trunk-based Development (TBD, иногда называется магистральной разработкой) это модель ветвления, при которой в репозитории существует одна основная ветка, которая называется "стволом" (trunk). Эта ветка обычно называется master или release, код в ней находится в стабильном состоянии и готов к деплою в любой момент. Если разработчику нужно реализовать какое-то изменение, то он работает в ветке, которая отделяется от ствола. Такие ветки имеют короткий срок жизни, так как изменения вливаются в основную ветку как можно скорее.

    </details>

---

8. Состояние репозитория ушло на много коммитов вперед. Как откатить весь репозиторий к определенному коммиту?

    <details>
      <summary>Ответ</summary>

    git reset --hard <tag/branch/commit hash>

    </details>

---

9. В репозиторий запушен коммит с изменениями в двух файлах. Как откатить изменения этого коммита?

    <details>
      <summary>Ответ</summary>

    git revert <commit hash>

    </details>

---

10. Как удалить чувствительную информацию (логины, пароли) из истории коммитов?

    <details>
      <summary>Ответ</summary>

    Для полного удаления из репозитория файлов c чувствительной информацией можно использовать опенсорсные утилиты git-filter-repo и BFG Repo-Cleaner. Их использование подробно описано [здесь](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)

    </details>
