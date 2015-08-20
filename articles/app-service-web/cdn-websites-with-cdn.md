<properties 
	pageTitle="Usare la rete CDN di Azure nel servizio app di Azure" 
	description="Esercitazione che descrive come distribuire nel servizio app di Azure un'app Web che rende disponibile contenuto da un endpoint della rete CDN di Azure integrato" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>


<tags 
	ms.service="app-service-web" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="cephalin"/>



#<a name="intro"></a>Usare la rete CDN di Azure in Azure App Service

Il [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) può essere integrato con la rete [CDN di Azure](/services/cdn/) per migliorare le funzionalità di scalabilità globali intrinseche in [App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714), rendendo disponibile il contenuto delle app Web a livello globale da nodi server vicini ai clienti. Per un elenco aggiornato delle località in cui si trovano attualmente i nodi, vedere [qui](http://msdn.microsoft.com/library/azure/gg680302.aspx). In scenari, ad esempio, in cui vengono rese disponibili immagini statiche, questa integrazione può aumentare notevolmente le prestazioni del servizio app per app Web e migliorare significativamente l'esperienza utente dell'app Web in ogni parte del mondo.

L'integrazione di app Web con la rete CDN di Azure offre i vantaggi seguenti:

- Integrazione della distribuzione del contenuto (immagini, script e fogli di stile) come parte del [processo di distribuzione continua](web-sites-publish-source-control.md) dell'app Web
- Facile aggiornamento dei pacchetti NuGet nell'app Web nel servizio app di Azure, come le versioni di jQuery o Bootstrap 
- Gestione dell'applicazione Web e del contenuto gestito dalla rete CDN dalla stessa interfaccia di Visual Studio
- Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure

## Obiettivo di compilazione ##

Verrà distribuita un'app Web nel servizio app di Azure usando il modello MVC di ASP.NET predefinito in Visual Studio, si aggiungerà il codice per rendere disponibile contenuto da una rete CDN di Azure integrata, ad esempio un'immagine, i risultati delle azioni del controller e i file JavaScript e CSS predefiniti, e si scriverà anche il codice per configurare il meccanismo di fallback per i bundle forniti nel caso in cui la rete CDN sia offline.

## Prerequisiti ##

Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

-	Un [account Microsoft Azure](/account/) attivo
-	Visual Studio 2013 con [Azure SDK per .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. 
> + È possibile [aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio le app Web. 
> + È possibile [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
>
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="deploy"></a>
## Distribuire un'app Web in Azure con un endpoint della rete CDN integrato ##

In questa sezione verrà distribuito nel servizio app il modello di applicazione MCV di ASP.NET predefinito di Visual Studio 2013, che verrà quindi integrato con un nuovo endpoint della rete CDN. Seguire le istruzioni riportate di seguito:

1. In Visual Studio 2013 creare una nuova applicazione Web ASP.NET dalla barra dei menu selezionando **File > Nuovo > Progetto > Web > Applicazione Web ASP.NET**. Assegnargli un nome e fare clic su **OK**.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. Selezionare **MVC** e fare clic su **Gestisci sottoscrizioni**.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. Fare clic su **Sign In**.

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. Nella pagina di accesso eseguire l'accesso con l'account Microsoft usato per attivare l'account Azure.
7. Una volta effettuato l'accesso, fare clic su **Chiudi**. Quindi, fare clic su **OK** per continuare.

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Se non è stata creata un'app Web in Azure, è possibile farlo facilmente in Visual Studio. Nella finestra di dialogo **Configura sito Web di Microsoft Azure** verificare che il nome del sito sia univoco. Fare quindi clic su **OK**.

	<!--todo: need 2.5.1 screenshot-->
	![](media/cdn-websites-with-cdn/5-create-website.png)

9. Dopo aver creato l'applicazione ASP.NET, pubblicarla in Azure nel riquadro Attività di pubblicazione Web facendo clic su **Pubblica `<app name>`￼ su questo sito adesso**. Fare clic su **Pubblica** per completare il processo.

	<!--todo: need 2.5.1 screenshot-->
	![](media/cdn-websites-with-cdn/6-publish-website.png)

	Dopo il completamento della pubblicazione, l'app Web verrà visualizzata nel browser.

1. Per creare un endpoint della rete CDN, accedere al [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
2. Fare clic su **Nuovo** > **Servizi app** > **Rete CDN** > **Creazione rapida**. Selezionare **http://*&lt;sitename>*.azurewebsites.net/** e fare clic su **Crea**.

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	> [AZURE.NOTE]Dopo aver creato l'endpoint della rete CDN, nel portale di Azure viene visualizzato il relativo URL e il dominio di origine in esso integrato. È tuttavia possibile che la propagazione completa della configurazione del nuovo endpoint della rete CDN in tutte le località in cui si trovano i nodi della rete CDN richieda tempo.

3. Tornare al portale di Azure e nella scheda **Rete CDN** fare clic sul nome dell'endpoint della rete CDN appena creato.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. Fare clic su **Abilita stringa di query** per abilitare le stringhe di query nella cache della rete CDN. Dopo averle abilitate, lo stesso collegamento a cui si accede con diverse stringhe di query verrà memorizzato nella cache come voci separate.

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[AZURE.NOTE]Benché non sia necessario abilitare la stringa di query per questa sezione dell'esercitazione, per comodità è consigliabile farlo prima possibile, perché la propagazione a tutti i nodi della rete CDN di qualsiasi modifica apportata in questa fase richiederà del tempo e non è auspicabile che contenuti non abilitati per le stringhe query intasino la cache della rete CDN (l'aggiornamento del contenuto della rete CDN verrà discusso più avanti).

2. Fare clic sull'indirizzo dell'endpoint della rete CDN. Se l'endpoint è pronto, dovrebbe venire visualizzata l'app Web. Se viene visualizzato un errore **HTTP 404**, l'endpoint della rete CDN non è pronto. Potrebbe essere necessario attendere fino a un'ora affinché la configurazione della rete CDN sia propagata a tutti i nodi perimetrali.

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. Successivamente, provare ad accedere al file **\~/Content/bootstrap.css** nel progetto ASP.NET. Nella finestra del browser passare a **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**. Nella configurazione illustrata, questo URL è il seguente:

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	che corrisponde all'URL di origine seguente all'endpoint della rete CDN:

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	Quando si passa a **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**, viene richiesto di scaricare il file bootstrap.css inviato dall'app Web in Azure.

	![](media/cdn-websites-with-cdn/12-file-access.png)

È possibile accedere in maniera simile a qualsiasi URL pubblicamente accessibile all'indirizzo **http://*&lt;serviceName>*.cloudapp.net/**, direttamente dall'endpoint della rete CDN. ad esempio:

-	Un file con estensione js dal percorso /Script
-	Qualsiasi file di contenuto dal percorso /Content
-	Qualsiasi controller/azione 
-	Se la stringa di query viene abilitata sull'endpoint della rete CDN, qualsiasi URL con stringhe di query
-	L'intera app Web di Azure, se tutto il contenuto è pubblico

Tenere presente che non è sempre una buona idea (almeno in generale) rendere disponibile un'intera app Web di Azure tramite la rete CDN. È necessario valutare diversi aspetti:

-	Questo approccio richiede di rendere pubblico l'intero sito, perché la rete CDN di Azure non può gestire contenuti privati.
-	Se l'endpoint della rete CDN passa in modalità offline per qualsiasi motivo, ad esempio per scopi di manutenzione o a causa dell'errore di un utente, passa in modalità offline anche l'intera app Web, a meno che non sia possibile reindirizzare i clienti all'URL di origine **http://*&lt;sitename>*.azurewebsites.net/**. 
-	Anche con impostazioni di controllo della cache personalizzate (vedere [Configurare le opzioni di memorizzazione nella cache dei file statici nell'app Web di Azure](#caching)), un endpoint della rete CDN non migliora le prestazioni dei contenuti altamente dinamici. Se si è provato a caricare la home page dall'endpoint della rete CDN come sopra illustrato, si è osservato che il caricamento della pagina iniziale predefinita, cioè una pagina abbastanza semplice, ha richiesto almeno cinque secondi la prima volta. Si può dunque immaginare quale sarebbe l'esperienza del cliente se questa pagina includesse contenuto dinamico da aggiornare ogni minuto. Gestire contenuti dinamici da un endpoint della rete CDN richiede una scadenza breve della cache, che si traduce in frequenti mancati riscontri nella cache sull'endpoint della rete CDN. Ciò influisce negativamente sulle prestazioni dell'app Web di Azure e vanifica lo scopo di una rete CDN.

L'alternativa consiste nel determinare quale contenuto rendere disponibile dalla rete CDN di Azure, valutando caso per caso nell'app Web di Azure. A tale scopo, si è già visto come accedere ai singoli file di contenuto dall'endpoint della rete CDN. Più avanti verrà illustrato come gestire una specifica azione del controller attraverso l'endpoint CDN in [Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller).

<a name="caching"></a>
## Configurare le opzioni di memorizzazione nella cache dei file statici nell'app Web di Azure ##

Con l'integrazione della rete CDN di Azure nell'app Web di Azure, è possibile specificare in che modo memorizzare il contenuto statico nella cache nell'endpoint della rete CDN. A tale scopo, aprire il file *Web.config*dal progetto ASP.NET (ad esempio **cdnwebapp**) e aggiungere un elemento `<staticContent>` a `<system.webServer>`. Il codice XML riportato di seguito consente di configurare la cache in modo che scada dopo 3 giorni. 
<<pre class="prettyprint">
&lt;system.webServer&gt;
  &lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;
  ...
&lt;/system.webServer&gt;
</pre>

A questo punto, tutti i file statici nell'app Web di Azure osserveranno la stessa regola nella cache della rete CDN. Per un controllo più granulare delle impostazioni della cache, aggiungere un file *Web.config* in una cartella e quindi aggiungervi le impostazioni. Ad esempio, aggiungere un file *Web.config* alla cartella *\\Content* e sostituire il contenuto con il seguente linguaggio XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Queste impostazioni causano la memorizzazione nella cache di tutti i file statici della cartella *\Content* per 15 giorni.

Per altre informazioni su come configurare l'elemento `<clientCache>`, vedere l'argomento relativo alla [cache client &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Nella sezione [Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller) verrà illustrato anche come configurare le impostazioni della cache per ottenere i risultati dell'azione del controller nella cache della rete CDN.

<a name="controller"></a>
## Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure ##

Quando si integrano app Web con la rete CDN di Azure, è relativamente facile rendere disponibile contenuto dal controller attraverso la rete CDN di Azure. Anche in questo caso, se si sceglie di rendere disponibile l'intera app Web di Azure attraverso la rete CDN, non è necessario occuparsene personalmente, in quanto tutte le azioni del controller sono già raggiungibili attraverso la rete CDN. Tuttavia, per i motivi già indicati nella sezione [Distribuire un'app Web di Azure con un endpoint della rete CDN integrato](#deploy), è invece possibile scegliere di selezionare l'azione del controller da rendere disponibile dalla rete CDN di Azure. [Maarten Balliauw](https://twitter.com/maartenballiauw) mostra come farlo con un divertente controller MemeGenerator nel video sulla [riduzione della latenza sul Web con la rete CDN di Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). che viene riprodotto semplicemente in questo articolo.

Nell'app Web, si supponga di voler generare meme basati sull'immagine di un giovane Chuck Norris (foto di [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) come questa:

![](media/cdn-websites-with-cdn/cdn-5-memegenerator.PNG)

Si usa una semplice azione `Index` che consente ai clienti di specificare i superlativi nell'immagine, quindi genera il meme dopo aver pubblicato nell'azione. Poiché si tratta di Chuck Norris, ci si aspetta che questa pagina diventi enormemente popolare in tutto il mondo. È un ottimo esempio di come gestire contenuto semi dinamico con la rete CDN di Azure.

Seguire i passaggi precedenti per configurare questa azione del controller:

1. Nella cartella *\Controllers*, creare un nuovo file con estensione cs denominato *MemeGeneratorController.cs* e sostituire il contenuto con il codice seguente. Sostituire il percorso del file per `~/Content/chuck.bmp` e il nome della rete CDN per `yourCDNName`.
	<pre class="prettyprint">
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
	        static readonly Dictionary&lt;string, Tuple&lt;string ,string&gt;&gt; Memes = new Dictionary&lt;string, Tuple&lt;string, string&gt;&gt;();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple&lt;string, string&gt;(top, bottom));
	            }
	
	            return Content(&quot;&lt;a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;&gt;here&#39;s your meme&lt;/a&gt;&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple&lt;string, string&gt; data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;~/Content/chuck.bmp&quot;);
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
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
	                if (size.Height &lt; i.Height &amp;&amp;
	                     size.Width &lt; i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. Fare clic con il pulsante destro del mouse sull'azione `Index()` predefinita e selezionare **Aggiungi visualizzazione**.

	![](media/cdn-websites-with-cdn/cdn-6-addview.PNG)

3.  Accettare le impostazioni di seguito e fare clic su **Aggiungi**.

	![](media/cdn-websites-with-cdn/cdn-7-configureview.PNG)

4. Aprire il nuovo file *Views\\MemeGenerator\\Index.cshtml* e sostituire il contenuto con il semplice HTML seguente per inviare i superlativi:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Pubblicare di nuovo l'app Web di Azure e passare a **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index** nel browser.

Quando si inviano i valori del modulo a `/MemeGenerator/Index`, il metodo di azione `Index_Post` restituisce un collegamento al metodo di azione `Show` con il rispettivo identificatore di input. Facendo clic sul collegamento, si raggiunge il codice seguente: 
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple&lt;string, string&gt; data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Se il debugger locale è collegato, si avrà una normale esperienza di debug con un reindirizzamento locale. Se viene eseguito nell'app Web di Azure, si viene reindirizzati a:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

che corrisponde all'URL di origine seguente in corrispondenza dell'endpoint della rete CDN:

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Dopo la regola di riscrittura URL precedentemente applicata, il file effettivo che viene memorizzato nella cache dell'endpoint della rete CDN è il seguente:

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Sarà quindi possibile usare l'attributo `OutputCacheAttribute` sul metodo `Generate` per specificare come memorizzare nella cache il risultato dell'azione, che verrà rispettato dalla rete CDN di Azure. Il codice seguente specifica la scadenza di una cache entro un'ora (3.600 secondi).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Analogamente, è possibile rendere disponibile contenuto da qualsiasi azione del controller nell'app Web di Azure attraverso la rete CDN di Azure, con l'opzione di memorizzazione nella cache desiderata.

Nella sezione successiva verrà illustrato come gestire gli script e i file CSS in bundle e minimizzati attraverso la rete CDN di Azure.

<a name="bundling"></a>
## Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure ##

Gli script e i fogli di stile CSS cambiano in maniera non frequente e sono i principali candidati per la cache della rete CDN di Azure. Il modo più semplice per integrare la creazione di bundle e la minimizzazione con la rete CDN di Azure consiste nel rendere disponibile l'intera app Web attraverso la rete CDN. Tuttavia, poiché si potrebbe voler evitare questo approccio per i motivi descritti in [Integrare un endpoint della rete CDN di Azure con l'app Web di Azure e rendere disponibile contenuto statico nelle pagine Web dalla rete CDN di Azure](#deploy), verrà ora mostrato come procedere mantenendo l'esperienza di sviluppo desiderata per quanto riguarda la creazione di aggregazione e la minimizzazione di ASP.NET, ad esempio:

-	Ottima esperienza della modalità di debug
-	Distribuzione semplificata
-	Aggiornamenti immediati delle versioni di script/css dei client
-	Meccanismo di fallback in caso di errore dell'endpoint della rete CDN
-	Riduzione delle modifiche del codice

Nel progetto ASP.NET creato in [Integrare un endpoint della rete CDN di Azure con l'app Web di Azure e rendere disponibile contenuto statico nelle pagine Web dalla rete CDN di Azure](#deploy), aprire *App\_Start\\BundleConfig.cs* e osservare le chiamate del metodo `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

La prima istruzione `bundles.Add()` aggiunge un'aggregazione di script alla directory virtuale `~/bundles/jquery`. Quindi, aprire *Views\\Shared\_Layout.cshtml* per vedere come viene eseguito il rendering del bundle di script. Dovrebbe essere possibile trovare la riga di codice Razor seguente:

    @Scripts.Render("~/bundles/jquery")

Quando questo codice Razor viene eseguito nell'app Web di Azure, eseguirà il rendering di un tag `<script>` per il bundle di script in modo simile al seguente:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Tuttavia, quando il codice viene eseguito in Visual Studio digitando `F5`, comporta il rendering individuale di ogni file di script nell'aggregazione (nel caso sopra citato, l'aggregazione contiene un solo file di script):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Ciò consente di eseguire il debug del codice JavaScript nell'ambiente di sviluppo e allo stesso tempo di ridurre le connessioni client simultanee (creazione di bundle) e migliorare le prestazioni di download dei file (minimizzazione) in produzione. È un'ottima funzionalità da mantenere con l'integrazione nella rete CDN di Azure. Per di più, dal momento che il bundle di cui è stato eseguito il rendering contiene una stringa di versione generata automaticamente, è opportuno replicare tale funzionalità in modo che ogni volta che si aggiorna la versione di jQuery attraverso NuGet è possibile aggiornarla sul lato client non appena possibile.

Attenersi alla procedura seguente per integrare la creazione di bundle e la minimizzazione ASP.NET con l'endpoint della rete CDN.

1. Tornando a *App\_Start\\BundleConfig.cs*, modificare i metodi `bundles.Add()` in modo da usare un [costruttore di aggregazioni](http://msdn.microsoft.com/library/jj646464.aspx) diverso, che specifichi un indirizzo di rete CDN. A questo scopo, sostituire la definizione del metodo `RegisterBundles` con il codice seguente:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/{0}?v=&quot; + version;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	Assicurarsi di sostituire `<yourCDNName>` con il nome della rete CDN.

	In altri termini, si imposta `bundles.UseCdn = true` e si aggiunge un URL della rete CDN definito con precisione a ogni aggregazione. Ad esempio, il primo costruttore nel codice:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	è lo stesso di:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Questo costruttore comunica alle operazioni di creazione di bundle e minimizzazione ASP.NET di eseguire il rendering dei singoli file di script quando ne viene eseguito il debug a livello locale, ma di usare l'indirizzo della rete CDN specificato per accedere allo script in questione. Notare tuttavia due importanti caratteristiche di questo URL della rete CDN definito con precisione:
	
	-	L'origine per questo URL della rete CDN è `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`, che in realtà è la directory virtuale dell'aggregazione di script nell'applicazione Web.
	-	Poiché si sta usando un costruttore CDN, il tag dello script CDN per il bundle non contiene più la stringa di versione generata automaticamente nell'URL di cui è stato eseguito il rendering. È necessario generare manualmente una stringa di versione univoca ogni volta che il bundle di script viene modificato per forzare un mancato riscontro nella cache nella rete CDN di Azure. Allo stesso tempo, questa stringa di versione univoca deve rimanere costante per tutta la durata della distribuzione per aumentare i riscontri nella cache nella rete CDN di Azure dopo aver distribuito il bundle.
	-	La stringa di query v=<W.X.Y.Z> effettua il pull da *Properties\\AssemblyInfo.cs* nel progetto ASP.NET. È possibile prevedere un flusso di lavoro di distribuzione che includa l'incremento della versione di assembly ogni volta che si pubblica su Azure. In alternativa, è possibile modificare solo il file *Properties\\AssemblyInfo.cs* nel progetto per incrementare automaticamente la stringa di versione ogni volta che si compila, usando il carattere jolly "*". ad esempio:

			[assembly: AssemblyVersion("1.0.0.*")]
	
		Qualsiasi altra strategia volta a semplificare la generazione di una stringa univoca per la durata della distribuzione avrà esito positivo.

3. Ripubblicare l'applicazione ASP.NET e accedere alla home page.
 
4. Visualizzare il codice HTML relativo alla pagina. Dovrebbe essere possibile visualizzare l'URL della rete CDN di cui è stato eseguito il rendering, con una stringa di versione univoca ogni volta che si ripubblicano le modifiche all'app Web di Azure. Ad esempio:
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

5. In Visual Studio eseguire il debug l'applicazione ASP.NET in Visual Studio digitando `F5`.,

6. Visualizzare il codice HTML relativo alla pagina. Sarà ancora possibile visualizzare ciascun file di script di cui sia stato eseguito il rendering, in modo che l'esperienza di debug sia coerente in Visual Studio.
	<pre class="prettyprint">
	...
	
	    &lt;link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/&gt;
	&lt;link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/&gt;
	
	    &lt;script src=&quot;/Scripts/modernizr-2.6.2.js&quot;&gt;&lt;/script&gt;
	
	...
	
	    &lt;script src=&quot;/Scripts/jquery-1.10.2.js&quot;&gt;&lt;/script&gt;
	
	    &lt;script src=&quot;/Scripts/bootstrap.js&quot;&gt;&lt;/script&gt;
	&lt;script src=&quot;/Scripts/respond.js&quot;&gt;&lt;/script&gt;
	
	...    
	</pre>

<a name="fallback"></a>
## Meccanismo di fallback per gli URL della rete CDN ##

Se si verifica un errore nell'endpoint della rete CDN di Azure per un motivo qualsiasi, è consigliabile configurare l'accesso della pagina Web al server Web di origine come opzione di fallback per il caricamento di JavaScript o Bootstrap. È già abbastanza spiacevole perdere le immagini nell'app Web a causa della mancata disponibilità della rete CDN, ma ancora più grave è perdere le funzionalità essenziali della pagina fornite dagli script e dai fogli di stile.

La classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contiene una proprietà denominata [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) che consente di configurare il meccanismo di fallback per l'errore della rete CDN. Per usare questa proprietà, seguire i passaggi descritti di seguito:

1. Nel progetto ASP.NE, aprire *App\_Start\\BundleConfig.cs* dove è stato aggiunto un URL della rete CDN in ogni [costruttore di bundle](http://msdn.microsoft.com/library/jj646464.aspx) e aggiungere il codice `CdnFallbackExpression` in quattro posizioni come indicato per aggiungere il meccanismo di fallback ai bundle predefiniti.  
<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					{ CdnFallbackExpression = &quot;window.jquery&quot; }
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					{ CdnFallbackExpression = &quot;$.validator&quot; }
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					{ CdnFallbackExpression = &quot;window.Modernizr&quot; }
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					{ CdnFallbackExpression = &quot;$.fn.modal&quot; }
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	Quando `CdnFallbackExpression` non è Null, lo script viene inserito nel linguaggio HTML per provare se l'aggregazione è stata caricata correttamente. In caso contrario, accedere all'aggregazione direttamente dal server Web dell'origine. Questa proprietà deve essere impostata su un'espressione JavaScript che verifichi se il rispettivo bundle CDN è stato caricato correttamente. L'espressione necessaria per testare ogni bundle differisce in base al contenuto. Per i bundle predefiniti sopra riportati:
	
	-	`window.jquery` viene definito nel file jquery-{version}.js
	-	`$.validator` viene definito nel file jquery.validate.js
	-	`window.Modernizr` viene definito nel file modernizer-{version}.js
	-	`$.fn.modal` viene definito nel file bootstrap.js
	
	Come è possibile osservare, non è stata impostata la proprietà CdnFallbackExpression per l'aggregazione `~/Cointent/css`, e questo perché attualmente esiste un [bug in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) che inserisce un tag `<script>` per il file CSS di fallback invece del tag `<link>` previsto.
	
	[Ember Consulting Group](https://github.com/EmberConsultingGroup/StyleBundleFallback) offre ad ogni modo un'ottima soluzione di [fallback StyleBundle](https://github.com/EmberConsultingGroup).

2. Per usare questa soluzione alternativa, creare un nuovo file con estensione CS nella cartella *App\_Start* del progetto ASP.NET, denominata *StyleBundleExtensions.cs* e sostituirne il contenuto con il [codice di GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. In *App\_Start\\StyleFundleExtensions.cs* rinominare lo spazio dei nomi con lo spazio dei nomi dell'applicazione ASP.NET (ad esempio **cdnwebapp**).

3. Tornare a `App_Start\BundleConfig.cs` e sostituire l'ultima istruzione `bundles.Add` con il seguente codice:
<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    .IncludeFallback("~/Content/css", "sr-only", "width", "1px")
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Questo nuovo metodo di estensione si basa sulla stessa idea di inserire lo script nel linguaggio HTML per cercare in DOM il nome di classe, il nome di regola e il valore di regola corrispondenti definiti nel bundle CSS, eseguendo il fallback sul server Web in caso non riesca a trovare la corrispondenza.

4. Pubblicare di nuovo l'app Web di Azure e accedere alla home page.
5. Visualizzare il codice HTML relativo alla pagina. Gli script inseriti dovrebbero essere simili al seguente:    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;&lt;link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; /&gt;&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;&lt;script src=&quot;/Content/css&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;
	
	...	
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;
	
	...
	</pre>

	Si noti che lo script inserito per l'aggregazione CSS contiene ancora residui della proprietà `CdnFallbackExpression` nella riga:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Poiché però la prima parte dell'espressione || restituirà sempre true (nella riga subito sopra), la funzione document.write() non verrà mai eseguita.

6. Per verificare il funzionamento dello script di fallback, tornare indietro al dashboard dell'endpoint della rete CDN e fare clic su **Disabilita endpoint**.

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Aggiornare la finestra del browser per l'app Web di Azure. Si noterà ora che tutti gli script e i fogli di stile vengono caricati correttamente.

## Altre informazioni 
- [Panoramica della Rete per la distribuzione di contenuti (CDN) di Azure](../cdn-overview.md)
- [Rendere disponibile il contenuto dalla rete CDN di Azure nell'applicazione Web](../cdn-serve-content-from-cdn-in-your-web-application.md)
- [Integrare un servizio cloud con la rete CDN di Azure](../cdn-cloud-service-with-cdn.md)
- [Creazione di aggregazioni e minimizzazione ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Uso della rete CDN per Azure](../cdn-how-to-use-cdn.md)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!-----HONumber=August15_HO6-->