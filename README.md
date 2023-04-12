# Рабочая среда с Laravel, Vite and Vue

В этом гайде я расскажу о том, как можно настроить проект на Laravel добавив к нему Vue. В релизе [v9.19](https://github.com/laravel/framework/releases/tag/v9.19.0) заменили [Laravel Mix](https://laravel-mix.com/) на [Vite](https://vitejs.dev). По некоторой информации от моих коллег Vite еще сыроват, однако скорость у него сумасшедшая. Ну а если речь идет не о супер серьезном проекте, то почему-бы и нет?

Важно понимать, что мой гайд не будет основан на готовых решениях типа - [Laravel Breeze](https://laravel.com/docs/9.x/starter-kits#breeze-and-inertia). Наша задача развернуть все самостоятельно. 

Я использовал Laravel версии `9.45.1`

## Установка Vue

Vite идет из коробки вместе с Laravel, а значит нам достаточно установить и настроить Vue. Я буду использовать Vue 3. Некоторым это может не понравится, но я могу обосновать свое решение. Сам по себе Vue 3 колоссально не отличается от второй версии. Он позволяет использовать как [Composition API](https://vuejs.org/guide/extras/composition-api-faq.html), так и старый добрый [Options API](https://v3.ru.vuejs.org/ru/api/options-api.html). Однако, если третья версия все же не устраивает по каким-то причинам, например важный для вас набор пакетов не поддерживает Vue 3, то можно использовать вторую.

Установим Vue и Vue Loader.

```shell
npm i vue@next vue-loader@next
```

Также не забудем про драйвер, который позволит использовать Vue вместе с Vite.

```shell
npm i @vitejs/plugin-vue
```

## Настройка Vite

Теперь необходимо настроить сам Vite. В корне проекта начиная с версии 9.19 появился файл - `vite.config.js`

Вот его стандартное содержимое:

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```

Добавим плагин для Vue, который мы установили чуть раньше:

```diff
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
+import vue from '@vitejs/plugin-vue'

export default defineConfig({
    plugins: [
+        vue(),
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```

## Инициализация Vue

Теперь давайте сделаем все необходимое, чтобы запустить наше приложение. 
Для начала добавим первый .vue компонент, который будет выступать основным шаблоном для нашего приложения. Я добавлю его сюда - `resources/js/vue/App.vue`.

Вот его код, чтобы вам было не скучно:

```vue
<template>
  <h1>Hello, world!</h1>
</template>

<script>
export default {
  name: "App"
}
</script>
```

Теперь откроем `resources/js/app.js` и добавим немного кода.

```javascript
import './bootstrap';

// импортируем экземпляр Vue приложения
import { createApp } from 'vue';

// наш тот самый первый компонент
import App from './vue/App.vue';

// и немного дефолтных мелочей
const app = createApp(App)
app.mount('#app')
```

Последнее - это добавление нашего скрипта на саму страничку. С появлением Vite появилась и директива `@vite()`, которая нам сейчас пригодится.

Возьмем `welcome.blade.php` и напишем в нем вот такой код:

```html
<!doctype html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
<!-- элемент, в котором будет Vue приложение -->
<div id="app"></div>
<!-- импорт всего необходимого через Vite -->
@vite('resources/js/app.js')
</body>
</html>
```

## Запуск приложения

Я покажу на примере `php artisan serve`, потому-что это очень распространенный вариант, однако это также хорошо работает и c [Laravel Sail](https://laravel.com/docs/9.x/sail), который я использую постоянно.

Выполняем команду:

```shell
npm run dev
```

На экране появится нечто подобное:

```text
  VITE v4.0.3  ready in 212 ms

  ➜  Local:   http://localhost:5174/
  ➜  Network: use --host to expose
  ➜  press h to show help

  LARAVEL v9.45.1  plugin v0.7.3

  ➜  APP_URL: http://localhost
```

Наверняка вас интересует что это такое - `http://localhost:5174`. Как вы могли догадаться это далеко не наше приложение, а _...the Vite development server that provides Hot Module Replacement for your Laravel application..._ Именно эта штука обеспечивает супер быструю горячую перезагрузку после редактирования кода, а также через этот хост подтягиваются все скрипты и стили. 

Непосредственно нашего приложения это - `APP_URL`. Его нужно подкорректировать, указав тот хост, который мы получим после запуска с помощью `php artisan serve`. Как правило, это - `http://127.0.0.1:8000`.

Когда мы настроили конфиг, запустили `npm run dev` и `php artisan serve` - наше приложение готово к работе. Попробуйте изменить что-нибудь в `App.vue` и проверить горячую перезагрузку.

Если у вас ничего не работает (как это было у меня), то попробуйте немного дополнить ваш конфиг Vite.

```diff
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitejs/plugin-vue'

export default defineConfig({
+    server: {
+        hmr: {
+            host: 'localhost',
+        },
+    },
    plugins: [
        vue(),
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```
## Vue Router
_Coming soon.._
## Vuex
_Coming soon.._
## TypeScript
_Coming soon.._
