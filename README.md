# Чат-бот

🎫 Скомпилируем, протестируем и развернем смарт-контракт чат-бота. Знакомство с моделью актора в TON

🌟 Конечным результатом станет конвейер для разработки смарт-контрактов.

# Чекпоинт 0: 🎁 Установка 🎒

Необходимо: 
* [Git](https://git-scm.com/downloads)
* [Node](https://nodejs.org/en/download/) (Use Version 18 LTS)
* [Yarn](https://classic.yarnpkg.com/en/docs/install/#mac-stable)

(⚠️ Не устанавливайте linux пакет `yarn` убедитесь, что устанавливаете через `npm i -g yarn` или даже `sudo npm i -g yarn`!)

```sh
git clone https://github.com/romanovichim/TONQuest2.git
```
```sh
cd challenge-2
yarn install
```
---

# Чекпоинт 1: 💻 Смарт-контракт чат-бота 🔈

Проект на `ton/sandbox`, давайте посмотрим на смарт-контракт, зайдем в папку `contracts` и откроем файл `chatbot.fc`

    #include "imports/stdlib.fc";

    () recv_internal(int msg_value, cell in_msg, slice in_msg_body) impure {
      ;; если вы отправите на контракт более 0.001 TON, то он ответит "doge"!

      ;; игнорировать отправителя, если в сообщении менее 0.001 TON - они нужны за газ
      ;; отправить ответное сообщение
      if (msg_value < 10000000) {
        return ();
      }

      ;; анализируем ячейку in_msg cчтобы получить нужную информацию
      slice cs = in_msg.begin_parse(); ;; преобразуем ячейку in_msg в слайс
      int flags = cs~load_uint(4); ;; загружуем флаги, хотя нам это и не нужно
      slice sender_address = cs~load_msg_addr(); ;; загружаем адрес отправителя

      slice msg_text = "reply"; ;; "doge" будет нашим ответным комментарием

      ;; формируем ячейку сообщения, которое нужно будет отправить обратно
      ;; справку смотреть здесь https://ton.org/docs/develop/smart-contracts/messages
      cell msg = begin_cell()
          .store_uint(0x18, 6)
          .store_slice(sender_address) ;; отвечаем отправителю
          .store_coins(100) ;; отправим 100 нано TON, чтобы это было не нулевое сообщение, которое некоторые кошельки могут игнорировать
          .store_uint(0, 1 + 4 + 4 + 64 + 32 + 1 + 1)
          .store_uint(0, 32) ;; устанавливаем op == 0 потому что нам не нужен op
          .store_slice(msg_text) ;; вставляем "doge" в комментарий
      .end_cell();

      ;; отправляем сообщение, справка здесь https://ton.org/docs/develop/func/stdlib/#send_raw_message
      send_raw_message(msg, 3);

    }

---

# Чекпоинт 2: 💿 Компиляция hexBoC 🔨

Ячейка представляет структуру данных в блокчейне TON. Bag of Cells (BoC) — это формат сериализации ячеек в байтовые массивы. Для удобства работы со смарт-контрактом мы сначала преобразуем его в шестнадцатеричное представление BoC, а затем используем так, как нам удобно в тестах, развертывании и т.д.

Скомпилировать смарт-контракт следующей командой:

```sh
yarn compile
```

Скомпилированный hexBoc будет размещаться в папке `build`

---

# Чекпоинт 3: 🎾 Тесты 😵

Перед тем, как деплоить контракт, хорошо бы его протестировать и для этого мы используем `ton/sandbox`. Тесты расположены в папке `tests` и запускаются командой:

```sh
yarn test
```

Убедитесь, что все тесты прошли успешно.

---

# Чекпоинт 4: 🚀 Деплой 🚀

Для деплоя смарт-контракта выполните команду:

```sh
yarn deploy
```

Отсканируйте QR код своим кошельком Tonkeeper и подтвердите транзакцию.
Под QR кодом будет ссылка на смарт-контракт, сохраните ее.

В качестве примера:

![image](https://user-images.githubusercontent.com/18370291/253951126-77652e61-8b29-4ffb-aece-de3307f78cea.png)

---

# Чекпоинт 5: 📮 Отправка сообщения чат-боту 📮

Протестируем смарт-контракт в тестовой сети. Для упрощения имеется скрипт, который позволит быстро отправить сообщение. Наберите команду:

```sh
yarn send
```

Отсканируйте QR код своим кошельком Tonkeeper и подтвердите транзакцию.
Под QR кодом будет ссылка на смарт-контракт, в следующем шаге она нам понадобится.

---

# Чекпоинт 6:  🏄 Проверка диалога 🏄

Откройте в эксплорере ссылку, которую сохранили и посмотрите ответ чат-бота:

![image](https://user-images.githubusercontent.com/18370291/253953065-ad730aca-b657-49b5-a2cf-7143cdc26dd7.png)


---

# ⚔️ Дополнительные задания


Быстрые результаты — это здорово, но чтобы играть дольше и наслаждаться экосистемой, я предлагаю вам следующие уроки:
- Проанализируйте [смарт-контракт](https://github.com/romanovichim/TonFunClessons_Eng/blob/main/lessons/pipeline/chatbot.md)
- Научитесь писать [тесты](https://github.com/romanovichim/TonFunClessons_Eng/blob/main/lessons/pipeline/chatbottest.md) для контрактов, отправляющих сообщения
 

# 🏆 Награда

Поздравления! Вас ждет награда из коллекции <a target="_blank" href="https://getgems.io/collection/EQDZwCyBbgYionONWorPUX6PrmFh3PHdJtl8fDMqo3mYUfux">"TON Speedrun"</a>:

<img style="border-radius: 10pt; margin: 25pt auto; display: block;" width="40%" src="https://ton-devrel.s3.eu-central-1.amazonaws.com/tonspeedrun/0/image.jpg">

Просто отсканируйте QR code, который будет сгенерирован командой:
```sh
yarn reward
```
