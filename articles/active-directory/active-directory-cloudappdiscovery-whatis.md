---
title: Trovare applicazioni cloud non gestite con Cloud App Discovery in Azure Active Directory | Microsoft Docs
description: Fornisce informazioni sull'individuazione e la gestione delle applicazioni con Cloud App Discovery, quali sono i vantaggi e il relativo funzionamento.
services: active-directory
keywords: cloud app discovery, gestione delle applicazioni
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Trovare applicazioni cloud non gestite con Cloud App Discovery
## <a name="summary"></a>Summary

Cloud App Discovery di Azure Active Directory offre ora un'esperienza di individuazione avanzata senza agenti con tecnologia Microsoft Cloud App Security. Per usare Cloud App Discovery è sufficiente accedere con le credenziali di Azure AD Premium P1. Questo aggiornamento è disponibile senza costi aggiuntivi per tutti i clienti di Azure AD Premium P1. Iniziare con l'articolo [Configurare Cloud App Discovery in Azure AD](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started), quindi provare [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/).

> [!IMPORTANT] 
> L'esperienza attuale di Azure AD Cloud App Discovery con l'individuazione basata su agente sarà disattivata il 5 marzo 2018 e dopo questa data gli agenti saranno disabilitati e i dati eliminati. Si consiglia di agire prima del 5 marzo in modo da avere il tempo di raggiungere la necessaria efficienza con la nuova esperienza ed evitare interruzioni del servizio.  
 
**Cloud App Discovery consente di:**

* Individuare le applicazioni cloud in uso e misurarne l'utilizzo in base al numero di utenti, al volume di traffico o al numero di richieste Web all'applicazione.
* Identificare gli utenti che usano un'applicazione.
* Esportare i dati per analisi offline.
* Portare le applicazioni sotto il controllo IT e abilitare la funzionalità Single Sign-On per la gestione degli utenti.

## <a name="how-it-works"></a>Funzionamento
1. Gli agenti di utilizzo dell'applicazione vengono installati nei computer dell'utente.
2. Le informazioni sull'utilizzo delle applicazioni acquisite dagli agenti vengono inviate tramite un canale crittografato sicuro al servizio Cloud App Discovery.
3. Il servizio Cloud App Discovery valuta i dati e genera report.

![Diagramma di Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Passaggi successivi
* [Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Impostazioni del Registro di sistema di Cloud App Discovery per servizi proxy con porte personalizzate](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Log modifiche dell'agente di Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

