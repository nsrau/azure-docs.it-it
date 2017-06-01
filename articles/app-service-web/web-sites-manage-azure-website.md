---
title: Scalare un&quot;app Web in Servizio app di Azure
description: Collegamenti a risorse per la gestione di un&quot;applicazione web nel servizio di applicazione Azure.
services: app-service\web
documentationcenter: 
author: erikre
manager: erikre
editor: 
ms.assetid: d5e2887a-84f9-4747-a573-867635cb8b39
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 08a84ace63465e7c058add69a25687fe46c7ea07
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="manage-a-web-app-in-azure-app-service"></a>Scalare un'app Web in Servizio app di Azure
Questo argomento include i collegamenti alle risorse per la gestione di un'app Web nel [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Nella gestione sono incluse tutte le attività che consentono al sito Web di funzionare alla perfezione. 

Nel corso del ciclo di vita di un sito, vengono eseguite diverse attività di gestione, a mano a mano che si passa dalla distribuzione iniziale alle normali attività operative, di manutenzione e di aggiornamento.

Nel portale di Azure è possibile eseguire diverse attività di gestione dei siti Web.

## <a name="before-you-deploy-your-web-app-to-production"></a>Prima della distribuzione del sito alla produzione
### <a name="choose-a-tier"></a>Scegliere un livello
Servizio di applicazione Azure è disponibile in cinque livelli: libero, Shared, Basic, Standard e Premium. Per ulteriori informazioni sulle funzionalità e i prezzi di ciascun livello, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/app-service/). 

* [Piani di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) consentono di raggruppare più applicazioni web con lo stesso livello.
* È sempre possibile [cambiare livello](web-sites-scale.md) dopo aver creato il sito Web.

### <a name="configuration"></a>Configurazione
Utilizzare il [portale di Azure](https://portal.azure.com/) per impostare varie opzioni di configurazione. Per informazioni dettagliate, vedere [Configurazione delle app Web in Servizio app di Azure](web-sites-configure.md). Di seguito è riportato un rapido elenco di controllo:

* Selezionare le **versioni runtime** per .NET, PHP, Java o Python, se necessario.
* Abilitare **WebSocket** se il sito Web utilizza il protocollo WebSocket. Sono incluse le app che usano [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](web-sites-nodejs-chat-app-socketio.md).
* Sul sito vengono eseguiti lavori Web continui? In caso affermativo, abilitare l'opzione **Sempre attivo**.
* Impostare il **documento predefinito**, ad esempio index.html.

Oltre a tali impostazioni di configurazione di base, può essere opportuno configurare quanto segue:

* **Secure Socket Layer (SSL)** . Per utilizzare SSL con un nome di dominio personalizzato, è necessario ottenere un certificato SSL e configurare il sito Web in modo tale che lo utilizzi. Vedere [Abilitazione di HTTPS per un'app Web nel servizio app di Azure](app-service-web-tutorial-custom-ssl.md).
* **Nome di dominio personalizzato.** L'applicazione web dispone automaticamente un sottodominio in azurewebsites.net. È possibile associare un nome di dominio personalizzato, ad esempio contoso.com. Vedere [Configurazione di un nome di dominio personalizzato nel servizio app di Azure](app-service-web-tutorial-custom-domain.md).

Configurazione specifica per ciascun linguaggio:

* **PHP**: [Configurazione di PHP nelle app Web di Servizio app di Azure](web-sites-php-configure.md).
* **Python**: [configurazione Python con Azure applicazione servizio Web App](web-sites-python-configure.md)

## <a name="while-your-web-app-is-running"></a>Durante l'esecuzione di un'applicazione web
Quando il sito è in esecuzione, accertarsi che sia disponibile e che assicuri la scalabilità in base al traffico degli utenti. Potrebbe essere necessario risolvere eventuali problemi.

### <a name="monitoring"></a>Monitoraggio
* Attraverso il portale di Azure, è possibile [aggiungere metriche delle prestazioni](web-sites-monitor.md) quali utilizzo della CPU e numero di richieste dei client.
* [Applicare la scalabilità al proprio sito Web](web-sites-scale.md) per rispondere al traffico. A seconda del livello, è possibile applicare la scalabilità al numero di VM e/o le dimensioni delle istanze delle VM. Nel piano Standard, è inoltre possibile impostare la scalabilità automatica da applicare automaticamente ai siti secondo una pianificazione fissa o in risposta al carico.  

### <a name="backups"></a>Backup
* Impostare i [backups automatici](web-sites-backup.md) del sito Web. Ulteriori informazioni sui backup sono disponibili in [questo video](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
* Ulteriori informazioni sulle opzioni per il [ripristino database](../sql-database/sql-database-business-continuity.md) nel database SQL di Azure

### <a name="troubleshooting"></a>Risoluzione dei problemi
* In caso di problemi, procedere con la [risoluzione dei problemi in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), utilizzando log di diagnostica e debug attivo nel cloud. 
* Oltre a Visual Studio, esistono diversi modi per ottenere i log di diagnostica. Vedere [Abilitazione della registrazione diagnostica per app Web nel servizio app di Azure](web-sites-enable-diagnostic-log.md).
* Per le applicazioni Node.js, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Ripristino dei dati
* [Ripristino](web-sites-restore.md) di un sito Web di cui si era eseguito un backup in precedenza.

## <a name="when-you-update-your-web-app"></a>Durante l'aggiornamento del sito Web
Se non sono stati abilitati i backup automatici, è possibile creare un [backup manuale](web-sites-backup.md).

Valutare l'opportunità di applicare una [distribuzione a fasi](web-sites-staged-publishing.md). Questa opzione consente di pubblicare aggiornamenti a una distribuzione a fasi che viene eseguita in parallelo alla distribuzione di produzione. 

Se si utilizza Visual Studio Team Services, è possibile configurare la distribuzione continua dal controllo codice sorgente:

* [Utilizzo di Controllo della versione di Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
* [Utilizzo di Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)

<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website



