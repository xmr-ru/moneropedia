---
# Использование приватных ключей в Monero
---

В случае с Monero корневой приватный ключ генерируется [случайным образом](https://wiki.xmr.ru/cryptography/prng.html). Другие приватные ключи детерминировано выводятся на основе корневого приватного ключа.

Приватный ключ необходимо хранить в секрете.

Приватный ключ является **большим целым** числом, которое невозможно угадать, например:
`108555083659983933209597798445644913612440610624038028786991485007418559037440`

Приватный ключ имеет длину 256 бит.

Приватный ключ — **скалярная** величина, а это значит, что он однозначен.

В уравнениях скалярные величины представлены **строчными буквами**.

## Связь с Ed25519​

Будучи просто случайным целым числом, приватный ключ не является чем-то специфическим для какой-либо определённой схемы в асимметричной криптографии.

В контексте криптографии на эллиптических кривых, используемой Monero, приватный ключ является числом, на которое умножается базовая точка `G`.
Результатом умножения является публичный ключ `P` (ещё одна точка на кривой). Умножение точки на число имеет специальное определение в криптографии на эллиптических кривых.
Подробную информацию можно найти в [этом руководстве](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/).

###  Надёжность ключа​

Перед выведением публичного ключа приватный ключ обрабатывается по модулю `1`, где `1` является максимальной скалярной величиной, допустимой для [кривой edwards25519](https://wiki.xmr.ru/cryptography/asymmetric/edwards25519.html).

`1` является числом порядка 2^252, таким образом, эффективная надёжность ключа составляет 252 бита, а не 256 бит.
Это стандартная ситуация для криптографии на эллиптических кривых, и, скорее, является косметическим нюансом, а не проблемой.

## Шифрование​

В контексте, в котором с этим вопросом сталкивается пользователь, целое число приватного ключа:

1. Берётся по модулю `1` во избежание поделки.
2. Выражается как массив из 32 байт в формате следования, [начиная с младшего](https://en.wikipedia.org/wiki/Endianness#Little) (первый байт является наименее значимым).
3. Преобразуется в шестнадцатеричную форму, например: `b3588a87056fb21dc4d052d59e83b54293882e646b543c29478e4cf45c28a402`

## Приватный ключ траты​

Приватный ключ траты используется для траты Monero.

Если конкретнее, он используется для построения одноразовых приватных ключей, позволяющих тратить соответствующие выходы.

## Приватный ключ просмотра​

Приватный ключ просмотра позволяет проверить ваши входящие транзакции в непрозрачном блокчейне.

## Одноразовые приватные ключи​

Одноразовые приватные ключи как конструкция используются в [скрытых адресах](https://monero.stackexchange.com/questions/1409/constructing-a-stealth-monero-address).
