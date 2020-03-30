---
title: Eseguire il debug delle applicazioni proxy di applicazione - Azure Active Directory Documenti Microsoft
description: Eseguire il debug dei problemi con le applicazioni proxy di applicazione di Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382065"
---
# <a name="debug-application-proxy-application-issues"></a>Debug dei problemi con l'applicazione Application Proxy 

Questo articolo consente di risolvere i problemi relativi alle applicazioni proxy di applicazione di Azure Active Directory (Azure AD). Se si utilizza il servizio proxy di applicazione per l'accesso remoto a un'applicazione Web locale, ma si verificano problemi di connessione all'applicazione, usare questo diagramma di flusso per eseguire il debug dei problemi dell'applicazione. 

## <a name="before-you-begin"></a>Prima di iniziare

Durante la risoluzione dei problemi relativi al proxy di applicazione, è consigliabile iniziare con i connettori. In primo luogo, seguire il flusso di risoluzione dei problemi in Debug dei problemi del [connettore proxy](application-proxy-debug-connectors.md) di applicazione per assicurarsi che i connettori proxy di applicazione siano configurati correttamente. Se i problemi persistono, tornare a questo articolo per risolvere i problemi relativi all'applicazione.  

Per ulteriori informazioni sul proxy di applicazione, vedere:

- [Accesso remoto alle applicazioni locali tramite il proxy di applicazione](application-proxy.md)
- [Connettori proxy di applicazione](application-proxy-connectors.md)
- [Installare e registrare un connettore](application-proxy-add-on-premises-application.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Diagramma di flusso per i problemi delle applicazioni

Questo diagramma di flusso illustra i passaggi per il debug di alcuni dei problemi più comuni relativi alla connessione all'applicazione. Per informazioni dettagliate su ogni passaggio, vedere la tabella che segue il diagramma di flusso.

![Diagramma di flusso che mostra i passaggi per il debug di un'applicazione](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Azione | Descrizione | 
|---------|---------|---------|
|1 | Aprire un browser, accedere all'app e immettere le credenziali | Prova a usare le tue credenziali per accedere all'app e verifica la presenza di eventuali errori relativi all'utente, ad esempio Non è possibile accedere a [questa app aziendale.](application-proxy-sign-in-bad-gateway-timeout-error.md) |
|2 | Verificare l'assegnazione dell'utente all'app | Assicurarsi che l'account utente disponga dell'autorizzazione per accedere all'app dall'interno della rete aziendale e quindi testare l'accesso all'app seguendo i passaggi descritti in [Testare l'applicazione.](application-proxy-add-on-premises-application.md#test-the-application) Se i problemi di accesso persistono, vedere [Come risolvere gli errori](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)di accesso .  |
|3 | Aprire un browser e provare ad accedere all'app | Se viene visualizzato immediatamente un errore, verificare che il proxy di applicazione sia configurato correttamente. Per informazioni dettagliate su messaggi di errore specifici, vedere [Risoluzione dei problemi](application-proxy-troubleshoot.md)relativi al proxy di applicazione e dei messaggi di errore .  |
|4 | Controllare la configurazione del dominio personalizzato o risolvere l'errore | Se la pagina non viene visualizzata, assicurarsi che il dominio personalizzato sia configurato correttamente esaminando Utilizzo di [domini personalizzati](application-proxy-configure-custom-domain.md).<br></br>Se la pagina non viene caricata e viene visualizzato un messaggio di errore, risolvere l'errore facendo riferimento a [Risoluzione dei problemi](application-proxy-troubleshoot.md)relativi al proxy di applicazione e ai messaggi di errore . <br></br>Se la visualizzazione di un messaggio di errore richiede più di 20 secondi, è possibile che si sia verificato un problema di connettività. Passare all'articolo Eseguire il debug dei [connettori proxy](application-proxy-debug-connectors.md) di applicazione.  |
|5 | Se i problemi persistono, andare al debug del connettore | Potrebbe essersi verificato un problema di connettività tra il proxy e il connettore o tra il connettore e il back-end. Passare all'articolo Eseguire il debug dei [connettori proxy](application-proxy-debug-connectors.md) di applicazione. |
|6 | Pubblicare tutte le risorse, controllare gli strumenti di sviluppo del browser e correggere i collegamenti | Assicurarsi che il percorso di pubblicazione includa tutte le immagini, gli script e i fogli di stile necessari per l'applicazione. Per informazioni [dettagliate, vedere Aggiungere un'app locale ad Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) <br></br>Utilizzare gli strumenti di sviluppo del browser (strumenti F12 in Internet Explorer o Microsoft Edge) e verificare la presenza di problemi di pubblicazione come descritto nella [pagina Applicazione non viene visualizzato correttamente.](application-proxy-page-appearance-broken-problem.md) <br></br>Le opzioni di revisione per la risoluzione dei collegamenti interrotti nei [collegamenti nella pagina non funzionano.](application-proxy-page-links-broken-problem.md) |
|7 | Verificare la latenza di rete | Se la pagina viene caricata lentamente, vedere i modi per ridurre al minimo la latenza di rete in [Considerazioni sulla riduzione della latenza.](application-proxy-network-topology.md#considerations-for-reducing-latency) | 
|8 | Vedere ulteriori informazioni sulla risoluzione dei problemi | Se i problemi persistono, trovare ulteriori articoli sulla risoluzione dei problemi nella [documentazione sulla risoluzione dei problemi](application-proxy-troubleshoot.md)relativi al proxy di applicazione . |

## <a name="next-steps"></a>Passaggi successivi


* [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
* [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
* [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
* [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
