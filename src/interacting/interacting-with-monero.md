---
# Взаимодействие с Monero
---

Вы можете пользоваться Monero посредством десктоп GUI кошелька или кошелька с поддержкой командной строки (CLI) и программируемого API.

Помимо этого, узлы Monero взаимодействуют друг с другом в составе одноранговой сети.

## Обзор установочной директории

После распаковки вы увидите несколько исполняемых файлов, а также руководство пользователя GUI кошелька в формате PDF.

Проект Monero отделяет логическую часть сетевых узлов от логической части кошельков. Логическая часть кошельков обеспечивается тремя независимыми пользовательскими интерфейсами: GUI, CLI и HTTP API.

```
# cd monero-gui-v0.17.1.9

# ---- guide to Monero GUI ----

monero-gui-wallet-guide.pdf

# ---- main executable files -----------

monerod

monero-wallet-cli
monero-wallet-gui
monero-wallet-rpc

# ---- extra executable files -----------

extras/monero-wallet-cli
extras/monero-wallet-rpc
extras/monero-blockchain-prune
extras/monero-gen-trusted-multisig
extras/monero-gen-ssl-cert
extras/monero-blockchain-export
extras/monero-blockchain-import

# ---- don't bother with these ----------

extras/monero-blockchain-stats
extras/monero-blockchain-mark-spent-outputs
extras/monero-blockchain-prune-known-spent-data
extras/monero-blockchain-usage
extras/monero-blockchain-ancestry
extras/monero-blockchain-depth
```

## Исполняемые файлы

| Исполняемый файл           | Описание  
| -------------------------- |:-----------------------------------------------------------------------------------------------------------------------------------
| `monerod`                  | Демон полного узла. Не требует наличия кошелька. См.  раздел <br />[Документация](/interacting/monerod.md).
| `monero-wallet-gui`        | Логическая часть кошелька и __графический пользовательский интерфейс__. <br />Требует наличия запущенного демона `monerod`.
| `monero-wallet-cli`        | Логическая часть кошелька и пользовательский интерфейс с __поддержкой командной строки__. <br />Требует наличия запущенного демона `monerod`.
| `monero-wallet-rpc`        | Логическая часть кошелька и __HTTP API__ (протокол JSON-RPC). <br />Требует наличия запущенного демона `monerod`.
| `monero-blockchain-prune`  | Обрезание существующего локального блокчейна. Позволяет сэкономить до 2/3 дискового пространства (сократить занимаемое до 45 Гб (по состоянию на январь 2021 года)). Лучше использовать эту опцию, а не `monerod --prune-blockchain`, которая только логически освобождает место внутри файла, в то время как сам файл остаётся большим. При использовании `monero-blockchain-prune` создаётся сжатая копия файла блокчейна. См. [tutorial1](https://monero.stackexchange.com/questions/11454/how-do-i-utilize-blockchain-pruning-in-the-gui-monero-wallet-gui), [tutorial2](https://www.publish0x.com/solareclipse/howto-prune-shrink-the-database-of-the-monero-blockchain-on-xpgwjx).
| `monero-gen-ssl-cert`      | Создание 4096-битного приватного ключа RSA и самоподписанного сертификата TLS для использования с RPC интерфейсом `monerod`. Note, Monero daemon automatically generates TLS certificate on each restart. Manual generation with this tool is only useful if you want to pin TLS certificate fingerprint in your monero wallet. См. [pull request](https://github.com/monero-project/monero/pull/5495).
| `monero-gen-trusted-multisig`          | Создание multisig-кошелька. <br />См. главу [Мультиподпись](/multisignature/multisignature.md).
| `monero-blockchain-export` | Экспорт блокчейна в файл `blockchain.raw`.
| `monero-blockchain-import` | Импорт файла `blockchain.raw`, в идеале — вашей собственной копии.


## Унаследованные исполняемые файлы

Скорее всего, вам никогда не придётся пользоваться унаследованными инструментами.

| Исполняемый файл           | Описание
| -------------------------- |:-----------------------------------------------------------------------------------------------------------------------------------
| `monero-blockchain-stats`              | Выводит статистику, такую как количество транзакции (за сутки), блоков (за сутки) и байт (за сутки) на основе вашей локальной цепочки блоков.
| `monero-blockchain-mark-spent-outputs` | Продвинутый инструмент, позволяющий избежать проблем, связанных с анонимностью, которые могут возникнуть вследствие форка Monero. Как правило, таких ситуаций не возникает. <br />См. соответствующий [коммит](https://github.com/monero-project/monero/commit/df6fad4c627b99a5c3e2b91b69a0a1cc77c4be14#diff-0410fba131d9a7024ed4dcf9fb4a4e07) и [пул реквест](https://github.com/monero-project/monero/pull/3322).
| `monero-blockchain-prune-known-spent-data`  | Более ранняя опция ограниченного обрезания, позволяющая удалять известные потраченные выходы транзакций (использовалась до реализации RCT). Сегодня рекомендуется использовать `monero-blockchain-prune`. Позволяет сэкономить всего около 200 Мб. См. [коммит](https://github.com/monero-project/monero/commit/d855f9bb92dbfab707a0e37505906366de818a14).
| `monero-blockchain-usage`              | Продвинутый инструмент, позволяющий избежать проблем, связанных с анонимностью, которые могут возникнуть вследствие форка Monero. <br />См. соответствующий [коммит](https://github.com/monero-project/monero/commit/0590f62ab64cf023d397b995072035986931a6b4) и [пул реквест](https://github.com/monero-project/monero/pull/3322).
| `monero-blockchain-ancestry`           | Продвинутый исследовательский инструмент, позволяющий узнать исходную транзакцию, блок или блокчейн. Не используется большинством пользователей. <br />См. соответствующий [пул реквест](https://github.com/monero-project/monero/pull/4147/files).
| `monero-blockchain-depth`              | Продвинутый исследовательский инструмент, позволяющий узнать глубину транзакции, блока или блокчейна. Не используется большинством пользователей. <br />См. соответствующий [коммит](https://github.com/monero-project/monero/commit/289880d82d3cb206a2cf4ae67d2deacdab43d4f4#diff-34abcc1a0c100efb273bf36fb95ebfa0).


## Взаимодействие

Существует всего несколько способов взаимодействия с программным обеспечением Monero.
Вероятно, наиболее удивительным для новичков станет тот факт, что демон `monerod` принимает команды, вводимые с интерактивной клавиатуры, будучи уже запущенным.

Также следует отметить, что HTTP API разбит между `monerod` и `monero-wallet-rpc`. Чтобы пользоваться полным API, необходимо запустить и задействовать оба демона. Это соответствует разбиению логических частей кошельков и узлов, о которых говорилось выше.

Все варианты реализации кошельков зависят от запущенного демона `monerod`.

| Исполняемый файл           | p2p сеть            | Команды узла, вводимые посредством клавиатуры | HTTP API узла | Команды кошелька, вводимые посредством клавиатуры | HTTP API кошелька | Использование кошелька посредством GUI |
|:--------------------------:|:-------------------:|:---------------------------------------------:|:-------------:|:-------------------------------------------------:|:-----------------:|:--------------------------------------:|
| `monerod`                  | ✔                   | ✔                                             | ✔             |                                                   |                   |                                        |
| `monero-wallet-cli`        |                     |                                               |               | ✔                                                 |                   |                                        |
| `monero-wallet-rpc`        |                     |                                               |               |                                                   | ✔                 |                                        |
| `monero-wallet-gui`        |                     |                                               |               |                                                   |                   | ✔                                      |

## Директория данных

Это места хранения данных блокчейна, регистрационных файлов и журнала подключения к p2p сетям.
По умолчанию директории данных создаются следующим образом:

* `$HOME/.bitmonero/` (Linux и macOS);
* `C:\ProgramData\bitmonero\` (Windows).

Следует помнить о том, что:

* директория данных скрыта в соответствии с правилами ОС;
* название директории `bitmonero` является историческим «артефактом», существовавшим ещё до появления Monero в результате форка Bitmonero, то есть примерно 2000 лет до н. э.

Директория данных содержит:

* `lmdb/` (директорию базы данных блокчейна);
* `p2pstate.bin` (сохранённые в памяти данные о найденных одноранговых узлах);
* `bitmonero.log` (файл журнала).

Директория также может содержать поддиректории для отладочной (stagenet) и тестовой (testnet) сетей, отражающие одну и ту же структуру:

* `stagenet/` (директория данных отладочной сети);
* `testnet/` (директория данных тестовой сети).
