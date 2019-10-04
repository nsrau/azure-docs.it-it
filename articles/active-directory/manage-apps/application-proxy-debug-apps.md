---
title: Eseguire il debug di applicazioni proxy di applicazione-Azure Active Directory | Microsoft Docs
description: Problemi di debug con applicazioni proxy di applicazione Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 06b8edcb0f912bfd35137e197253b20b9459448f
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057754"
---
# <a name="debug-application-proxy-application-issues"></a>Problemi di debug dell'applicazione proxy di applicazione 

Questo articolo consente di risolvere i problemi relativi alle applicazioni proxy di applicazione Azure Active Directory (Azure AD). Se si usa il servizio proxy di applicazione per l'accesso remoto a un'applicazione Web locale, ma si verificano problemi di connessione all'applicazione, usare questo diagramma di flusso per eseguire il debug dei problemi dell'applicazione. 

## <a name="before-you-begin"></a>Prima di iniziare

Quando si risolvono i problemi del proxy di applicazione, è consigliabile iniziare con i connettori. Per prima cosa, seguire il flusso di risoluzione dei [problemi del connettore del proxy di applicazione](application-proxy-debug-connectors.md) per verificare che i connettori proxy dell'applicazione siano configurati correttamente. Se si verificano ancora problemi, tornare a questo articolo per risolvere i problemi relativi all'applicazione.  

Per ulteriori informazioni sul proxy di applicazione, vedere:

- [Accesso remoto alle applicazioni locali tramite il proxy di applicazione](application-proxy.md)
- [Connettori del proxy di applicazione](application-proxy-connectors.md)
- [Installare e registrare un connettore](application-proxy-add-on-premises-application.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Diagramma di flusso per problemi relativi alle applicazioni

Questo diagramma di flusso illustra i passaggi per eseguire il debug di alcuni dei problemi più comuni relativi alla connessione all'applicazione. Per informazioni dettagliate su ogni passaggio, vedere la tabella che segue il diagramma di flusso.

![Diagramma di flusso con i passaggi per il debug di un'applicazione](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Azione | DESCRIZIONE | 
|---------|---------|---------|
|1 | Aprire un browser, accedere all'app e immettere le credenziali | Provare a usare le credenziali per accedere all'app e verificare la presenza di eventuali errori correlati all'utente, ad esempio [non è possibile accedere a questa app aziendale](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Verificare l'assegnazione utente all'app | Assicurarsi che l'account utente disponga dell'autorizzazione per accedere all'app dall'interno della rete aziendale e quindi testare l'accesso all'app seguendo la procedura descritta in [testare l'applicazione](application-proxy-add-on-premises-application.md#test-the-application). Se i problemi di accesso permangono, vedere [come risolvere gli errori di accesso](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Aprire un browser e provare ad accedere all'app | Se viene visualizzato un errore immediatamente, verificare che il proxy di applicazione sia configurato correttamente. Per informazioni dettagliate su messaggi di errore specifici, vedere [risolvere i problemi del proxy dell'applicazione e i messaggi di errore](application-proxy-troubleshoot.md).  |
|4 | Controllare la configurazione del dominio personalizzato o risolvere l'errore | Se la pagina non viene visualizzata, verificare che il dominio personalizzato sia configurato correttamente esaminando l'uso di [domini personalizzati](application-proxy-configure-custom-domain.md).<br></br>Se la pagina non viene caricata e viene visualizzato un messaggio di errore, risolvere l'errore riferendosi per [risolvere i problemi del proxy di applicazione e i messaggi di errore](application-proxy-troubleshoot.md). <br></br>Se sono necessari più di 20 secondi per visualizzare un messaggio di errore, potrebbe verificarsi un problema di connettività. Vedere l'articolo sulla risoluzione dei problemi relativi ai [connettori proxy dell'applicazione di debug](application-proxy-debug-connectors.md) .  |
|5 | Se i problemi permangono, vai al debug del connettore | Potrebbe essersi verificato un problema di connettività tra il proxy e il connettore o tra il connettore e il back-end. Vedere l'articolo sulla risoluzione dei problemi relativi ai [connettori proxy dell'applicazione di debug](application-proxy-debug-connectors.md) . |
|6 | Pubblicare tutte le risorse, controllare gli strumenti di sviluppo del browser e correggere i collegamenti | Verificare che il percorso di pubblicazione includa tutte le immagini, gli script e i fogli di stile necessari per l'applicazione. Per informazioni dettagliate, vedere [aggiungere un'app locale a Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Usare gli strumenti di sviluppo del browser (strumenti F12 in Internet Explorer o Microsoft Edge) e verificare la presenza di problemi di pubblicazione come descritto nella [pagina dell'applicazione non viene visualizzata correttamente](application-proxy-page-appearance-broken-problem.md). <br></br>Esaminare le opzioni per la risoluzione dei collegamenti interrotti nei [collegamenti della pagina non funzionano](application-proxy-page-links-broken-problem.md). |
|7 | Verificare la latenza di rete | Se la pagina viene caricata lentamente, informazioni sui modi per ridurre al minimo la latenza di rete in [considerazioni sulla riduzione della latenza](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Vedere la guida alla risoluzione dei problemi aggiuntiva | Se i problemi permangono, trovare altri articoli sulla risoluzione dei problemi nella [documentazione relativa alla risoluzione dei](application-proxy-page-appearance-broken-problem.md)problemi del proxy di applicazione. |

## <a name="next-steps"></a>Passaggi successivi


* [Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori](application-proxy-connector-groups.md)
* [Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)
* [Risolvere i problemi di errore del proxy di applicazione e del connettore](application-proxy-troubleshoot.md)
* [Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](application-proxy-register-connector-powershell.md)
