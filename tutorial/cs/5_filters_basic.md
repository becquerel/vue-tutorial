## Trocha teorie

V dnešní části seriálu se podíváme na to, jak formátovat výstup do HTML. Může se nám stát, že máme nějaké hodnoty ve formátu, se kterým se nám dobře pracuje v kódu, ale pro uživatele je mnohem přívětivějsí, když uvidí data zformátována jinak. Typickým příkladem jsou velká čísla, datum a čas, atd., kde je formát používaný v kódu pro normálního uživatele poněkud nepřehledný.

Existuje několik možností, jak toho docílit. První možností by bylo mít v datech pro stejný údaj uloženy dvě hodnoty, jednu pro kód, a druhou pro zobrazení uživatelům. To je sice možné, relativně jednoduché, ale ne úplně efektivní řešení, protože tímto vlastně duplikujeme hodnotu a máme ji uloženou v ruzných tvarech na dvou různých místech. Naštěstí Vue nabízí tzv. Filtry, které řeší přesně tento problém - tj. konvertují data z jednoho formátu do jiného pro použití v šablonách, ale nijak nám nemění zdrojová data.

Tolik k teorii, a můžeme se pustit do vlastní implementace. Naše aplikace zobrazuje aktuální čas kliknutí, ale namísto data v nějakém rozumném formátu vypisuje ISO timestamp, což je pro uživatele nepraktické a "ošklivé". Naším cílem je zformátovat datum tak, aby bylo pro uživatele čitelné a takové, na jaký je zvyklý.

## Změna v kódu předchozí kapitoly
*V předchozí verzi aplikace jsme ukládali data do pole hodnot jako UNIX timestampy, nicméně kvůli kompatibilitě se všemi prohlížeči (specificky mám na mysli Safari) a kvůli názornosti kódu změníme aplikaci tak, aby ukládala datum v ISO formátu (`YYYY-MM-DDTHH:mm:ss.xxxZ`)a nikoliv jako UNIX timestamp.*

*Změníme tedy řádek `this.items.push(Date.now());` následovně:*
``` js
    var now = new Date();  // create new Date object instance
    this.items.push(now.toISOString());  // and push ISO string to items
```

## Implementace filtru

V [oficiální dokumentaci k filtrům](https://vuejs.org/v2/guide/filters.html) zjistíme, že na naší komponentě je možné vyrobit novou property s názvem `filters`, kde jsou filtry definované. Každý filtr poté bude definovaný jako funkce. Vzhledem k tomu, že vyrábíme filtr pro formátování data, pojmenujeme jej `date`

Přidáme tedy tento nový kód do naší komponenty:

``` js
filters: {
    date: function (value) {
        return value;
    }
},
```

Tímto jsme si definovali nový filtr, co jsme přesně napsali vysvětlíme za malou chvíli. Předtím ještě řekneme aplikaci, aby tento filtr používala. V naší smyčce který vypisuje časy kliknutí změnímě kód takto

``` html
<li v-for="(item, index) in items" v-on:click="removeClick(index)">
    {{ index }}: {{ item | date }}
</li>
```

Jediné co se změnilo je část `{{ item | date }}`, kde máme uvedenou hodnotu v proměnné `item`, poté znak `|` (pipe, v češtině jsem pro tento znak slyšel výraz "svislítko"), který dává Vue instrukce, že po hodnotě bude následovat nějaký filtr. A nakonec je název vlastního filtru. V podstatě tedy říkáme `vezmi hodnotu proměnné "item" (ve které máme ISO timestamp), aplikuj na ní filtr "date" a tuto hodnotu vypiš`.

Když nyní zobrazíme aplikaci, tak zjistíme, že se nic nezměnílo, a že jsou data vypsaná pořád jako timestampy. Je tedy nutné se podívat na to, jak se definuje filtr jako takový.

Jak již bylo zmíněno, každý filtr je definovaný jako funkce. V nejzákladnější variantě má funkce filtry jeden argument - to je hodnota, na kterou se filtr bude aplikovat. V našem případě to bude tedy ISO timestamp kliknutí.

Dále musí funkce filtru něco vracet - tím něčím je myšleno zkonvertovaná hodnota, kterou chceme zobrazit na stránce.

Když se podíváme na naši implementaci, tak zjistíme, že vlastně vracíme stejnou hodnotu jako kterou dostáváme, a že tedy k žádné konverzi nedochází

``` js
date: function (value) {
   return value;
}
```

Upravíme tedy funkci filtru tak, aby skutečné konvertovala timestamp na hezky zformátované datum a čas.

``` js
date: function (value) {
    var date = new Date(value); // create new instance of Date object from the given ISO string
    return date.toLocaleString(); // and return date&time formatted according to user language settings
}
```
kód není příliš komplikovaný a je okomentovaný. Jde jen o to, že nejprve si vytvořímé novou instanci nativního Javascriptového Date objektu, kterému předáme jako argument náš ISO string s datem kliku. A na druhém řádku tento zformátujeme pomocí funkce `toLocaleString()`, která datum zformátuje do tvaru dle jazykového nastavení operašního systému a prohlížeče, a vrátí zpět. Když si teď otevřeme aplikaci, tak by nám měla zobrazovat seznam kliknutí ve formátu, který je čitelný pro uživatele.

## Další možnosti a využití filtrů

Samozřejmě je možné si s formátováním vyhrát, vyrobit si vlastní formát data s využitím metod na [Date objektu](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) . Podstatné je to, aby funkce filtru vždy vracela zformátovanou hodnotu.

Použití filtrů je skvělé například pro formátování čísel (přidání oddělovače tisíců, desetinná místa, apod.), či pro zformátování telefonního čísla tak, aby bylo vždy ve stejném formátu, PSČ, nebo třeba zobrazení ceny včetně DPH, možností využití je bezpočet.

Do filtrů je dokonce možné posílat i dalši argumenty a na jejich základě data formátovat, ale to si necháme až do jednoho z následujících dílů seriálu.

Na závěr ještě [kompletní zdrojový kód dnešního dílu](https://github.com/becquerel/vue-tutorial/blob/master/example5%20-%20filters/index.html).
