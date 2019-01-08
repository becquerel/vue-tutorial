V předchozím díle jsme použili naši první direktivu `v-on`, dnes se podíváme na to, jaké další direktivy se nám budou hodit v našich aplikacích, a konkrétně na direktivy pro podmíněné zobrazování obsahu. V podstatě se jedná o analogii na klasické konstrukce dostupné v téměř jakémkoliv programovacím jazyce, a to `if`, `else` a `else if`

## v-if
Jednou z nejčastěji používaných direktiv bude direktiva pro podmíněné zobrazení nějakého obsahu. Pomocí `v-if` budeme schopní zobrazit HTML tagy, pokud bude splněna zadaná podmínka. Řekněme, že povolíme na tlačítko kliknout maximálně 5x, a poté by mělo toto tlačítko zmizet. Jak na to ? Náš button element rozšíříme právě o direktivu `v-if`

``` html
<button v-if="clickCounter < 5" v-on:click="buttonClick()">Click Me</button>
```
Schválně zkuste klikat na tlačítko ... v okamžiku, kdy na něj kliknete 5x, tak zmizí. Znovu si detailně rozpitváme použitou direktivu `v-if="clickCounter < 5"`. `v-if` je název direktivy, a uvnitř uvozovek máme logický javascriptový výraz. Direktiva funguje tak, že tento výraz vyhodnotí, a zobrazí element na kterém je použitá pokud je výraz vyhodnocený jako `true`.

Náš výraz uvnitř uvozovek je `clickCounter < 5`. `clickCounter` je data property, naše počítadlo, které se zvyšuje od jedničku po každém kliknutí na tlačítko. Takže my zde máme nastaveno, že se tlačítko zobrazí pouze pokud bude počítadlo menší než 5, a poté tlačítko zmizí.

Důležité je uvědomit si, že není nutné direktivu aplikovat pouze na button, ale je možné ji aplikovat na jakýkoliv nadřazený element. Můžeme tedy aplikaci upravit takto:
``` html
...
<div v-if="clickCounter < 5" >
    <button v-on:click="buttonClick()">Click Me</button>
    Clicked {{ clickCounter }} times.
</div>
...
```
Co se stane teď je, že zmizí nejenom samotné tlačítko, ale i celý `div` na který aplikujeme direktivu, a to včetně textu "Clicked X times"

## v-else
Pro lepší uživatelské komfort naší aplikace budeme nyní chtít uživateli zobrazit poté co klikne 5x zprávu o tom, že dále již není možné pokračovat. K tomu použijeme direktivu `v-else`. Upravíme tedy kód
``` html
    <div v-if="clickCounter < 5" >
        <button v-on:click="buttonClick()">Click Me</button>
        Clicked {{ clickCounter }} times.
    </div>
    <div v-else>
        Sorry. You already clicked too many times.
    </div>
```
Do kódu jsme teď přidali nový `div`, který má aplikovanou direktivu `v-else`. Tato direktiva nemá žádné parametry, a chová se jako přesný opak poslední použité direktivy `v-if`. Takže pokud predchoí `v-if` direktiva byla vyhodnocena jako **true** a zobrazuje svůj obsah, `v-else` bude vyhodnocena jako **false** a svůj obsah nezobrazí (a naopak).

## v-else-if
Pro úplnost dodáme ještě direktivu `v-else-if`. Aplikaci upravíme takto:
``` html
    <div v-if="clickCounter < 5" >
        <button v-on:click="buttonClick()">Click Me</button>
        Clicked {{ clickCounter }} times.
    </div>
    <div v-else-if="clickCounter < 8" >
        <button v-on:click="buttonClick()">Click Carefully</button>
        Clicked {{ clickCounter }} times.
    </div>
    <div v-else>
        Sorry. You already clicked too many times.
    </div>
```
Původní `v-if` podmínka zůstává zachována, ale mezi `v-if` a `v-else` jsme ještě vložili další div s direktivou `v-else-if="clickCounter < 8"`. Aplikace se tedy teď bude chovat tak, ze pokud jste kliknuli na tlačítko méně nez 5x, zobrazí první div s tlačítkem "Click Me". Pokud jste klikli 5x a více, první `v-if` se vyhodnotí jako **false**, a Vue přejde k vyhodnocení další podmínky, což je v našem případě `v-else-if="clickCounter < 8"`. Pokud jste tedy klikli alespoň 5x, ale zároveň méně jak 8x, aplikace zobrazí jiné tlačítko "Click Carefully". A konečně pokud jste kliknuli osmkrát a více, aplikace zobrazí pouze zprávu že jste klikli příliš mnohokrát, a žádné tlačítko nezobrazí

## v-show
**v-show** je direktiva, která má naprosto stejnou syntaxi jako **v-if**, liší se však v tom, jak ji framework vkládá do HTML.

Pokud je podmínka ve **v-if** vyhodnocena jako **false**, tak je příslušný element úplně odstraněn z DOMu stránky, a když si zobrazíte zdrojový kód přes dev konzoli, tento element zde vůbec nenajdete.

Když však namísto **v-if** použijete **v-show**, element bude do HTML kódů renderovaný, ale bude skrytý pomocí CSS stylu `display: none;`. To může být v některých případěch výhodné. Například pokud se vám často mění podmínka pro **v-if**, a direktiva je použitá na nějakou složitejší HTML strukturu, prohlížeč musí při každé změně podmínky element odstranit nebo naopak přidat do DOMu. Oproti tomu při použití **v-show** je tento element v DOMu permanentně, a pouze se zobrazuje a skrývá pomocí CSS, cože je pro prohlížeč méně náročná operace.

Na druhou stranu, **v-show** má o něco vyšší režii při prvotním vykreslení stránky - právě proto, že se do DOMu renderuje vždy, a případně se skryje. Oproti tomu **v-if** se bude generovat pouze v případě, že se skutečně má zobrazit.

Zda je výhodnější použít **v-if** nebo **v-show** záleží na konkrétních případech, ale je nutné pamatovat na to, že **v-show** neumí pracovat s **v-else** nebo **v-else-if**, a jeho použití je tak do jisté míry omezenější.

Pokud bychom chtěli přepsat naší aplikaci za použití **v-show** místo **v-if**, vypadalo by to asi takto:

``` html
        <div v-show="clickCounter < 5" >
            <button v-on:click="buttonClick()">Click Me</button>
            Clicked {{ clickCounter }} times.
        </div>
        <div v-show="clickCounter >= 5 && clickCounter < 8" >
            <button v-on:click="buttonClick()">Click Carefully</button>
            Clicked {{ clickCounter }} times.
        </div>
        <div v-show="clickCounter >= 8">
            Sorry. You already clicked too many times.
        </div>
```
kvůli tomu, že **v-show** nepodporuje **v-else-if** ani **v-else**, je zápis o něco krkolomnější, protože namísto `v-else-if="clickCounter < 8"` musíme psát `v-show="clickCounter >= 5 && clickCounter < 8"`. Zároveň je obtížnější tento kód měnit, protože jednu podmínku je nyní nutné měnit na více místech.

Na závěr ještě [odkaz na zdrojové kódy](https://github.com/becquerel/vue-tutorial/blob/master/example3/index.html)