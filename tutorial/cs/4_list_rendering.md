Další často se opakující operací je zobrazení seznamu objektů pomocí direktivy **v-for**. Toto se hodí například pokud budete mít aplikaci s databází uživatelů a chcete zobrazit jejich seznam, ale samozřejmě to není jediné použití. Jedinou podmínkou je to, že budeme zobratovat nějaké **pole prvků** (pričemž v poli můžou být řetězce, čísla, ale i další pole či objekty)

Pro vyzkoušení konceptu opět rozšíříme naši vzorovou aplikaci, a přidámne další data property `items`:
``` js
    data: function() {
        return {
            heading: 'Click Counter',
            message: 'This component shows how many time you clicked on the button',
            clickCounter: 0,
            items: [1,3,5,7,9]
        }
    },
```
data property `items` není nic jiného, než pole číselných hodnot. Budeme nyní chtít toto pole hodnot vypsat do stránky jako **ul -> li** seznam. Rozšíříme tedy naší sablonu a přidáme do ní kód pro vygenerování seznamu
``` html
        <ul>
            <li v-for="item in items">
                {{ item }}
            </li>
        </ul>
```
**ul** je jen klasický root element pro HTML seznam, ale na elementu **li** máme použitou direktivyu **v-for**, kterou si opět podrobně rozebereme `<li v-for="item in items">`

Jak je již zřejmé, **v-for** je název direktivy, a podstatné budou opět hodnoty v závorkách, tedy `item in items`. Tímto frameworku říkáme, aby se podíval do komponenty, hledal zde data property `items`, a pokud tato bude pole, pro každou jednotlivou položku v HTML stránce vytvoří nový element **li** a zároveň hodnotu té konkrétní položky uloží do lokální proměnné `item`

S proměnnou **item** dále můžeme pracovat, a uvnitř elementu **li** ji používat. V našem případě ji pouze vypisujeme pomocí `{{ item }}`. Výsledkem by tedy měl být HTML kód, ul -> li konstrukce, která bude mít 5 položek a vypíše pod sebe hodnoty 1,3,5,7,9

## Přidávání položek ze seznamu
nyní sice můžeme vypsat pole statických hodnot, ale abychom měli funkční aplikaci, bude se hodit, když budeme umět prvky do seznamu dynamicky přidávat a nebo je odebírat.

Pro začátek vyprázdníme data property `items: []`, aby aplikace neměla při úvodním načtení žádné prvky ke zobrazení. Máme aplikaci, která nám počítá kolikrát jsme kliknuli na tlačítko, takže se rozhodneme, že chceme zaznamenávat čas každého kliknutí. Co tedy musíme udělat - v okamžiku, kdy klikneme na tlačítko, tak zjistíme aktuální čas, a vložíme jej do pole `items`.

Díky tomu, že my již nasloucháme na stisknutí tlačítka a zvyšujeme počítadlo o jedničku, využijeme stejnou funkci, tj. `buttonClick`, a jen ji příslušně rozšíříme:

``` js
    methods: {
        buttonClick: function() {
            this.clickCounter++;
            this.items.push(Date.now())
        }
    }
```
Když nyní budeme klikat na tlačítko, tak namísto pevně definovaných čísel nám budou do seznamu naskakovat čísla vytvořená funkcí `Date.now()`. Jedná se o statickou funkci standardího JavaScriptového objektu `Date`, která vrací aktuální počet milisekund od počátku UNIX epochy, tj. od 1.1.1970.

Není to však úplně uživatelsky přívětivé, mnohem lepší by bylo mít možnost zobrazit datum a čas v nějaké "polidštěné" podobě. Než se však pustíme do tohoto úkolu, ukážeme si, jak položky ze seznamu odebrat.

## Odebírání položek se seznamu
Položku ze seznamu budeme chtít odebrat tím, že na ní klikneme (zároveň budeme chtít i udělat to, že pokud odebereme položku ze seznamu, snížíme i počítadlo o 1). Abychom toho dosáhli, nejprve upravíme šablonu s **v-for** direktivou
``` html
<li v-for="(item, index) in items">
```
původní `item` jsme zde nahradili konstrukcí `(item, index)`. Tímto Vue říkáme, že do proměnné `item` vloží každou jednotlivou položku z pole `items` (toto se od předchozího příkladu nemění), ale zároveň zde definujeme i novou proměnnou `index`. Pokud požijeme tuto kontrukci, tak Vue pro každou iteraci do lokální proměnné `index` vloží právě index té konkrétní položky. Toto bude lépe pochopitelné na ucelenějším příkladě. Upravíme kód takto:
``` html
<li v-for="(item, index) in items">
    {{ index }}: {{ item }}
</li>
```
když teď budeme klikat na tlačítko, tak se první položka zobrazí jako 0: timestamp, druhá jako 1: timestamp, apod., protože zde zobrazujeme jak index, tak samotnou hodnotu položky.

Zatím však stále nemáme implementovanou funkcionalitu pro odebírání položek se seznamu. To uděláme následujícím způsobem
``` html
<ul>
    <li v-for="(item, index) in items" v-on:click="removeClick(index)">
        {{ index }}: {{ item }}
    </li>
</ul>
```
direktiva **v-for** zde zůstává, ale na položku **li** přidáváme již známou direktivu **v-on:click**, a při kliknutí spustíme funkci `removeClick` s parametrem index (přičemž index je práve index té konkrétní položky v poli `items`)

Poslední věc, která nám chybí je tedy implementace vlastní metody `removeClick`. Do sekce `methods` tedy přidáme implementaci této metody:
``` js
    removeClick: function(index) {
        this.clickCounter--;
        this.items.splice(index, 1);
    }
```
tato metoda (funkce) nám říká, že snížíme počítadlo kliků o jedničku, a na druhém řádku odebereme jednu položku z data property `items` pomocí funkce `splice()`, což je standardní funkčnost JavaScriptu pro odstranění položek z pole, viz https://www.w3schools.com/jsref/jsref_splice.asp . Díky tomu, že do funkce posíláme hodnotu `index`, víme, jakou položku máme z pole odstranit.

Na závěr ještě [kompletní zdrojový kód k této části seriálu](https://github.com/becquerel/vue-tutorial/blob/master/example4%20-%20list%20rendering/index.html), a v příští lekci si ukážeme, jaké máme možnosti formátování data a času, aby nám aplikace neukazovala jen "syrové" timestampy.