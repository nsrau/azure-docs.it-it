<properties linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create websites - Azure service management" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Come creare un sito Web

Questo argomento descrive come creare un sito Web dalla raccolta oppure mediante il portale di gestione.

Per informazioni su come distribuire il contenuto in un sito Web di Azure creato, vedere la sezione relativa alla **distribuzione** in [Siti Web di Azure][Siti Web di Azure].

## Sommario

-   [Procedura: Creare un sito Web mediante il portale di gestione][Procedura: Creare un sito Web mediante il portale di gestione]
-   [Procedura: Creare un sito Web dalla raccolta][Procedura: Creare un sito Web dalla raccolta]
-   [Procedura: Eliminare un sito Web][Procedura: Eliminare un sito Web]
-   [Passaggi successivi][Passaggi successivi]

## <a name="createawebsiteportal"></a>Procedura: Creare un sito Web mediante il portale di gestione

Per creare un sito Web in Azure, attenersi alla procedura seguente.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic sull'icona **Create New** in basso a sinistra nel portale di gestione.

3.  Fare clic sull'icona **Sito Web** e quindi sull'icona **Creazione rapida**, immettere un valore per l'URL e selezionare il segno di spunta accanto a **Crea sito Web** nell'angolo in basso a destra della pagina.

4.  In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creazione del sito Web \<*nome del sito Web*\> completata**. Per passare al sito Web, fare clic su **Sfoglia** nella parte inferiore della pagina del portale.

5.  Nel portale fare clic sul nome del sito Web visualizzato nell'elenco dei siti Web per aprire la pagina di gestione **Avvio rapido del sito**.

6.  Nella pagina **Avvio rapido** sono visualizzate le opzioni per ottenere gli strumenti di sviluppo per il sito Web, configurare la pubblicazione del sito o configurare la distribuzione da un provider del controllo del codice sorgente come TFS o Git. La pubblicazione con FTP è configurata per impostazione predefinita per i siti Web e il nome host FTP è visualizzato in **Nome host FTP** nella sezione **Riepilogo rapido** della pagina **Dashboard**. Prima della pubblicazione con FTP o Git, scegliere l'opzione **Reimposta credenziali di distribuzione** nella pagina **Dashboard** per poter eseguire l'autenticazione con l'host FTP o il repository Git durante la distribuzione di contenuto nel sito Web.

7.  Nella pagina di gestione **Configura** le impostazioni visualizzate per il sito Web sono suddivise nelle seguenti categorie:

 -   **General**: consente di impostare la versione di .NET Framework o PHP richiesta dall'applicazione Web. Per i siti in modalità Standard l'opzione **Platform** consente di scegliere tra un ambiente a 32 o a 64 bit.

 -   **Certificates**: in modalità Standard è possibile caricare certificati SSL per domini personalizzati.

 -   **Domain Names**: nelle modalità Condiviso e Standard è possibile visualizzare e gestire nomi di dominio personalizzati per il sito Web.

 -   **SSL Bindings**: in modalità Standard è possibile assegnare certificati SSL ai nomi di dominio personalizzati mediante SSL basato su IP o SNI.

 -   **Deployments**: consente di configurare le distribuzioni quando si configura la distribuzione dal controllo del codice sorgente.

 -   **Application Diagnostics**: consente di impostare le opzioni per la raccolta di tracce di diagnostica da un'applicazione Web che è stata instrumentata con tracce.

 -   **Site Diagnostics**: consente di impostare le opzioni di registrazione per la raccolta di informazioni diagnostiche per il sito Web. Le opzioni includono Web Server Logging, Detailed Error Messages e Failed Request Tracing.

 -   **Monitoraggio**: per i siti Web in modalità Standard è possibile usare questa funzionalità per testare la disponibilità di endpoint HTTP o HTTPS.

 -   **App Settings**: consente di specificare coppie nome-valore che verranno caricate dall'applicazione Web all'avvio. Per i siti .NET queste impostazioni verranno inserite nelle impostazioni di **AppSettings** della configurazione .NET in fase di esecuzione, sostituendo le impostazioni esistenti. Per i siti PHP e Node queste impostazioni saranno disponibili come variabili di ambiente in fase di esecuzione.

 -   **Connection Strings**: consente di visualizzare e modificare le stringhe di connessione. Per i siti .NET tali stringhe verranno inserite nelle impostazioni di **connectionStrings** della configurazione .NET in fase di esecuzione, sostituendo le voci esistenti in cui la chiave è uguale al nome del database collegato. Per i siti PHP e Node queste impostazioni saranno disponibili come variabili di ambiente in fase di esecuzione.

 -   **Default Documents**: il documento predefinito di un sito Web è la pagina che viene visualizzata per impostazione predefinita quando un utente visita un sito Web. Aggiungere a questo elenco il documento predefinito dell'applicazione Web, se non è già presente. Il documento predefinito del sito Web è in genere visualizzato all'inizio dell'elenco.

 -   **Handler Mappings**: consente di specificare i processori script che gestiranno le richieste relative a estensioni file specifiche, ad esempio \*.php.

## <a name="howtocreatefromgallery"></a>Procedura: Creare un sito Web dalla raccolta

[WACOM.INCLUDE [website-from-gallery][website-from-gallery]]

## <a name="deleteawebsite"></a>Procedura: Eliminare un sito Web

Per eliminare i siti Web viene usata l'icona **Elimina** del portale di gestione di Azure. L'icona **Elimina** è disponibile nel portale di Azure quando si fa clic su **Siti Web** per visualizzare l'elenco di tutti i siti Web, nonché nella parte inferiore di tutte le pagine di gestione del sito Web.

## <a name="nextsteps"></a>Passaggi successivi

Per altre informazioni, vedere [Siti Web di Azure][Siti Web di Azure].

  [Siti Web di Azure]: /it-it/documentation/services/web-sites/
  [Procedura: Creare un sito Web mediante il portale di gestione]: #createawebsiteportal
  [Procedura: Creare un sito Web dalla raccolta]: #howtocreatefromgallery
  [Procedura: Eliminare un sito Web]: #deleteawebsite
  [Passaggi successivi]: #nextsteps
  [portale di gestione di Azure]: http://manage.windowsazure.com/
  [website-from-gallery]: ../includes/website-from-gallery.md
