<properties 
	pageTitle="Gestione di un sito Web Azure" 
	description="Collegamenti alle risorse per la gestione di un sito Web di Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2014" 
	ms.author="mwasson"/>

# Gestione di un sito Web Azure

In questo argomento sono riportati i collegamenti alle risorse per la gestione di un sito Web Azure. Nella gestione sono incluse tutte le attività che consentono al sito Web di funzionare alla perfezione. 

Nel corso del ciclo di vita di un sito, vengono eseguite diverse attività di gestione, a mano a mano che si passa dalla distribuzione iniziale alle normali attività operative, di manutenzione e di aggiornamento.

- [Prima della distribuzione del sito alla produzione]
- [Durante l'esecuzione del sito Web]
- [Durante l'aggiornamento del sito Web]

Nel portale Azure è possibile eseguire diverse attività di gestione dei siti Web. Per altre informazioni, vedere [Gestione dei siti Web attraverso il portale di gestione di Microsoft Azure](http://azure.microsoft.com/it-it/documentation/articles/web-sites-manage/).

## Prima della distribuzione del sito alla produzione

### Scegliere un livello

I siti Web Azure possono essere di quattro livelli: Gratuito, Condiviso, Di base e Standard. Per altre informazioni sulle funzionalità e i prezzi di ciascun livello, vedere [Dettagli prezzi](http://azure.microsoft.com/it-it/pricing/details/websites/). 

- I [piani di hosting Web](http://azure.microsoft.com/it-it/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) consentono di raggruppare più siti Web nello stesso livello.
- È sempre possibile [cambiare livello](http://azure.microsoft.com/it-it/documentation/articles/web-sites-scale/) dopo aver creato il sito Web..

### Configurazione

Usare il [portale di gestione di Azure](https://manage.windowsazure.com/) per impostare varie opzioni di configurazione. Per maggiori dettagli, vedere [Come configurare i siti Web](http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure/). Di seguito è riportato un rapido elenco di controllo:

- Selezionare **versioni runtime** per .NET, PHP, Java o Python, se necessario.
- Abilitare **WebSockets** se il sito Web usa il protocollo WebSocket. Sono incluse le app che usano [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](http://azure.microsoft.com/it-it/documentation/articles/web-sites-nodejs-chat-app-socketio/).
- Sul sito vengono eseguiti lavori Web continui? In caso affermativo, abilitare l'opzione **Always On**.
- Impostare il **documento predefinito**, ad esempio index.html.

Oltre a tali impostazioni di configurazione di base, può essere opportuno configurare quanto segue:

- Crittografia **Secure Socket Layer (SSL)**. Per usare SSL con un nome di dominio personalizzato, è necessario ottenere un certificato SSL e configurare il sito Web in modo tale che lo utilizzi. Vedere [Abilitare HTTPS per un sito Web di Azure](http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure-ssl-certificate/).
- **Nome di dominio personalizzato.** Al sito Web viene automaticamente associato un sottodominio in azurewebsites.net. È possibile associare un nome di dominio personalizzato, ad esempio contoso.com. Vedere [Configurare un nome di dominio personalizzato](http://azure.microsoft.com/it-it/documentation/articles/web-sites-custom-domain-name/).

Configurazione specifica del linguaggio:

- **PHP**: [Come configurare PHP in Siti Web di Azure](http://azure.microsoft.com/it-it/documentation/articles/web-sites-php-configure/).
- **Python**: [Configurazione di Python con Siti Web di Azure](http://azure.microsoft.com/it-it/documentation/articles/web-sites-python-configure/)


## Durante l'esecuzione del sito Web

Quando il sito è in esecuzione, accertarsi che sia disponibile e che assicuri la scalabilità in base al traffico degli utenti. Potrebbe essere necessario risolvere eventuali problemi.

### Monitoraggio

- Attraverso il portale di gestione, è possibile [aggiungere metriche delle prestazioni](http://azure.microsoft.com/it-it/documentation/articles/web-sites-monitor) quali utilizzo della CPU e numero di richieste dei client.
- Per maggiori dettagli, usare New Relic per il monitoraggio e la gestione delle prestazioni. Vedere [Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure](http://azure.microsoft.com/it-it/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Applicare la scalabilità al proprio sito Web](http://azure.microsoft.com/it-it/documentation/articles/web-sites-scale/) per rispondere al traffico. A seconda del livello, è possibile applicare la scalabilità al numero di VM e/o le dimensioni delle istanze delle VM. Nel piano Standard, è inoltre possibile impostare la scalabilità automatica da applicare automaticamente ai siti secondo una pianificazione fissa o in risposta al carico.  
 
### Backups

- Impostare i [backup automatici](http://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/) del sito Web. Ulteriori informazioni sui backup sono disponibili in [questo video](http://azure.microsoft.com/it-it/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Ulteriori informazioni sulle opzioni per il [ripristino database](http://msdn.microsoft.com/it-it/library/azure/hh852669.aspx) nel database SQL di Azure.

### Risoluzione dei problemi

- In caso di problemi, procedere con la [risoluzione dei problemi in Visual Studio](http://azure.microsoft.com/it-it/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug), usando log di diagnostica e debug attivo nel cloud. 
- Oltre a Visual Studio, esistono diversi modi per ottenere i log di diagnostica. Vedere [Abilitazione della registrazione diagnostica per Siti Web di Azure](http://azure.microsoft.com/it-it/documentation/articles/web-sites-enable-diagnostic-log/).
- Per le applicazioni Node.js, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure](http://azure.microsoft.com/it-it/documentation/articles/web-sites-nodejs-debug/).

### Ripristino dei dati

- [Ripristino](http://azure.microsoft.com/it-it/documentation/articles/web-sites-restore/) di un sito Web di cui si era eseguito un backup in precedenza.


## Durante l'aggiornamento del sito Web

Se non sono stati abilitati i backup automatici, è possibile creare un [backup manuale](http://azure.microsoft.com/it-it/documentation/articles/web-sites-backup/).

Valutare l'opportunità di applicare una [distribuzione a fasi](http://azure.microsoft.com/it-it/documentation/articles/web-sites-staged-publishing/). Questa opzione consente di pubblicare aggiornamenti a una distribuzione a fasi che viene eseguita in parallelo alla distribuzione di produzione. 

Se si usa Visual Studio Online, è possibile configurare la distribuzione continua dal controllo codice sorgente:

- [Utilizzo di Controllo della versione di Team Foundation (TFVC)](http://azure.microsoft.com/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [Utilizzo di Git](http://azure.microsoft.com/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Prima della distribuzione del sito alla produzione]: #before-you-deploy-your-site-to-production
[Durante l'esecuzione del sito Web]: #while-your-website-is-running
[Durante l'aggiornamento del sito Web]: #when-you-update-your-website

 

<!--HONumber=42-->
