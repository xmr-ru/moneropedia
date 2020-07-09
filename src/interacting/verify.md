---
# Проверка целостности файлов
---

Проверку следует проводить **до распаковки архива и начала использования кошельком / демоном Monero**.

Инструкции были проверены для Linux. С небольшими изменениями они также должны работать с macOS.

## 1. Импорт PGP ключа ведущего разработчика

> Это единоразовое действие (при проверке последующих (новых) версий файлов (архива кошелька) Monero этот шаг можно пропустить).

Ведущие разработчики Monero подписывают список хешей для двоичных файлов.

binaryFate является одним из ведущих разработчиков Monero, подписывающим публикуемые версии файлов. Публичный ключ binaryFate доступен на GitHub в исходном коде проекта. Необходимо импортировать публичный ключ binaryFate в ваш набор ключей:

`curl https://raw.githubusercontent.com/monero-project/monero/master/utils/gpg_keys/binaryfate.asc | gpg --import`

Публичному ключу binaryFate можно доверять (отпечаток будет таким):

    gpg --edit-key '81AC591FE9C4B65C5806AFC3F0AF4D462A0BDF92'
    trust
    4

Если ключ с таким отпечатком не был найден, следует немедленно удалить импортированный ключ (gpg --delete-keys …), так как это будет означать, что ключ был изменён (вероятно, взломан).

## 2. Проверка подписи списка хешей (hashes.txt)

Список двоичных файлов и соответствующих хешей публикуется на [getmonero.org](https://www.getmonero.org/downloads/hashes.txt) и в нескольких других источниках, например, в примечаниях к новым версиям на [r/monero](https://reddit.com/r/monero).

Следует отметить, что канал публикации не имеет значения при условии, что подпись проверяется надлежащим образом!                                                                    u

Чтобы проверить хеши (их легитимность), следует выполнить:

`curl https://www.getmonero.org/downloads/hashes.txt | gpg --verify`

Полученный результат должен содержать следующую строку:

`gpg: Good signature from "binaryFate <binaryfate@getmonero.org>"`

## 3. Проверка хеша

На этом этапе проверяется легитимность опубликованных хешей.

Последний шаг заключается в сравнении опубликованного хеша с хешем SHA-256 скаченного архива.

Если вы ещё не сделали этого, скачайте [архив](https://www.getmonero.org/downloads/) с файлами Monero (но не распаковывайте его).

Замените пример имени файла скачанным архивом:

    file_name=monero-gui-linux-x64-v0.16.0.0.tar.bz2

    file_hash=`sha256sum $file_name | cut -c 1-64`

    curl https://www.getmonero.org/downloads/hashes.txt > /tmp/reference-hashes.txt

    # verify the signature (previous step is repeated here for completeness)
    gpg --verify /tmp/reference-hashes.txt

    # grep must print the hash (output cannot be empty)
    grep $file_hash /tmp/reference-hashes.txt

**Внимание!**

Если выход `grep` будет пустым, то следует всё проверить дважды, поскольку, очевидно, что хеши не будут совпадать.

Если `grep` выведет имя и хеш, значит, всё в порядке!
