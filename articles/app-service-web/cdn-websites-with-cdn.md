---
title: Usare la rete CDN di Azure nel servizio app di Azure
description: Esercitazione che descrive come distribuire nel servizio app di Azure un&quot;app Web che rende disponibile contenuto da un endpoint della rete CDN di Azure integrato
services: app-service\web,cdn
documentationcenter: .net
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: b81ee930-dd6d-4b65-971f-c4cb7902168c
ms.service: app-service
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9c52dcd46b8a293d7540bd1d00cfaeda3f8202a0


---
# <a name="use-azure-cdn-in-azure-app-service"></a>Usare la rete CDN di Azure nel servizio app di Azure
Il [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) può essere integrato con la [rete CDN di Azure](/services/cdn/) per migliorare le funzionalità di scalabilità globali intrinseche in [App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) , rendendo disponibile il contenuto delle app Web a livello globale da nodi server vicini ai clienti. Per un elenco aggiornato delle località in cui si trovano attualmente i nodi, vedere [qui](http://msdn.microsoft.com/library/azure/gg680302.aspx). In scenari, ad esempio, in cui vengono rese disponibili immagini statiche, questa integrazione può aumentare notevolmente le prestazioni del servizio app per app Web e migliorare significativamente l'esperienza utente dell'app Web in ogni parte del mondo. 

L'integrazione di app Web con la rete CDN di Azure offre i vantaggi seguenti:

* Integrazione della distribuzione del contenuto (immagini, script e fogli di stile) come parte del [processo di distribuzione continua](app-service-continuous-deployment.md) dell'app Web
* Facile aggiornamento dei pacchetti NuGet nell'app Web nel servizio app di Azure, come le versioni di jQuery o Bootstrap 
* Gestione dell'applicazione Web e del contenuto gestito dalla rete CDN dalla stessa interfaccia di Visual Studio
* Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-build"></a>Obiettivo di compilazione
Verrà distribuita un'app Web nel servizio app di Azure usando il modello MVC di ASP.NET predefinito in Visual Studio, si aggiungerà il codice per rendere disponibile contenuto da una rete CDN di Azure integrata, ad esempio un'immagine, i risultati delle azioni del controller e i file JavaScript e CSS predefiniti, e si scriverà anche il codice per configurare il meccanismo di fallback per i bundle forniti nel caso in cui la rete CDN sia offline.

## <a name="what-you-will-need"></a>Prerequisiti
Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

* Un [account Microsoft Azure](/account/)
* Visual Studio 2015 con [Azure SDK per .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409). Se si usa Visual Studio, la procedura può variare.

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure.
> 
> * È possibile [creare un account Azure gratuitamente](/pricing/free-trial/): si riceveranno crediti da usare per provare i servizi di Azure a pagamento e, anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio App Web.
> * È possibile [attivare i benefici della sottoscrizione Visual Studio](/pricing/member-offers/msdn-benefits-details/): con la sottoscrizione Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
> 
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint"></a>Distribuire un'app Web in Azure con un endpoint della rete CDN integrato
In questa sezione verrà distribuito nel servizio app il modello di applicazione MCV di ASP.NET predefinito di Visual Studio 2015, che verrà quindi integrato con un nuovo endpoint della rete CDN. Seguire le istruzioni riportate di seguito:

1. In Visual Studio 2015 creare una nuova applicazione Web ASP.NET dalla barra dei menu selezionando **File > Nuovo > Progetto > Web > Applicazione Web ASP.NET**. Assegnargli un nome e fare clic su **OK**.
   
    ![](media/cdn-websites-with-cdn/1-new-project.png)
2. Selezionare **MVC** e fare clic su **OK**.
   
    ![](media/cdn-websites-with-cdn/2-webapp-template.png)
3. Se non è ancora stato eseguito l'accesso all'account Azure, fare clic sull'icona dell'account nell'angolo superiore destro e seguire la finestra di dialogo per accedere all'account Azure. Al termine, configurare l'app come illustrato di seguito e quindi fare clic su **Nuovo** per creare un nuovo piano di servizio app per l'applicazione.  
   
    ![](media/cdn-websites-with-cdn/3-configure-webapp.png)
4. Configurare un nuovo piano di servizio app nella finestra di dialogo come illustrato di seguito e fare clic su **OK**. 
   
    ![](media/cdn-websites-with-cdn/4-app-service-plan.png)
5. Fare clic su **Crea** per creare l'app Web.
   
    ![](media/cdn-websites-with-cdn/5-create-website.png)
6. Dopo aver creato l'applicazione ASP.NET, pubblicarla in Azure nel riquadro attività del servizio app di Azure facendo clic su **Pubblica `<app name>` adesso in questa app Web**. Fare clic su **Pubblica** per completare il processo.
   
    ![](media/cdn-websites-with-cdn/6-publish-website.png)
   
    Dopo il completamento della pubblicazione, l'app Web verrà visualizzata nel browser. 
7. Per creare un endpoint della rete CDN, accedere al [portale di Azure](https://portal.azure.com). 
8. Fare clic su **+ Nuovo** > **Contenuti multimediali e rete CDN** > **Rete CDN**.
   
    ![](media/cdn-websites-with-cdn/create-cdn-profile.png)
9. Specificare **Rete CDN**, **Località**, **Gruppo di risorse**, **Piano tariffario**, quindi fare clic su **Crea**
   
    ![](media/cdn-websites-with-cdn/7-create-cdn.png)    
10. Nel pannello **Profilo CDN** fare clic sul pulsante **+ Endpoint**. Assegnargli un nome, selezionare **App Web** nell'elenco a discesa **Tipo origine** e l'app Web nell'elenco a discesa **Nome host dell'origine**, quindi fare clic su **Aggiungi**.  
    
     ![](media/cdn-websites-with-cdn/cdn-profile-blade.png)

    > [AZURE.NOTE] Dopo aver creato l'endpoint della rete CDN, nel pannello **Endpoint** viene visualizzato il relativo URL e il dominio di origine in esso integrato. È tuttavia possibile che la propagazione completa della configurazione del nuovo endpoint della rete CDN in tutte le località in cui si trovano i nodi della rete CDN richieda tempo. 

1. Tornare al pannello **Endpoint** e fare clic sul nome dell'endpoint della rete CDN appena creato.
   
    ![](media/cdn-websites-with-cdn/8-select-cdn.png)
2. Fare clic sul pulsante **Configura** . Nel pannello **Configura** selezionare **Memorizza nella cache tutti gli URL univoci** nell'elenco a discesa **Comportamento di memorizzazione nella cache della stringa di query**, quindi fare clic su **Salva**.

    ![](media/cdn-websites-with-cdn/9-enable-query-string.png)

Dopo averle abilitate, lo stesso collegamento a cui si accede con diverse stringhe di query verrà memorizzato nella cache come voci separate.

> [!NOTE]
> Benché non sia necessario abilitare la stringa di query per questa sezione dell'esercitazione, per comodità è consigliabile farlo prima possibile, perché la propagazione a tutti i nodi della rete CDN di qualsiasi modifica apportata in questa fase richiederà del tempo e non è auspicabile che contenuti non abilitati per le stringhe query intasino la cache della rete CDN (l'aggiornamento del contenuto della rete CDN verrà discusso più avanti).
> 
> 

1. Passare all'endpoint della rete CDN. Se l'endpoint è pronto, dovrebbe venire visualizzata l'app Web. Se viene visualizzato un errore **HTTP 404** , l'endpoint della rete CDN non è pronto. Potrebbe essere necessario attendere fino a un'ora affinché la configurazione della rete CDN sia propagata a tutti i nodi perimetrali. 
   
    ![](media/cdn-websites-with-cdn/11-access-success.png)
2. Successivamente, provare ad accedere al file **~/Content/bootstrap.css** nel progetto ASP.NET. Nella finestra del browser passare a **http://*&lt;nomereteCdn>*.azureedge.net/Content/bootstrap.css**. Nella configurazione illustrata, questo URL è il seguente:
   
        http://az673227.azureedge.net/Content/bootstrap.css
   
    che corrisponde all'URL di origine seguente all'endpoint della rete CDN:
   
        http://cdnwebapp.azurewebsites.net/Content/bootstrap.css
   
    Quando si passa a **http://*&lt;nomereteCdn>*.azureedge.net/Content/bootstrap.css**, viene richiesto di scaricare il file bootstrap.css inviato dall'app Web in Azure. 
   
    ![](media/cdn-websites-with-cdn/12-file-access.png)

È possibile accedere in maniera simile a qualsiasi URL pubblicamente accessibile all'indirizzo **http://*&lt;nomeServizio>*.cloudapp.net/**, direttamente dall'endpoint della rete CDN. ad esempio:

* Un file con estensione js dal percorso /Script
* Qualsiasi file di contenuto dal percorso /Content
* Qualsiasi controller/azione 
* Se la stringa di query viene abilitata sull'endpoint della rete CDN, qualsiasi URL con stringhe di query
* L'intera app Web di Azure, se tutto il contenuto è pubblico

Tenere presente che non è sempre una buona idea (almeno in generale) rendere disponibile un'intera app Web di Azure tramite la rete CDN. È necessario valutare diversi aspetti:

* Questo approccio richiede di rendere pubblico l'intero sito, perché la rete CDN di Azure non può gestire contenuti privati.
* Se l'endpoint della rete CDN passa in modalità offline per qualsiasi motivo, ad esempio per scopi di manutenzione o a causa dell'errore di un utente, passa in modalità offline anche l'intera app Web, a meno che non sia possibile reindirizzare i clienti all'URL di origine **http://*&lt;nomesito>*.azurewebsites.net/**. 
* Anche con impostazioni di controllo della cache personalizzate (vedere [Configurare le opzioni di memorizzazione nella cache dei file statici nell'app Web di Azure](#configure-caching-options-for-static-files-in-your-azure-web-app)), un endpoint della rete CDN non migliora le prestazioni dei contenuti altamente dinamici. Se si è provato a caricare la home page dall'endpoint della rete CDN come sopra illustrato, si è osservato che il caricamento della pagina iniziale predefinita, cioè una pagina abbastanza semplice, ha richiesto almeno cinque secondi la prima volta. Si può dunque immaginare quale sarebbe l'esperienza del cliente se questa pagina includesse contenuto dinamico da aggiornare ogni minuto. Gestire contenuti dinamici da un endpoint della rete CDN richiede una scadenza breve della cache, che si traduce in frequenti mancati riscontri nella cache sull'endpoint della rete CDN. Ciò influisce negativamente sulle prestazioni dell'app Web di Azure e vanifica lo scopo di una rete CDN.

L'alternativa consiste nel determinare quale contenuto rendere disponibile dalla rete CDN di Azure, valutando caso per caso nell'app Web di Azure. A tale scopo, si è già visto come accedere ai singoli file di contenuto dall'endpoint della rete CDN. Più avanti verrà illustrato come gestire una specifica azione del controller attraverso l'endpoint CDN in [Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#serve-content-from-controller-actions-through-azure-cdn).

## <a name="configure-caching-options-for-static-files-in-your-azure-web-app"></a>Configurare le opzioni di memorizzazione nella cache dei file statici nell'app Web di Azure
Con l'integrazione della rete CDN di Azure nell'app Web di Azure, è possibile specificare in che modo memorizzare il contenuto statico nella cache nell'endpoint della rete CDN. A tale scopo, aprire il file *Web.config* dal progetto ASP.NET (ad esempio **cdnwebapp**) e aggiungere un elemento `<staticContent>` a `<system.webServer>`. Il linguaggio XML seguente configura la scadenza della cache entro tre giorni.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

A questo punto, tutti i file statici nell'app Web di Azure osserveranno la stessa regola nella cache della rete CDN. Per un controllo più granulare delle impostazioni della cache, aggiungere un file *Web.config* in una cartella e quindi aggiungervi le impostazioni. Ad esempio, aggiungere un file *Web.config* alla cartella *\Content* e sostituire il contenuto con il seguente codice XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Queste impostazioni causano la memorizzazione nella cache di tutti i file statici della cartella *\Content* per 15 giorni.

Per altre informazioni su come configurare l'elemento `<clientCache>`, vedere [Client Cache &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache) (Cache client).

Nella prossima sezione, verrà anche descritto come configurare le impostazioni della cache per i risultati delle azioni del controller nella cache della rete CDN.

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure
Quando si integrano app Web con la rete CDN di Azure, è relativamente facile rendere disponibile contenuto dal controller attraverso la rete CDN di Azure. Anche in questo caso, se si sceglie di rendere disponibile l'intera app Web di Azure attraverso la rete CDN, non è necessario occuparsene personalmente, in quanto tutte le azioni del controller sono già raggiungibili attraverso la rete CDN. Tuttavia, per i motivi già indicati nella sezione [Distribuire un'app Web di Azure con un endpoint della rete CDN integrato](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), è invece possibile scegliere di selezionare l'azione del controller da rendere disponibile dalla rete CDN di Azure. [Maarten Balliauw](https://twitter.com/maartenballiauw) mostra come farlo con un divertente controller MemeGenerator nel video [Reducing latency on the web with the Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) (Riduzione della latenza sul Web con la rete CDN di Azure). che viene riprodotto semplicemente in questo articolo.

Nell'app Web, si supponga di voler generare meme basati sull'immagine di un giovane Chuck Norris (foto di [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) come questa:

![](media/cdn-websites-with-cdn/cdn-5-memegenerator.PNG)

Si usa una semplice azione `Index` che consente ai clienti di specificare i superlativi nell'immagine, quindi genera il meme dopo aver pubblicato nell'azione. Poiché si tratta di Chuck Norris, ci si aspetta che questa pagina diventi enormemente popolare in tutto il mondo. È un ottimo esempio di come gestire contenuto semi dinamico con la rete CDN di Azure. 

Seguire i passaggi precedenti per configurare questa azione del controller:

1. Nella cartella *\Controllers* creare un nuovo file con estensione cs denominato *MemeGeneratorController.cs* e sostituire il contenuto con il codice seguente. Sostituire il percorso del file per `~/Content/chuck.bmp` e il nome della rete CDN per `yourCDNName`.

        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace cdnwebapp.Controllers
        {
          public class MemeGeneratorController : Controller
          {
            static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

            public ActionResult Index()
            {
              return View();
            }

            [HttpPost, ActionName("Index")]
            public ActionResult Index_Post(string top, string bottom)
            {
              var identifier = Guid.NewGuid().ToString();
              if (!Memes.ContainsKey(identifier))
              {
                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
              }

              return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
            }

            [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
            public ActionResult Show(string id)
            {
              Tuple<string, string> data = null;
              if (!Memes.TryGetValue(id, out data))
              {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
              }

              if (Debugger.IsAttached) // Preserve the debug experience
              {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
              }
              else // Get content from Azure CDN
              {
                return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
              }
            }

            [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
            public ActionResult Generate(string top, string bottom)
            {
              string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
              Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

              using (Graphics graphics = Graphics.FromImage(bitmap))
              {
                SizeF size = new SizeF();
                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                }
                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                }
              }
              MemoryStream ms = new MemoryStream();
              bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
              return File(ms.ToArray(), "image/png");
            }

            private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
            {
              // Compute actual size, shrink if needed
              while (true)
              {
                size = g.MeasureString(text, font);

                // It fits, back out
                if (size.Height < i.Height &&
                     size.Width < i.Width) { return font; }

                // Try a smaller font (90% of old size)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
              }
            }
          }
        }

1. Fare clic con il pulsante destro del mouse sull'azione `Index()` predefinita e selezionare **Aggiungi visualizzazione**.
   
    ![](media/cdn-websites-with-cdn/cdn-6-addview.PNG)
2. Accettare le impostazioni di seguito e fare clic su **Aggiungi**.
   
   ![](media/cdn-websites-with-cdn/cdn-7-configureview.PNG)
3. Aprire il nuovo file *Views\MemeGenerator\Index.cshtml* e sostituire il contenuto con il semplice HTML seguente per inviare i superlativi:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
4. Pubblicare di nuovo l'app Web di Azure e passare a **http://*&lt;nomeServizio>*.cloudapp.net/MemeGenerator/Index** nel browser. 

Quando si inviano i valori del modulo a `/MemeGenerator/Index`, il metodo di azione `Index_Post` restituisce un collegamento al metodo di azione `Show` con il rispettivo identificatore di input. Facendo clic sul collegamento si raggiunge il codice seguente:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
      Tuple<string, string> data = null;
      if (!Memes.TryGetValue(id, out data))
      {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
      }

      if (Debugger.IsAttached) // Preserve the debug experience
      {
        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
      }
      else // Get content from Azure CDN
      {
        return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
      }
    }

Se il debugger locale è collegato, si avrà una normale esperienza di debug con un reindirizzamento locale. Se viene eseguito nell'app Web di Azure, si viene reindirizzati a:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

che corrisponde all'URL di origine seguente in corrispondenza dell'endpoint della rete CDN:

    http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Dopo la regola di riscrittura URL precedentemente applicata, il file effettivo che viene memorizzato nella cache dell'endpoint della rete CDN è il seguente:

    http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Sarà quindi possibile usare l'attributo `OutputCacheAttribute` sul metodo `Generate` per specificare come memorizzare nella cache il risultato dell'azione, che verrà rispettato dalla rete CDN di Azure. Il codice seguente specifica la scadenza di una cache entro un'ora (3.600 secondi).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Analogamente, è possibile rendere disponibile contenuto da qualsiasi azione del controller nell'app Web di Azure attraverso la rete CDN di Azure, con l'opzione di memorizzazione nella cache desiderata.

Nella sezione successiva verrà illustrato come gestire gli script e i file CSS in bundle e minimizzati attraverso la rete CDN di Azure. 

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure
Gli script e i fogli di stile CSS cambiano in maniera non frequente e sono i principali candidati per la cache della rete CDN di Azure. Il modo più semplice per integrare la creazione di bundle e la minimizzazione con la rete CDN di Azure consiste nel rendere disponibile l'intera app Web attraverso la rete CDN. Tuttavia, poiché si potrebbe voler evitare questo approccio per i motivi descritti in [Integrare un endpoint della rete CDN di Azure con l'app Web di Azure e rendere disponibile contenuto statico nelle pagine Web dalla rete CDN di Azure](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), verrà ora mostrato come procedere mantenendo l'esperienza di sviluppo desiderata per quanto riguarda la creazione di aggregazione e la minimizzazione di ASP.NET, ad esempio:

* Ottima esperienza della modalità di debug
* Distribuzione semplificata
* Aggiornamenti immediati delle versioni di script/css dei client
* Meccanismo di fallback in caso di errore dell'endpoint della rete CDN
* Riduzione delle modifiche del codice

Nel progetto ASP.NET creato in [Integrare un endpoint della rete CDN di Azure con l'app Web di Azure e rendere disponibile contenuto statico nelle pagine Web dalla rete CDN di Azure](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), aprire *App_Start\BundleConfig.cs* e osservare le chiamate del metodo `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

La prima istruzione `bundles.Add()` aggiunge un'aggregazione di script alla directory virtuale `~/bundles/jquery`. Aprire quindi *Views\Shared\_Layout.cshtml* per verificare come viene eseguito il rendering del bundle di script. Dovrebbe essere possibile trovare la riga di codice Razor seguente:

    @Scripts.Render("~/bundles/jquery")

Quando questo codice Razor viene eseguito nell'app Web di Azure, eseguirà il rendering di un tag `<script>` per il bundle di script in modo simile al seguente: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Tuttavia, quando il codice viene eseguito in Visual Studio digitando `F5`, comporta il rendering individuale di ogni file di script nell'aggregazione (nel caso sopra citato, l'aggregazione contiene un solo file di script):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Ciò consente di eseguire il debug del codice JavaScript nell'ambiente di sviluppo e allo stesso tempo di ridurre le connessioni client simultanee (creazione di bundle) e migliorare le prestazioni di download dei file (minimizzazione) in produzione. È un'ottima funzionalità da mantenere con l'integrazione nella rete CDN di Azure. Per di più, dal momento che il bundle di cui è stato eseguito il rendering contiene una stringa di versione generata automaticamente, è opportuno replicare tale funzionalità in modo che ogni volta che si aggiorna la versione di jQuery attraverso NuGet è possibile aggiornarla sul lato client non appena possibile.

Attenersi alla procedura seguente per integrare la creazione di bundle e la minimizzazione ASP.NET con l'endpoint della rete CDN.

1. Tornando ad *App_Start\BundleConfig.cs*, modificare i metodi `bundles.Add()` in modo da usare un [costruttore di aggregazioni](http://msdn.microsoft.com/library/jj646464.aspx) diverso, che specifichi un indirizzo di rete CDN. A questo scopo, sostituire la definizione del metodo `RegisterBundles` con il codice seguente:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
          bundles.UseCdn = true;
          var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
            .GetName().Version.ToString();
          var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?" + version;
   
          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                "~/Scripts/jquery-{version}.js"));
   
          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                "~/Scripts/jquery.validate*"));
   
          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizr")).Include(
                "~/Scripts/modernizr-*"));
   
          bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));
   
          bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
        }

    Assicurarsi di sostituire `<yourCDNName>` con il nome della rete CDN.

    In altri termini, si imposta `bundles.UseCdn = true` e si aggiunge un URL della rete CDN definito con precisione a ogni aggregazione. Ad esempio, il primo costruttore nel codice:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    è lo stesso di: 

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?<W.X.Y.Z>"))

    Questo costruttore comunica alle operazioni di creazione di bundle e minimizzazione ASP.NET di eseguire il rendering dei singoli file di script quando ne viene eseguito il debug a livello locale, ma di usare l'indirizzo della rete CDN specificato per accedere allo script in questione. Notare tuttavia due importanti caratteristiche di questo URL della rete CDN definito con precisione:

    - L'origine per questo URL della rete CDN è `http://<yourSiteName>.azurewebsites.net/bundles/jquery?<W.X.Y.Z>`, che in realtà è la directory virtuale dell'aggregazione di script nell'applicazione Web.
    - Poiché si sta usando un costruttore CDN, il tag dello script CDN per il bundle non contiene più la stringa di versione generata automaticamente nell'URL di cui è stato eseguito il rendering. È necessario generare manualmente una stringa di versione univoca ogni volta che il bundle di script viene modificato per forzare un mancato riscontro nella cache nella rete CDN di Azure. Allo stesso tempo, questa stringa di versione univoca deve rimanere costante per tutta la durata della distribuzione per aumentare i riscontri nella cache nella rete CDN di Azure dopo aver distribuito il bundle.

1. La stringa di query `<W.X.Y.Z>` effettua il pull da *Properties\AssemblyInfo.cs* nel progetto ASP.NET. È possibile prevedere un flusso di lavoro di distribuzione che includa l'incremento della versione di assembly ogni volta che si pubblica su Azure. In alternativa, è possibile modificare solo il file *Properties\AssemblyInfo.cs* nel progetto per incrementare automaticamente la stringa di versione ogni volta che si compila, usando il carattere jolly '*'. Ad esempio, modificare `AssemblyVersion` come illustrato di seguito:
   
        [assembly: AssemblyVersion("1.0.0.*")]
   
    Qualsiasi altra strategia volta a semplificare la generazione di una stringa univoca per la durata della distribuzione avrà esito positivo.
2. Ripubblicare l'applicazione ASP.NET e accedere alla home page.
3. Visualizzare il codice HTML relativo alla pagina. Dovrebbe essere possibile visualizzare l'URL della rete CDN di cui è stato eseguito il rendering, con una stringa di versione univoca ogni volta che si ripubblicano le modifiche all'app Web di Azure. ad esempio:  
   
        ...
        <link href="http://az673227.azureedge.net/Content/css?1.0.0.25449" rel="stylesheet"/>
        <script src="http://az673227.azureedge.net/bundles/modernizer?1.0.0.25449"></script>
        ...
        <script src="http://az673227.azureedge.net/bundles/jquery?1.0.0.25449"></script>
        <script src="http://az673227.azureedge.net/bundles/bootstrap?1.0.0.25449"></script>
        ...
4. In Visual Studio eseguire il debug l'applicazione ASP.NET in Visual Studio digitando `F5`., 
5. Visualizzare il codice HTML relativo alla pagina. Sarà ancora possibile visualizzare ciascun file di script di cui sia stato eseguito il rendering, in modo che l'esperienza di debug sia coerente in Visual Studio.  
   
        ...
        <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
        <script src="/Scripts/modernizr-2.6.2.js"></script>
        ...
        <script src="/Scripts/jquery-1.10.2.js"></script>
        <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
        ...    

## <a name="fallback-mechanism-for-cdn-urls"></a>Meccanismo di fallback per gli URL della rete CDN
Se si verifica un errore nell'endpoint della rete CDN di Azure per un motivo qualsiasi, è consigliabile configurare l'accesso della pagina Web al server Web di origine come opzione di fallback per il caricamento di JavaScript o Bootstrap. È già abbastanza spiacevole perdere le immagini nell'app Web a causa della mancata disponibilità della rete CDN, ma ancora più grave è perdere le funzionalità essenziali della pagina fornite dagli script e dai fogli di stile.

La classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contiene una proprietà denominata [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) che consente di configurare il meccanismo di fallback per l'errore della rete CDN. Per usare questa proprietà, seguire i passaggi descritti di seguito:

1. Nel progetto ASP.NET aprire *App_Start\BundleConfig.cs*, dove è stato aggiunto un URL della rete CDN in ogni [costruttore di bundle](http://msdn.microsoft.com/library/jj646464.aspx), quindi aggiungere il codice `CdnFallbackExpression` in quattro posizioni, come indicato, per aggiungere il meccanismo di fallback ai bundle predefiniti.  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
          var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
            .GetName().Version.ToString();
          var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
          bundles.UseCdn = true;
   
          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
                { CdnFallbackExpression = "window.jquery" }
                .Include("~/Scripts/jquery-{version}.js"));
   
          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
                { CdnFallbackExpression = "$.validator" }
                .Include("~/Scripts/jquery.validate*"));
   
          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
                { CdnFallbackExpression = "window.Modernizr" }
                .Include("~/Scripts/modernizr-*"));
   
          bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                { CdnFallbackExpression = "$.fn.modal" }
                .Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
          bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
        }
   
    Quando `CdnFallbackExpression` non è Null, lo script viene inserito nel linguaggio HTML per provare se l'aggregazione è stata caricata correttamente. In caso contrario, accedere all'aggregazione direttamente dal server Web dell'origine. Questa proprietà deve essere impostata su un'espressione JavaScript che verifichi se il rispettivo bundle CDN è stato caricato correttamente. L'espressione necessaria per testare ogni bundle differisce in base al contenuto. Per i bundle predefiniti sopra riportati:
   
   * `window.jquery` viene definito nel file jquery-{version}.js
   * `$.validator` viene definito nel file jquery.validate.js
   * `window.Modernizr` viene definito nel file modernizer-{version}.js
   * `$.fn.modal` viene definito nel file bootstrap.js
     
     Come è possibile osservare, non è stata impostata la proprietà CdnFallbackExpression per l'aggregazione `~/Cointent/css`, perché attualmente esiste un [bug in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) che inserisce un tag `<script>` per il file CSS di fallback invece del tag `<link>` previsto.
     
     Un'ottima soluzione di [Fallback Style Bundle](https://github.com/EmberConsultingGroup/StyleBundleFallback) è tuttavia offerta da [Ember Consulting Group](https://github.com/EmberConsultingGroup). 
2. Per usare questa soluzione alternativa, creare un nuovo file con estensione CS nella cartella *App_Start* del progetto ASP.NET, denominata *StyleBundleExtensions.cs*, e sostituirne il contenuto con il [codice GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 
3. In *App_Start\StyleFundleExtensions.cs* rinominare lo spazio dei nomi con lo spazio dei nomi dell'applicazione ASP.NET, ad esempio **cdnwebapp**. 
4. Tornare a `App_Start\BundleConfig.cs` e sostituire l'ultima istruzione `bundles.Add` con il codice seguente:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
          .IncludeFallback("~/Content/css", "sr-only", "width", "1px")
          .Include(
            "~/Content/bootstrap.css",
            "~/Content/site.css"));
   
    Questo nuovo metodo di estensione si basa sulla stessa idea di inserire lo script nel linguaggio HTML per cercare in DOM il nome di classe, il nome di regola e il valore di regola corrispondenti definiti nel bundle CSS, eseguendo il fallback sul server Web in caso non riesca a trovare la corrispondenza.
5. Pubblicare di nuovo l'app Web di Azure e accedere alla home page. 
6. Visualizzare il codice HTML relativo alla pagina. Gli script inseriti dovrebbero essere simili al seguente:    
   
    ```
    ...
    <link href="http://az673227.azureedge.net/Content/css?1.0.0.25474" rel="stylesheet"/>
   <script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i < len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az673227.azureedge.net/Content/css?1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                        }
                    }
                }
                return true;
            }())||document.write('<script src="/Content/css"><\/script>');</script>
   
    <script src="http://az673227.azureedge.net/bundles/modernizer?1.0.0.25474"></script>
     <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
    ... 
    <script src="http://az673227.azureedge.net/bundles/jquery?1.0.0.25474"></script>
    <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
     <script src="http://az673227.azureedge.net/bundles/bootstrap?1.0.0.25474"></script>
     <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
    ...
    ```
   
    Si noti che lo script inserito per l'aggregazione CSS contiene ancora residui della proprietà `CdnFallbackExpression` nella riga:
   
        }())||document.write('<script src="/Content/css"><\/script>');</script>
   
    Poiché però la prima parte dell'espressione || restituirà sempre true (nella riga subito sopra), la funzione document.write() non verrà mai eseguita.
7. Per verificare il funzionamento dello script di fallback, tornare indietro al pannello endpoint della rete CDN e fare clic su **Arresta**.
   
    ![](media/cdn-websites-with-cdn/13-test-fallback.png)
8. Aggiornare la finestra del browser per l'app Web di Azure. Si noterà ora che tutti gli script e i fogli di stile vengono caricati correttamente.

## <a name="more-information"></a>Altre informazioni
* [Panoramica della Rete per la distribuzione di contenuti (CDN) di Azure](../cdn/cdn-overview.md)
* [Uso della rete CDN di Azure](../cdn/cdn-create-new-endpoint.md)
* [Integrare un servizio cloud con la rete CDN di Azure](../cdn/cdn-cloud-service-with-cdn.md)
* [Creazione di aggregazioni e minimizzazione ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)




<!--HONumber=Nov16_HO3-->


