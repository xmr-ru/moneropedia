---
# MoneroPulse
---

## Что такое MoneroPulse?​

MoneroPulse является инфраструктурой, позволяющей производить экстренную проверку контрольных точек в блокчейне.

Таким образом, MoneroPulse, по сути, позволяет избежать разветвлений блокчейна в результате ошибок в протоколе консенсуса (как в [этом случае, произошедшем в 2014](https://monero.stackexchange.com/questions/421/what-happened-at-block-202612/424#424)).

Фактически, операторы MoneroPulse могут указывать, какой форк они считают действительным. Технически указываемой ими «контрольной точкой» являются хеш и высота блока.

По умолчанию полный узел Monero просто предупредит пользователей, если контрольная точка MoneroPulse не будет соответствовать форку, в котором она находится. Ошибка будет зарегистрирована в журнале событий и будет выделена красным цветом. Пользователи могут просто проигнорировать её. Но в идеале, следует проконсультироваться с сообществом и узнать, что происходит, после чего принять взвешенное решение, перейти ли на совпадающий с контрольной точкой форк, или же остаться в форке, предлагаемом по умолчанию.

Пользователи также могут установить автоматическую проверку контрольных точек в `monerod`, используя опцию `--enforce-dns-checkpointing`. В случае несоответствия `monerod` «откатит» локальный блокчейн на несколько блоков. В конечном счёте альтернативный («исправленный») форк станет «тяжелее» и узел последует ему, оставив «недействительный» форк. Данную опцию рекомендуется использовать с автоматическими полными узлами.

Подводя итог, можно сказать, что MoneroPulse представляет собой механизм экстренной проверки контрольных точек. Мы советуем пользоваться им.

## DNS как основа MoneroPulse

Контрольные точки сохраняются как TXT-записи DNS в доменах, контролируемых операторами.

Вот как можно получить доступ к контрольным точкам, используя любой DNS-клиент:

Пример:

    dig -t txt checkpoints.moneropulse.net +dnssec

В результате получаем:

    (cut)

    ;; ANSWER SECTION:
    checkpoints.moneropulse.net. 299 IN  TXT  "1288616:875ac1bc7aa6c5eedc5410abb9c694034f9e7f79dce4c60698baf37009cb6365"
    checkpoints.moneropulse.net. 299 IN  TXT  "375000:c80c23e387585e12ffb6649d678e9ba328181797b9583a6d8911b77e25375737"
    checkpoints.moneropulse.net. 299 IN  TXT  "325000:4260d56368267bc2a70dd58d73c5ecf23b4e4d96e63c29a868e4a679b0741c7f"
    checkpoints.moneropulse.net. 299 IN  TXT  "233000:4f69bec2af6c0852412bdd10c19e6af10c8d738fe2618b5511a98efd03ab477e"
    checkpoints.moneropulse.net. 299 IN  TXT  "450000:4d098b511ca97723e81737c448343cfd4e6dadb3d8a0e757c6e4d595e6e48357"
    checkpoints.moneropulse.net. 299 IN  TXT  "250000:f59d31839bd909ec8830b4f7f66ff213f0bd006334c8523daee452725e5c7a79"
    checkpoints.moneropulse.net. 299 IN  TXT  "550000:c2e80a636438bd9f7a7ab432a6ad297e35540d80ff5b868bca098124cad2ff8c"
    checkpoints.moneropulse.net. 299 IN  TXT  "650000:1d567f2b491324375a825895c5e7b52857b38e4fed0e42c40909c2d52240b4e0"
    checkpoints.moneropulse.net. 299 IN  TXT  "800000:2ced10aa85357ab6c14bb12b6b56d1dde28940820dda30911b73a5cc9a301760"
    checkpoints.moneropulse.net. 299 IN  TXT  "850000:00e2b557dde9fd4a9e2e3dd7ddac962f5ca475eb1095bc50aa757fd1218ab0a5"
    checkpoints.moneropulse.net. 299 IN  TXT  "900000:d9958d0e7dcf91a5a7b11de225927bf7efc6eb26240315ce12372be902cc1337"
    checkpoints.moneropulse.net. 299 IN  TXT  "913193:5292d5d56f6ba4de33a58d9a34d263e2cb3c6fee0aed2286fd4ac7f36d53c85f"
    checkpoints.moneropulse.net. 299 IN  TXT  "913269:f8302e6b8ba1c49aad9a854b8d6c79d8272c6239dcbba5a75ed0784c1d4f56a1"
    checkpoints.moneropulse.net. 299 IN  TXT  "350000:74da79f6a136969abd6364bd3d37af273c408d6471e8ab598e80569b42415f86"
    checkpoints.moneropulse.net. 299 IN  TXT  "400000:1b2b0e7a30e59691491529a3d506d1ba3d6052d0f6b52198b7330b28a6f1b6ac"
    checkpoints.moneropulse.net. 299 IN  TXT  "500000:2428f0dbe49796be05ed81b347f53e1f7f44aed0abf641446ec2b94cae066b02"
    checkpoints.moneropulse.net. 299 IN  TXT  "600000:f5828ebf7d7d1cb61762c4dfe3ccf4ecab2e1aad23e8113668d981713b7a54c5"
    checkpoints.moneropulse.net. 299 IN  TXT  "700000:12be9b3d210b93f574d2526abb9c1ab2a881b479131fd0d4f7dac93875f503cd"
    checkpoints.moneropulse.net. 299 IN  TXT  "300000:0c1cd46df6ccff90ec4ab493281f2583c344cd62216c427628990fe9db1bb8b6"
    checkpoints.moneropulse.net. 299 IN  RRSIG  TXT 13 3 300 20180922151845 20180920131845 35273 moneropulse.net. 8CyqtsM2f9o6OHZYqtGPVf+8gcFM+eUyoMi29LlkcLtK1AXbZlKqCcdN NvdvB+4OzepmpTanSc+TbLWbz/sIzA==

Следует отметить наличие подписи DNSSEC в конце.

Контрольные точки дублируются на нескольких DNS-серверах:

Основная сеть (Mainnet):

    checkpoints.moneropulse.se
    checkpoints.moneropulse.org
    checkpoints.moneropulse.net
    checkpoints.moneropulse.co

Отладочная сеть (Stagenet):

    stagenetpoints.moneropulse.se
    stagenetpoints.moneropulse.org
    stagenetpoints.moneropulse.net
    stagenetpoints.moneropulse.co

Тестовая сеть (Testnet):

    testpoints.moneropulse.se
    testpoints.moneropulse.org
    testpoints.moneropulse.net
    testpoints.moneropulse.co

## MoneroPulse как вектор атаки

Следует отметить, что MoneroPulse не создаёт блоки и не может самостоятельно разветвить блокчейн. Этот механизм просто подсказывает, какой из форков является действительным.

В случае удачного взлома MoneroPulse злоумышленник сможет остановить работу всех автоматических узлов, выдавая им фальшивые контрольные точки. Этому частично препятствуют DNSSEC и использование множества доменов. Monero предполагает соответствие всех контрольных точек во всех доменах. Таким образом, взлом одного домена или регистратора не повлечёт за собой какого-либо сбоя.

MoneroPulse также повышает авторитет операторов в случае спорных хардфорков. Но несмотря на все положительные стороны, MoneroPulse, по сути, централизует полномочия, сосредотачивая их в руках ведущих разработчиков или того, кто в конкретный момент времени пользуется инфраструктурой MoneroPulse.

### Кто является оператором MoneroPulse?

Операторами MoneroPulse являются выбранные ведущие разработчики.

## «ВНИМАНИЕ! Не было получено двух действительных DNS записей контрольных точек MoneroPulse» (WARNING: no two valid MoneroPulse DNS checkpoint records were received). Что делать в этом случае?​

Это означает, что используемый вами DNS-сервер не подтверждает флаг +dnssec, необходимый для безопасного запроса контрольных точек.

По умолчанию операционная система использует DNS-сервер вашего поставщика интернет-услуг (ISP).

Чтобы исправить положение, вам необходимо либо изменить DNS-сервер в целом в сетевых настройках вашей системы, либо только для `monerod`.

Многие предпочитают использовать DNS-серверы Google или Cloudfare, а не те, что предлагаются ISP.

DNS-сервер Google:

    DNS_PUBLIC=tcp://8.8.8.8 ./monerod

DNS-сервер Cloudfare:

    DNS_PUBLIC=tcp://1.1.1.1 ./monerod


## Ссылки

* [Ответ на StackExchange](https://monero.stackexchange.com/questions/679/what-is-moneropulse?noredirect=1&lq=1)
* [Ответ в Reddit канале](https://www.reddit.com/r/Monero/comments/419qdd/p2p4warning_no_two_valid_moneropulse_dns/)
* [Исходный код Monero](https://github.com/monero-project/monero/blob/ff7dc087ae5f7de162131cea9dbcf8eac7c126a1/src/checkpoints/checkpoints.cpp)
