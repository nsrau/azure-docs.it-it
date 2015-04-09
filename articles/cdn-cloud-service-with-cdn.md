<properties 
	pageTitle="Integrare un servizio cloud con la rete CDN di Azure" 
	description="Un'esercitazione che illustra come distribuire un servizio cloud che fornisce contenuto da un endpoint della rete CDN di Azure integrato" 
	services="cdn, cloud-services" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="tysonn"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="cephalin"/>

<a name="intro"></a>
# Integrare un servizio cloud con la rete CDN di Azure #

È possibile integrare un servizio cloud con la rete CDN di Azure, per rendere disponibile qualsiasi contenuto dal percorso `~/CDN` del servizio cloud. Questo approccio offre i vantaggi seguenti:

- Facile distribuzione e aggiornamento di immagini, script e fogli di stile nelle directory del progetto servizio cloud
- Facile aggiornamento dei pacchetti NuGet nel servizio cloud, come le versioni jQuery o Bootstrap 
- Gestione dell'applicazione Web e del contenuto gestito dalla rete CDN dalla stessa interfaccia di Visual Studio
- Flusso di lavoro di distribuzione unificato per l'applicazione Web e il contenuto gestito dalla rete CDN
- Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure

## Contenuto dell'esercitazione ##

In questa esercitazione si apprenderà come:

-	[Integrare un endpoint della rete CDN di Azure con il servizio cloud e rendere disponibile contenuto statico nelle pagine Web dalla rete CDN di Azure](#deploy)
-	[Configurare le impostazioni della cache per il contenuto statico nel servizio cloud](#caching)
-	[Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller)
-	[Rendere disponibile contenuto in bundle e minimizzato attraverso la rete CDN di Azure, mantenendo comunque l'esperienza di debug in Visual Studio](#bundling)
-	[Configurare il fallback di script e file CSS quando la rete CDN di Azure è offline](#fallback) 

## Obiettivo di compilazione ##

Verrà distribuito un ruolo Web del servizio cloud usando il modello MVC di ASP.NET predefinito, si aggiungerà il codice per gestire il contenuto da una rete CDN di Azure integrata, ad esempio un'immagine, i risultati dell'azione del controller e i file JavaScript e CSS predefiniti, e si scriverà anche il codice per configurare il meccanismo di fallback per i bundle serviti nel caso in cui la rete CDN è offline.

## Prerequisiti ##

Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

-	Un [account Microsoft Azure](/account/) attivo
-	Visual Studio 2013 con [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure:
> + È possibile [aprire un account Azure gratuitamente](/pricing/free-trial/). Si riceveranno crediti da usare per provare i servizi di Azure a pagamento e, una volta esauriti i crediti, sarà comunque possibile mantenere l'account e continuare a usare i servizi di Azure gratuiti, come Siti Web.
> + È possibile [attivare i vantaggi dell'abbonamento MSDN](/pricing/member-offers/msdn-benefits-details/). L'abbonamento MSDN offre crediti ogni mese, da usare per i servizi di Azure a pagamento.

<a name="deploy"></a>
## Distribuire un servizio cloud con un endpoint della rete CDN integrato ##

In questa sezione verrà distribuito il modello di applicazione MVC di ASP.NET predefinito in Visual Studio 2013 a un ruolo Web del servizio cloud, che verrà quindi integrato con un nuovo endpoint della rete CDN. Seguire le istruzioni riportate di seguito:

1. In Visual Studio 2013 creare un nuovo servizio cloud di Azure dalla barra dei menu, scegliendo **File > Nuovo > Progetto > Cloud > Servizio cloud Azure**. Assegnare un nome al servizio e fare clic su **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Selezionare **Ruolo Web ASP.NET** e fare clic sul pulsante **>**. Fare clic su OK.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Selezionare **MVC** e fare clic su **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Pubblicare ora questo ruolo Web in un servizio cloud di Azure. Fare clic con il pulsante destro del mouse sul progetto servizio cloud e scegliere **Pubblica**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Se non è ancora stato effettuato l'accesso a Microsoft Azure, fare clic sul pulsante **Accedi**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. Nella pagina di accesso eseguire l'accesso con l'account Microsoft usato per attivare l'account Azure.
7. Una volta effettuato l'accesso, fare clic su **Avanti**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Supponendo che non sia stato creato un servizio cloud né un account di archiviazione, Visual Studio aiuterà a crearli entrambi. Nella finestra di dialogo **Crea servizio cloud e account** digitare il nome del servizio desiderato. Fare quindi clic su **Crea**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Nella pagina Impostazioni di pubblicazione verificare la configurazione e fare clic su **Pubblica**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE] Il processo di pubblicazione per i servizi cloud richiede tempi elevati. L'opzione Abilita Distribuzione Web per tutti i ruoli Web può velocizzare il debug del servizio cloud fornendo aggiornamenti rapidi (ma temporanei) dei ruoli Web. Per altre informazioni su questa opzione, vedere [Pubblicazione di un servizio cloud con gli strumenti di Azure](http://msdn.microsoft.com/library/ff683672.aspx).

	Quando la finestra **Log attività di Microsoft Azure** indica che lo stato di pubblicazione è **Completato**, è possibile creare un endpoint della rete CDN integrato con il servizio cloud. 

1. Per creare un endpoint della rete CDN, accedere al [portale di gestione di Azure](http://manage.windowsazure.com/). 
2. Fare clic su **Nuovo** > **Servizi app** > **Rete CDN** > **Creazione rapida**. Selezionare **http://*&lt;nomeservizio>*.cloudapp.net/cdn/** e fare clic su **Crea**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[AZURE.NOTE] Dopo aver creato l'endpoint della rete CDN, nel portale di Azure viene visualizzato il relativo URL e il dominio di origine in esso integrato. È tuttavia possibile che la completa propagazione della configurazione del nuovo endpoint della rete CDN a tutte le ubicazioni dei nodi CDN richieda del tempo. 

	Notare che l'endpoint della rete CDN è associato al percorso **cdn/** del servizio cloud. È possibile creare una cartella **cdn** nel progetto **WebRole1** oppure usare la riscrittura URL per eliminare tutti i collegamenti in ingresso di questo percorso. In questa esercitazione si procederà in quest'ultimo modo.

3. Tornare nel portale di Azure e nella scheda **Rete CDN** fare clic sul nome dell'endpoint della rete CDN appena creato.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. Fare clic su **Abilita stringa di query** per abilitare le stringhe di query nella cache della rete CDN. Dopo averle abilitate, lo stesso collegamento a cui si accede con diverse stringhe di query verrà memorizzato nella cache come voci separate.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[AZURE.NOTE] Benché non sia necessario abilitare la stringa di query per questa sezione dell'esercitazione, per comodità è consigliabile farlo prima possibile, perché la propagazione a tutti i nodi della rete CDN di qualsiasi modifica apportata in questa fase richiederà del tempo e non è auspicabile che contenuti non abilitati per le stringhe query intasino la cache della rete CDN (l'aggiornamento del contenuto della rete CDN verrà discusso più avanti).

3. Eseguire il ping dell'endpoint della rete CDN per assicurarsi che sia stato propagato ai nodi della rete CDN. Può essere necessario attendere fino a un'ora prima che risponda ai ping.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. Tornare in Visual Studio 2013, aprire **Web.config** nel progetto **WebRole1** e aggiungere il codice seguente nel tag `<system.webServer>`:  
	<pre class="prettyprint">
	&lt;system.webServer&gt;
	  <mark>&lt;rewrite&gt;
	    &lt;rules&gt;
	      &lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
	        &lt;match url=&quot;^cdn/(.*)$&quot;/&gt;
	        &lt;action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/&gt;
	      &lt;/rule&gt;
	    &lt;/rules&gt;
	  &lt;/rewrite&gt;</mark>
      ...
	&lt;/system.webServer&gt;
	</pre>

4. Pubblicare nuovamente il servizio cloud. Fare clic con il pulsante destro del mouse sul progetto servizio cloud e scegliere **Pubblica**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. Quando lo stato di pubblicazione è **Completato**, aprire una finestra del browser e passare a **http://*&lt;nomereteCdn>*.vo.msecnd.net/Content/bootstrap.css**. Nella configurazione illustrata, questo URL è il seguente:

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	che corrisponde all'URL di origine seguente nell'endpoint della rete CDN:

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	Dopo la riscrittura URL nell'app Web, il file effettivo che viene memorizzato nella cache della rete CDN è il seguente:

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	Quando si passa a **http://*&lt;nomereteCdn>*.vo.msecnd.net/Content/bootstrap.css**, viene richiesto di scaricare il file bootstrap.css inviato dall'app Web pubblicata. 

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

È possibile accedere allo stesso modo a qualsiasi URL accessibile pubblicamente all'indirizzo **http://*&lt;nomeServizio>*.cloudapp.net/**, direttamente dall'endpoint della rete CDN. Ad esempio:

-	Un file con estensione js dal percorso /Script
-	Qualsiasi file di contenuto dal percorso /Content
-	Qualsiasi controller/azione 
-	Se la stringa di query viene abilitata sull'endpoint della rete CDN, qualsiasi URL con stringhe di query

Infatti, con la configurazione precedente è possibile ospitare l'intero servizio cloud da **http://*&lt;nomereteCdn>*.vo.msecnd.net/**. Se si passa a **http://az632148.vo.msecnd.net/**, si ottiene il risultato dell'azione da Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Ciò non significa, ad ogni modo, che sia sempre una buona idea (o almeno in generale) rendere disponibile un intero servizio cloud attraverso la rete CDN. È necessario valutare diversi aspetti:

-	Questo approccio richiede di rendere pubblico l'intero sito, perché la rete CDN di Azure non può gestire contenuti privati.
-	Se l'endpoint della rete CDN passa in modalità offline per qualsiasi motivo, per scopi di manutenzione o a causa dell'errore di un utente, passa in modalità offline anche l'intero servizio cloud, a meno che non sia possibile reindirizzare i clienti all'URL di origine **http://*&lt;nomeServizio>*.cloudapp.net/**. 
-	Anche con impostazioni di controllo della cache personalizzate (vedere [Configurare le opzioni di memorizzazione nella cache dei file statici nel servizio cloud](#caching)), un endpoint della rete CDN non migliora le prestazioni dei contenuti altamente dinamici. Se si è provato a caricare la home page dall'endpoint della rete CDN come sopra illustrato, si è osservato che il caricamento della pagina iniziale predefinita, cioè una pagina abbastanza semplice, ha richiesto almeno cinque secondi la prima volta. Si può dunque immaginare quale sarebbe l'esperienza del cliente se questa pagina includesse contenuto dinamico da aggiornare ogni minuto. Gestire contenuti dinamici da un endpoint della rete CDN richiede una scadenza breve della cache, che si traduce in frequenti mancati riscontri nella cache sull'endpoint della rete CDN. Ciò influisce negativamente sulle prestazioni del servizio cloud e vanifica lo scopo di una rete CDN.

L'alternativa consiste nel determinare quale contenuto rendere disponibile dalla rete CDN di Azure, valutando caso per caso nel servizio cloud. A tale scopo, si è già visto come accedere ai singoli file di contenuto dall'endpoint della rete CDN. Il modo in cui rendere disponibile un'azione del controller specifica attraverso l'endpoint della rete CDN verrà descritto in [Rendere disponibile il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller).

È possibile specificare una regola di riscrittura URL più restrittiva per limitare il contenuto accessibile attraverso l'endpoint della rete CDN. Ad esempio, per limitare la riscrittura URL alla cartella *\Scripts*, modificare le regole di riscrittura precedenti in questo modo:   
<pre class="prettyprint">
&lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
  &lt;match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/&gt;
  &lt;action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/&gt;
&lt;/rule&gt;
</pre>

<a name="caching"></a>
## Configurare le opzioni di memorizzazione nella cache dei file statici nel servizio cloud ##

Con l'integrazione della rete CDN di Azure nel servizio cloud è possibile specificare in che modo si vuole memorizzare il contenuto statico nella cache dell'endpoint della rete CDN. A questo scopo, aprire *Web.config* dal progetto di ruolo Web, ad esempio WebRole1, e aggiungere un elemento `<staticContent>` a `<system.webServer>`. Il linguaggio XML seguente configura la scadenza della cache entro tre giorni.  
<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

A questo punto, tutti i file statici nel servizio cloud osserveranno la stessa regola nella cache della rete CDN. Per un controllo più granulare delle impostazioni della cache, aggiungere un file *Web.config* in una cartella, aggiungendo qui anche le impostazioni. Ad esempio, aggiungere un file *Web.config* alla cartella *\Content* e sostituire il contenuto con il codice XML seguente:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Questa impostazione determina la memorizzazione nella cache per 15 giorni di tutti i file statici della cartella *\Content*.

Per altre informazioni su come configurare l'elemento `<clientCache>`, vedere la pagina relativa alla [cache client &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

In [Rendere disponibile il contenuto dalle azioni del controller attraverso la rete CDN di Azure](#controller)verrà anche descritto come configurare le impostazioni della cache per i risultati delle azioni del controller nella cache della rete CDN.

<a name="controller"></a>
## Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure ##

Quando si integra un ruolo Web del servizio cloud nella rete CDN di Azure, è relativamente facile gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure. Invece di rendere disponibile il servizio cloud direttamente attraverso la rete CDN (come descritto sopra), [Maarten Balliauw](https://twitter.com/maartenballiauw) mostra come farlo con un divertente controller MemeGenerator nel video sulla [riduzione della latenza sul Web con la rete CDN di Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). che viene riprodotto semplicemente in questo articolo.

Nel servizio cloud, si supponga di voler generare meme basati sull'immagine di un giovane Chuck Norris (foto di [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) come questa:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

È possibile usare una semplice azione `Index`, che consente ai clienti di specificare i superlativi nell'immagine e che quindi genera il meme dopo la pubblicazione dell'azione. Poiché si tratta di Chuck Norris, ci si aspetta che questa pagina diventi enormemente popolare in tutto il mondo. È un ottimo esempio di come gestire contenuto semi dinamico con la rete CDN di Azure. 

Seguire i passaggi precedenti per configurare questa azione del controller:

1. Nella cartella *\Controllers* creare un nuovo file con estensione cs chiamato *MemeGeneratorController.cs* e sostituire il contenuto con il codice seguente. Assicurarsi di sostituire la parte evidenziata con il nome della propria rete CDN.  
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
	
	namespace WebRole1.Controllers
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
	                return Redirect(string.Format(&quot;http://<mark>&lt;yourCdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
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

2. Fare clic con il pulsante destro del mouse sull'azione `Index()` predefinita e scegliere **Aggiungi visualizzazione**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Accettare le impostazioni seguenti e fare clic su **Aggiungi**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Aprire il nuovo file *Views\MemeGenerator\Index.cshtml* e sostituire il contenuto con il semplice codice HTML seguente per inviare i superlativi:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Pubblicare di nuovo il servizio cloud e passare a **http://*&lt;nomeServizio>*.cloudapp.net/MemeGenerator/Index** nel browser. 

Quando si inviano i valori del form a `/MemeGenerator/Index`, il metodo di azione `Index_Post` restituisce un collegamento al metodo di azione `Show` con il rispettivo identificatore di input. Facendo clic sul collegamento si raggiunge il codice seguente:  
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
        return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Se il debugger locale è collegato, si avrà una normale esperienza di debug con un reindirizzamento locale. Se viene eseguito nel servizio cloud, si verrà reindirizzati a:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

che corrisponde all'URL di origine seguente in corrispondenza dell'endpoint della rete CDN:

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Dopo la regola di riscrittura URL precedentemente applicata, il file effettivo che viene memorizzato nella cache dell'endpoint della rete CDN è il seguente:

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

È quindi possibile usare l'attributo `OutputCacheAttribute` nel metodo `Generate` per specificare il modo in cui il risultato dell'azione deve essere memorizzato nella cache, che verrà rispettato anche dalla rete CDN. Il codice seguente specifica la scadenza di una cache entro un'ora (3.600 secondi).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Analogamente, è possibile rendere disponibile il contenuto da qualsiasi azione del controller nel servizio cloud attraverso la rete CDN di Azure, con l'opzione di memorizzazione nella cache desiderata.

Nella sezione successiva verrà illustrato come gestire gli script e i file CSS in bundle e minimizzati attraverso la rete CDN di Azure. 

<a name="bundling"></a>
## Integrazione di creazione di bundle e minimizzazione ASP.NET con la rete CDN di Azure ##

Gli script e i fogli di stile CSS cambiano in maniera non frequente e sono i principali candidati per la cache della rete CDN di Azure. Il modo più semplice per integrare la creazione di bundle e la minimizzazione con la rete CDN di Azure consiste nel rendere disponibile l'intero ruolo Web. Tuttavia, poiché potrebbe non essere auspicabile, verrà illustrato come procedere pur mantenendo l'esperienza desiderata dallo sviluppatore per quanto riguarda la creazione di bundle e la minimizzazione ASP.NET, ad esempio:

-	Ottima esperienza della modalità di debug
-	Distribuzione semplificata
-	Aggiornamenti immediati delle versioni di script/css dei client
-	Meccanismo di fallback in caso di errore dell'endpoint della rete CDN
-	Riduzione delle modifiche del codice

Nel progetto **WebRole1** creato nella sezione [Integrare un endpoint della rete CDN di Azure con il servizio cloud e rendere disponibile contenuto statico nelle pagine Web dalla rete CDN di Azure](#deploy) aprire *App_Start\BundleConfig.cs* e osservare le chiamate al metodo `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

La prima istruzione `bundles.Add()` aggiunge un bundle di script nella directory virtuale `~/bundles/jquery`. Quindi, aprire *Views\Shared\_Layout.cshtml* per osservare come viene eseguito il rendering del tag del bundle di script. Dovrebbe essere possibile trovare la riga di codice Razor seguente:

    @Scripts.Render("~/bundles/jquery")

Quando questo codice Razor viene eseguito nel ruolo Web di Azure, eseguirà il rendering di un tag `<script>` per il bundle di script in modo simile al seguente: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Tuttavia, quando il codice viene eseguito in Visual Studio digitando `F5`, esegue singolarmente il rendering di ogni file di script nel bundle (nel caso precedente, il bundle contiene un solo file di script):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Ciò consente di eseguire il debug del codice JavaScript nell'ambiente di sviluppo e allo stesso tempo di ridurre le connessioni client simultanee (creazione di bundle) e migliorare le prestazioni di download dei file (minimizzazione) in produzione. È un'ottima funzionalità da mantenere con l'integrazione nella rete CDN di Azure. Per di più, dal momento che il bundle di cui è stato eseguito il rendering contiene una stringa di versione generata automaticamente, è opportuno replicare tale funzionalità in modo che ogni volta che si aggiorna la versione di jQuery attraverso NuGet è possibile aggiornarla sul lato client non appena possibile.

Attenersi alla procedura seguente per integrare la creazione di bundle e la minimizzazione ASP.NET con l'endpoint della rete CDN.

1. Tornare a *App_Start\BundleConfig.cs* e modificare i metodi `bundles.Add()` in modo da usare un [costruttore di bundle](http://msdn.microsoft.com/library/jj646464.aspx) diverso, che specifichi un indirizzo di rete CDN. A questo scopo, sostituire la definizione del metodo `RegisterBundles` con il codice seguente:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
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

	Assicurarsi di sostituire `<yourCDNName>` con il nome della rete CDN.

	In altri termini, si sta impostando `bundles.UseCdn = true`, aggiungendo a ogni bundle un URL della rete CDN definito con precisione. Ad esempio, il primo costruttore nel codice:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	è lo stesso di: 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Questo costruttore comunica alle operazioni di creazione di bundle e minimizzazione ASP.NET di eseguire il rendering dei singoli file di script quando ne viene eseguito il debug a livello locale, ma di usare l'indirizzo della rete CDN specificato per accedere allo script in questione. Notare tuttavia due importanti caratteristiche di questo URL della rete CDN definito con precisione:
	
	-	L'origine per questo URL della rete CDN è `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, che in realtà è la directory virtuale del bundle di script nel servizio cloud.
	-	Poiché si sta usando un costruttore CDN, il tag dello script CDN per il bundle non contiene più la stringa di versione generata automaticamente nell'URL di cui è stato eseguito il rendering. È necessario generare manualmente una stringa di versione univoca ogni volta che il bundle di script viene modificato per forzare un mancato riscontro nella cache nella rete CDN di Azure. Allo stesso tempo, questa stringa di versione univoca deve rimanere costante per tutta la durata della distribuzione per aumentare i riscontri nella cache nella rete CDN di Azure dopo aver distribuito il bundle.
	-	La stringa di query v=<W.X.Y.Z> esegue il pull da *Properties\AssemblyInfo.cs* nel progetto di ruolo Web. È possibile prevedere un flusso di lavoro di distribuzione che includa l'incremento della versione di assembly ogni volta che si pubblica su Azure. In alternativa, è possibile modificare solo *Properties\AssemblyInfo.cs* nel progetto per incrementare automaticamente la stringa di versione a ogni compilazione, usando il carattere jolly '*'. Ad esempio:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Qualsiasi altra strategia volta a semplificare la generazione di una stringa univoca per la durata della distribuzione avrà esito positivo.

3. Ripubblicare il servizio cloud e accedere alla home page.
 
4. Visualizzare il codice HTML relativo alla pagina. Dovrebbe essere possibile visualizzare l'URL della rete CDN di cui è stato eseguito il rendering, con una stringa di versione univoca ogni volta che si ripubblicano le modifiche al servizio cloud, Ad esempio:  
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

5. In Visual Studio eseguire il debug del servizio cloud digitando `F5`. 

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

Se si verifica un errore nell'endpoint della rete CDN di Azure per un motivo qualsiasi, è consigliabile configurare l'accesso della pagina Web al server Web di origine come opzione di fallback per il caricamento di JavaScript o Bootstrap. Un conto è perdere le immagini nel sito a causa della mancata disponibilità della rete CDN, un altro è perdere funzionalità essenziali della pagina fornite dagli script e dai fogli di stile.

La classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contiene una proprietà chiamata [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) che permette di configurare il meccanismo di fallback per gli errori della rete CDN. Per usare questa proprietà, seguire i passaggi descritti di seguito:

1. Nel progetto di ruolo Web aprire *App_Start\BundleConfig.cs*, in cui è stato aggiunto un URL della rete CDN in ogni [costruttore di bundle](http://msdn.microsoft.com/library/jj646464.aspx), e apportare le modifiche evidenziate indicate di seguito per aggiungere il meccanismo di fallback ai bundle predefiniti:  
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

	Quando `CdnFallbackExpression` non è null, lo script viene inserito nel codice HTML per provare se il bundle viene caricato correttamente e, in caso contrario, accedere direttamente al bundle dal server Web di origine. Questa proprietà deve essere impostata su un'espressione JavaScript che verifichi se il rispettivo bundle CDN è stato caricato correttamente. L'espressione necessaria per testare ogni bundle differisce in base al contenuto. Per i bundle predefiniti sopra riportati:
	
	-	`window.jquery` viene definito nel file jquery-{version}.js
	-	`$.validator` viene definito nel file jquery.validate.js
	-	`window.Modernizr` viene definito nel file modernizer-{version}.js
	-	`$.fn.modal` viene definito nel file bootstrap.js
	
	Come si può notare, la proprietà CdnFallbackExpression non è stata impostata per il bundle `~/Cointent/css`. Il motivo è che attualmente esiste un [bug in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) che inserisce un tag `<script>` per il file CSS di fallback invece del tag `<link>` previsto.
	
	È comunque disponibile un ottimo [fallback Style Bundle](https://github.com/EmberConsultingGroup/StyleBundleFallback), offerto da [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. Per usare questa soluzione alternativa, nella cartella *App_Start* del progetto di ruolo Web creare un nuovo file con estensione cs chiamato *StyleBundleExtensions.cs* e sostituirne il contenuto con il [codice di GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. In *App_Start\StyleFundleExtensions.cs* rinominare lo spazio dei nomi usando il nome del ruolo Web, ad esempio **WebRole1**. 

3. Tornare a `App_Start\BundleConfig.cs` e modificare l'ultima istruzione `bundles.Add` con il codice evidenziato seguente:  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Questo nuovo metodo di estensione usa la stessa idea di inserire lo script nel linguaggio HTML per cercare in DOM il nome di classe, il nome di regola e il valore di regola corrispondenti definiti nel bundle CSS, eseguendo il fallback sul server Web in caso non riesca a trovare la corrispondenza.

4. Pubblicare di nuovo il servizio cloud e accedere alla home page. 
5. Visualizzare il codice HTML relativo alla pagina. Gli script inseriti dovrebbero essere simili al seguente:    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
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
	            }())||document.write(&#39;&lt;script src=&quot;/Content/css&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>

	Notare che lo script inserito per il bundle CSS contiene ancora residui della proprietà `CdnFallbackExpression` nella riga:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Poiché però la prima parte dell'espressione || restituirà sempre true (nella riga subito sopra), la funzione document.write() non verrà mai eseguita.

# Altre informazioni #
- [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Rendere disponibile il contenuto dalla rete CDN di Azure nell'applicazione Web](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Integrare un sito Web di Azure con la rete CDN di Azure](cdn-websites-with-cdn.md)
- [Creazione di bundle e minimizzazione con ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Uso della rete CDN per Azure](cdn-how-to-use.md)

<!--HONumber=49-->