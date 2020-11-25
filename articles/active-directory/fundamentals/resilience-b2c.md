---
title: Resilienza della compilazione nella gestione delle identità e dell'accesso dei clienti usando Azure AD B2C | Microsoft Docs
description: Metodi per compilare la resilienza nella gestione delle identità e degli accessi dei clienti usando Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba7b8ff2555fd7014cf16f66745721c6425ff868
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029524"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Resilienza della compilazione nella gestione delle identità e dell'accesso dei clienti con Azure Active Directory B2C

[Azure Active Directory (ad) B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview) è una piattaforma di gestione delle identità e degli accessi (CIAM) del cliente progettata per facilitare l'avvio corretto delle applicazioni critiche per i clienti. Sono disponibili molte funzionalità predefinite per la [resilienza](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) progettate per consentire il ridimensionamento del servizio in base alle esigenze e migliorare la resilienza in caso di potenziali interruzioni. Inoltre, quando si avvia un'applicazione mission-critical, è importante prendere in considerazione vari elementi di progettazione e configurazione dell'applicazione, nonché il modo in cui l'applicazione viene configurata all'interno Azure AD B2C per assicurarsi di ottenere un comportamento resiliente in risposta a scenari di interruzione o di errore. In questo articolo verranno illustrate alcune procedure consigliate che consentono di aumentare la resilienza.

Un servizio resiliente è un servizio che continua a funzionare nonostante le interruzioni. Per contribuire a migliorare la resilienza nel servizio, è possibile:

- informazioni su tutti i componenti

- eliminazione di singoli punti di errore

- isolamento dei componenti non riusciti per limitare l'effetto

- fornire ridondanza con meccanismi di failover rapidi e percorsi di recupero

Quando si sviluppa l'applicazione, è consigliabile considerare come [aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni](resilience-app-development-overview.md) con i componenti di identità della soluzione. Questo articolo tenta di risolvere i miglioramenti per la resilienza specifici per applicazioni Azure AD B2C. Le raccomandazioni sono raggruppate in base alle funzioni di CIAM.

![Image Mostra i componenti ](media/resilience-b2c/high-level-components.png) di CIAM nelle sezioni successive, ti guideremo a compilare la resilienza nelle aree seguenti:

- [Esperienza dell'utente finale](resilient-end-user-experience.md): abilitare un piano di fallback per il flusso di autenticazione e mitigare il potenziale impatto causato da un'interruzione del servizio di autenticazione Azure ad B2C.

- [Interfacce con processi esterni](resilient-external-processes.md): resilienza di compilazione nelle applicazioni e nelle interfacce tramite il recupero da errori.  

- [Procedure consigliate](resilience-b2c-developer-best-practices.md)per gli sviluppatori: evitare la fragilità a causa di problemi comuni dei criteri personalizzati e migliorare la gestione degli errori nelle aree come le interazioni con i verificatori di attestazioni, le applicazioni di terze parti e le API REST.

- [Monitoraggio e analisi](resilience-with-monitoring-alerting.md): valutare l'integrità del servizio monitorando gli indicatori chiave e rilevare gli errori e le interruzioni delle prestazioni tramite gli avvisi.

- [Resilienza di compilazione nell'infrastruttura di autenticazione](resilience-in-infrastructure.md)

- [Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni](resilience-app-development-overview.md)
