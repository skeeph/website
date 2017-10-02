---
layout: post
title:  "Подключение Google Analytics к приложению Angular 2"
date:   2017-09-24 16:22:46 +0300
categories: angular
---

Для подключения сервиса аналитики Google Analytics нужно добавить код отслежив--ания на каждую веб-страницу, которую необходимо отслеживать. Но как известно, приложения на фреймворке Angular 2/4 являются одностраничными веб-приложениями(SPA).

Мы все равно можем использовать Google Analytics для отслеживания страниц на SPA-приложениях. Можно вручную писать код для этого. Но лучше следовать принципу DRY и использовать готовые приложения для этого.

Одним из таких приложений является [Angulartics2][angulartics2]. Angulartics2 может собирать данные для аналитики не только для сервиса Google Analytics, но и для множества других. Полный список можно посмотреть на [оф.сайте][1].

Для подключения метрик GA к приложению нужно выполнить следующие шаги.

1.Добавить модуль в проект, выполнив комманду:

`npm install angulartics2 --save`

или в случае если вы используете утилиту yarn:

`yarn add angulartics2`

2.Создать проект в Google Analytics, и получить код отслеживания, который выглядит так
{% highlight html %}
<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-123056128-2', 'auto');
  ga('send', 'pageview');
</script>
{% endhighlight %}

3.Вставить этот кусок кода в файл `index.html` после открывающего тега `body`. Также необходимо убрать(закомментировать, удалить) строку `ga('send', 'pageview');` из этого кода. Эта строка отвечает за отслеживание показов страницы. Нам она не нужна, т.к. эту функцию будет выполнять Angulartics2.

4.Далее в модуле приложения(`app.module.ts`) нужно добавить строку

`import { Angulartics2Module, Angulartics2GoogleAnalytics } from 'angulartics2';`

в начале файла, и в разделе импортов объявления модуля, добавить строку

`Angulartics2Module.forRoot([ Angulartics2GoogleAnalytics ])`

Файл `app.module.ts` должен выглядеть следующим образом:

{% highlight typescript %}
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { Angulartics2Module, Angulartics2GoogleAnalytics } from 'angulartics2';

@NgModule({
  imports: [
    BrowserModule,
    Angulartics2Module.forRoot([ Angulartics2GoogleAnalytics ])
  ],
  declarations: [ AppComponent ],
  bootstrap: [ AppComponent ]
})
{% endhighlight %}

5.И наконец нужно добавить в конструктор компонента приложения(`AppComponent`) аргумент `angulartics2GoogleAnalytics: Angulartics2GoogleAnalytics`, получая таким образом следующее

{% highlight typescript %}
@Component({...})
export class AppComponent {
  constructor(angulartics2GoogleAnalytics: Angulartics2GoogleAnalytics) {}
}
{% endhighlight %}

---

Все, готово. Теперь работает отслеживание посещения страниц в приложении Angular 2/4. При необходимости можно также добавлять отслеживание различных действий на сайте.


[angulartics2]: https://github.com/angulartics/angulartics2
[1]: http://angulartics.github.io/