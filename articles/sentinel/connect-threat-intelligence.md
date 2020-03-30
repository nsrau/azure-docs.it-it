---
title: Connettere i dati di intelligence sulle minacce ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di intelligence sulle minacce ad Azure Sentinel.Learn about how to connect threat intelligence data to Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588043"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Connetti i dati dei fornitori di informazioni sulle minacce

> [!IMPORTANT]
> I connettori dati di Threat Intelligence in Azure Sentinel sono attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel consente di importare gli indicatori di minaccia utilizzati dall'organizzazione, in modo da migliorare la capacità degli analisti della sicurezza di rilevare e assegnare priorità alle minacce note. Diverse funzionalità di Azure Sentinel diventano quindi disponibili o sono state migliorate:Several features from Azure Sentinel then become available or are enhanced:

- **Analytics** include un set di modelli di regole pianificati che è possibile abilitare per generare avvisi e eventi imprevisti in base alle corrispondenze degli eventi di log dagli indicatori di minaccia.

- **Le cartelle di lavoro** forniscono informazioni riepilogate sugli indicatori di minaccia importati in Azure Sentinel e sugli avvisi generati dalle regole di analisi che corrispondono agli indicatori di minaccia.

- **Le** query di caccia consentono agli investigatori di sicurezza di utilizzare indicatori di minaccia nel contesto di scenari di caccia comuni.

- **I notebook** possono utilizzare gli indicatori di minaccia quando si analizzano le anomalie e si cercano comportamenti dannosi.

È possibile trasmettere gli indicatori di minaccia ad Azure Sentinel usando uno dei prodotti DELLA piattaforma DI threat intelligence (TIP) integrata elencati nella sezione successiva, la connessione ai server TAXII o l'integrazione diretta con [l'API Microsoft Graph Security tiIndicators](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Prodotti integrati della piattaforma di intelligence sulle minacce

- [Piattaforma MISP Open Source Threat Intelligence](https://www.misp-project.org/)
    
    Per uno script di esempio che fornisce ai client istanze MISP per eseguire la migrazione degli indicatori di minaccia all'API Microsoft Graph Security, vedere [MISP to Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Miniera Palo Alto Networks](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Per istruzioni guidate, vedere [Invio di prodotti IOC all'API Microsoft Graph Security con MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Piattaforma ThreatConnect](https://threatconnect.com/solution/)

    Per informazioni, vedere [ThreatConnect Integrations](https://threatconnect.com/integrations/) e cercare Microsoft Graph Security API nella pagina.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Connettere Azure Sentinel alla piattaforma di intelligence sulle minacce

## <a name="prerequisites"></a>Prerequisiti  

- Azure AD role of either Global administrator or Security administrator to grant permissions to your TIP product or custom application that uses direct integration with the Microsoft Graph Security tiIndicators API.

- Autorizzazioni di lettura e scrittura nell'area di lavoro di Azure Sentinel per archiviare gli indicatori delle minacce.

## <a name="instructions"></a>Istruzioni

1. [Registrare un'applicazione](/graph/auth-v2-service#1-register-your-app) in Azure Active Directory per ottenere un ID applicazione, un segreto dell'applicazione e un ID tenant di Azure Active Directory.Register an application in Azure Active Directory to get an application ID, application secret, and Azure Active Directory tenant ID. Questi valori sono necessari quando si configura il prodotto o l'app TIP integrata che utilizza l'integrazione diretta con microsoft Graph Security tiIndicators API.

2. [Configurare le autorizzazioni API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) per l'applicazione registrata: aggiungere l'autorizzazione dell'applicazione Microsoft Graph **ThreatIndicators.ReadWrite.OwnedBy** all'applicazione registrata.

3. Chiedere all'amministratore tenant di Azure Active Directory di concedere il consenso dell'amministratore all'applicazione registrata per l'organizzazione. Dal portale di Azure: Azure **Active Directory** > **App Registrations** > **\<_nome_>** > app**View API Permissions** > **Concedere il consenso di amministratore per \< _il nome_>del tenant**.

4. Configurare il prodotto o l'app TIP che usa l'integrazione diretta con l'API Microsoft Graph Security tiIndicators per inviare indicatori ad Azure Sentinel specificando quanto segue:
    
    a. Valori per l'ID, il segreto e l'ID tenant dell'applicazione registrata.
    
    b. Per il prodotto di destinazione, specificare Azure Sentinel.For the target product, specify Azure Sentinel.
    
    c. Per l'azione, specificare alert.

5. Nel portale di Azure passare a**Connettori dati** **di Azure Sentinel** > e quindi selezionare il connettore **Threat Intelligence Platforms (Anteprima).**

6. Selezionare **Apri pagina connettore**, quindi **Connetti**.

7. Per visualizzare gli indicatori di minaccia importati in Azure Sentinel, passare ad **Azure Sentinel - Logs** > **SecurityInsights**, quindi espandere **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Connettere Azure Sentinel ai server TAXIIConnect Azure Sentinel to TAXII servers

## <a name="prerequisites"></a>Prerequisiti  

- Autorizzazioni di lettura e scrittura nell'area di lavoro di Azure Sentinel per archiviare gli indicatori delle minacce.

- URI del server TAXII 2.0 e ID raccolta.

## <a name="instructions"></a>Istruzioni

1. Nel portale di Azure passare a**Connettori dati** **di Azure Sentinel** > e quindi selezionare il connettore **Threat Intelligence - TAXII (anteprima).**

2. Selezionare **Apri pagina connettore**.

3. Specificare le informazioni obbligatorie e facoltative nelle caselle di testo.

4. Selezionare **Aggiungi** per abilitare la connessione al server TAXII 2.0.

5. Se si dispone di server TAXII 2.0 aggiuntivi: ripetere i passaggi 3 e 4.

6. Per visualizzare gli indicatori di minaccia importati in Azure Sentinel, passare ad **Azure Sentinel - Logs** > **SecurityInsights**, quindi espandere **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Passaggi successivi

In this document, you learned how to connect your threat intelligence provider to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti.

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
