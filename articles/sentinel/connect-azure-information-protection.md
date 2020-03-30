---
title: Connettere Azure Information Protection ad Azure SentinelConnect Azure Information Protection to Azure Sentinel
description: Informazioni su come connettere i dati di Azure Information Protection in Azure Sentinel.Learn how to connect Azure Information Protection data in Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588553"
---
# <a name="connect-data-from-azure-information-protection"></a>Connettere dati da Azure Information ProtectionConnect data from Azure Information Protection

> [!IMPORTANT]
> Il connettore dati di Azure Information Protection in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere le informazioni di registrazione da Azure Information Protection in Azure Sentinel configurando il connettore dati di Azure Information Protection.You can stream logging information from [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) into Azure Sentinel by configuring the Azure Information Protection data connector. Azure Information Protection consente di controllare e proteggere i dati sensibili, indipendentemente dal fatto che siano archiviati nel cloud o in locale.

Se i [report centrali per Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) sono già configurati in modo che le informazioni di registrazione di questo servizio vengano archiviate nella stessa area di lavoro di Log Analytics attualmente selezionata per Azure Sentinel, è possibile ignorare la configurazione di questo connettore dati. Le informazioni di registrazione di Azure Information Protection sono già disponibili per Azure Sentinel.The logging information from Azure Information Protection is already available to Azure Sentinel.

Tuttavia, se la registrazione delle informazioni da Azure Information Protection verrà visualizzata in un'area di lavoro di Log Analytics diversa da quella attualmente selezionata per Azure Sentinel, eseguire una delle operazioni seguenti:However, if logging information from Azure Information Protection is going to a different Log Analytics workspace than the you've currently selected for Azure Sentinel, do one of the following:

- Modificare l'area di lavoro selezionata in Azure Sentinel.Change the workspace selected in Azure Sentinel.

- Modificare l'area di lavoro per Azure Information Protection, operazione che è possibile eseguire configurando questo connettore dati.
    
    Se si modifica l'area di lavoro, i nuovi dati di report per Azure Information Protection verranno ora archiviati nell'area di lavoro usata per Azure Sentinel e i dati cronologici non saranno disponibili per Azure Sentinel.If you change the workspace, new reporting data for Azure Information Protection will now be stored in the workspace you're using for Azure Sentinel, and historical data isn't available to Azure Sentinel. Inoltre, se l'area di lavoro precedente è configurata per query personalizzate, avvisi o API REST, è necessario riconfigurarle per l'area di lavoro di Azure Sentinel se si vuole continuare a usarle per Azure Information Protection. Non è necessaria alcuna riconfigurazione per i client e i servizi che usano Azure Information Protection.No reconfiguration is needed for clients and services that use Azure Information Protection.

## <a name="prerequisites"></a>Prerequisiti

- Uno dei ruoli di amministratore di Azure AD seguenti per il tenant: 
    - Amministratore di Azure Information ProtectionAzure Information Protection administrator
    - Amministratore della sicurezza
    - Amministratore di conformità
    - Amministratore dei dati di conformità
    - Amministratore globale
    
    > [!NOTE]
    > Non è possibile usare il ruolo di amministratore di Azure Information Protection se il tenant si trova nella piattaforma di [etichettatura unificata.](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
    
    Questi ruoli di amministratore sono necessari solo per la configurazione del connettore di Azure Information Protection e non sono necessari quando Azure Sentinel è connesso ad Azure Information Protection.These administrator roles are required only for configuring the Azure Information Protection connector, and't required when Azure Sentinel is connected to Azure Information Protection.

- Autorizzazioni per leggere e scrivere nell'area di lavoro Log Analytics usata per Azure Sentinel e Azure Information Protection.

- Azure Information Protection è stato aggiunto al portale di Azure.Azure Information Protection has been added to the Azure portal. Se è necessaria assistenza per questo passaggio, vedere Aggiungere Azure Information Protection al portale di Azure.If you need help with this step, see [Add Azure Information Protection to the Azure portal.](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)

## <a name="connect-to-azure-information-protection"></a>Connettersi ad Azure Information ProtectionConnect to Azure Information Protection

Usare le istruzioni seguenti se non è stata configurata un'area di lavoro di Log Analytics per Azure Information Protection oppure è necessario modificare l'area di lavoro in cui sono archiviate le informazioni di registrazione di Azure Information Protection.Use the following instructions if you haven't configured a Log Analytics workspace for Azure Information Protection, or you need to change the workspace that stores the Azure Information Protection logging information.

1. In Azure Sentinel selezionare **Connettori dati**e quindi **Azure Information Protection (anteprima)**.

2. Selezionare **Apri pagina connettore**.

3. Nel pannello Configura analisi (anteprima) selezionare l'area di lavoro attualmente usata per Azure Sentinel.On the **Configure analytics (Preview)** blade, select the workspace that you're currently using for Azure Sentinel. Se si seleziona un'area di lavoro diversa, i dati di report da Azure Information Protection non saranno disponibili per Azure Sentinel.If you select a different workspace, the reporting data from Azure Information Protection won't be available to Azure Sentinel.

4. Dopo aver selezionato un'area di lavoro, selezionare **OK** e lo **stato** del connettore dovrebbe ora passare a **Connesso**.

5. I dati dei report di Azure Information Protection vengono archiviati nella tabella **InformationProtectionLogs_CL** all'interno dell'area di lavoro selezionata. 
    
    Per usare lo schema pertinente in Monitoraggio di Azure per i dati dei report, cercare **InformationProtectionEvents**. Per informazioni su queste funzioni di evento, vedere la sezione Informazioni di [riferimento sugli schemi descrittivi per le funzioni di evento](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) nella documentazione di Azure Information Protection.For information about these event functions, see the Friendly schema reference for event functions section from the Azure Information Protection documentation.

## <a name="next-steps"></a>Passaggi successivi

In this document, you learned how to connect Azure Information Protection to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
