<properties 
	pageTitle="Integrare un sito Web di Azure con la rete CDN di Azure" 
	description="Un'esercitazione che illustra come distribuire un sito Web che fornisce contenuto da un endpoint della rete CDN di Azure integrato" 
	services="cdn, web-sites" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/02/2014" 
	ms.author="cephalin"/>

<a name="intro"></a>
# Integrare un sito Web di Azure con la rete CDN di Azure #

Siti Web di Azure può essere integrato con [CDN di Azure ](http://azure.microsoft.com/services/cdn/), migliorando in tal modo le funzionalità di scalabilità globale inerenti in Siti Web di Azure tramite la pubblicazione del contenuto del sito Web a livello globale da nodi server vicini ai clienti (l'elenco aggiornato di tutti i percorsi dei nodi correnti è disponibile [in questa pagina](http://msdn.microsoft.com/library/azure/gg680302.aspx). Questa integrazione aumenta notevolmente le prestazioni di Siti Web di Azure nonché l'esperienza utente del sito Web in tutto il mondo. 

L'integrazione di Siti Web di Azure con la rete CDN di Azure offre i vantaggi seguenti:

- Integrazione della distribuzione del contenuto (immagini, script e fogli di stile) come parte del processo di [distribuzione continua](http://azure.microsoft.com/documentation/articles/web-sites-publish-source-control/) del sito Web di Azure
- Facile aggiornamento dei pacchetti NuGet nel sito Web di Azure, come le versioni jQuery o Bootstrap 
- Gestione dell'applicazione Web e del contenuto gestito dalla rete CDN dalla stessa interfaccia di Visual Studio
- Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure

## Contenuto dell'esercitazione ##

In questa esercitazione si apprenderà come:

-	[Integrare un endpoint della rete CDN di Azure con il sito Web di Azure e gestire il contenuto statico nelle pagine Web dalla rete CDN di Azure](#deploy)
-	[Configurare le impostazioni della cache per il contenuto statico nel sito Web di Azure](#caching)
-	[Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller)
-	[Gestire contenuto in bundle e minimizzato attraverso la rete CDN di Azure mantenendo contemporaneamente l'esperienza di debug dello script in Visual Studio](#bundling)
-	[Configurare il fallback degli script e i file CSS quando la rete CDN di Azure non è in linea](#fallback) 

## Obiettivo di compilazione ##

Verrà distribuito un sito Web di Azure usando il modello MVC di ASP.NET predefinito in Visual Studio, si aggiungerà il codice per gestire il contenuto da una rete CDN di Azure integrata, ad esempio un'immagine, i risultati dell'azione del controller e i file JavaScript e CSS predefiniti, e si scriverà anche il codice per configurare il meccanismo di fallback per i bundle serviti nel caso in cui la rete CDN non sia in linea.

## Prerequisiti ##

Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

-	Un [account Microsoft Azure](http://azure.microsoft.com/account/) attivo
-	Visual Studio 2013 con [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Per completare l'esercitazione, è necessario un account Azure.</h5>
  <ul>
    <li>È possibile <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F">aprire un account Azure gratuitamente</a>. Si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web.</li>
    <li>È possibile <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">attivare i benefici della sottoscrizione MSDN</a>. Con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.</li>
  <ul>
</div>

<a name="deploy"></a>
## Distribuire un sito Web di Azure con un endpoint della rete CDN integrato ##

In questa sezione verrà distribuito il modello di applicazione MVC di ASP.NET predefinito in Visual Studio 2013 a un sito Web di Azure, che verrà quindi integrato con un nuovo endpoint della rete CDN. Seguire le istruzioni riportate di seguito:

1. In Visual Studio 2013 creare una nuova applicazione Web ASP.NET dalla barra dei menu selezionando **File > Nuovo > Progetto > Web > Applicazione Web ASP.NET **. Assegnarli un nome e fare clic su **OK**.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. Selezionare **MVC** e fare clic su **Gestisci sottoscrizioni**.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. Fare clic su **Accedi**.

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. Nella pagina di accesso eseguire l'accesso con l'account Microsoft usato per attivare l'account Azure.
7. Una volta effettuato l'accesso, fare clic su **Chiudi**. Quindi, fare clic su **OK** per continuare.

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Supponendo che non sia stato creato un sito Web di Azure, Visual Studio consente di crearlo. Nella finestra di dialogo **Configura sito Web di Microsoft Azure** assicurarsi che il nome del sito sia univoco Quindi, fare clic su **OK**.

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. Una volta creata l'applicazione ASP.NET, pubblicarla in Azure nel riquadro Attività di pubblicazione Web facendo clic su **Pubblica `<nome app>` nel sito**. Fare clic su **Pubblica** per completare il processo.

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	Una volta completata la pubblicazione, il sito Web di Azure verrà pubblicato nel browser. 

1. Per creare un endpoint della rete CDN, accedere al [portale di gestione di Azure](http://manage.windowsazure.com/). 
2. Fare clic su **Nuovo** > **Servizi app** > **Rete CDN** > **Creazione rapida**. Selezionare **http://*<nomesito>*.azurewebsites.net/** e fare clic su **Crea**.

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	>[WACOM.NOTE] Dopo aver creato l'endpoint della rete CDN, nel portale di Azure viene visualizzato il relativo URL e il dominio di origine in esso integrato. È tuttavia possibile che la completa propagazione della configurazione del nuovo endpoint della rete CDN a tutte le ubicazioni dei nodi della rete CDN richieda del tempo. 

3. Tornare al portale di Azure e nella scheda **Rete CDN** fare clic sul nome dell'endpoint della rete CDN appena creato.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. Fare clic su **Abilita stringa di query** per abilitare le stringhe di query nella cache della rete CDN. Dopo averle abilitate, lo stesso collegamento a cui si accede con diverse stringhe di query verrà memorizzato nella cache come voci separate.

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[WACOM.NOTE] Benché non sia necessario abilitare la stringa di query per questa sezione dell'esercitazione, per comodità è consigliabile farlo prima possibile, perché la propagazione a tutti i nodi della rete CDN di qualsiasi modifica apportata in questa fase richiederà del tempo e non è auspicabile che contenuti non abilitati per le stringhe query intasino la cache della rete CDN (l'aggiornamento del contenuto della rete CDN verrà discusso più avanti).

2. Fare clic sull'indirizzo dell'endpoint della rete CDN. Se l'endpoint è pronto, verrà visualizzato il sito Web visualizzato. Se viene visualizzato un errore **HTTP 404**, l'endpoint della rete CDN non è pronto. Potrebbe essere necessario attendere fino a un'ora affinché la configurazione della rete CDN sia propagata a tutti i nodi di edge. 

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. Successivamente, provare ad accedere al file **~/Content/bootstrap.css** nel progetto ASP.NET. Nella finestra del browser passare a **http://*<nomeCdn>*.vo.msecnd.net/Content/bootstrap.css**. Nella configurazione illustrata questo URL è il seguente:

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	che corrisponde all'URL di origine seguente all'endpoint della rete CDN:

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	Quando si passa a **http://*<nomeCdn>*.vo.msecnd.net/Content/bootstrap.css**, verrà richiesto di scaricare il file bootstrap.css inviato dal sito Web di Azure. 

	![](media/cdn-websites-with-cdn/12-file-access.png)

È possibile accedere in maniera simile a qualsiasi URL pubblicamente accessibile all'indirizzo **http://*<nomeServizio>*.cloudapp.net/**, direttamente dall'endpoint della rete CDN. ad esempio:

-	Un file con estensione js dal percorso /Script
-	Qualsiasi file di contenuto dal percorso /Content
-	Qualsiasi controller/azione 
-	Se la stringa di query viene abilitata sull'endpoint della rete CDN, qualsiasi URL con stringhe di query
-	L'intero sito Web di Azure, se tutto il contenuto è pubblico

Tenere presente che non sempre una buona idea (o almeno in generale) rendere disponibile un intero sito Web di Azure attraverso la rete CDN. È necessario valutare diversi aspetti:

-	Questo approccio richiede di rendere pubblico l'intero sito, perché la rete CDN di Azure non può gestire contenuti privati.
-	Se l'endpoint della rete CDN passa alla modalità offline per un qualsiasi motivo, che si tratti di manutenzione pianificata o un errore dell'utente, l'intero sito Web viene portato offline, a meno che sia possibile reindirizzare i clienti all'URL di origine **http://*<nomesito>*.azurewebsites.net/**. 
-	Anche con le impostazioni di controllo della cache (vedere [Configurare le opzioni di memorizzazione nella cache dei file statici nel sito Web di Azure](#caching)), un endpoint della rete CDN non migliora le prestazioni dei contenuti altamente dinamici. Se si è provato a caricare la home page dall'endpoint della rete CDN come sopra illustrato, si è osservato che il caricamento della pagina iniziale predefinita, cioè una pagina abbastanza semplice, ha richiesto almeno cinque secondi la prima volta. Si può dunque immaginare quale sarebbe l'esperienza del cliente se questa pagina includesse contenuto dinamico da aggiornare ogni minuto. Gestire contenuti dinamici da un endpoint della rete CDN richiede una scadenza breve della cache, che si traduce in frequenti mancati riscontri nella cache sull'endpoint della rete CDN. Ciò influisce negativamente sulle prestazioni del sito Web di Azure e vanifica lo scopo di una rete CDN.

L'alternativa consiste nel determinare quale contenuto rendere disponibile dalla rete CDN di Azure, valutando caso per caso nel sito Web di Azure. A tale scopo, si è già visto come accedere ai singoli file di contenuto dall'endpoint della rete CDN. Più avanti verrà illustrato come gestire una specifica azione del controller attraverso l'endpoint della rete CDN in [Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller).

<a name="caching"></a>
## Configurare le opzioni di memorizzazione nella cache dei file statici nel sito Web di Azure ##

Con l'integrazione della rete CDN di Azure nel sito Web di Azure è possibile specificare in che modo si vuole memorizzare il contenuto statico nella cache dell'endpoint della rete CDN. A tale scopo, aprire il file *Web.config* dal progetto ASP.NET (ad esempio **cdnwebapp**) e aggiungere un elemento `<staticContent>` a `<system.webServer>`. Il linguaggio XML seguente configura la scadenza della cache entro tre giorni.  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

A questo punto, tutti i file statici nel sito Web di Azure osserveranno la stessa regola nella cache della rete CDN. Per un controllo più granulare delle impostazioni della cache, aggiungere un file *Web.config* in una cartella e quindi aggiungervi le impostazioni. Ad esempio, aggiungere un file *Web.config* alla cartella *\Content* e sostituire il contenuto con il seguente linguaggio XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Queste impostazioni causano la memorizzazione nella cache di tutti i file statici della cartella *\Content* per 15 giorni.

Per altre informazioni su come configurare l'elemento `<clientCache>`, vedere l'argomento relativo alla [cache client <clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Nella sezione [Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller) verrà illustrato anche come configurare le impostazioni della cache per ottenere i risultati dell'azione del controller nella cache della rete CDN.

<a name="controller"></a>
## Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure ##

Quando si integra un sito Web di Azure nella rete CDN di Azure, è relativamente facile gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure.. Anche in questo caso, se si decide di usare l'intero sito Web di Azure tramite la rete CDN, non è necessario occuparsi di tale gestione poiché tutte le azioni del controller sono già raggiungibili attraverso la rete CDN. Tuttavia, per i motivi già esposti nella sezione [Distribuire un sito Web di Azure con un endpoint della rete CDN integrato](#deploy), è possibile decidere invece di selezionare l'azione del controller desiderata per gestire il contenuto dalla rete CDN di Azure. [Maarten Balliauw](https://twitter.com/maartenballiauw) illustra come farlo con un divertente controller MemeGenerator nel video relativo alla [riduzione della latenza sul Web con la rete CDN di Microsoft Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). che viene riprodotto semplicemente in questo articolo.

Si supponga di volere generare nel sito Web dei meme basati su un'immagine di un giovane Chuck Norris (foto di [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) come questa:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Si usa una semplice azione `Index` che consente ai clienti di specificare i superlativi nell'immagine, quindi genera il meme dopo aver pubblicato nell'azione. Poiché si tratta di Chuck Norris, ci si aspetta che questa pagina diventi enormemente popolare in tutto il mondo. È un ottimo esempio di come gestire contenuto semi dinamico con la rete CDN di Azure. 

Seguire i passaggi precedenti per configurare questa azione del controller:

1. Nella cartella *\Controllers* creare un nuovo file con estensione cs denominato *MemeGeneratorController.cs* e sostituire il contenuto con il codice seguente. Assicurarsi di sostituire la parte evidenziata con il percorso file e il nome della propria rete CDN.
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
	        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

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
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
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
	                return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
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
	</pre>

2. Fare clic con il pulsante destro del mouse sull'azione `Index()` predefinita e selezionare **Aggiungi visualizzazione**..

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Accettare le impostazioni di seguito e fare clic su **Aggiungi**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Aprire il nuovo file *Views\MemeGenerator\Index.cshtml* e sostituire il contenuto con il semplice HTML seguente per inviare i superlativi:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Pubblicare nuovamente il servizio cloud e passare a **http://*<nomeServizio>*.cloudapp.net/MemeGenerator/Index** nel browser. 

Quando si inviano i valori del modulo a `/MemeGenerator/Index`, il metodo di azione `Index_Post` restituisce un collegamento al metodo di azione `Show` con il rispettivo identificatore di input. Facendo clic sul collegamento si raggiunge il codice seguente:  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
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
        return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Se il debugger locale è collegato, si avrà una normale esperienza di debug con un reindirizzamento locale. Se viene eseguito nel sito Web di Azure, si verrà reindirizzati a:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

che corrisponde all'URL di origine seguente in corrispondenza dell'endpoint della rete CDN:

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Dopo la regola di riscrittura URL precedentemente applicata, il file effettivo che viene memorizzato nella cache dell'endpoint della rete CDN è il seguente:

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Sarà quindi possibile usare l'attributo `OutputCacheAttribute` sul metodo `Generate` per specificare come memorizzare nella cache il risultato dell'azione, che verrà rispettato dalla rete CDN di Azure. Il codice seguente specifica la scadenza di una cache entro un'ora (3.600 secondi).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Analogamente, è possibile rendere disponibile il contenuto da qualsiasi azione del controller nel sito Web di Azure attraverso la rete CDN di Azure, con l'opzione di memorizzazione nella cache desiderata.

Nella sezione successiva verrà illustrato come gestire gli script e i file CSS in bundle e minimizzati attraverso la rete CDN di Azure. 

<a name="bundling"></a>
## Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure ##

Gli script e i fogli di stile CSS cambiano in maniera non frequente e sono i principali candidati per la cache della rete CDN di Azure. Il modo più semplice per integrare la creazione di bundle e la minimizzazione con la rete CDN di Azure consiste nel rendere disponibile l'intero sito Web. Tuttavia, poiché è possibile scegliere di adottare questo approccio per i motivi descritti nella sezione dedicata all'[integrazione di endpoint della rete CDN di Azure con il sito Web di Azure e alla pubblicazione di contenuto statico nelle pagine Web di CDN di Azure](#deploy), verrà illustrato come procedere pur mantenendo l'esperienza desiderata dallo sviluppatore per quanto riguarda la creazione di bundle e la minimizzazione ASP.NET, ad esempio:

-	Ottima esperienza della modalità di debug
-	Distribuzione semplificata
-	Aggiornamenti immediati delle versioni di script/css dei client
-	Meccanismo di fallback in caso di errore dell'endpoint della rete CDN
-	Riduzione delle modifiche del codice

Nel progetto ASP.NET creato nella sezione dedicata all'[integrazione di un endpoint della rete CDN di Azure con il sito Web di Azure e alla pubblicazione di contenuto statico nelle pagine Web di CDN di Azure](#deploy), aprire *App_Start\BundleConfig.cs* e osservare le chiamate del metodo `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

La prima istruzione `bundles.Add()` aggiunge un bundle di script alla directory virtuale `~/bundles/jquery`. Quindi, aprire *Views\Shared\_Layout.cshtml* per vedere come viene eseguito il rendering del tag del bundle di script. Dovrebbe essere possibile trovare la riga di codice Razor seguente:

    @Scripts.Render("~/bundles/jquery")

Quando questo codice Razor viene eseguito nel sito Web di Azure, verrà eseguito il rendering di un tag `<script>` per il bundle di script simile al seguente: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Tuttavia, quando il codice viene eseguito in Visual Studio digitando `F5`, verrà eseguito il rendering individuale di ciascun file di script nel bundle (nel caso sopra citato, il bundle contiene un solo file di script):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Ciò consente di eseguire il debug del codice JavaScript nell'ambiente di sviluppo e allo stesso tempo di ridurre le connessioni client simultanee (creazione di bundle) e migliorare le prestazioni di download dei file (minimizzazione) in produzione. È un'ottima funzionalità da mantenere con l'integrazione nella rete CDN di Azure. Per di più, dal momento che il bundle di cui è stato eseguito il rendering contiene una stringa di versione generata automaticamente, è opportuno replicare tale funzionalità in modo che ogni volta che si aggiorna la versione di jQuery attraverso NuGet è possibile aggiornarla sul lato client non appena possibile.

Attenersi alla procedura seguente per integrare la creazione di bundle e la minimizzazione ASP.NET con l'endpoint della rete CDN.

1. Tornare a *App_Start\BundleConfig.cs*, modificare i metodi `bundles.Add()` in modo da usare un [costruttore di bundle](http://msdn.microsoft.com/library/jj646464.aspx) differente, che specifichi un indirizzo di rete CDN. A tale scopo, sostituire la definizione del metodo `RegisterBundles` con il codice seguente:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	Assicurarsi di sostituire `<NomeCDN>` con il nome della rete CDN di Azure.

	In altri termini, si imposta `bundles.UseCdn = true` e si aggiunge un URL della rete CDN definito con precisione a ciascun bundle. Ad esempio, il primo costruttore nel codice:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	è lo stesso di: 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Questo costruttore comunica alle operazioni di creazione di bundle e minimizzazione ASP.NET di eseguire il rendering dei singoli file di script quando ne viene eseguito il debug a livello locale, ma di usare l'indirizzo della rete CDN specificato per accedere allo script in questione. Notare tuttavia due importanti caratteristiche di questo URL della rete CDN definito con precisione:
	
	-	L'origine di questo URL della rete CDN è `http://<nomeSito>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`, che in realtà è la directory virtuale del bundle di script nell'applicazione Web.
	-	Poiché si sta usando un costruttore CDN, il tag dello script CDN per il bundle non contiene più la stringa di versione generata automaticamente nell'URL di cui è stato eseguito il rendering. È necessario generare manualmente una stringa di versione univoca ogni volta che il bundle di script viene modificato per forzare un mancato riscontro nella cache nella rete CDN di Azure. Allo stesso tempo, questa stringa di versione univoca deve rimanere costante per tutta la durata della distribuzione per aumentare i riscontri nella cache nella rete CDN di Azure dopo aver distribuito il bundle.
	-	La stringa di query v=<W.X.Y.Z> effettua il pull da *Properties\AssemblyInfo.cs* nel progetto ASP.NET. È possibile prevedere un flusso di lavoro di distribuzione che includa l'incremento della versione di assembly ogni volta che si pubblica su Azure. In alternativa, è possibile modificare solo il file *Properties\AssemblyInfo.cs* nel progetto per incrementare automaticamente la stringa di versione ogni volta che si compila, usando il carattere jolly "*". ad esempio:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Qualsiasi altra strategia volta a semplificare la generazione di una stringa univoca per la durata della distribuzione avrà esito positivo.

3. Ripubblicare l'applicazione ASP.NET e accedere alla home page.
 
4. Visualizzare il codice HTML relativo alla pagina. Dovrebbe essere possibile visualizzare l'URL della rete CDN di cui è stato eseguito il rendering, con una stringa di versione univoca ogni volta che si ripubblicano le modifiche al sito Web di Azure, ad esempio:  
	<pre class="prettyprint">
	...

    <link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;>&lt;/script>

	...

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;></script>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;></script>

	...</pre>

5. In Visual Studio eseguire il debug l'applicazione ASP.NET in Visual Studio digitando `F5`., 

6. Visualizzare il codice HTML relativo alla pagina. Sarà ancora possibile visualizzare ciascun file di script di cui sia stato eseguito il rendering, in modo che l'esperienza di debug sia coerente in Visual Studio.  
	<pre class="prettyprint">
	...
	
	    <link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/>
	<link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/>
	
	    <script src=&quot;/Scripts/modernizr-2.6.2.js&quot;></script>
	
	...
	
	    <script src=&quot;/Scripts/jquery-1.10.2.js&quot;></script>
	
	    <script src=&quot;/Scripts/bootstrap.js&quot;></script>
	<script src=&quot;/Scripts/respond.js&quot;></script>
	
	...    
	</pre>

<a name="fallback"></a>
## Meccanismo di fallback per gli URL della rete CDN ##

Se si verifica un errore nell'endpoint della rete CDN di Azure per un motivo qualsiasi, è consigliabile configurare l'accesso della pagina Web al server Web di origine come opzione di fallback per il caricamento di JavaScript o Bootstrap. Un conto è perdere le immagini nel sito a causa della mancata disponibilità della rete CDN, un altro è perdere funzionalità essenziali della pagina fornite dagli script e dai fogli di stile.

La classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contiene una proprietà denominata [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) che consente di configurare il meccanismo di fallback per l'errore della rete CDN. Per usare questa proprietà, seguire i passaggi descritti di seguito:

1. Nel progetto ASP.NET aprire *App_Start\BundleConfig.cs* a cui è stato aggiunto un URL della rete CDN a ogni [costruttore di bundle](http://msdn.microsoft.com/library/jj646464.aspx), e apportare le modifiche evidenziate di seguito per aggiungere il meccanismo di fallback ai bundle predefiniti:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	Quando `CdnFallbackExpression` non è Null, lo script viene inserito nel linguaggio HTML per provare se il bundle è stato caricato correttamente. In caso contrario, accedere al bundle direttamente dal server Web dell'origine. Questa proprietà deve essere impostata su un'espressione JavaScript che verifichi se il rispettivo bundle CDN è stato caricato correttamente. L'espressione necessaria per testare ogni bundle differisce in base al contenuto. Per i bundle predefiniti sopra riportati:
	
	-	`window.jquery` viene definito nel file jquery-{version}.js
	-	`$.validator` viene definito nel file jquery.validate.js
	-	`window.Modernizr` viene definito nel file modernizer-{version}.js
	-	`$.fn.modal` viene definito nel file bootstrap.js
	
	Come è possibile osservare, non è stata impostata la proprietà CdnFallbackExpression per il bundle `~/Cointent/css`. e questo perché attualmente esiste un [bug in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) che inserisce un tag `<script>` per il file CSS di fallback invece del tag `<link>` previsto.
	
	È comunque disponibile un'ottima soluzione di [fallback Style Bundle](https://github.com/EmberConsultingGroup/StyleBundleFallback) offerta da [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. Per usare questa soluzione alternativa, creare un nuovo file con estensione CS nella cartella *App_Start* del progetto ASP.NET, denominata *StyleBundleExtensions.cs* e sostituirne il contenuto con il [codice di GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. In *App_Start\StyleFundleExtensions.cs* rinominare lo spazio dei nomi con lo spazio dei nomi dell'applicazione ASP.NET (ad esempio **cdnwebapp**). 

3. Tornare a `App_Start\BundleConfig.cs` e modificare l'ultima istruzione `bundles.Add` con il codice evidenziato seguente:  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Questo nuovo metodo di estensione usa la stessa idea di inserire lo script nel linguaggio HTML per cercare in DOM il nome di classe, il nome di regola e il valore di regola corrispondenti definiti nel bundle CSS, eseguendo il fallback sul server Web in caso non riesca a trovare la corrispondenza.

4. Pubblicare nuovamente il sito Web di Azure e accedere alla home page. 
5. Visualizzare il codice HTML relativo alla pagina. Gli script inseriti dovrebbero essere simili al seguente:    
	<pre class="prettyprint">...
	
		<link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/>
	<mark><script>(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i < len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;<link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; />&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;<script src=&quot;/Content/css&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;></script>
	<mark><script>(window.Modernizr)||document.write(&#39;<script src=&quot;/bundles/modernizr&quot;><\/script>&#39;);</script></mark>
	
	...	
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;></script>
	<mark><script>(window.jquery)||document.write(&#39;<script src=&quot;/bundles/jquery&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;></script>
	<mark><script>($.fn.modal)||document.write(&#39;<script src=&quot;/bundles/bootstrap&quot;><\/script>&#39;);</script></mark>
	
	...
	</pre>

	Si noti che lo script inserito per il bundle CSS contiene ancora residui della proprietà `CdnFallbackExpression` nella riga:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Poiché però la prima parte dell'espressione || restituirà sempre true (nella riga subito sopra), la funzione document.write() non verrà mai eseguita.

6. Per verificare il funzionamento dello script di fallback, tornare indietro al dashboard dell'endpoint della rete CDN e fare clic su **Disabilita endpoint**..

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Aggiornare la finestra del browser per il sito Web di Azure. Si noterà ora che tutti gli script e i fogli di stile vengono caricati correttamente.

# Altre informazioni #
- [Panoramica della Rete di distribuzione dei contenuti (CDN) di Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Rendere disponibile il contenuto dalla rete CDN di Azure nell'applicazione Web](http://azure.microsoft.com/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [Integrare un servizio cloud con la rete CDN di Azure](http://azure.microsoft.com/documentation/articles/cdn-cloud-service-with-cdn/)
- [Creazione di bundle e minimizzazione ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Uso della rete CDN per Azure](http://azure.microsoft.com/documentation/articles/cdn-how-to-use/)

<!--HONumber=46--> 
