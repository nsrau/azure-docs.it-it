<properties 
	pageTitle="Trasformazione ed estensione del sito" 
	description="Da definire" 
	authors="cephalin" 
	writer="cephalin" 
	editor="mollybos" 
	manager="wpickett" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Trasformazione ed estensione del sito

Le dichiarazioni XDT ([XML Document Transformation](http://msdn.microsoft.com/it-it/library/dd465326.aspx)) consentono di trasformare il file [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) in Siti Web di Microsoft Azure. È inoltre possibile usare le dichiarazioni XDT per aggiungere estensioni private al sito, in modo da consentire azioni di amministrazione del sito personalizzate. In questo articolo è disponibile un'estensione del sito PHP Manager di esempio, che consente la gestione di impostazioni PHP mediante un'interfaccia Web.


<!-- MINI TOC -->

* [Trasformazione della configurazione del sito in ApplicationHost.config](#transform)
* [Estensione del sito](#extend)
	* [Informazioni generali sulle estensioni private del sito](#overview)
	* [Esempio di estensioni del sito: PHP Manager](#SiteSample)
		* [App Web di PHP Manager](#PHPwebapp)
		* [File applicationHost.xdt](#XDT)
	* [Distribuzione dell'estensione del sito](#deploy)

<h2><a id="transform"></a>Trasformazione della configurazione del sito in ApplicationHost.config</h2>
La piattaforma Siti Web di Azure offre flessibilità e controllo per la configurazione del sito. Benché il file di configurazione standard ApplicationHost.config di IIS non sia disponibile per la modifica diretta in Siti Web di Azure, la piattaforma supporta un modello di trasformazione dichiarativo do ApplicationHost.config basato su dichiarazioni XDT (XML Document Transformation).

Per avvalersi di tale funzionalità per la trasformazione, è necessario creare un file ApplicationHost.xdt con contenuto XDT e posizionarlo sotto la radice del sito. Quindi, nella pagina **Configura** del portale Microsoft Azure, configurare l'impostazione app `WEBSITE_PRIVATE_EXTENSIONS` su 1 (può essere necessario riavviare il sito). 

Il seguente esempio del file applicationHost.xdt mostra come aggiungere una nuova variabile di ambiente personalizzata a un sito che usa PHP 5.4.

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

 
Un file di log che include lo stato e i dettagli relativi alla trasformazione è disponibile nella radice FTP in LogFiles\Transform.

Per esempi aggiuntivi, vedere [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

**Nota**<br />
Non è possibile rimuovere o riordinare gli elementi dell'elenco di moduli in  `system.webServer` ma è possibile aggiungere elementi all'elenco. 


<h2><a id="extend"></a>Estensione del sito</h2>
<h3><a id="overview"></a>Informazioni generali sulle estensioni private del sito</h3>
Siti Web di Azure supporta le estensioni del sito come punto di estendibilità per azioni amministrative del sito. Alcune funzionalità della piattaforma Siti Web di Azure sono in effetti implementate come estensioni del sito preinstallate. Benché non sia possibile modificare le estensioni preinstallate della piattaforma, sarà possibile creare e configurare estensioni private per i propri siti. Questa funzionalità si basa anche sulle dichiarazioni XDT. Di seguito sono riportati i passaggi chiave per la creazione di un'estensione privata del sito:

1. **Contenuto** dell'estensione del sito: creare un'applicazione Web supportata da Siti Web di Azure
2. **Dichiarazione** dell'estensione del sito: creare un file ApplicationHost.xdt
3. **Distribuzione** dell'estensione del sito: inserire contenuti nella cartella SiteExtensions in `root`
4.  **Abilitazione** dell'estensione del sito: impostare il valore  `WEBSITE_PRIVATE_EXTENSIONS` dell'app su 1

È necessario che i collegamenti interni per l'applicazione Web facciano riferimento a un percorso relativo al percorso applicazione specificato nel file ApplicationHost.xdt. Dopo ogni modifica al file ApplicationHost.xdt sarà necessario un riciclo del sito. 

**Nota**: informazioni aggiuntive relative a questi elementi chiave sono disponibili all'indirizzo [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). L'esempio dettagliato incluso illustra la procedura per la creazione e l'abilitazione di un'estensione privata del sito. Il codice sorgente per l'esempio di PHP Manager seguente è disponibile per il download all'indirizzo [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

<h3><a id="SiteSample"></a>Esempio di estensioni del sito: PHP Manager</h3>

PHP Manager è un'estensione del sito che consente agli amministratori di sito di visualizzare e configurare con facilità le impostazioni PHP mediante un'interfaccia Web, invece di dovere modificare direttamente i file INI di PHP. I file di configurazione comuni per PHP includono il file php.ini, disponibile in Programmi, e il file .user.ini, disponibile nella cartella radice del sito. Poiché non è possibile modificare il file php.ini direttamente nella piattaforma Siti Web di Azure, l'estensione PHP Manager usa il file user.ini per applicare le modifiche relative alle impostazioni.

<h4><a id="PHPwebapp"></a>App Web di PHP Manager</h4>
	
Di seguito è riportata la home page del sito Web di PHP Manager:

![TransformSitePHPUI][TransformSitePHPUI]

Come si può notare, un'estensione del sito è analoga a una normale applicazione Web, ma include un file ApplicationHost.xdt aggiuntivo nella cartella radice del sito. Ulteriori dettagli sul file ApplicationHost.xdt sono disponibili nella sezione successiva di questo articolo.

L'estensione PHP Manager è stata creata mediante il modello di applicazione Web MVC 4 ASP.NET di Visual Studio. La visualizzazione seguente di Esplora soluzioni mostra la struttura dell'estensione del sito PHP Manager.

![TransformSiteSolEx][TransformSiteSolEx]

L'unica logica speciale necessaria per l'I/O file consente di indicare la posizione della directory wwwroot del sito. Come illustrato nell'esempio seguente, la variabile di ambiente "HOME" indica il percorso della radice del sito e il percorso wwwroot può essere creato mediante l'aggiunta di "site\wwwroot":


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

È tuttavia necessario prestare attenzione alla gestione dei collegamenti interni nelle estensioni del sito.  Se sono presenti collegamenti nei file HTML che forniscono i percorsi assoluti per i collegamenti interni nel sito, sarà necessario assicurarsi che davanti a tali collegamenti sia aggiunto il nome dell'estensione come radice del sito, poiché la radice del sito per l'estensione è ora "/`[your-extension-name]`/" anziché solo "/". È pertanto necessario aggiornare di conseguenza qualsiasi collegamento interno. Si supponga ad esempio che il codice includa un collegamento al seguente esempio: 

`"<a href="/Home/Settings">Impostazioni PHP</a>"`

Quando il collegamento fa parte di un'estensione del sito, è necessario che abbia il seguente formato:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"` 

In alternativa, è possibile usare solo percorsi relativi nel sito Web oppure, nel caso di siti Web ASP.NET, usare il metodo `@Html.ActionLink` che crea automaticamente i collegamenti appropriati.

<h4><a id="XDT"></a>File applicationHost.xdt</h4>

Il codice per l'estensione del sito deve essere inserito in %HOME%\SiteExtensions\[your-extension-name]. Questa sarà la radice dell'estensione.  

Per registrare l'estensione del sito con il file applicationHost.config, sarà necessario inserire un file denominato ApplicationHost.xdt nella radice dell'estensione. I contenuti del file ApplicationHost.xdt dovrebbero essere analoghi ai seguenti:

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

In tale modo, un nuovo percorso applicazione verrà aggiunto all'elenco di siti `system.applicationHost` nel sito SCM. Il sito SCM è un endpoint di amministrazione di siti, con credenziali di accesso specifiche Ha l'URL  `https://[your-site-name].scm.azurewebsites.net`.  

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

<h3><a id="deploy"></a>Distribuzione dell'estensione del sito</h3>

Per installare l'estensione del sito, è possibile usare FTP per copiare tutti i file dell'app Web nella cartella `\SiteExtensions\[your-extension-name]` del sito in cui si desidera installare l'estensione.  Assicurarsi di copiare anche il file ApplicationHost.xdt nello stesso percorso.

Nel portale Siti Web di Azure passare quindi alla scheda **Configura** per il sito Web associato all'estensione. Nella sezione **impostazioni app** aggiungere la chiave  `WEBSITE_PRIVATE_EXTENSIONS` e assegnarle il valore `1`.

![TransformSiteappSettings][TransformSiteappSettings]

Infine, nel portale Microsoft Azure, riavviare il sito Web per abilitare l'estensione.

![TransformSiteRestart][TransformSiteRestart]

L'estensione del sito dovrebbe essere visibile all'indirizzo:


`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]` 

Si noti che l'aspetto dell'URL è uguale a quello dell'URL del sito, ad eccezione del fatto che usa HTTPS e include ".scm". 

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
[TransformSiteappSettings]: ./media/web-sites-transform-extend/TransformSiteappSettings.png
[TransformSiteRestart]: ./media/web-sites-transform-extend/TransformSiteRestart.png





<!--HONumber=42-->
