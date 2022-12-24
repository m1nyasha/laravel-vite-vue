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
        +vue(),
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```