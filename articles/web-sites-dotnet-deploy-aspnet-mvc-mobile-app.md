<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle=".NET ASP.NET MVC 5 mobile website - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande" />

# Distribuzione di un'applicazione Web ASP.NET MVC 5 per dispositivi mobili su Siti Web di Azure

In questa esercitazione verranno illustrate le nozioni di base per lo sviluppo di un'applicazione Web ASP.NET MVC 5
per dispositivi mobili e la distribuzione di tale applicazione in Windows Azure. Ai fini di questa esercitazione, è necessario disporre di
[Microsoft Visual Studio Express 2013 per il Web][Microsoft Visual Studio Express 2013 per il Web]
o della versione professionale di Visual Studio.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### Scopo dell'esercitazione

Durante questa esercitazione si aggiungeranno funzionalità mobili alla semplice
applicazione di elenco conferenze fornita nel [progetto iniziale][progetto iniziale]. La schermata seguente illustra le sessioni ASP.NET nell'applicazione
completata, così come vengono visualizzate nell'emulatore di browser negli strumenti di sviluppo F12 di
Internet Explorer 11.

![][0]

È possibile usare gli strumenti di sviluppo F12 di Internet Explorer 11 e lo [strumento
Fiddler][strumento
Fiddler] per eseguire il debug
dell'applicazione. 

### Acquisizione di competenze

In questa esercitazione si apprenderà:

-   Come usare Visual Studio 2013 per pubblicare un'applicazione Web direttamente in un sito Web di Windows Azure.
-   Come i modelli ASP.NET MVC 5 usano il framework CSS Bootstrap per
    migliorare la visualizzazione sui dispositivi mobili.
-   Come creare visualizzazioni specifiche del dispositivo per browser di destinazione specifici
    del dispositivo, ad esempio quelli di iPhone e Android.
-   Come creare visualizzazioni reattive (che rispondono a browser differenti
    su dispositivi differenti).

## Configurazione dell'ambiente di sviluppo

Installare Azure SDK per .NET Framework per configurare l'ambiente di sviluppo.

1.  Per installare Azure SDK per .NET, fare clic sul collegamento seguente. Se Visual Studio 2013 non è ancora presente, verrà installato tramite questo collegamento. Per completare questa esercitazione, è necessario disporre di Visual Studio 2013. [Azure SDK per Visual Studio 2013][Azure SDK per Visual Studio 2013]
2.  Nell'Installazione guidata piattaforma Web fare clic su **Installa** e procedere con l'installazione.

    ![Installazione guidata piattaforma Web - Azure SDK per .NET][Installazione guidata piattaforma Web - Azure SDK per .NET]

Sarà inoltre necessario disporre di un emulatore di browser per dispositivi mobili. Usare uno qualsiasi di quelli elencati
di seguito:

-   Emulatore di browser disponibile negli [strumenti di sviluppo F12 di Internet Explorer 11][strumenti di sviluppo F12 di Internet Explorer 11] (usato in tutte le schermate del browser
    per dispositivi mobili). Dispone di set di impostazioni della stringa agente utente per Windows Phone 8, Windows Phone 7 e Apple iPad.
-   Emulatore di browser disponibile in [Google Chrome DevTools][Google Chrome DevTools]. Sono disponibili set di impostazioni per diversi dispositivi Android, oltre che per Apple iPhone, Apple iPad e Amazon Kindle Fire. Emula anche gli eventi tocco.
-   [Emulatore mobile di Opera][Emulatore mobile di Opera]

Sono disponibili alcuni progetti con codice sorgente C# a integrazione
di questo argomento:

-   [Download progetto iniziale][progetto iniziale]
-   [Download progetto completato][Download progetto completato]

## Passaggi dell'esercitazione

-   [Distribuzione del progetto iniziale in un sito Web di Windows Azure][Distribuzione del progetto iniziale in un sito Web di Windows Azure]
-   [Framework CSS Bootstrap][Framework CSS Bootstrap]
-   [Override di visualizzazioni, layout e visualizzazioni parziali][Override di visualizzazioni, layout e visualizzazioni parziali]
-   [Miglioramento dell'elenco Speakers][Miglioramento dell'elenco Speakers]
-   [Miglioramento dell'elenco Tags][Miglioramento dell'elenco Tags]
-   [Miglioramento dell'elenco Dates][Miglioramento dell'elenco Dates]
-   [Miglioramento della visualizzazione SessionsTable][Miglioramento della visualizzazione SessionsTable]
-   [Miglioramento della visualizzazione SessionByCode][Miglioramento della visualizzazione SessionByCode]

### <a name="bkmk_DeployStarterProject"></a>Distribuzione del progetto iniziale in un sito Web di Windows Azure

1.  Scaricare il [progetto iniziale][progetto iniziale] dell'applicazione di elenco conferenze.

2.  In Esplora risorse fare quindi clic con il pulsante destro del mouse sul file Mvc5Mobile.zip e scegliere *Proprietà*.

3.  Nella finestra di dialogo **Proprietà - Mvc5Mobile.zip** fare clic sul
    pulsante **Sblocca**. L'operazione di sblocco impedisce la visualizzazione dell'avviso di sicurezza
    quando si tenta di usare un file *zip* scaricato
    dal Web.

4.  Fare clic con il pulsante destro del mouse sul file *Mvc5Mobile.zip* e scegliere **Estrai tutto** per
    decomprimere il file.

5.  In Visual Studio aprire il file *Mvc5Mobile.sln*.

6.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

    ![][1]

7.  In Pubblica sito Web fare clic su **Siti Web di Windows Azure**.

    ![][2]

8.  Fare clic su **Accedi**.

    ![][3]

9.  Digitare il proprio nome utente Windows Azure e fare clic su **Continua**.

    ![][4]

10. Digitare la password e fare clic su **Accedi**.

    ![][5]

11. A questo punto è stato eseguito l'accesso alla finestra di dialogo relativa alla selezione del sito Web esistente. Fare clic su **Nuovo**.

    ![][6]

12. Nel campo **Nome sito** specificare un prefisso univoco per il nome del sito. Il nome del sito completo sarà *\<prefisso\>*.azurewebsites.net. Nel campo **Area** selezionare un'area geografica. Fare quindi clic su **Crea**.

    ![][7]

13. Nella finestra di dialogo Pubblica sito Web verranno inserite le impostazioni relative al nuovo sito Web. Fare clic su **Pubblica**.

    ![][8]

    Dopo che Visual Studio ha completato la pubblicazione del progetto iniziale in Siti Web di Windows Azure, viene avviato il browser desktop e viene visualizzato il sito Web live.

14. Avviare l'emulatore di browser per dispositivi mobili, copiare l'URL dell'
    applicazione per conferenze (*<prefix>*.azurewebsites.net) nell'emulatore, quindi fare clic sul
    pulsante nell'angolo superiore destro e selezionare **Browse by tag**. Se si usa Internet
    Explorer 11 come browser predefinito, è sufficiente digitare `F12`, quindi
    `Ctrl+8` e infine modificare il profilo del browser in **Windows Phone**.
    L'immagine riportata di seguito mostra la visualizzazione *AllTags*, risultante dalla selezione di
    **Browse by tag**, in modalità verticale.

    ![][9]

> [WACOM.NOTE] Mentre si esegue il debug dell'applicazione MVC 5 da Visual Studio, è possibile pubblicare di nuovo il sito Web in Windows Azure per verificare il sito live direttamente dall'emulatore per dispositivi mobili o dall'emulatore del browser.

Il display è molto leggibile su un dispositivo mobile. È già possibile vedere
alcuni degli effetti grafici applicati dal framework CSS Bootstrap.
Fare clic sul collegamento **ASP.NET**.

![][10]

La visualizzazione relativa ai tag ASP.NET viene ridotta in modo da rientrare per intero nello schermo, operazione eseguita automaticamente
da Bootstrap. È tuttavia possibile migliorare tale visualizzazione per adattarla al
browser per dispositivi mobili. La colonna **Date**, ad esempio, è
di difficile lettura. Più avanti in questa esercitazione verrà illustrato come modificare la visualizzazione *AllTags* per adattarla allo schermo
dei dispositivi mobili.

## <a name="bkmk_bootstrap"></a>Framework CSS Bootstrap

Una delle novità del modello MVC 5 è il supporto integrato per Bootstrap. È già stato illustrato come
Bootstrap consenta di migliorare le diverse schermate
dell'applicazione. Ad esempio, quando la larghezza del browser è ridotta, la barra di spostamento nella parte superiore della schermata
è automaticamente comprimibile. Sul browser desktop,
provare a ridimensionare la finestra e osservare come cambiano le dimensioni e l'aspetto
della barra di spostamento. Questa è una dimostrazione della progettazione Web reattiva integrata in
Bootstrap.

Per vedere l'aspetto dell'app Web senza Bootstrap, aprire
*App\_Start\\BundleConfig.cs* e impostare come commento le righe contenenti
*bootstrap.js* e *bootstrap.css*. Il codice riportato di seguito mostra le ultime
due istruzioni del metodo `RegisterBundles` dopo la modifica:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Premere `Ctrl+F5` per eseguire l'applicazione.

La barra di spostamento comprimibile è ora un normale elenco
non ordinato. Fare di nuovo clic su **Browse by tag**, quindi su **ASP.NET**.
Nella schermata dell'emulatore per dispositivi mobili, la tabella non è più ridotta per rientrare interamente
nello schermo ed è necessario scorrerla in senso orizzontale
per visualizzarne la parte destra.

![][11]

Annullare le modifiche e aggiornare il browser
per verificare che la schermata per i dispositivi mobili sia stata ripristinata.

Bootstrap non è specifico di ASP.NET MVC 5 e le funzionalità che fornisce possono essere usate
in qualsiasi applicazione Web. Ora, tuttavia, Bootstrap è integrato nel modello di progetto
ASP.NET MVC 5 per consentire all'applicazione Web MVC 5 di sfruttarne i vantaggi
per impostazione predefinita.

Per altre informazioni, visitare il sito Web di
[Bootstrap][Bootstrap].

Nella sezione seguente verrà illustrato come creare visualizzazioni specifiche del
browser per dispositivi mobili.

## <a name="bkmk_overrideviews"></a>Override di visualizzazioni, layout e visualizzazioni parziali

È possibile eseguire l'override di una visualizzazione (inclusi i layout e le visualizzazioni parziali) per i
browser per dispositivi mobili in generale, per un singolo browser per dispositivi mobili o per un
qualsiasi browser specifico. Per fornire una visualizzazione specifica per dispositivi mobili è possibile copiare un file
di visualizzazione e aggiungere *.Mobile* al nome del file. Ad esempio, per creare la visualizzazione
*Index* per dispositivi mobili, è possibile copiare *Views\\Home\\Index.cshtml* in
*Views\\Home\\Index.Mobile.cshtml*.

In questa sezione verrà illustrato come creare un file di layout specifico per dispositivi mobili.

Per iniziare, copiare *Views\\Shared\\\_Layout.cshtml* in
*Views\\Shared\\\_Layout.Mobile.cshtml*. Aprire *\_Layout.Mobile.cshtml*
 e modificare il titolo da **MVC5 Application** in **MVC5 Application
(Mobile)**.

In ogni chiamata di `Html.ActionLink` per la barra di spostamento, rimuovere "Browse by" da ciascun collegamento
*ActionLink*. Il codice seguente mostra il tag `<ul class="nav navbar-nav">` completato del file di layout per dispositivi mobili.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copiare il file *Views\\Home\\AllTags.cshtml* in
*Views\\Home\\AllTags.Mobile.cshtml*. Aprire il nuovo file e modificare l'elemento
`<h2>` da "Tags" in "Tags (M)":

    <h2>Tags (M)</h2>

Passare alla pagina Tags usando un browser desktop e un emulatore di browser
per dispositivi mobili. L'emulatore di browser per dispositivi mobili mostra le due modifiche apportate
(titolo da *\_Layout.Mobile.cshtml* e titolo da
*AllTags.Mobile.cshtml*).

![][12]

La visualizzazione desktop, invece, non è stata modificata (con i titoli da *\_Layout.cshtml* e
*AllTags.cshtml*).

![][13]

## <a name="bkmk_browserviews"></a>Creazione di visualizzazioni specifiche del browser

Oltre a visualizzazioni specifiche del dispositivo mobile e del desktop, è possibile anche
creare visualizzazioni per un particolare browser. È possibile, ad esempio, creare
visualizzazioni specifiche per i browser di iPhone o Android. In questa sezione
verrà illustrato come creare un layout per il browser di iPhone e una versione iPhone
della visualizzazione *AllTags*.

Aprire il file *Global.asax* e aggiungere il codice seguente all'ultima riga del metodo
`Application_Start`.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Questo codice definisce una nuova modalità di visualizzazione denominata "iPhone" che verrà associata
a ciascuna richiesta in ingresso. Se la richiesta in ingresso soddisfa la
condizione definita, ovvero se l'agente utente contiene la stringa
"iPhone", ASP.NET MVC cercherà le visualizzazioni il cui nome contiene il suffisso
"iPhone".

> [WACOM.NOTE] Quando si aggiungono modalità di visualizzazione specifiche del browser per dispositivi mobili, ad esempio per i browser di iPhone e Android, assicurarsi di impostare il primo argomento su `0` (inserirlo all'inizio dell'elenco), in modo che la modalità specifica del browser abbia la precedenza sul modello mobile (\*.Mobile.cshtml). Se invece il primo posto dell'elenco è occupato dal modello mobile, questo sarà selezionato al posto della modalità di visualizzazione specificata (la prima corrispondenza ha la priorità e il modello mobile viene usato per tutti i browser per dispositivi mobili).

Nel codice fare clic con il pulsante destro del mouse su `DefaultDisplayMode`, scegliere **Resolve**,
quindi scegliere `using System.Web.WebPages;`. In questo modo viene aggiunto un riferimento allo spazio dei nomi
`System.Web.WebPages`, ovvero la posizione in cui i tipi
`DisplayModeProvider` e `DefaultDisplayMode` vengono definiti.

![][14]

In alternativa, è possibile aggiungere manualmente la riga seguente alla sezione
`using` del file.

    using System.Web.WebPages;

Salvare le modifiche. Copiare il file
*Views\\Shared\\\_Layout.Mobile.cshtml* in
*Views\\Shared\\\_Layout.iPhone.cshtml*. Aprire il nuovo file
e modificare il nome da `MVC5 Application (Mobile)` in
`MVC5 Application (iPhone)`.

Copiare il file *Views\\Home\\AllTags.Mobile.cshtml* in
*Views\\Home\\AllTags.iPhone.cshtml*. Nel nuovo file modificare
l'elemento `<h2>` da "Tags (M)" in "Tags (iPhone)".

Eseguire l'applicazione. Eseguire un emulatore di browser per dispositivi mobili, assicurarsi che il relativo agente
utente sia impostato su "iPhone" e passare alla visualizzazione *AllTags*. Se si usa
l'emulatore negli strumenti di sviluppo F12 di Internet Explorer 11,
configurare l'emulazione come segue:

-   Profilo del browser = **Windows Phone**
-   Stringa agente utente = **Custom**
-   Stringa personalizzata = **Apple-iPhone5C1/1001.525**

La schermata seguente mostra la visualizzazione *AllTags* sottoposta a rendering
nell'emulatore negli strumenti di sviluppo F12 di Internet Explorer 11 con la stringa agente utente personalizzata (in questo caso, una stringa agente utente iPhone 5C).

![][15]

Nel browser per dispositivi mobili selezionare il collegamento **Speakers**. Poiché non è disponibile
una visualizzazione mobile (*AllSpeakers.Mobile.cshtml*), viene eseguito il rendering della visualizzazione dei relatori predefinita
(*AllSpeakers.cshtml*) usando la visualizzazione di layout mobile
(*\_Layout.Mobile.cshtml*). Come mostrato di seguito, il titolo **MVC5 Application
(Mobile)** è definito in *\_Layout.Mobile.cshtml*.

![][16]

È possibile disabilitare a livello globale il rendering di una visualizzazione predefinita (non mobile)
all'interno di un layout mobile impostando `RequireConsistentDisplayMode` su
`true` nel file *Views\\\_ViewStart.cshtml*, come mostrato di seguito:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Se `RequireConsistentDisplayMode` è impostato su `true`, il layout mobile
(*\_Layout.Mobile.cshtml*) viene usato solo per le visualizzazioni mobili, ovvero quando il
file della visualizzazione è in formato ***NomeVisualizzazione**.Mobile.cshtml*. Può essere opportuno
impostare `RequireConsistentDisplayMode` su `true` se il layout mobile
non interagisce correttamente con le visualizzazioni non mobili. La schermata seguente mostra
il modo in cui la pagina *Speakers* viene sottoposta a rendering quando `RequireConsistentDisplayMode` è
impostato su `true`, senza la stringa "(Mobile)" nella barra di spostamento nella parte superiore della schermata.

![][17]

È possibile disabilitare la modalità di visualizzazione coerente in una visualizzazione specifica impostando
`RequireConsistentDisplayMode` su `false` nel file di visualizzazione. Il
markup seguente presente nel file *Views\\Home\\AllSpeakers.cshtml* imposta
`RequireConsistentDisplayMode` su `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

In questa sezione è stato spiegato come creare layout e visualizzazioni mobili e
come creare layout e visualizzazioni per dispositivi specifici, ad esempio iPhone.
Il principale vantaggio offerto dal framework CSS Bootstrap, tuttavia, è il
layout reattivo, ovvero la possibilità di applicare un singolo foglio di stile
a browser per desktop, telefoni cellulari e tablet per assicurare un aspetto uniforme e
coerente. Nella sezione seguente verrà illustrato come usare Bootstrap per creare
visualizzazioni per dispositivi mobili.

## <a name="bkmk_Improvespeakerslist"></a> Miglioramento dell'elenco Speakers

Come si è appena osservato, la visualizzazione *Speakers* è leggibile, ma i collegamenti sono
di dimensioni ridotte e difficili da selezionare con un tocco su un dispositivo mobile. In questa sezione
verrà illustrato come rendere la visualizzazione *AllSpeakers* adatta a dispositivi mobili, con collegamenti
ben visibili e facili da toccare e una casella di ricerca per trovare rapidamente
i relatori.

È possibile usare lo stile di Bootstrap relativo ai [gruppi elenchi collegati][gruppi elenchi collegati] per
migliorare la visualizzazione *Speakers*. In *Views\\Home\\AllSpeakers.cshtml*
sostituire il contenuto del file Razor con il codice riportato di seguito.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

L'attributo `class="list-group"` presente nel tag `<div>` applica lo stile di
Bootstrap relativo agli elenchi, mentre l'attributo `class="input-group-item"`
applica a ogni collegamento lo stile di Bootstrap relativo alle voci di elenco.

Aggiornare il browser per dispositivi mobili. La visualizzazione aggiornata avrà un aspetto simile al seguente:

![][18]

Lo stile di Bootstrap relativo ai [gruppi elenchi collegati][gruppi elenchi collegati] rende l'intera casella di ogni
collegamento selezionabile con un clic, aumentando in questo modo la semplicità d'uso. Passare alla
visualizzazione desktop e osservare l'aspetto uniforme e coerente.

![][19]

Anche se la visualizzazione per il browser per dispositivi mobili è stata migliorata, scorrere
il lungo elenco di relatori è ancora difficile. Bootstrap non fornisce un
filtro di ricerca pronto all'uso, ma è possibile aggiungere questa funzionalità con
poche righe di codice. È necessario innanzitutto aggiungere alla visualizzazione una casella di ricerca e
quindi collegare tale casella al codice JavaScript relativo alla funzione di filtro. In
*Views\\Home\\AllSpeakers.cshtml* aggiungere un tag \\<form\> subito dopo il tag \\<h2\> come mostrato di seguito:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Si noti che a entrambi i tag `<form>` e `<input>` sono applicati gli stili
di Bootstrap. L'elemento `<span>` aggiunge un'icona
[glyphicon][glyphicon] di Bootstrap alla
casella di ricerca.

Nella cartella *Scripts* aggiungere un file JavaScript denominato *filter.js*. Aprire
il file e incollarvi il codice seguente:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

È inoltre necessario includere il filtro.js nei bundle registrati. Aprire
*App\_Start\\BundleConfig.cs* e modificare i primi bundle. Modificare la
prima istruzione `bundles.Add` (per il bundle **jquery**) in modo che includa
*Scripts\\filter.js*, come mostrato di seguito:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Il bundle **jquery** è già stato sottoposto a rendering dalla visualizzazione predefinita *\_Layout*
. In un secondo tempo è possibile usare lo stesso codice JavaScript per applicare
la funzionalità di filtro ad altre visualizzazioni elenco.

Aggiornare il browser per dispositivi mobili e passare alla visualizzazione *AllSpeakers*. Nella
casella di ricerca digitare "sc". L'elenco dei relatori viene ora filtrato
in base alla stringa di ricerca.

![][20]

## <a name="bkmk_improvetags"></a> Miglioramento dell'elenco Tags

Come la visualizzazione *Speakers*, anche la visualizzazione *Tags* è leggibile, ma i collegamenti
sono di dimensioni ridotte e difficili da selezionare con un tocco su un dispositivo mobile. È possibile intervenire sulla visualizzazione *Tags* nello stesso modo in cui è stata migliorata la visualizzazione *Speakers*. Usare le modifiche del codice descritte in precedenza, ma applicare in *Views\\Home\\AllTags.cshtml* la seguente sintassi del metodo `Html.ActionLink`:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Il browser desktop aggiornato avrà il seguente aspetto:

![][21]

Il browser per dispositivi mobili aggiornato avrà il seguente aspetto:

![][22]

> [WACOM.NOTE] È possibile che sul browser per dispositivi mobili sia ancora presente la formattazione originale dell'elenco, senza l'applicazione dello stile di Bootstrap: si tratta del risultato della precedente azione di creazione di visualizzazioni specifiche del dispositivo mobile. Tuttavia, poiché ora si sta usando il framework CSS Bootstrap per creare una progettazione Web reattiva, rimuovere le visualizzazioni e il layout specifici del dispositivo mobile. Una volta eseguita l'eliminazione, il browser per dispositivi mobili aggiornato visualizzerà lo stile di Bootstrap.

## <a name="bkmk_improvedates"></a> Miglioramento dell'elenco Dates

È possibile intervenire sulla visualizzazione *Dates* nello stesso modo in cui sono state migliorate le visualizzazioni *Speakers* e
*Tags*. Usare le modifiche del codice descritte in precedenza, ma applicare in *Views\\Home\\AllDates.cshtml* la seguente sintassi del metodo `Html.ActionLink`:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Si otterrà una visualizzazione del browser per dispositivi mobili aggiornato simile a quella riportata di seguito:

![][23]

È possibile migliorare ulteriormente la visualizzazione *Dates* organizzando i valori di data e ora
in base alla data. È possibile eseguire questa operazione usando lo stile di
Bootstrap relativo ai [pannelli][pannelli]. Sostituire
il contenuto del file *Views\\Home\\AllDates.cshtml* con il
codice seguente:

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Il codice crea un tag `<div class="panel panel-primary">` separato per
ogni data dell'elenco e usa il [gruppo elenchi collegati][gruppi elenchi collegati] per i
rispettivi collegamenti, come indicato in precedenza. Si osservi di seguito l'aspetto del browser per dispositivi mobili
quando viene eseguito questo codice:

![][24]

Passare al browser desktop. Si noti ancora una volta l'aspetto uniforme e coerente.

![][25]

## <a name="bkmk_improvesessionstable"></a> Miglioramento della visualizzazione SessionsTable

In questa sezione verrà illustrato come creare la visualizzazione *SessionsTable* in modo specifico
per dispositivi mobili. Questa modifica è più impegnativa di quelle apportate in precedenza.

Nel browser per dispositivi mobili toccare il pulsante **Tag**, quindi immettere `asp`
nella casella di ricerca.

![][26]

Toccare il collegamento **ASP.NET**.

![][27]

Come mostrato dalla figura, la visualizzazione è formattata come tabella, soluzione normalmente adottata
per il browser desktop. Questa formattazione, tuttavia,
risulta di difficile lettura su un dispositivo mobile. Per risolvere questo problema, aprire il file
*Views\\Home\\SessionsTable.cshtml* e sostituirne il contenuto
con il codice seguente:

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

Il codice esegue tre operazioni:

-   Usa il [gruppo elenchi collegati personalizzato][gruppo elenchi collegati personalizzato]
    di Bootstrap per formattare le informazioni della sessione in verticale, in modo da facilitarne la lettura
    su un browser per dispositivi mobili (usando classi quali list-group-item-text).
-   Applica il [sistema griglia][sistema griglia] al
    layout, in modo che gli elementi della sessione scorrano in orizzontale nel browser
    desktop e in verticale in quello per dispositivi mobili (mediante la classe col-md-4).
-   Usa [utilità reattive][utilità reattive] per
    nascondere i tag della sessione durante la visualizzazione sul browser per dispositivi mobili (mediante la classe hidden-xs).

È possibile anche toccare il collegamento di un titolo per passare alla relativa sessione. La figura seguente
mostra le modifiche apportate al codice.

![][0]

Il sistema griglia di Bootstrap applicato formatta automaticamente le sessioni
in senso verticale nel browser per dispositivi mobili. Si noti anche che i tag
non vengono visualizzati. Passare al browser desktop.

![][28]

Si noti che ora nel browser desktop i tag sono visualizzati. Si noti anche che il sistema griglia di Bootstrap
applicato ha organizzato gli elementi della sessione in due colonne. Se si ingrandisce
la finestra del browser, gli elementi verranno visualizzati in tre colonne.

## <a name="bkmk_improvesessionbycode"></a> Miglioramento della visualizzazione SessionByCode

Come operazione conclusiva, la visualizzazione *SessionByCode* verrà modificata per adattarla allo schermo dei dispositivi mobili.

Nel browser per dispositivi mobili toccare il pulsante **Tag**, quindi immettere `asp`
nella casella di ricerca.

![][26]

Toccare il collegamento **ASP.NET**. Vengono visualizzate le sessioni relative ai tag ASP.NET.

![][0]

Scegliere il collegamento **Building a Single Page Application with ASP.NET and
AngularJS**.

![][29]

La visualizzazione desktop è accettabile, ma è possibile migliorarne facilmente l'aspetto usando i componenti dell'interfaccia utente grafica di Bootstrap.

Aprire il file *Views\\Home\\SessionByCode.cshtml* e sostituirne il contenuto
con il markup seguente:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

Il nuovo markup usa lo stile di Bootstrap relativo ai pannelli per adattare la visualizzazione allo schermo dei dispositivi mobili.

Aggiornare il browser per dispositivi mobili. La figura seguente riflette le modifiche
apportate al codice.

![][30]

## Riepilogo e revisione

Nel corso di questa esercitazione è stato mostrato come usare ASP.NET MVC 5 per sviluppare
applicazioni Web per dispositivi mobili. Sono state illustrate le seguenti operazioni:

-   Distribuzione di un'applicazione ASP.NET MVC 5 su Siti Web di Azure
-   Uso di Bootstrap per creare un layout Web reattivo nell'applicazione
    MVC 5
-   Override di layout, visualizzazioni e visualizzazioni parziali, sia in modo globale sia per
    una singola visualizzazione
-   Controllo del layout ed esecuzione di un override parziale usando la proprietà
    `RequireConsistentDisplayMode`
-   Creazione di visualizzazioni per browser specifici, ad esempio quello per
    iPhone
-   Applicazione di stili Bootstrap in codice Razor

## Vedere anche

-   Sito Web di [Bootstrap][Bootstrap]
-   [Blog ufficiale di Bootstrap][Blog ufficiale di Bootstrap]
-   [Tutorial Twitter Bootstrap su Tutorial Republic][Tutorial Twitter Bootstrap su Tutorial Republic]
-   [Editor e strumenti Bootstrap][Editor e strumenti Bootstrap]
-   [Procedure consigliate per applicazioni Web W3C per dispositivi mobili][Procedure consigliate per applicazioni Web W3C per dispositivi mobili]
-   [Candidate recommendation W3C per query sui supporti][Candidate recommendation W3C per query sui supporti]

<!-- Internal Links -->
<!-- External Links -->
<!-- Images -->

  [Microsoft Visual Studio Express 2013 per il Web]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
  [progetto iniziale]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
  [0]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
  [Azure SDK per Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Installazione guidata piattaforma Web - Azure SDK per .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
  [strumenti di sviluppo F12 di Internet Explorer 11]: http://msdn.microsoft.com/it-it/library/ie/dn255001.aspx
  [Google Chrome DevTools]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
  [Emulatore mobile di Opera]: http://www.opera.com/developer/tools/mobile/
  [Download progetto completato]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
  [Distribuzione del progetto iniziale in un sito Web di Windows Azure]: #bkmk_DeployStarterProject
  [Framework CSS Bootstrap]: #bkmk_bootstrap
  [Override di visualizzazioni, layout e visualizzazioni parziali]: #bkmk_overrideviews
  [Miglioramento dell'elenco Speakers]: #bkmk_Improvespeakerslist
  [Miglioramento dell'elenco Tags]: #bkmk_improvetags
  [Miglioramento dell'elenco Dates]: #bkmk_improvedates
  [Miglioramento della visualizzazione SessionsTable]: #bkmk_improvesessionstable
  [Miglioramento della visualizzazione SessionByCode]: #bkmk_improvesessionbycode
  [1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
  [Bootstrap]: http://getbootstrap.com/
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
  [14]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
  [15]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
  [16]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
  [17]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
  [gruppi elenchi collegati]: http://getbootstrap.com/components/#list-group-linked
  [18]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
  [19]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
  [glyphicon]: http://getbootstrap.com/components/#glyphicons
  [20]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
  [21]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
  [22]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
  [23]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
  [pannelli]: http://getbootstrap.com/components/#panels
  [24]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
  [25]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
  [26]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
  [27]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
  [gruppo elenchi collegati personalizzato]: http://getbootstrap.com/components/#list-group-custom-content
  [sistema griglia]: http://getbootstrap.com/css/#grid
  [utilità reattive]: http://getbootstrap.com/css/#responsive-utilities
  [28]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
  [29]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
  [30]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
  [Blog ufficiale di Bootstrap]: http://blog.getbootstrap.com/
  [Tutorial Twitter Bootstrap su Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
  [Editor e strumenti Bootstrap]: http://www.bootply.com/
  [Procedure consigliate per applicazioni Web W3C per dispositivi mobili]: http://www.w3.org/TR/mwabp/
  [Candidate recommendation W3C per query sui supporti]: http://www.w3.org/TR/css3-mediaqueries/
