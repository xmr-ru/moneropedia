---
# Мультиподпись
---

В случае с криптовалютами мультиподпись (multisig) позволяет подписывать транзакции, используя более одного приватного ключа. Средства, защищённые мультиподписью, могут быть потрачены, только если для подписания имеется «M из N» ключей.

Вот некоторые примеры использования этой схемы:
* совместно используемый счёт (1 из 2; муж и жена — оба обладают полным доступом к средствам);
* согласованно используемый счёт (2 из 2; муж и жена должны согласовать трату средств);
* «пороговый» счёт (2 из 3; в качестве независимой третьей стороны привлекается эскроу-служба, которая подписывает транзакцию либо вместе с продавцом, либо с покупателем, если продавцу и покупателю не удаётся договориться);
* безопасный счёт (2 из 3; один владелец контролирует все три ключа, но обеспечивает их безопасность различными средствами, чтобы диверсифицировать риски);
* произвольный пороговый счёт (M из N; некоторые криптовалюты обеспечивают совершенную гибкость с точки зрения количества подписантов).

## Структура мультиподписи Monero

Monero не использует мультиподписи напрямую (по крайней мере в классическом понимании этой схемы). В данном случае схема multisig имитируется путём секретного разделения.

Транзакции по-прежнему подписываются при помощи одного ключа траты. Ключ траты представляет собой сумму всех N приватных ключей. Обоснование такого решения строится на необходимости отделения мультиподписей от кольцевых.

Давайте рассмотрим схему «2 из 3». У нас есть три участника. Каждый из участников получает по два приватных ключа, при этом пары не повторяются между участниками. Таким образом, пара участников вместе обладает всеми тремя приватными ключами, необходимыми для создания приватного ключа траты.

Использование мультиподписи является опцией, реализуемой на уровне кошелька. Вы никогда не сможете узнать, основываясь на данных блокчейна, какие транзакции были подписаны при помощи мультиподписи.

После настройки multisig-кошелька каждому участнику становится известен публичный адрес и приватный ключ просмотра. Это необходимо для того, чтобы участники смогли распознать и расшифровать транзакции, которые будут подписаны ими совместно.

## Настройка multisig-кошелька

Схема multisig может использоваться только при использовании кошелька с поддержкой командной строки (CLI). Перед тем как вы решите воспользоваться мультиподписью, следует изучить, как работает CLI-кошелёк.

Тем, кто только начинает своё знакомство со схемой мультиподписи, во избежание потери своих Monero настоятельно рекомендуется делать это в **отладочной сети** (stagenet).

Для тех, кто не в курсе, stagenet - это отдельный, но функционально идентичный экземпляр сети Monero, созданный в целях тестирования. Чтобы подключиться к отладочной сети, при создании и запуске соответствующего кошелька будет достаточно добавить флаг --stagenet.

### 1: Создание нового кошелька

Для начала необходимо создать новый кошелёк. **Мультиподпись не будет работать с кошельком, на который ранее уже поступали средства**.

Итак, сначала создаётся новый кошелёк. Приведённый ниже код предполагает, что вы используете удалённый узел. Но в идеале рекомендуется использовать собственный узел:

```
./monero-wallet-cli --stagenet --daemon-address address-URL  # Create your wallet
```

В приведённой выше строке следует заменить address-URL на фактический URL узла Monero, к которому вы хотите подключиться. На момент написания статьи список удалённых узлов можно было найти по адресу: [monero.fail](https://monero.fail). По умолчанию в списке отображаются серверы основной сети (mainnet), поэтому сначала следует отфильтровать их, выбрав узлы с поддержкой stagenet.

Затем **активируйте** функцию мультиподписи:

```
set enable-multisig-experimental 1
```

Если вы не установили этот флаг, то попробуйте ввести первую команду, после чего вы увидите следующее сообщение:

```
Error: Multisig is disabled.
Error: Multisig is an experimental feature and may have bugs. Things that could go wrong include: funds sent to a multisig wallet can't be spent at all, can only be spent with the participation of a malicious group member, or can be stolen by a malicious group member.
Error: You can enable it with:
Error: Set enable-multisig-experimental 1
```

Данное сообщение, по сути, является напоминанием о том, что функция мультиподписи всё ещё является экспериментальной и может  содержать ошибки. Подробнее об этом сообщении можно прочитать ниже.

**Рекомендация**: По умолчанию в случае с CLI-кошельком применяется таймаут экрана, равный 90 секундам. По истечении этого времени вам будет предложено ввести пароль для продолжения работы. К сожалению, как только таймаут истечёт, процесс создания мультиподписи будет прерван.

Чтобы увеличить тайм-аут до 10 минут, необходимо ввести следующую команду:

```
set inactivity-lock-timeout 600
```

Чтобы полностью отключить таймаут (только для текущей сессии), используется команда:

```
set inactivity-lock-timeout 0
```

### 2: Команда prepare_multisig

Прежде всего, каждый участник самостоятельно генерирует **данные инициализации**, которые **не являются адресом**.

Затем каждый из участников по защищённому каналу отправляет свои данные инициализации всем остальным участникам.

Тем не менее, если вы создаёте кошелёк с функцией мультиподписи, не привлекая к этому внешних участников, будет достаточно передать данные между окнами терминалов.

Введите первую команду:

```
prepare_multisig
```

**Примечание**: Если использовать эту команду с кошельком, который использовался ранее, появится следующее сообщение об ошибке:

```
Error: This wallet has been used before, please use a new wallet to create a multisig wallet
```

После ввода команды `prepare_multisig` появится подобное сообщение:

```
MultisigxV2R1C9Bd2LNS9oDXLwDWbVbWc53nfUJpFnQqPDDtHksVVrY33DADgnhKetL5Swgk477uP1AENAy2pz11zW73NGqZojTai2TSDyARK3QR8uVt1t26oW21mFdZtd8iuNqTPBjuCc2q9jaRzqUG75rXtnn8eD5DwJX6NaMP63o2n2fta7dXZcpM
Send this multisig info to all other participants, then use make_multisig <threshold> <info1> [<info2>...] with others' multisig info
This includes the PRIVATE view key, so needs to be disclosed only to that multisig wallet's participants
```

Длинная строка, начинающаяся с Multisig, очень важна и будет передана другим кошелькам на следующем этапе.

Но прежде чем перейти к следующему шагу, необходимо выполнить команду `prepare_multisig` для остальных кошельков, которые вы хотите использовать в своей схеме мультиподписи. Например, ещё двум кошелькам, если используется схема мультиподписи «2 из 3».

### 3: Команда make_multisig

С помощью этой команды устанавливается пороговое значение для вашего кошелька с функцией мультиподписи, а затем передаются данные инициализации от других участников. Данные инициализации -это длинная строка, начинающаяся с Multisig, о которой говорилось выше.

Например, если вы решили использовать схему мультиподписи «2 из 2», то пороговое значение будет равно 2, и вам будет необходимо передать 1 набор данных для инициации. Если же вы используете схему «3 из 5», то пороговое значение уже будет равно 3, и вы будете должны отправить 4 набора данных.

Если взять в качестве примера схему «2 из 3», в CLI вводится следующая команда:

```
make_multisig 2 <data1> <data2>
```

Что на практике выглядит следующим образом:

```
make_multisig 2 MultisigxV2R1MyhE1hgED7AFwytsfW44s7G4abNHFKhwfJH9kvB2Q7xNVBdJAyY9gm7eJkHVRo1T3Hb6PeYsyzUrqQsmpBByDq4iRywanpRLxLN2JKuvKPBDayAywAHBzGxdnGiyoXhLdnZiU6Azy3VNocwH1jgfFvYDUUCo7H8mFacnLUFVLC8LjEfz MultisigxV2R1CzwgGBTPxb51nWfvLg7mYPRBnqDgppZq85E745qR1NvGNCLBaHSCmUQ4JRb41tW9PUerAgz9pKHJ5NpKgE6vsZnpLJkCP3u4zwcXJW3UHjABc446jdQegP1hyHnGgJpah8RmdeLcLCAqa6WXgt3xJoz6QF5o66tnCiyJkYyebjWeXV2y
```

Но если бы вы использовали схему «3 из 5», то вместо этого нужно было бы ввести такую команду:

```
make_multisig 3 <data1> <data2> <data3> <data4>
```

После выполнения этой команды на каждом кошельке, будет запущен второй раунд передачи данных инициализации, который выглядит следующим образом:

```
Another step is needed
MultisigxV2Rn1LVYohry597ZfzPhWnuL6qcueBNdq4ivrP7zqDm4W5eKBhxgdcERcSvFs8F5EkLSuYFyKfBEeh4Fui6xHTeRqb7cWshXY96WruxMaSxMafTdPn48ko52e8UHvA4kWwpuPidBYg5dyVWoQLWgqCMDANxWnjhenw6HTwpT96yB8n1a16oQEYyQWg66r2sZHi9RMmivTsihnMq66rTHKPKKau1SHButDwQ
```

**Примечание**: Если вы допустите ошибку, например, введёте неправильное пороговое значение или пропустите какие-то данные инициализации, то функция отмены не предусмотрена. Кошелёк будет создан неправильно, и вам придётся создавать его заново.

### 4: Команда exchange_multisig_keys

После того, как с помощью предыдущей команды будет задано пороговое значение, для приёма данных инициализации от других участников необходимо использовать команду `exchange_multisig_keys`. Никакого порогового значения при этом не указывается.

Например:

```
exchange_multisig_keys <data1> <data2>
```

В общей сложности команда используется один или два раза.

Команда используется один раз, если ваш кошелёк имеет пороговое значение, равное общему количеству участников, например, 2 из 2.

Дважды, если пороговое значение отличается, например, 2 из 3.

Продолжая с тем же примером с мультиподписью, которая строится по схеме «2 из 3», после ввода команды `exchange_multisig_keys` появится следующее сообщение:

```
Another step is needed
MultisigxV2Rn1WCSNqbsjuTXaPVfFsk3ekFF444yFN5PMCXcQHv1Pv794ZdkDZRnfVGgeP5JwpysR3ingQtQMMnmQDEXnP4qgdnh3SU2NXvfe7kMaSxMafTdPn48ko52e8UHvA4kWwpuPidBYg5JdJwdEAh8Ud7kBFX34zP33ZBbrYXcQbQKTcM3XQ8AEP8bVXHVqQSGzkAkjZRp3H63k6ZSXSYdH9WaC9pdr9FV3tx
Send this multisig info to all other participants, then use exchange_multisig_keys <info1> [<info2>...] with others' multisig info
```

Затем второй и последний раз вводим:

```
exchange_multisig_keys <data1> <data2>
```

И в результате получаем:

```
Multisig wallet has been successfully created. Current wallet type: 2/3
Multisig address: 56MD1L4zky3bFXDQb9qvSx7PDbg8F4x1HgPrFNrDnGnYDqFZcWGswWc1p2moFa1F44ccJniY9Wkzk6urkJbEDvubHqYtkcs
```

В итоге **публичный адрес** и **приватный ключ просмотра** кошелька становятся известны всем участникам.

Таким образом, если вы являетесь единственным участником в процессе создания кошелька с функцией мультиподписи, вы поймёте, что всё прошло успешно, когда увидите один и тот же адрес у всех кошельков.

## Получение средств

### 1: Передача средств на кошелёк с функцией мультиподписи

Адреса, создаваемые с помощью кошельков с функцией мультиподписи, работают точно так же, как и обычные адреса, никак не связанные с мультиподписью. Это означает, что:
* каждый кошелёк позволяет создавать подадреса самостоятельно без привлечения других сторон;
* все участники смогут увидеть поступающие средства, так как у всех будет иметься общий приватный ключ просмотра.

Основное различие обнаруживается при попытке потратить средства с кошелька с функцией мультиподписи. О том, как это сделать, написано в разделе «Трата средств».

### 2: Проверка баланса счёта

Чтобы проверить баланс, следует открыть один из кошельков с функцией мультиподписи и ввести команду `refresh`.

Выполнение команды обновит кошелёк и состояние баланса. Процесс выполнения будет выглядеть так же, как показано ниже, но с другой суммой:

```
Starting refresh...
Refresh done, blocks received: 0                                
Currently selected account: [0] Primary account
Tag: (No tag assigned)
Balance: 10.000000000000, unlocked balance: 10.000000000000 (Some owned outputs have partial key images - import_multisig_info needed)
```

Если в конце вы увидите это предложение:

```
(Some owned outputs have partial key images - import_multisig_info needed)
```

Это будет означать, что вы не синхронизировали свой кошелёк с необходимым, соответствующим заданному пороговому значению, количеством кошельков (1 в случае построения мультиподписи по схеме «2 из 3»), которое необходимо для того, чтобы выходы можно было потратить.

Для отображения полученных средств и соответствующих дат также использовать команду `show_transfers`. Результат при этом будет выглядеть следующим образом:

```
 1263592     in unlocked       2023-01-09 21:13:59      10.000000000000 c5a3eec347401b1e263f45577b840c036568aa841eb2ebc6eb1332c1bc281f28 0000000000000000 0.000000000000 76Matb:10.000000000000 1 -
```

## Трата средств

Прежде чем перейти к описанию процесса расходования средств с использованием функции мультиподписи, необходимо составить общее представление о том, как это происходит. Существует два основных этапа:
1. **Обмен частичными образами ключей** - Как минимум, отправителю необходимо получить частичный образ ключа от других участников (одного или нескольких), которые в дальнейшем будут подписывать транзакцию вместе с ним. Они должны экспортировать файл и поделиться им с тем, кто собирается расходовать средства, после чего этот человек импортирует данный файл в свой кошелёк.
2. **Создание, подписание и отправка транзакции** - Транзакция создаётся и записывается в файл, который должен быть подписан другими участниками схемы. После подписания файл может быть передан в сеть.

### Подготовка к трате средств

_**Подготовительный этап 1: Экспорт частичного образа ключа**_

Перед проведением транзакции пользователю необходимо получить частичный образ ключа от кошелька или кошельков, которые впоследствии примут участие в подписании транзакции.

В нашем примере с построением мультиподписи по схеме «2 из 3» пользователю необходимо получить один частичный образ ключа, так как пороговое значение равно 2.

Владельцу кошелька, с которого будет отправлен частичный образ ключа, необходимо ввести команду:

```
export_multisig_info key1
```

где **key1** может быть любым именем файла.

```
Multisig info exported to key1
```

Файл будет сохранён в текущей рабочей директории терминала.

Затем файл необходимо передать кошельку, который создаст транзакцию, в которой будут тратиться средства.

**Подготовительный этап 2: Импорт частичного образа ключа**

Теперь, когда у кошелька есть частичный образ ключа, его можно импортировать. Если файл находится в текущей рабочей директории, команда будет выглядеть следующим образом:

```
import_multisig_info key1
```

Если импортируются два или большее количество образов ключей, то их также следует последовательно указать. Например:

```
import_multisig_info key1 key2 key3
```

После выполнения данной команды кошелёк покажет, сколько новых входов было проверено. Например, если был проверен один выход, это будет выглядеть так:

```
Height 1263592, txid <c5a3eec347401b1e263f45577b840c036568aa841eb2ebc6eb1332c1bc281f28>, 10.000000000000, idx 0/1
Multisig info imported. Number of outputs updated: 1
```

### Трата средств

**Шаг 1. Создание неподписанной транзакции**

Новая транзакция может быть создана _**любым**_ из кошельков, поддерживающих функцию мультиподписи. Однако, во избежание нестандартных ситуаций, следует создавать только одну транзакцию за раз. Если всё же что-то пойдёт не так, чтобы исправить возникшую ситуацию, следует повторить шаги 1 и 2.

Кошелёк, инициирующий передачу средств, организует такую передачу в соответствии со стандартным процессом, используемым CLI-кошельком:

```
transfer <address> <amount>
```

Например:

```
[wallet 56MD1L]: transfer 72Qv1pqug5rX1qS77Bj9C4XBbrvdYRJLM6769bseDytqVZWV2iQxGDnZ85KmubdiCQgtZjeb4fPdUNGq8Foae5b1Bo77T64 5
Wallet password:
Transaction 1/1:
Spending from address index 1
Sending 5.000000000000.  The transaction fee is 0.000167640000
Is this okay?  (Y/Yes/N/No): Yes
```

Результат будет выглядеть следующим образом:

```
Unsigned transaction(s) successfully written to file: multisig_monero_tx
```

Теперь в текущей рабочей директории будет находиться файл с именем **multisig_monero_tx**, который затем будет передан другому участнику мультиподписи.

**Шаг 2. Подписание транзакции**

Теперь в кошельке, который был выбран для совместного подписания транзакции, необходимо выполнить команду:

```
sign_multisig multisig_monero_tx
```

После её выполнения должно появиться подобное сообщение:

```
Loaded 1 transactions, for 10.000000000000, fee 0.000167640000, sending 5.000000000000 to 72Qv1pqug5rX1qS77Bj9C4XBbrvdYRJLM6769bseDytqVZWV2iQxGDnZ85KmubdiCQgtZjeb4fPdUNGq8Foae5b1Bo77T64, 4.999832360000 change to 56MD1L4zky3bFXDQb9qvSx7PDbg8F4x1HgPrFNrDnGnYDqFZcWGswWc1p2moFa1F44ccJniY9Wkzk6urkJbEDvubHqYtkcs, with min ring size 16, dummy encrypted payment ID. Is this okay?  (Y/Yes/N/No): y
Transaction successfully signed to file multisig_monero_tx, txid 82132a4302188b15c87916c05df79755dafb9ada78c39164937c246a4c2dee0a
It may be relayed to the network with submit_multisig
```

**Примечание**: После синхронизации частичных образов ключей предусмотрено определённое временное окно, в течение которого можно создать и отправить транзакцию. Не знаю точно, какова его точная продолжительность. Но если бездействовать слишком долго, появится следующее сообщение:

```
Error: Multisig error: This signature was made with stale data: export fresh multisig data, which other participants must then use
```

Проблема решается повторным выполнением шагов 1 и 2 подготовки к расходованию. После этого можно вернуться непосредственно к этапу расходования средств.

**Шаг 3. Отправка транзакции в сеть**

Теперь, когда транзакция была совместно подписана всеми участниками, её можно передать в сеть. Это можно сделать из любого кошелька, если в нём имеется совместно подписанный файл multisig_monero_tx. Для этого следует использовать команду:

```
 submit_multisig multisig_monero_tx
```

На экране появится сообщение, аналогичное этому:

```
[wallet 56MD1L]: submit_multisig multisig_monero_tx
Wallet password:
Loaded 1 transactions, for 10.000000000000, fee 0.000167640000, sending 5.000000000000 to 72Qv1pqug5rX1qS77Bj9C4XBbrvdYRJLM6769bseDytqVZWV2iQxGDnZ85KmubdiCQgtZjeb4fPdUNGq8Foae5b1Bo77T64, 4.999832360000 change to 56MD1L4zky3bFXDQb9qvSx7PDbg8F4x1HgPrFNrDnGnYDqFZcWGswWc1p2moFa1F44ccJniY9Wkzk6urkJbEDvubHqYtkcs, with min ring size 16, dummy encrypted payment ID. Is this okay?  (Y/Yes/N/No): y
Transaction successfully submitted, transaction <82132a4302188b15c87916c05df79755dafb9ada78c39164937c246a4c2dee0a>
You can check its status by using the `show_transfers` command.
```

Таким образом, транзакция была отправлена в сеть. Если вы захотите создать ещё одну транзакцию, то вам придётся вернуться на этап подготовки и заново синхронизировать частичные образы ключей.

## Мнемонические фразы

В случае с обычным кошельком можно создать мнемоническую фразу, сохранить её резервную копию и впоследствии использовать её для восстановления кошелька.

Кошельки с функцией мультиподписи также обеспечивают такую возможность. Единственное отличие заключается в том, что мнемоническая фраза представляет собой **длинную строку**, состоящую из букв и цифр, а не набор слов. К сожалению, при этом приходится кодировать слишком большой объём данных, чтобы он поместился в обычном выходе мнемонической фразы.

Чтобы получить доступ к мнемонической фразе кошелька, необходимо открыть кошелёк в CLI и ввести команду `seed`. Команда выводит сообщение, аналогичное этому:

```
NOTE: the following string can be used to recover access to your wallet. Write them down and store them somewhere safe and secure. Please do not store them in your email or on file storage services outside of your immediate control.
020000000300000051512b513c603a023df44e2400ad58b3f4751e2dcba44b1d4316be5adffd330b773b3818a07ab6ccc4ffcee1feed5526296b52f5ea0844eb8562cb3e63e8154c5c91f0cfd102a830d8692cec64e662f7ffac4cb82dd950c891a92a22dc80930ab78dd01a1ec7ed04d90eff530d2af9d4e40670576863492357727c6615620e95d2e962632518d958040b710474a4c944cb3a286e3fe9ad0b1d651ff6d8b4c50c81a74423650210bbcb7b427435e90b3eb494cb108bd148cab56e5352303d55070d35e703217b7b051a96af50c1c912bc372f65a4ffa93631ae009a544106bd5dfcf477573387f159ce6cae10fb2ffe63f55e75ed94d0893ece9b67b0a1cf0fc2034ca04ef7c862494a13237bfaa8e822f824ea59de561353f2ac01fbc279280c
```

**Примечание**: Мнемоническая фраза может быть использована только для восстановления того кошелька, на базе которого она была создана. Резервное копирование каждого кошелька должно осуществляться отдельно.

> Внимание! Функция является экспериментальной!

До появления в середине 2022 года пул-реквеста [#8149](https://github.com/monero-project/monero/pull/8149), функция мультиподписи Monero имела ряд известных ошибок.

В рамках PR #8149 эти ошибки были устранены, равно как и ошибки, выявленные в процессе независимого [аудита мультиподписи](https://github.com/monero-project/monero/pull/8149#issuecomment-1167912258).

Однако всё ещё существует вероятность появления **неизвестной ошибки**, которая в худшем случае позволит злоумышленнику, входящему в состав группы подписантов, заполучить все средства, имеющиеся в кошельке, поддерживающем функцию мультиподписи.

Двумя возможными шагами к дальнейшему тестированию и повышению безопасности мультиподписи Monero могли бы стать завершение разработки официальной спецификации и проведение стороннего аудита. Однако в настоящее время сроки не определены.

Следует отметить, что риски, связанные с возможным появлением неизвестной ошибки, зависят от конкретного сценария использования. Например:
* Если планируется использовать мультиподпись совместно с другими пользователями, то такой риск существует.
* Если же планируется использовать мультиподпись исключительно для шардирования холодного кошелька и его хранения в разных местах, то риск может быть снижен. Для создания риска должны одновременно совпасть два бстоятельства:
  * должен найтись злоумышленник, способный использовать неизвестную ошибку в схеме мультиподписи Monero;
  * такой злоумышленник должен получить доступ к одному из предположительно защищённых кошельков с функцией мультиподписи.

Однако если эксплойт станет общеизвестным, то риск возрастёт, поскольку злоумышленнику уже не придётся вычислять его, а будет достаточно найти ваш кошелёк и воспользоваться этим эксплойтом.

Таким образом, если кто-то рискнёт использовать схему мультиподписи в рамках сценария, предусмотренного пунктом b, то такому пользователю будет необходимо с особой тщательностью следить за дальнейшей разработкой функции мультиподписи, чтобы в случае обнаружения эксплойта быстро принять необходимые меры.

## Ссылки

По приведённым ниже ссылкам можно найти очень подробные руководства, послужившие основой для написания данного документа. Обратите внимание, что оба руководства немного устарели, поскольку за прошедшее время некоторые команды CLI были обновлены.

* [https://monero.stackexchange.com/questions/5646/how-to-use-monero-multisignature-wallets-2-2-2-3](https://monero.stackexchange.com/questions/5646/how-to-use-monero-multisignature-wallets-2-2-2-3)
* [https://taiga.getmonero.org/project/rbrunner7-really-simple-multisig-transactions/wiki/23-multisig-in-cli-wallet](https://taiga.getmonero.org/project/rbrunner7-really-simple-multisig-transactions/wiki/23-multisig-in-cli-wallet)
