---
title: Trovare applicazioni cloud non gestite con Cloud App Discovery in Azure Active Directory | Microsoft Docs
description: Fornisce informazioni sull'individuazione e la gestione delle applicazioni con Cloud App Discovery, quali sono i vantaggi e il relativo funzionamento.
services: active-directory
keywords: cloud app discovery, gestione delle applicazioni
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Trovare applicazioni cloud non gestite con Cloud App Discovery
## <a name="summary"></a>Riepilogo

Cloud App Discovery è una funzionalità dell'edizione Premium di Azure Active Directory che consente di individuare eventuali applicazioni cloud non gestite usate dai dipendenti dell'azienda. Nelle aziende moderne spesso i reparti IT non sono a conoscenza di tutte le applicazioni cloud usate dai membri dell'organizzazione per svolgere il proprio lavoro. L'accesso non autorizzato ai dati aziendali, le possibili perdite di dati e altri rischi di sicurezza rappresentano una preoccupazione per gli amministratori. A causa di questa mancanza di consapevolezza, la creazione di un piano per la gestione dei rischi per la sicurezza potrebbe sembrare complicata.

> [!TIP] 
> Osservare i miglioramenti introdotti in Cloud App Discovery di Azure Active Directory (Azure AD), ottimizzati dall'[integrazione con Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

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

