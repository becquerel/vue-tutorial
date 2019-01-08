# Úvod

Moderní webová aplikace se dnes již neobejde bez trochy toho JavaScriptu. Já sám jsem byl dlouhá léta zastáncem teorie, že JavaScript je vhodný pro předběžnou validaci formulářů (přičemž skutečná validace stejně probíhá na backendu), případně pro ruzné schovávání a zobrazování delších bloků textů, které není třeba mit zobrazeny pořád. Časem jsem k tomu přidal používání jQuery pluginů, například pro datepicker nebo colorpicker. Nicméně za posledních několik let jsem byl nucen svůj názor zásadně přehodnotit, a dnes můžu říct, že si bez JavaScriptu už nedokážu svoji práci představit.

Existuje celá řada frameworků (či knihoven), které mají práci s JavaScriptem ulehčovat. Troufnu si říct, že nejpoužívanějšími jsou Angular a React, ale těm se teď věnovat nechci. Místo toho se chci zaměřit na framework, který byl až donedávna poměrně neznámým, a tím je Vue.JS (https://vuejs.org/), ale rychle získává na popularitě.

U Vue je vidět silná inspirace původním Angularem (podobná syntaxe), ale narozdíl od Angularu se nejedná o plnohodnotný framework, ale spíše o knihovnu, ktera řeší pouze renderování HTML. Tím se zase mnohem více blíží Reactu, který má podobné zaměření.

Seriál počítá s použitím aktuální verze Vue.JS, což je v tuto chvíli 2.3.3

**Disclaimer 1**: předpokladem je alespoň základní znalost HTML, CSS a JavaScriptu. Nebudu vysvětlovat obecné koncepty, co je to DOM, události, syntaxi JavaScriptu jako takového, apod. Na druhou stranu se však budu snažit celý seriál pojmout jako intro do jiného způsobu práce s JavaScriptem, snažit se vysvětlit základní koncepty polopaticky, někdy i za cenu nepřesného zjednodušení. Ideální cílovou skupinou tak mohou být vývojáři, kteří momentélně pracují v jQuery nebo vanilla javascriptem, a chtějí si rozšířit obzory (ale doufám, že bude seriál prospěšný i ostatním)

**Disclaimer 2**: občas budu používat porovnání s Reactem či Angularem. Podotýkám, že nemám nic ani proti jednomu z těchto frameworků a oba aktivně používám. Neříkám, že Vue je lepší nebo horší, ale budu zmiňovat spíše jeho výhody.

Veškeré zdrojové kódy k ukázkám budou dostupné [na GitHubu](https://github.com/becquerel/vue-tutorial)

# Proč Vue ?

Výhodou pro použití Vue je jeho relativní jednoduchost, rychlost a nízké bariéry pro efektivní vývoj. Je možné napsat funkční aplikaci či komponentu, a to velmi rychle, čímž se liší od Angularu či Reactu. I tyto dva frameworky ušly kus cesty, ale dle mého názoru je přece jen začít s Vue jednodušší.

Vue není plnohodnotný framework, jedná se, podobně jako u Reactu, pouze o knihovnu, která řeší jednu věc, a tou je renderování HTML. Je samozrejmě možné jej použít i pro větší SPA aplikace, ale vyžaduje to již nějaké zkušenosti s návrhem JS aplikací, protože Vue vývojáře nenutí dodržovat pevně danou strukturu. Kde však Vue bude skutečně excelovat jsou jednotlivé malé komponenty, části stránky, které můžou mít svoji vlastní logiku, ale jsou relativně nezávislé na zbytku stránky. Typickým příkladem může být například komponenta pro vyhledávání (search box, který bude při psaní rovnou zobrazovat výsledky vyhledávání), nebo emailový kontaktní formulář vložený do patičky stránky. Dalším příkladem užitečné komponenty by mohlo být nějaké dynamické menu, nebo klidně i accordion (https://www.w3schools.com/howto/howto_js_accordion.asp)

# Jak začít

Jednoduchost, s jakou je možné vytvořit si svoji první Vue aplikaci, považuji za velmi silnou stránku. Když jsem poprvé zkoušel React, tak se přiznám, že jsem jako backenďák tento první pokus vzdal. Nutil mě totiž nainstalovat celou řadu podpůrných technologií, a nebyl jsem schopný je všechny takto najednou absorbovat. Podobné to bylo v případě Angularu. Časem jsem se k těmto technologiím prokousal, ale jako první zkušenost s JS frameworky to skutečně nebylo to pravé.

Oproti tomu, pokud chcete začít s Vue, tak pro prvotní experimenty stačí importovat vlastní framework z CDN a rovnou začít psát. Jako první experiment vytvoříme klasickou "Hello World" aplikaci ve Vue.JS. Jak na to ? Velmi jednoduše. Do HTML stránky vložíme standardní HTML kód, který bude sloužit jako šablona, či "kontejner" pro naši aplikaci.

``` html
    <div id="vue-app">
        Hello Vue !
    </div>
```

Když nyní zobrazíte stránku, zobrazí se zde text "Hello Vue !". Tohle ale není žádný JavaScript, jedná se přece o úplně klasické HTML. Což je přesně ono, Vue ve své podstatě vychází z HTML, a dokáže "rozšířit" jeho chování. Abychom tedy udělali skutečnou Vue aplikaci, potřebujeme ještě importovat vlastní Vue, přidáme tedy jen externí JavaScript z CDN

``` html
    ...
    <script src="https://unpkg.com/vue@2.3.3"></script>
</body>
```
**UPDATE 2017-06-02** některé prohlížeče nedokáží fungovat s unpkg korektně. Pokud je to i váš případ, je možné adresu nahradit alternativní CDN, například `https://cdnjs.cloudflare.com/ajax/libs/vue/2.3.3/vue.js`. Více informací na oficiálním webu https://vuejs.org/v2/guide/installation.html

a dále spustíme vlastní Vue aplikaci:
``` html
<script type="text/javascript">
    var app = new Vue({
        el: '#vue-app'
    });
</script>
```

Tato naše "aplikace" se vyznačuje tím, že nic nedělá, chová se úplně stejně, jako kdybychom zde žádný JavaScript neměli. Je to vlastně jen odstavec s textem uvnitř. Nicméně i tak několik komentářů:
* náš `div` v HTML má nastaveno `id="vue-app"`
* v okamžiku, kdy vytváříme novou Vue instanci, předáváme jako jeden z parametrů (v našem případě jediný parametr) `el: '#vue-app'`. Dle hodnoty `el` Vue pozná, který element v HTML stránce si má vzít "pod kontrolu". Takže my vlastně říkáme "nastartuj novou Vue aplikaci, která bude ovládat HTML element s id=vue-app"

Je sice hezké, že máme div, který je "pod kontrolou" Vue, ale zatím jsme skutečně ničeho nedosáhli. Změníme tedy aplikaci tak, aby místo statického textu zobrazovala text který načte z JavaScriptu. Nejprve rozšíříme naši Vue aplikaci (komponentu) o novou property `data`, takže naše komponenta pak bude vypadat nějak takto:
``` js
    var app = new Vue({
        el: '#vue-app',
        data: function() {
            return {
                message: 'Hello from Vue !'
            }
        }
    });
```
property `data` (jejíž hodnotou musí být funkce, která vrací javascriptový objekt) slouží, jak její název napovídá, k uložení dat / informací ke komponentě. V našem případě máme uloženou property `message` s hodnotou `Hello from Vue !`

Poslední věc, kterou uděláme - zobrazíme tento text, namísto původního fixního textu. V našem HTML změníme fixní text `Hello Vue` na `{{ message }}`
``` html
<div id="vue-app">
    {{ message }}
</div>
```
Jak bylo uvedeno výše, Vue má "pod kontrolou" celý html tag `<div id="vue-app">`, a tento zápis dává instrukci Vue frameworku, aby se podíval do komponenty která tento HTML tag ovládá, a hledal v ní data property `message`. V našem případě ji najde, a zde vypíše její hodnotu, což je v našem případě zpráva `Hello from Vue !`

[Kompletní zdrojový kód](https://github.com/becquerel/vue-tutorial/blob/master/example1/index.html)

Námi vytvořená aplikace je skutečně triviální, má jen demonstrovat jednoduchost použití, nicméně někde je nutné začít. V dalším díle popíšu trochu podrobněji několik základních konceptů a vyrobíme aplikaci, která bude i něco dělat.