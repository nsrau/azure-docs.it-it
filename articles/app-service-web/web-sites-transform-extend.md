<properties
	pageTitle="Configurazione avanzata ed estensioni dell'app Web di Servizio app di Azure"
	description="Utilizzare le dichiarazioni XMDT (XML Document Transformation) per trasformare il file ApplicationHost.config nell'app Web di Servizio Web di Azure e per aggiungere estensioni provate per abilitare azioni di amministrazione personalizzate.";"
	authors="cephalin"
	writer="cephalin"
	editor="mollybos"
	manager="wpickett"
	services="app-service\web"
	documentationCenter=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="cephalin"/>

# Configurazione avanzata ed estensioni dell'app Web di Servizio app di Azure

Le dichiarazioni XDT [(XML Document Transformation)](http://msdn.microsoft.com/library/dd465326.aspx) consentono di trasformare il file [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) in app Web in Servizio app di Azure. È inoltre possibile usare le dichiarazioni XDT per aggiungere estensioni private in modo da consentire azioni di amministrazione personalizzate nell'app Web. In questo articolo è disponibile un'estensione dell'app PHP Manager di esempio, che consente la gestione di impostazioni PHP mediante un'interfaccia Web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configurazione avanzata tramite ApplicationHost.config
La piattaforma Servizio app offre flessibilità e controllo per la configurazione dell'app Web. Benché il file di configurazione standard ApplicationHost.config di IIS non sia disponibile per la modifica diretta in Servizio app, la piattaforma supporta un modello di trasformazione dichiarativo di ApplicationHost.config basato su dichiarazioni XDT (XML Document Transformation).

Per avvalersi di tale funzionalità di trasformazione, è necessario creare un file ApplicationHost.xdt con contenuto XDT e posizionarlo sotto la radice del sito. Potrebbe essere necessario riacciare l'app Web per rendere effettive le modifiche.

Il seguente esempio di applicationHost.xdt mostra come aggiungere una nuova variabile di ambiente personalizzata a un'app Web che utilizza PHP 5.4.

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.webServer>
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
         				</environmentVariables>
      				</application>
    			</fastCgi>
  		</system.webServer>
	</configuration>


Un file di log che include lo stato e i dettagli relativi alla trasformazione è disponibile nella radice FTP in LogFiles\\Transform.

Per esempi aggiuntivi, vedere [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

**Nota**<br /> Non è possibile rimuovere o riordinare gli elementi dell'elenco di moduli in `system.webServer`, ma è possibile aggiungere elementi all'elenco.


##<a id="extend"></a> Estendere l'app Web

###<a id="overview"></a> Panoramica delle estensioni di app Web private

Servizi app supporta le estensioni di app Web come punto di estensibilità per le azioni di amministrazione. Alcune funzionalità della piattaforma Servizio app sono in effetti implementate come estensioni preinstallate. Benché non sia possibile modificare le estensioni preinstallate della piattaforma, è possibile creare e configurare estensioni private per le proprie app Web. Questa funzionalità si basa anche sulle dichiarazioni XDT. Di seguito sono riportati i passaggi chiave per la creazione di un'estensione privata di app Web:

1. Estensione di app Web **content**: creare applicazioni Web supportate da Servizio app
2. Estensione di app Web **declaration**: creare un file ApplicationHost.xdt
3. Estensione di app Web **deployment**: collocare il contenuto nella cartella SiteExtensions in `root`

È necessario che i collegamenti interni per l'app Web facciano riferimento a un percorso relativo al percorso applicazione specificato nel file ApplicationHost.xdt. Dopo ogni modifica al file ApplicationHost.xdt sarà necessario il riavvio dell'app Web.

informazioni aggiuntive relative a questi elementi chiave sono disponibili all'indirizzo **[https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)**.

L'esempio dettagliato incluso illustra la procedura per la creazione e l'abilitazione di un'estensione privata dell'app Web. Il codice sorgente per l'esempio di PHP Manager seguente è disponibile per il download all'indirizzo [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a> Esempio di estensione app Web: PHP Manager

PHP Manager è un'estensione di app Web che consente agli amministratori di app Web di visualizzare e configurare con facilità le impostazioni PHP mediante un'interfaccia Web, invece di dovere modificare direttamente i file INI di PHP. I file di configurazione comuni per PHP includono il file php.ini, disponibile in Programmi, e il file .user.ini, disponibile nella cartella radice dell'app Web. Poiché non è possibile modificare il file php.ini direttamente nella piattaforma Servizio app, l'estensione PHP Manager usa il file .user.ini per applicare le modifiche relative alle impostazioni.

####<a id="PHPwebapp"></a> Applicazione Web PHP Manager

Di seguito è riportata la home page della distribuzione di PHP Manager:

![TransformSitePHPUI][TransformSitePHPUI]

Come si può notare, un'estensione di app Web è analoga a una normale applicazione Web, ma include un file ApplicationHost.xdt nella cartella radice dell'app Web. Ulteriori dettagli sul file ApplicationHost.xdt sono disponibili nella sezione successiva di questo articolo.

L'estensione PHP Manager è stata creata mediante il modello di applicazione Web MVC 4 ASP.NET di Visual Studio. La visualizzazione seguente di Esplora soluzioni mostra la struttura dell'estensione di PHP Manager.

![TransformSiteSolEx][TransformSiteSolEx]

L'unica logica speciale necessaria per l'I/O file consente di indicare la posizione della directory wwwroot dell'app Web. Come illustrato nell'esempio seguente, la variabile di ambiente "HOME" indica il percorso della radice dell'app Web e il percorso wwwroot può essere creato mediante l'aggiunta di "site\\wwwroot":

	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
    		return userSettingsFile;
	}


Quando si dispone del percorso della directory, sarà possibile usare operazioni normali di I/O file per la lettura e la scrittura nei file.

È tuttavia necessario prestare attenzione alla gestione dei collegamenti interni nelle estensioni di app Web. Se sono presenti collegamenti nei file HTML che forniscono i percorsi assoluti per i collegamenti interni nell'app Web, sarà necessario assicurarsi che davanti a tali collegamenti sia aggiunto il nome dell'estensione come radice. poiché la radice del sito per l'estensione è ora "/`[your-extension-name]`/" invece di essere solo "/". È pertanto necessario aggiornare di conseguenza qualsiasi collegamento interno. Si supponga ad esempio che il codice includa un collegamento all'elemento seguente:

`"<a href="/Home/Settings">PHP Settings</a>"`

Quando il collegamento fa parte di un'estensione dell'app Web, è necessario che abbia il formato seguente:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

In alternativa è possibile usare solo percorsi relativi nell'applicazione Web oppure, nel caso di applicazioni ASP.NET NET , utilizzando il metodo `@Html.ActionLink` che crea automaticamente i collegamenti appropriati.

####<a id="XDT"></a> File applicationHost.xdt

Il codice per l'estensione di app Web va sotto %HOME%\\SiteExtensions\\your-extension-name. Questa sarà la radice dell'estensione.

Per registrare l'estensione dell'app Web con il file applicationHost.config, sarà necessario inserire un file denominato ApplicationHost.xdt nella radice dell'estensione. Il contenuto del file ApplicationHost.xdt deve essere analogo al seguente:

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

Il nome selezionato come nome dell'estensione deve essere uguale al nome della cartella radice dell'estensione.

In tale modo, un nuovo percorso applicazione verrà aggiunto all'elenco di siti `system.applicationHost` nel sito SCM. Il sito SCM è un endpoint di amministrazione di siti, con credenziali di accesso specifiche Ha l'URL `https://[your-site-name].scm.azurewebsites.net`.

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\SiteExtensions[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

###<a id="deploy"></a> Distribuzione dell'estensione di app Web

Per installare l'estensione dell'app Web, è possibile utilizzare FTP per copiare tutti i file dell'applicazione Web nella cartella `\SiteExtensions[your-extension-name]` dell'app Web su cui si desidera installare l'estensione. Assicurarsi di copiare anche il file ApplicationHost.xdt nello stesso percorso. Riavviare l'app Web per abilitare l'estensione.

L'estensione dell'app Web dovrebbe essere visibile all'indirizzo:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Si noti che l'aspetto dell'URL è uguale a quello dell'URL dell'app Web, ad eccezione del fatto che utilizza HTTPS e include ".scm".

È possibile disabilitare tutte le estensionii provate (non preinstallate) per l'app Web durante sviluppo e analisi aggiungendo impostazioni di un'app con la chiave `WEBSITE_PRIVATE_EXTENSIONS` e il valore `0`.

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 

<!---HONumber=Sept15_HO3-->