---
# Хеш-функция Keccak-256
---

В качестве хеш-функции Monero использует Keccak. В частности, в большинстве случаев используется функция Keccak-256, обеспечивающая 32-байтовые хеши.

Keccak является лучшей хеш-функцией, разработанной специалистами не из АНБ. Функция Keccak победила в [конкурсе NIST](https://en.wikipedia.org/wiki/NIST_hash_function_competition) (Национальный институт науки и техники), став официальной функцией SHA3.

## Варианты использования

Monero не использует функцию Keccak для доказательства работы. Он используется:

* генератором случайных чисел;
* для хеширования блоков;
* для хеширования транзакций;
* с образами приватных ключей скрытых адресов (для защиты от двойной траты);
* с контрольной суммой публичных адресов;
* в RingCT;
* в multisig
* в Bulletproofs

...и, вероятно, для многих других вещей.

## Keckack-256 и SHA3-256

SHA3-256 является функцией Keccak-256, но с изменённым NIST заполнением.
Поэтому оригинальная версия Keccak-256 выдаёт значение хеша отличное от того, которое выдаёт функция SHA3-256 c изменённым NIST.

Monero использует оригинальную версию Keccak-256.
Стандарт NIST был опубликован только в августе 2015, в то время как Monero появилась 18 апреля 2014.

## Ссылки

* [Исходный код Keccak, используемый Monero](https://github.com/monero-project/monero/blob/5c2dfe157b48a486eb2b92dcf8789b3b1eb20f60/src/crypto/keccak.c)
* [SHA3 в Wikipedia](https://en.wikipedia.org/wiki/SHA-3)
* [Keccak-256 и SHA3-256](https://ethereum.stackexchange.com/questions/550/which-cryptographic-hash-function-does-ethereum-use) (разъяснение функций на stackexchange Ethereum)
* [Онлайн инструмент для вычисления Keccak-256 и SHA3-256](https://emn178.github.io/online-tools/keccak_256.html)
