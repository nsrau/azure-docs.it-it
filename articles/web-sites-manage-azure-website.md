<properties title="Manage an Azure website" pageTitle="Manage an Azure website" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson"></tags>

# Gestione di un sito Web Azure

In questo argomento sono riportati i collegamenti alle risorse per la gestione di un sito Web Azure. Nella gestione sono incluse tutte le attività che consentono al sito Web di funzionare alla perfezione.

Nel corso del ciclo di vita di un sito, vengono eseguite diverse attività di gestione, a mano a mano che si passa dalla distribuzione iniziale alle normali attività operative, di manutenzione e di aggiornamento.

-   [Prima della distribuzione del sito alla produzione][]
-   [Durante l'esecuzione del sito Web][]
-   [Durante l'aggiornamento del sito Web][]

Nel portale Azure è possibile eseguire diverse attività di gestione dei siti Web. Per ulteriori informazioni, vedere [Gestione dei siti Web attraverso il portale di gestione di Microsoft Azure][].

## Prima della distribuzione del sito alla produzione

### Scegliere un livello

I siti Web Azure possono essere di quattro livelli: Gratuito, Condiviso, Di base e Standard. Per ulteriori informazioni sulle funzionalità e i prezzi di ciascun livello, vedere [Dettagli prezzi][].

-   I [piani di hosting Web][] consentono di raggruppare più siti Web nello stesso livello.
-   È sempre possibile [cambiare livello][] dopo aver creato il sito Web.

### Configurazione

Utilizzare il [portale di gestione di Azure][] per impostare varie opzioni di configurazione. Per maggiori dettagli, vedere [Come configurare i siti Web][]. Di seguito è riportato un rapido elenco di controllo:

-   Selezionare le **versioni runtime** per .NET, PHP, Java o Python, se necessario.
-   Abilitare **WebSocket** se il sito Web utilizza il protocollo WebSocket. Sono incluse le app che utilizzano [ASP.NET SignalR][] o [socket.io][].
-   Sul sito vengono eseguiti lavori Web continui? In caso affermativo, abilitare l'opzione **Sempre attivo**.
-   Impostare il **documento predefinito**, ad esempio index.html.

Oltre a tali impostazioni di configurazione di base, può essere opportuno configurare quanto segue:

-   Crittografia **Secure Socket Layer (SSL)**. Per utilizzare SSL con un nome di dominio personalizzato, è necessario ottenere un certificato SSL e configurare il sito Web in modo tale che lo utilizzi. Vedere [Abilitare HTTPS per un sito Web di Azure][].
-   **Nome di dominio personalizzato** Al sito Web viene automaticamente associato un sottodominio in azurewebsites.net. È possibile associare un nome di dominio personalizzato, ad esempio contoso.com. Vedere [Configurare un nome di dominio personalizzato][].

Configurazione specifica per ciascun linguaggio:

-   **PHP**: [Come configurare PHP in Siti Web di Azure][].
-   **Python**: [Configurazione di Python con Siti Web di Azure][]

## Durante l'esecuzione del sito Web

Quando il sito è in esecuzione, accertarsi che sia disponibile e che assicuri la scalabilità in base al traffico degli utenti. Potrebbe essere necessario risolvere eventuali problemi.

### Monitoraggio

-   Attraverso il portale di gestione, è possibile [aggiungere metriche delle prestazioni][] quali utilizzo della CPU e numero di richieste dei client.
-   Per maggiori dettagli, utilizzare New Relic per il monitoraggio e la gestione delle prestazioni. Vedere [Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure][].
-   [Applicare la scalabilità al proprio sito Web][cambiare livello] per rispondere al traffico. A seconda del livello, è possibile applicare la scalabilità al numero di VM e/o le dimensioni delle istanze delle VM. Nel piano Standard, è inoltre possibile impostare la scalabilità automatica da applicare automaticamente ai siti secondo una pianificazione fissa o in risposta al carico.

### Backups

-   Impostare i [backups automatici][] del sito Web. Ulteriori informazioni sui backup sono disponibili in [questo video][].
-   Ulteriori informazioni sulle opzioni per il [ripristino database][] nel database SQL di Azure

### Risoluzione dei problemi

-   In caso di problemi, procedere con la [risoluzione dei problemi in Visual Studio][], utilizzando log di diagnostica e debug attivo nel cloud.
-   Oltre a Visual Studio, esistono diversi modi per ottenere i log di diagnostica. Vedere [Abilitazione della registrazione diagnostica per Siti Web di Azure][].
-   Per le applicazioni Node.js, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure][].

### Ripristino dei dati

-   [Ripristino][] di un sito Web di cui si era eseguito un backup in precedenza.

## Durante l'aggiornamento del sito Web

Se non sono stati abilitati i backup automatici, è possibile creare un [backup manuale][backups automatici].

Valutare l'opportunità di applicare una [distribuzione a fasi][]. Questa opzione consente di pubblicare aggiornamenti a una distribuzione a fasi che viene eseguita in parallelo alla distribuzione di produzione.

Se si utilizza Visual Studio Online, è possibile configurare la distribuzione continua dal controllo codice sorgente:

-   [Utilizzo di Controllo della versione di Team Foundation (TFVC)][]
-   [Utilizzo di Git][]

<!-- Anchors. -->

  [Prima della distribuzione del sito alla produzione]: #before-you-deploy-your-site-to-production
  [Durante l'esecuzione del sito Web]: #while-your-website-is-running
  [Durante l'aggiornamento del sito Web]: #when-you-update-your-website
  [Gestione dei siti Web attraverso il portale di gestione di Microsoft Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-manage/
  [Dettagli prezzi]: http://azure.microsoft.com/en-us/pricing/details/websites/
  [piani di hosting Web]: http://azure.microsoft.com/it-it/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
  [cambiare livello]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-scale/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [Come configurare i siti Web]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Abilitare HTTPS per un sito Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure-ssl-certificate/
  [Configurare un nome di dominio personalizzato]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-custom-domain-name/
  [Come configurare PHP in Siti Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-php-configure/
  [Configurazione di Python con Siti Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-python-configure/
  [aggiungere metriche delle prestazioni]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-monitor
  [Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [backups automatici]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/
  [questo video]: http://azure.microsoft.com/it-it/documentation/videos/azure-websites-automatic-and-easy-backup/
  [ripristino database]: http://msdn.microsoft.com/en-us/library/azure/hh852669.aspx
  [risoluzione dei problemi in Visual Studio]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Abilitazione della registrazione diagnostica per Siti Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-enable-diagnostic-log/
  [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-nodejs-debug/
  [Ripristino]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-restore/
  [distribuzione a fasi]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-staged-publishing/
  [Utilizzo di Controllo della versione di Team Foundation (TFVC)]: http://azure.microsoft.com/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Utilizzo di Git]: http://azure.microsoft.com/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
