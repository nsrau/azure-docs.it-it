---
title: Visibilità e controllo delle app con Microsoft Cloud App Security
description: Questa guida di avvio rapido illustra come identificare i livelli di rischio delle app, impedire violazioni e perdite in tempo reale e usare i connettori delle app per sfruttare le API del provider per la visibilità e la governance.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e3e0c10a7ec6b42420db30955ae4911bca27cc0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651227"
---
# <a name="cloud-app-visibility-and-control"></a>Visibilità e controllo delle app cloud

Per sfruttare tutti i vantaggi offerti dalle app e dai servizi cloud, un team IT deve trovare il giusto equilibrio tra il supporto dell'accesso e il mantenimento del controllo per proteggere i dati critici. Microsoft Cloud App Security offre ampia visibilità, controllo sul transito dei dati e funzionalità di analisi sofisticate per identificare e contrastare le minacce informatiche in tutti i servizi cloud Microsoft e di terze parti.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Individuare e gestire shadow IT nella rete

Se si chiede agli amministratori IT quante app cloud pensano che i dipendenti usino, in media rispondono 30 o 40, quando in realtà la media è superiore a 1.000 app separate usate dai dipendenti dell'organizzazione. Shadow IT aiuta a conoscere e identificare le app in uso e il livello di rischio associato. L'80% dei dipendenti usa app non approvate che non sono state esaminate e che potrebbero non essere conformi ai criteri di sicurezza e conformità. Dal momento che i dipendenti possono accedere alle risorse e alle app dall'esterno della rete aziendale, non è neanche più sufficiente stabilire regole e criteri nei firewall.

È possibile usare Microsoft Cloud App Discovery (una funzionalità di Azure Active Directory Premium P1) per individuare le app in uso, esplorare il rischio a esse associato, configurare i criteri per identificare nuove app rischiose e annullare l'approvazione di queste app per bloccarle in modo nativo con il dispositivo proxy o firewall.

- Individuare e identificare Shadow IT
- Valutare e analizzare
- Gestire le app
- Report di individuazione Shadow IT avanzati
- Controllare le app approvate
 
### <a name="learn-more"></a>Altre informazioni

- [Individuare e gestire shadow IT nella rete](/cloud-app-security/tutorial-shadow-it)
- [App individuate con Cloud App Security](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Visibilità e controllo della sessione utente 

Nelle aree di lavoro odierne, spesso non è sufficiente sapere cosa accade nell'ambiente cloud a cose fatte. Si vogliono impedire violazioni e perdite in tempo reale, prima che i dipendenti mettano intenzionalmente o inavvertitamente i dati e l'organizzazione a rischio. In combinazione con Azure Active Directory (Azure AD), Microsoft Cloud App Security offre queste funzionalità in un'esperienza olistica e integrata con Controllo app per l'accesso condizionale. 

Il controllo della sessione usa un'architettura di proxy inverso ed è integrato in modo univoco con Accesso condizionale di Azure AD. Accesso condizionale di Azure AD consente di applicare i controlli di accesso sulle app dell'organizzazione in base a determinate condizioni. Le condizioni definiscono chi (utente o gruppo di utenti), cosa (quali app cloud) e dove (a quali posizioni e reti) viene applicato un criterio di accesso condizionale. Dopo aver determinato le condizioni, è possibile indirizzare gli utenti a Cloud App Security in cui è possibile proteggere i dati in tempo reale.  

Con questo controllo è possibile:  
- Controllare i download di file
- Monitorare gli scenari B2B  
- Controllare l'accesso ai file  
- Proteggere i documenti al download  
 
### <a name="learn-more"></a>Altre informazioni

- [Proteggere le app con il controllo sessioni in Cloud App Security](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Controlli e visibilità avanzati delle app 

I connettori di app usano i vantaggi offerti dalle API dei provider di app per consentire a Microsoft Cloud App Security maggiore visibilità e controllo delle app a cui si esegue la connessione. Cloud App Security sfrutta le API fornite dal provider di servizi cloud. Ogni servizio ha un proprio framework e proprie limitazioni dell'API, ad esempio la limitazione della larghezza di banda della rete, i limiti delle API, le finestre API dinamiche di spostamento temporale e altre ancora. Il team del prodotto Cloud App Security ha usato questi servizi per ottimizzare l'uso delle API e garantire prestazioni ottimali. Prendendo in considerazione le diverse limitazioni imposte dai servizi sulle API, i motori Cloud App Security usano la capacità massima consentita. Alcune operazioni, ad esempio l'analisi di tutti i file nel tenant, richiedono numerose chiamate API in modo da essere distribuite in un periodo più lungo. Si prevede che alcuni criteri vengano eseguiti per diverse ore o giorni. 
 
### <a name="learn-more"></a>Altre informazioni  

- [Connettere le app in Cloud App Security](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Passaggi successivi

- [Individuare e gestire shadow IT nella rete](/cloud-app-security/tutorial-shadow-it)
- [App individuate con Cloud App Security](/cloud-app-security/discovered-apps)
- [Proteggere le app con il controllo sessioni in Cloud App Security](/cloud-app-security/proxy-intro-aad)
- [Connettere le app in Cloud App Security](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)