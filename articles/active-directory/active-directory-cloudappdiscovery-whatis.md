---
title: Ricerca di applicazioni cloud non gestite con Cloud App Discovery | Documentazione Microsoft
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
ms.date: 07/05/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 011cdf5f1e1b78832a8e4f18f4eef0f376860c45
ms.contentlocale: it-it
ms.lasthandoff: 12/28/2016


---
# <a name="finding-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Ricerca di applicazioni cloud non gestite con Cloud App Discovery
## <a name="overview"></a>Panoramica
Nelle aziende moderne spesso i reparti IT non sono a conoscenza di tutte le applicazioni cloud usate dai membri dell'organizzazione per svolgere il proprio lavoro. L'accesso non autorizzato ai dati aziendali, le possibili perdite di dati e altri rischi di sicurezza rappresentano una preoccupazione per gli amministratori. A causa di questa mancanza di consapevolezza, la creazione di un piano per la gestione dei rischi per la sicurezza potrebbe sembrare complicata.

Cloud App Discovery è una funzionalità dell'edizione Premium di Azure Active Directory (AD) che consente di individuare le applicazioni cloud usate dai dipendenti dell'azienda.

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

Per iniziare a usare Cloud App Discovery, vedere l' [introduzione a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

## <a name="related-articles"></a>Articoli correlati
* [Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Guida alla distribuzione di criteri di gruppo di Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
* [Guida alla distribuzione di Cloud App Discovery System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
* [Impostazioni del Registro di sistema di Cloud App Discovery per servizi proxy con porte personalizzate](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Log modifiche dell'agente di Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Domande frequenti di Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)


