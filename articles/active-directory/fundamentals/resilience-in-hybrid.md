---
title: Crea un'autenticazione ibrida più resiliente in Azure Active Directory
description: Una guida per architetti e amministratori IT sulla creazione di un'infrastruttura ibrida resiliente.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c45b362bc37df71346fc3b635c8ae4a51f62cdc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919583"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Resilienza della compilazione nell'architettura ibrida

L'autenticazione ibrida consente agli utenti di accedere alle risorse basate sul cloud con le proprie identità in locale. Un'infrastruttura ibrida include componenti sia locali che cloud.

* I componenti cloud includono Azure AD, risorse e servizi di Azure, app basate sul cloud dell'organizzazione e applicazioni SaaS.

* I componenti locali includono applicazioni locali, risorse quali database SQL e un provider di identità come Windows Server Active Directory. 

> [!IMPORTANT]
> Quando si pianifica la resilienza nell'infrastruttura ibrida, è fondamentale ridurre al minimo le dipendenze e i singoli punti di errore. 

Microsoft offre tre meccanismi per l'autenticazione ibrida. Le opzioni sono elencate in ordine di resilienza. Se possibile, è consigliabile implementare la sincronizzazione dell'hash delle password.

* La [sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md) (pH) USA Azure ad Connect per sincronizzare l'identità e un hash dell'hash della Password per Azure ad, consentendo agli utenti di accedere alle risorse basate sul cloud con la password master in locale. PH ha dipendenze locali solo per la sincronizzazione e non per l'autenticazione.

* [L'autenticazione pass-through](../hybrid/how-to-connect-pta.md) reindirizza gli utenti a Azure ad per l'accesso. Quindi, il nome utente e la password vengono convalidati in base a Active Directory in locale, tramite un agente distribuito nella rete aziendale. PTA dispone di un footprint locale degli agenti Azure AD PTA che risiedono nei server locali.

* I clienti [federativi](../hybrid/whatis-fed.md) distribuiscono un servizio federativo, ad esempio ad FS, quindi Azure ad convalida l'asserzione SAML prodotta dal servizio federativo. La Federazione ha la massima dipendenza dall'infrastruttura locale e quindi più punti di errore. 

   
È possibile che si stiano utilizzando uno o più di questi metodi all'interno dell'organizzazione. Per altre informazioni, vedere [Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD](../hybrid/choose-ad-authn.md). Questo articolo contiene un albero delle decisioni che può essere utile per decidere la metodologia.

## <a name="password-hash-synchronization"></a>Sincronizzazione dell'hash delle password

L'opzione di autenticazione ibrida più semplice e resiliente per Azure AD è la [sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md) che non ha alcuna dipendenza dell'infrastruttura di identità locale durante l'elaborazione delle richieste di autenticazione. Quando le identità con hash delle password vengono sincronizzate con Azure AD, gli utenti possono eseguire l'autenticazione alle risorse cloud senza dipendenze dai componenti di identità locali. 

![Diagramma dell'architettura di pH](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Se si sceglie questa opzione di autenticazione, non si verificherà alcuna distorsione quando i componenti di identità locali diventano non disponibili. L'interruzione locale può verificarsi per diversi motivi, tra cui errori hardware, interruzioni dell'alimentazione, calamità naturali e attacchi malware. 

### <a name="how-do-i-implement-phs"></a>Ricerca per categorie implementare pH?

Per implementare pH, vedere le risorse seguenti:

* [Implementare la sincronizzazione dell'hash delle password con Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Abilitare la sincronizzazione dell'hash delle password](../hybrid/how-to-connect-password-hash-synchronization.md)

Se i requisiti sono tali che non è possibile usare pH, usare l'autenticazione pass-through.

## <a name="pass-through-authentication"></a>Autenticazione pass-through

L'autenticazione pass-through presenta una dipendenza dagli agenti di autenticazione che risiedono in locale sui server. È presente una connessione permanente o un bus di servizio tra Azure AD e gli agenti PTA locali. Il firewall, i server che ospitano gli agenti di autenticazione e la Active Directory locale di Windows Server (o un altro provider di identità) sono tutti potenziali punti di errore. 

![Diagramma dell'architettura di PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Ricerca per categorie implementare PTA?

Per implementare l'autenticazione pass-through, vedere le risorse seguenti.

* [Funzionamento dell'autenticazione pass-through](../hybrid/how-to-connect-pta-how-it-works.md)

* [Approfondimento di sicurezza sull'autenticazione pass-through](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Installare Azure AD autenticazione pass-through](../hybrid/how-to-connect-pta-quick-start.md)

* Se si utilizza PTA, definire una [topologia a disponibilità elevata](../hybrid/how-to-connect-pta-quick-start.md).

 ## <a name="federation"></a>Federazione

La Federazione implica la creazione di una relazione di trust tra Azure AD e il servizio federativo, che include lo scambio di endpoint, certificati per la firma di token e altri metadati. Quando viene ricevuta una richiesta Azure AD, viene letta la configurazione e l'utente viene reindirizzato agli endpoint configurati. A questo punto, l'utente interagisce con il servizio federativo, che rilascia un'asserzione SAML convalidata da Azure AD. 

Nel diagramma seguente viene illustrata una topologia di un Active Directory Federation Services aziendale (AD FS), una distribuzione che include server di Federazione e proxy applicazione Web ridondanti tra più data center locali. Questa configurazione si basa sui componenti dell'infrastruttura di rete aziendale, ad esempio DNS, bilanciamento carico di rete con funzionalità di affinità geografica, firewall e così via. Tutti i componenti e le connessioni locali sono soggetti a errori. Per ulteriori informazioni, visitare la [documentazione sulla pianificazione della capacità ad FS](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) .

> [!NOTE]
>  La Federazione ha il numero più elevato di dipendenze locali e pertanto i punti di errore più potenziali. Sebbene questo diagramma mostri AD FS, gli altri provider di identità locali sono soggetti a considerazioni di progettazione simili per ottenere disponibilità elevata, scalabilità ed esecuzione del failover.

![Diagramma dell'architettura della Federazione](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Ricerca per categorie implementare la Federazione?

Se si implementa una strategia di autenticazione federata o si desidera renderla più resiliente, vedere le risorse seguenti.

* [Che cos'è l'autenticazione federata](../hybrid/whatis-fed.md)

* [Funzionamento della federazione](../hybrid/how-to-connect-fed-whatis.md)

* [Elenco di compatibilità di federazione di Azure AD](../hybrid/how-to-connect-fed-compatibility.md)

* Segui la [documentazione sulla pianificazione della capacità di ad FS](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [Distribuzione di AD FS in Azure IaaS](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [Abilitare la pH](../hybrid/tutorial-phs-backup.md) insieme alla Federazione

## <a name="next-steps"></a>Passaggi successivi
Risorse di resilienza per amministratori e architetti
 
* [Resilienza della compilazione con la gestione delle credenziali](resilience-in-credentials.md)

* [Resilienza di compilazione con gli Stati dei dispositivi](resilience-with-device-states.md)

* [Resilienza della compilazione usando la valutazione di accesso continuo (CAE)](resilience-with-continuous-access-evaluation.md)

* [Resilienza di compilazione nell'autenticazione utente esterna](resilience-b2b-authentication.md)

* [Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione](resilience-on-premises-access.md)

Risorse di resilienza per gli sviluppatori

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
