<properties  linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create web sites - Azure service management" metaKeywords="Azure creating web site, Azure deleting website" description="Learn how to create a web site using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Web Site" authors="timamm" solutions="" manager="" editor="" />
# Come creare un sito Web

In questo argomento viene illustrato come creare un sito Web dalla
raccolta oppure mediante il portale di gestione.

Per informazioni su come distribuire il contenuto in un sito Web di
Azure creato, vedere la sezione relativa alla **distribuzione** in [Siti
Web di Azure](/en-us/documentation/services/web-sites/).
## Sommario

* [Procedura: Creare un sito Web mediante il portale di
  gestione](#createawebsiteportal)
* [Procedura: Creare un sito Web dalla
  raccolta](#howtocreatefromgallery)
* [Procedura: Eliminare un sito Web](#deleteawebsite)
* [Passaggi successivi](#nextsteps)
## <a name="createawebsiteportal"></a>Procedura: Creare un sito Web mediante il portale di gestione

Per creare un sito Web in Azure, attenersi alla procedura seguente.

1.  Eseguire l'accesso al [portale di gestione di Azure][1].

2.  Fare clic sull'icona **Create New** in basso a sinistra nel portale
    di gestione.

3.  Fare clic sull'icona **Web Site** e quindi su **Quick Create**,
    immettere un valore per l'URL e selezionare il segno di spunta
    accanto a **Create Web Site** nell'angolo in basso a destra della
    pagina.

4.  In seguito alla creazione del sito Web verrà visualizzato il
    messaggio **Creating web site <*nome sito Web*> succeeded**.
    Per passare al sito Web, fare clic su **Browse** nella parte
    inferiore della pagina del portale.

5.  Nel portale fare clic sul nome del sito Web visualizzato
    nell'elenco dei siti per aprire la pagina di gestione **Quick
    Start** del sito.

6.  Nella pagina **Quick Start** sono visualizzate le opzioni per
    ottenere gli strumenti di sviluppo per il sito Web, configurare la
    pubblicazione del sito o configurare la distribuzione da un provider
    del controllo del codice sorgente come TFS o Git. La pubblicazione
    con FTP è configurata per impostazione predefinita per i siti Web e
    il nome host FTP è visualizzato in **FTP Host Name** nella sezione
    **Quick Glance** della pagina **Dashboard**. Prima della
    pubblicazione con FTP o Git, scegliere l'opzione **Reset deployment
    credentials** nella pagina **Dashboard** per poter eseguire
    l'autenticazione con l'host FTP o l'archivio Git durante la
    distribuzione di contenuto nel sito Web.

7.  Nella pagina di gestione **Configure** le impostazioni visualizzate
    per il sito Web sono suddivise nelle seguenti categorie:

	* **General**: consente di impostare la versione di .NET Framework o
  PHP richiesta dall'applicazione Web. Per i siti in modalità Standard
  l'opzione **Platform** consente di scegliere tra un ambiente a 32 o a
  64 bit.

* **Certificates**: in modalità Standard è possibile caricare
  certificati SSL per domini personalizzati.

* **Domain Names**: nelle modalità Shared e Standard è possibile
  visualizzare e gestire nomi di dominio personalizzati per il sito Web.

* **SSL Bindings**: in modalità Standard è possibile assegnare
  certificati SSL ai nomi di dominio personalizzati mediante SSL basato
  su IP o SNI.

	* **Deployments**: consente di configurare le distribuzioni quando si
  configura la distribuzione dal controllo del codice sorgente.

	* **Application Diagnostics**: consente di impostare le opzioni per la
  raccolta di tracce di diagnostica da un'applicazione Web che è stata
  instrumentata con tracce.

* **Site Diagnostics**: consente di impostare le opzioni di
  registrazione per la raccolta di informazioni diagnostiche per il sito
  Web. Le opzioni includono Web Server Logging, Detailed Error Messages
  e Failed Request Tracing.

* **Monitoring**: per i siti Web in modalità Standard è possibile
  utilizzare questa funzionalità per testare la disponibilità di
  endpoint HTTP o HTTPS.

* **App Settings**: consente di specificare coppie nome-valore che
  verranno caricate dall'applicazione Web all'avvio. Per i siti .NET
  queste impostazioni verranno inserite nelle impostazioni di
  **AppSettings** della configurazione .NET in fase di esecuzione,
  sostituendo le impostazioni esistenti. Per i siti PHP e Node queste
  impostazioni saranno disponibili come variabili di ambiente in fase di
  esecuzione.

	* **Connection Strings**: consente di visualizzare e modificare le
  stringhe di connessione. Per i siti .NET tali stringhe verranno
  inserite nelle impostazioni di **connectionStrings** della
  configurazione .NET in fase di esecuzione, sostituendo le voci
  esistenti in cui la chiave è uguale al nome del database collegato.
  Per i siti PHP e Node queste impostazioni saranno disponibili come
  variabili di ambiente in fase di esecuzione.

	* **Default Documents**: il documento predefinito di un sito Web è la
  pagina che viene visualizzata per impostazione predefinita quando un
  utente visita un sito Web. Aggiungere a questo elenco il documento
  predefinito dell'applicazione Web, se non è già presente. Il
  documento predefinito del sito Web è in genere visualizzato
  all'inizio dell'elenco.

* **Handler Mappings**: consente di specificare i processori script che
  gestiranno le richieste relative a estensioni file specifiche, ad
  esempio \*.php.
## <a name="howtocreatefromgallery"></a>Procedura: Creare un sito Web dalla raccolta

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]
## <a name="deleteawebsite"></a>Procedura: Eliminare un sito Web

Per eliminare i siti Web viene utilizzata l'icona **Delete** del
portale di gestione di Azure. L'icona **Delete** è disponibile nel
portale Azure quando si fa clic su **Web Sites** per visualizzare
l'elenco di tutti i siti Web, nonché nella parte inferiore di tutte le
pagine di gestione del sito Web.
## <a name="nextsteps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Siti Web di
Azure](/en-us/documentation/services/web-sites/).



[1]: http://manage.windowsazure.com/
