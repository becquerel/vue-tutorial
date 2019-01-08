V prvním díle seriálu jsme si ukázali, jak vytvořit první triviální aplikaci, ale ještě než se pustíme dále, musíme probrat několik základních konceptů.

# Vue komponenty
Základní jednotkou pro vývoj ve Vue.JS jsou tzv. komponenty. Komponentu můžeme chápat jako HTML tag, který může mít nějaké vylepšené/rozšířené chování či vzhled. Komponenty jsou specifikované šablonou ("jak komponenta vypadá") a chováním ("co se stane když vykonáme nějakou událost na komponentě, např. klikneme na tlačítko")

## Šablony

Šablonu komponenty definujeme pomocí HTML (a CSS). Stylování pomocí CSS necháme v tuto chvíli stranou a zaměříme se na HTML část. Když vezmeme příklad z předchozí kapitoly, tak šablona naší komponenty vypadala takto:
``` html
    <div id="vue-app">
        {{ message }}
    </div>
```
Pro lepší názornost teď šablonu rozšíříme
``` html
    <div id="vue-app">
        <h1>{{ heading }}<h1>
        <p>
            {{ message }}
        </p>
    </div>
```
Tato komponenta teď zobrazí v nadpise text, který definujeme v data objektu jako `heading`, a zároveň zobrazuje stejnou `message` property jako v předchozím případě.

## Chování
Chování komponenty je definováno v JavaScriptové sekci. Naše komponenta má prozatím velice jednoduché chování, a to že zobrazuje pevně zadaný text. Aby fungoval náš příklad, musíme JavaScript komponenty rozšířit o data property `heading`
``` js
        var app = new Vue({
            el: '#vue-app',
            data: function() {
                return {
                    heading: 'Click Counter',
                    message: 'This component shows how many time you clicked on the button',
                    clickCounter: 0
                }
            }
        });
```
Toto jak jistě uznáte není vůbec zajímavé a prakticky téměř k ničemu, takže komponentu dále rozšíříme. Jak je možná zřejmé z textů, vyrobíme si komponentu, která bude ukazovat počet kliknutí na tlačítko.

Do JS kódu jsme přidali i data property `clickCounter`, kterou použijeme pro uchování informace o tom, kolikrát jsme kliknuli na tlačítko. Výchozí stav bude 0.

Co nám chybí je zobrazení počítadla a tlačítko, na které bude možné kliknout. Velmi jednoduše přidáme do šablony kód pro vypsání další data property `clickCounter` a HTML markup pro tlačítko: 
``` html
    ...
    <p>
        {{ message }}
    </p>
    <button>Click Me</button>
    Clicked {{ clickCounter }} times.
```
Naše komponenta bude vypadat nějak takto
<img src="http://user-image.logdown.io/user/17319/blog/16746/post/1877031/kTJrP3zSFmGcVJZbUmU5_clickCounterComponent.jpg" alt="clickCounterComponent.jpg">
ale stále jí něco chybí - a to funkcionalita která při kliknutí na tlačítko zvýší počítadlo o 1.

## Naslouchání na události
Abychom dosáhli našeho cíle, musíme zajistit, že se při kliknutí na tlačítko provede nějaká akce (zvýšení počítadla o 1). Musíme tedy nastavit, že kliknutí na tlačítko provede nějakou operaci. HTML pro tlačítko rozšíříme tímto způsobem:
``` js
<button v-on:click="buttonClick()">Click Me</button>
```
Co je však ono `v-on:click="buttonClick()"` ? Jedná se o tzv. Vue direktivu, která frameworku říká, co ma provést při konkrétní události. Pokud direktivu rozpitváme ještě více, dostaneme několik částí:
* `v-on:` zápis pro Vue direktivu, definujeme, že budeme naslouchat na nějakou událost která se na HTML elementu stane
* `click` je název vlastní události na kterou chceme reagovat. V našem případě je to klik na tlačítko, ale může to být jakákoliv událost, kterou prohlížeč dokáže detekovat, například keyup, mouseover, focus, apod.
* `="buttonClick()"` a zde nastavujeme, jaká akce se má provést v případě, že dojde k události, v našem případě budeme volat funkci `buttonClick()`

Celý zápis by se tedy dal do "lidské řeči" přeložit jako "při kliknutí na toto tlačítko spusť funkci `buttonClick()`"

Pokud byste nyní aplikaci zkusili spustit a kliknete na tlačítko, v konzoli prohližeče vyskočí chyba
```
[Vue warn]: Property or method "buttonClick" is not defined on the instance but referenced during render. Make sure to declare reactive data properties in the data option.`
```
je to logické, protože nám ještě chybí poslední část aplikace, a tou je právě implementace funkce `buttonClick()` která se má spustit při kliknutí na tlačítko. Pokud z šablony voláme nějakou funkci, Vue se podívá do komponenty, která šablonu používá, a hledá na ni funkci s příslušným názvem. V našem případě však zatím žádnou takovouto funkci definovanou nemáme.

Kód komponenty tedy musíme dále rozšířit a implementovat zde naši funkci (ve Vue označovanou jako metoda) `buttonClick`.

``` js
        ...
            clickCounter: 0
        }
    },
    methods: {
        buttonClick: function() {
            this.clickCounter++;
        }
    }

```
Přidali jsme novou sekci `methods` do komponenty, a v ní jsme definovali `buttonClick` jako funkci. Obsahem funkce je pouze `this.clickCounter++;`. Další z Vue konceptů je ten, že veškerá data a metody jsou v rámci komponenty přístupné pomocí klíčového slova `this`. Lze to chápat tak, že pokud jste v JavaScript kódu komponenty, tak `this` vždy odkazuje na instanci té komponenty. Z HTML šablony se k datům a metodám přistupuje bez `this`.

Jediné, co tedy v rámci metody děláme je, že zvyšujeme data property `clickCounter` o 1. Pokud tedy klikneme na tlačítko, zvyšíme počítadlo o 1.

## Reaktivita

Pokud nyní budete klikat na tlačítko, uvidíte, že se dochází k okamžitému zvýšení počítadla na stránce s aplikací. Není potřeba nikde definovat, že se má překreslit stránka, ale stará se o to sám framework. Komponenta totiž vždy zobrazí aktuální hodnotu data property. Toto má na svědomí koncept, který je označován jako Reaktivita. Pro podrobný technický popis jak tento koncept funguje ve Vue můžete navštívit oficiální dokumentaci https://vuejs.org/v2/guide/reactivity.html , ale pro nás je nyní nejpodstatnější to, že jakoukoliv změnu v data property framework automaticky detekuje a překreslí dle toho stránku v prohlížeči.

Aby reaktivita ve Vue fungovala správně, je nutné jednotlivé data properties definovat předem, a to i v případě, že je jejich hodnota prázdná. Vue bude díky tomu o těchto datech vědět, a bude automaticky detekovat jejich změny.

# Závěr
naše aplikace toho pořád moc neumí, ale my bychom už měli zvládat dva základní koncepty, bez kterých se dále neobejdeme, a to zobrazování dat v šabloně, a reakce na události. V příští části tyto základní koncepty ještě rozšíříme a probereme další nutné direktivy.

Na závěr ještě odkaz na zdrojový kód:
https://github.com/becquerel/vue-tutorial/blob/master/example2/index.html