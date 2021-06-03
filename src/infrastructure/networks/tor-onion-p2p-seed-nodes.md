---
# Сид-узлы Tor Onion и P2P сеть Monero
---

## Когда они необходимы?

Такие узлы необходимы только в том случае, если вы запускаете полный узел и хотите транслировать локальные транзакции через Tor (с помощью опции `tx-proxy`, см. [руководство для monerod](http://xmr.wiki.ru/interacting/monerod.html#tori2p)). Это позволяет замаскировать ваш IP-адрес (как отправителя транзакции) от тех, кто ведёт наблюдение за P2P-сетью, сверх встроенного в Monero протокола Dandelion++.

## Что такое сид-узлы P2P сети?

Ваш демон Monero обнаруживает другие узлы P2P, но ему при этом нужно с чего-то начать. Такие начальные узлы известны как «сид-узлы». В сети Clearnet начальные узлы жёстко запрограммированы, поэтому не требуется никакой настройки.

В сети Tor жёстко запрограммированные сид-узлы отсутствуют. Их приходится указывать вручную при помощи опции `add-peer` в `monero.conf`.

## Фрагменты конфигурации

Работа фрагментов (сниппеты) конфигурации была протестирована 11 января 2020 года. Ими занимаются волонтёры, и их работа и поддержка **не гарантирована**. Ниже показано, как их можно быстро протестировать.

```
# monero.conf snippet
# ...
add-peer=moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion:18080
add-peer=monerozf6koypqrt.onion:18080
add-peer=zbjkbsxc5munw3qusl7j2hpcmikhqocdf4pqhnhtpzw5nt5jrmofptid.onion:18083        # https://github.com/monero-project/monero/blob/master/src/p2p/net_node.inl
add-peer=rno75kjcw3ein6i446sqby2xkyqjarb75oq36ah6c2mribyklzhurpyd.onion:28083        # это пример основной сети, несмотря на странный порт
add-peer=sqzrokz36lgkng2i2nlzgzns2ugcxqosflygsxbkybb4xn6gq3ouugqd.onion:18083        # очень нестабильный пример, работает в 1 случае из 3
add-peer=blzchctiibfjfvtukctsydhquucz2oajnxnfc5hh4ix35gyqjhdnaqqd.onion:18083        # полный узел от автора monerodocs.org
# ...
```

## Как протестировать луковые сид-узлы?

Необходимо установить и запустить демон Tor на своём ноутбуке. Вам также понадобится инструмент командной строки `torsocks`, который либо устанавливается вместе с Tor, либо должен быть установлен отдельно.

### Тестирование с помощью torsocks и telnet

Вам необходимо установить tor, torsocks и telnet.

Чтобы протестировать определённый луковый сервис:

    torsocks telnet moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion 18080

**Ожидаемый результат** (отсутствие ошибок разрешения домена):

```
1610372702 ERROR torsocks[11332]: Unable to resolve. Status reply: 4 (in socks5_recv_resolve_reply() at socks5.c:677)
Trying 127.42.42.0...
Connected to moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion.
Escape character is '^]'.
```

### Тестирование с помощью torsocks и proxychains

Вам необходимо установить tor, torsocks и proxychains.

Чтобы протестировать определённый луковый сервис:

    proxychains nmap -Pn -p 18080 moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion

Ожидаемый результат (пример):

````
[proxychains] Strict chain  ...  127.0.0.1:9050  ...  moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion:18080  ...  OK
Nmap scan report for moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion (224.0.0.1)
Host is up (0.74s latency).
rDNS record for 224.0.0.1: all-systems.mcast.net
PORT      STATE SERVICE
18080/tcp open  unknown
````

## Недостаточно луковых сид-узлов

Луковые сид-узлы — дефицитный ресурс. Большинство узлов поддерживается лишь временно их же авторами.


Вы можете внести значительный вклад в производительность и устойчивость P2P сети Monero, запустив полный узел Monero с поддержкой лукового сервиса.
