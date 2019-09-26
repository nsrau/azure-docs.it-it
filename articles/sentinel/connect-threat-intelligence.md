---
title: Connettere i dati di intelligence per le minacce ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di intelligence per le minacce ad Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: c1c374977460de04d2339ba8c93019cf9152dbe3
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262708"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Connettere i dati dai provider di intelligence per le minacce

> [!IMPORTANT]
> Il connettore dati delle piattaforme Intelligence per le minacce in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel consente di importare gli indicatori di minaccia utilizzati dall'organizzazione, che possono migliorare la capacità degli analisti della sicurezza di rilevare e assegnare priorità alle minacce note. Diverse funzionalità di Azure Sentinel diventano disponibili o migliorate:

- **Analytics** include un set di modelli di regole pianificate che è possibile abilitare per generare avvisi ed eventi imprevisti basati sulle corrispondenze degli eventi di log dagli indicatori di minaccia.

- Le **cartelle di lavoro** forniscono informazioni riepilogative sugli indicatori di minaccia importati in Sentinel di Azure e tutti gli avvisi generati dalle regole di analisi corrispondenti agli indicatori di minaccia.

- Le query di **caccia** consentono agli investigatori della sicurezza di usare gli indicatori di minaccia nel contesto di scenari di caccia comuni.

- I **notebook** possono usare gli indicatori di minaccia quando si esaminano le anomalie e si cercano comportamenti dannosi.

È possibile trasmettere gli indicatori di minaccia ad Azure Sentinel usando uno dei prodotti integrati della piattaforma di intelligence per le minacce (TIP) elencati nella sezione successiva o usando l'integrazione diretta con l' [API tiIndicators di sicurezza di Microsoft Graph](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Prodotti integrati della piattaforma di intelligence per le minacce

- [Piattaforma di intelligence per le minacce Open Source MISP](https://www.misp-project.org/)
    
    Per uno script di esempio che fornisce ai client con istanze MISP per eseguire la migrazione degli indicatori di minaccia all'API di sicurezza Microsoft Graph, vedere lo [script MISP to Microsoft Graph Security](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Per istruzioni guidate, vedere [invio di IOC all'API di sicurezza Microsoft Graph usando MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Piattaforma ThreatConnect](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Prerequisiti  

- Azure AD ruolo di amministratore globale o amministratore della sicurezza per concedere le autorizzazioni al prodotto TIP o all'applicazione personalizzata che usa l'integrazione diretta con l'API di sicurezza di Microsoft Graph tiIndicators.

- Autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel per archiviare gli indicatori di minaccia.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Connettere Sentinel di Azure al provider di intelligence per le minacce

1. [Registrare un'applicazione](/graph/auth-v2-service#1-register-your-app) in Azure Active Directory per ottenere un ID applicazione, un segreto dell'applicazione e un ID tenant di Azure Active Directory. Questi valori sono necessari quando si configura il prodotto TIP integrato o l'app che usa l'integrazione diretta con Microsoft Graph Security tiIndicators API.

2. [Configurare le autorizzazioni API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) per l'applicazione registrata: Aggiungere l'autorizzazione dell'applicazione Microsoft Graph **ThreatIndicators. ReadWrite. OwnedBy** all'applicazione registrata.

3. Chiedere al Azure Active Directory amministratore tenant di concedere il consenso dell'amministratore all'applicazione registrata per l'organizzazione. Nel portale di Azure: **Azure Active Directory** > **registrazioni app** ***app*visualizzazionenome>dell'API concedere il consenso dell'amministratore per\<**  >  >  >  ***nome del tenant*.> \<**

4. Configurare il prodotto TIP o l'app che usa l'integrazione diretta con Microsoft Graph Security tiIndicators API per inviare indicatori ad Azure Sentinel specificando quanto segue:
    
    a. Valori per l'ID, il segreto e l'ID tenant dell'applicazione registrata.
    
    b. Per il prodotto di destinazione, specificare Azure Sentinel.
    
    c. Per l'azione, specificare Alert.

5. Nella portale di Azure passare a**connettori dati** **sentinella** > di Azure e quindi selezionare il connettore delle piattaforme di intelligence per le **minacce (anteprima)** .

6. Selezionare **Apri connettore pagina**, quindi **Connetti**.

7. Per visualizzare gli indicatori di minaccia importati in Sentinel di Azure, passare ad **Azure Sentinel-logs** > **SecurityInsights**, quindi espandere **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere il provider di intelligence per le minacce ad Azure Sentinel. Per altre informazioni su Sentinel di Azure, vedere gli articoli seguenti.

- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).