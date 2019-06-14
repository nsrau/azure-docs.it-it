---
title: Eseguire il debug di applicazioni Proxy di applicazione - Azure Active Directory | Microsoft Docs
description: Eseguire il debug di problemi con applicazioni Proxy di applicazione di Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172266"
---
# <a name="debug-application-proxy-application-issues"></a>Il debug dei problemi dell'applicazione Proxy dell'applicazione 

Questo articolo consente di risolvere i problemi con applicazioni Proxy di applicazione di Azure Active Directory (Azure AD). Se si usa il servizio Proxy di applicazione per l'accesso remoto a un'applicazione web in locale, ma si verificano problemi durante la connessione all'applicazione, usare questo diagramma di flusso per il debug dei problemi dell'applicazione. 

## <a name="before-you-begin"></a>Prima di iniziare

Risolvere i problemi di Proxy dell'applicazione, è consigliabile che iniziare con i connettori. In primo luogo, seguire il flusso di risoluzione dei problemi nelle [emette Debug Application Proxy Connector](application-proxy-debug-connectors.md) per assicurarsi che i connettori del Proxy di applicazione siano configurati correttamente. Se si verificano ancora problemi, tornare a questo articolo per risolvere i problemi dell'applicazione.  

Per altre informazioni sul Proxy di applicazione, vedere:

- [Accesso remoto alle applicazioni locali tramite il Proxy di applicazione](application-proxy.md)
- [Connettori Proxy di applicazione](application-proxy-connectors.md)
- [Installare e registrare un connettore](application-proxy-add-on-premises-application.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Diagramma di flusso per i problemi di applicazioni

Questo diagramma di flusso illustra i passaggi per il debug di alcuni dei problemi più comuni con la connessione all'applicazione. Per informazioni dettagliate su ogni passaggio, vedere la tabella che segue il diagramma di flusso.

![Diagramma di flusso che illustra la procedura per il debug di un'applicazione](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Azione | Descrizione | 
|---------|---------|---------|
|1 | Aprire un browser, accedere all'app e immettere le credenziali | Provare a usare le credenziali per accedere all'app e cercare eventuali errori correlati all'utente, ad esempio [questa app aziendale non è possibile accedervi](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Verificare l'assegnazione di utente all'app | Assicurarsi che l'account utente disponga dell'autorizzazione per accedere all'app dall'interno della rete aziendale e quindi testare l'accesso all'app, seguire i passaggi descritti in [testare l'applicazione](application-proxy-add-on-premises-application.md#test-the-application). Se persistono problemi di accesso, vedere [come risolvere gli errori di accesso](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors).  |
|3 | Aprire un browser e provare ad accedere all'app | Se viene immediatamente visualizzato un errore, verificare che il Proxy di applicazione sia configurato correttamente. Per informazioni dettagliate sui messaggi di errore specifici, vedere [problemi di risolvere i problemi di Proxy dell'applicazione e i messaggi di errore](application-proxy-troubleshoot.md).  |
|4 | Controllare la configurazione di domini personalizzati o risolvere l'errore | Se la pagina non viene affatto visualizzata, assicurarsi che il dominio personalizzato sia configurato correttamente esaminando [utilizzo di domini personalizzati](application-proxy-configure-custom-domain.md).<br></br>Se la pagina non viene caricato e viene visualizzato un messaggio di errore, risolvere l'errore consultando [problemi di risolvere i problemi di Proxy dell'applicazione e i messaggi di errore](application-proxy-troubleshoot.md). <br></br>Se sono necessari più di 20 secondi per un messaggio di errore venga visualizzato, potrebbero esserci problemi di connettività. Andare alla [connettori Proxy di applicazione di eseguire il Debug](application-proxy-debug-connectors.md) risoluzione dei problemi di articolo.  |
|5 | Se i problemi persistono, andare al connettore di debug | Potrebbe esserci un problema di connettività tra il proxy e il connettore o tra il connettore e il back-end. Andare alla [connettori Proxy di applicazione di eseguire il Debug](application-proxy-debug-connectors.md) risoluzione dei problemi di articolo. |
|6 | Pubblicare tutte le risorse, controllare gli strumenti di sviluppo del browser e risolvere i collegamenti | Assicurarsi che il percorso di pubblicazione include tutte le immagini necessarie, gli script e fogli di stile per l'applicazione. Per informazioni dettagliate, vedere [aggiungere un'app da sito locale ad Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Usare gli strumenti di sviluppo (strumenti F12 di Internet Explorer o Microsoft Edge) del browser e controllo per la pubblicazione di problemi, come descritto in [pagina dell'applicazione non viene visualizzata correttamente](application-proxy-page-appearance-broken-problem.md). <br></br>Esaminare le opzioni per la risoluzione di collegamenti interrotti nella [i collegamenti nella pagina non funzionano](application-proxy-page-links-broken-problem.md). |
|7 | Controllo per la latenza di rete | Se il caricamento della pagina lenta, informazioni sui modi per ridurre al minimo la latenza di rete in [considerazioni per ridurre la latenza](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Vedere altre informazioni sulla risoluzione dei problemi | Se i problemi persistono, trovare ulteriori articoli sulla risoluzione dei problemi nel [Proxy di applicazione di risoluzione dei problemi documentazione](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>Passaggi successivi


* [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
* [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
* [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
* [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
