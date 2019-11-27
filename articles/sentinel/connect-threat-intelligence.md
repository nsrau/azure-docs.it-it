---
title: Connettere i dati di intelligence per le minacce ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di intelligence per le minacce ad Azure Sentinel.
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: cabailey
ms.openlocfilehash: 33edeb04e88a01efafaf69b850ed87120671ed11
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384127"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Connettere i dati dai provider di intelligence per le minacce

> [!IMPORTANT]
> I connettori dati di intelligence per le minacce in Sentinel di Azure sono attualmente disponibili in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel consente di importare gli indicatori di minaccia utilizzati dall'organizzazione, in modo da migliorare la capacità degli analisti della sicurezza di rilevare e classificare in ordine di priorità le minacce note. Diverse funzionalità di Azure Sentinel diventano disponibili o migliorate:

- **Analytics** include un set di modelli di regole pianificate che è possibile abilitare per generare avvisi ed eventi imprevisti in base alle corrispondenze degli eventi di log dagli indicatori di minaccia.

- Le **cartelle di lavoro** forniscono informazioni riepilogative sugli indicatori di minaccia importati in Sentinel di Azure e tutti gli avvisi generati dalle regole di analisi corrispondenti agli indicatori di minaccia.

- Le query di **caccia** consentono agli investigatori della sicurezza di usare gli indicatori di minaccia nel contesto di scenari di caccia comuni.

- I **notebook** possono usare gli indicatori di minaccia quando si esaminano le anomalie e si cercano comportamenti dannosi.

È possibile trasmettere gli indicatori di minaccia ad Azure Sentinel usando uno dei prodotti integrati della piattaforma di intelligence per le minacce (TIP) elencati nella sezione successiva, connessione ai server TAXI o usando l'integrazione diretta con l' [API tiIndicators di sicurezza di Microsoft Graph](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Prodotti integrati della piattaforma di intelligence per le minacce

- [Piattaforma di intelligence per le minacce Open Source MISP](https://www.misp-project.org/)
    
    Per uno script di esempio che fornisce ai client con istanze MISP per eseguire la migrazione degli indicatori di minaccia all'API di sicurezza Microsoft Graph, vedere lo [script MISP to Microsoft Graph Security](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Per istruzioni guidate, vedere [invio di IOC all'API di sicurezza Microsoft Graph usando MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Piattaforma ThreatConnect](https://threatconnect.com/solution/)

    Per informazioni, vedere [integrazione di ThreatConnect](https://threatconnect.com/integrations/) e cercare Microsoft Graph API di sicurezza nella pagina.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Connettere Azure Sentinel alla piattaforma di intelligence per le minacce

## <a name="prerequisites"></a>prerequisiti  

- Azure AD ruolo di amministratore globale o amministratore della sicurezza per concedere le autorizzazioni al prodotto TIP o all'applicazione personalizzata che usa l'integrazione diretta con l'API di sicurezza di Microsoft Graph tiIndicators.

- Autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel per archiviare gli indicatori di minaccia.

## <a name="instructions"></a>Istruzioni

1. [Registrare un'applicazione](/graph/auth-v2-service#1-register-your-app) in Azure Active Directory per ottenere un ID applicazione, un segreto dell'applicazione e un ID tenant di Azure Active Directory. Questi valori sono necessari quando si configura il prodotto TIP integrato o l'app che usa l'integrazione diretta con Microsoft Graph Security tiIndicators API.

2. [Configurare le autorizzazioni dell'API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) per l'applicazione registrata: aggiungere l'autorizzazione dell'applicazione Microsoft Graph **ThreatIndicators. ReadWrite. OwnedBy** all'applicazione registrata.

3. Chiedere al Azure Active Directory amministratore tenant di concedere il consenso dell'amministratore all'applicazione registrata per l'organizzazione. Dal portale di Azure: **Azure Active Directory** > **registrazioni app** > \< **_nome dell'app_** >le **autorizzazioni > visualizzazione API** > **concedere il consenso dell'amministratore per \<nome del _tenant_** >.

4. Configurare il prodotto TIP o l'app che usa l'integrazione diretta con Microsoft Graph Security tiIndicators API per inviare indicatori ad Azure Sentinel specificando quanto segue:
    
    a. Valori per l'ID, il segreto e l'ID tenant dell'applicazione registrata.
    
    b. Per il prodotto di destinazione, specificare Azure Sentinel.
    
    C. Per l'azione, specificare Alert.

5. Nel portale di Azure passare ad **Azure Sentinel** > **connettori dati** , quindi selezionare il connettore per le piattaforme di intelligence per le **minacce (anteprima)** .

6. Selezionare **Apri connettore pagina**, quindi **Connetti**.

7. Per visualizzare gli indicatori di minaccia importati in Sentinel di Azure, passare ad **Azure Sentinel-Logs** > **SecurityInsights**, quindi espandere **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Connettere Azure Sentinel ai server TAXI

## <a name="prerequisites"></a>prerequisiti  

- Autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel per archiviare gli indicatori di minaccia.

- ID di raccolta e URI del server TAXIi 2,0.

## <a name="instructions"></a>Istruzioni

1. Nel portale di Azure passare ad **Azure Sentinel** > **connettori dati** e quindi selezionare il connettore Intelligence per le **minacce-Taxii (anteprima)** .

2. Selezionare **Apri connettore pagina**.

3. Specificare le informazioni obbligatorie e facoltative nelle caselle di testo.

4. Selezionare **Aggiungi** per abilitare la connessione al server taxii 2,0.

5. Se sono presenti altri server TAXIi 2,0: ripetere i passaggi 3 e 4.

6. Per visualizzare gli indicatori di minaccia importati in Sentinel di Azure, passare ad **Azure Sentinel-Logs** > **SecurityInsights**, quindi espandere **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere il provider di intelligence per le minacce ad Azure Sentinel. Per altre informazioni su Sentinel di Azure, vedere gli articoli seguenti.

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
