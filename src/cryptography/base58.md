---
# Base58
---

Base58 является схемой шифрования из двоичного в текстовый формат. Она подобна схеме Base64, но была изменена во избежание одновременного использования не алфавитно-числовых символов и букв, которые могут выглядеть схоже при печати. Для этого из схемы Base64 были исключены следующие символы: `IOl0+/`

Base58 требует строгого указания формата. Это приводит к тому, что одни варианты реализации несовместимы с другими, например, с точки зрения алфавитного порядка.

С подробностями можно ознакомиться в [Wikipedia](https://en.wikipedia.org/wiki/Base58).

## Base58 в Monero​

Monero использует собственный вариант Base58.

В случае с Monero шифрование Base58 происходит в 8-байтовых блоках, за исключением последнего блока, который составляют оставшиеся (8 или меньшее количество) байтов.

8-байтовый блок преобразуется в 11 или меньше символов Base58. Если блок преобразуется менее чем в 11 символов, выход дополняется единицами (1) (0 в Base58). Конечный блок также дополняется до нужного максимального размера в байтах, закодированного в Base58.

Преимуществом варианта реализации Monero является то, что выход имеет фиксированный размер, что не так в случае с простой схемой Base58. А недостаток состоит в том, что библиотеки, используемые по умолчанию, не работают.

Подробную информацию можно найти по следующим ссылкам для базового варианта реализации [Base58 на C++](https://github.com/monero-project/monero/blob/master/src/common/base58.cpp) и для неофициального варианта реализации [Base58 на Python](https://github.com/bigreddmachine/MoneroPy/blob/master/moneropy/base58.py).
