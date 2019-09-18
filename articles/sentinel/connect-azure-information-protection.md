---
title: Connessione dei dati Azure Information Protection ad Anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Azure Information Protection dati in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2019
ms.author: cabailey
ms.openlocfilehash: 0614d24b19ef39cebdf4cb47fdd2d44470ea59c0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067751"
---
# <a name="connect-data-from-azure-information-protection"></a>Connettere i dati da Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere le informazioni di registrazione da [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) in Sentinel di Azure configurando il connettore dati Azure Information Protection. Azure Information Protection consente di controllare e proteggere i dati sensibili, che siano archiviati nel cloud o in locale.

Se la [creazione di rapporti centrali per Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) è già configurata in modo che le informazioni di registrazione da questo servizio siano archiviate nella stessa area di lavoro di log Analytics attualmente selezionata per Azure Sentinel, è possibile ignorare la configurazione di Questo connettore di dati. Le informazioni di registrazione di Azure Information Protection sono già disponibili per la funzionalità Sentinel di Azure.

Tuttavia, se le informazioni di registrazione da Azure Information Protection passano a un'area di lavoro Log Analytics diversa da quella attualmente selezionata per Azure Sentinel, effettuare una delle operazioni seguenti:

- Modificare l'area di lavoro selezionata in Sentinel di Azure.

- Modificare l'area di lavoro per Azure Information Protection, che è possibile eseguire configurando questo connettore di dati.
    
    Se si modifica l'area di lavoro, i nuovi dati per la creazione di report per Azure Information Protection verranno ora archiviati nell'area di lavoro in uso per Sentinel di Azure e i dati cronologici non sono disponibili per Sentinel di Azure. Se, inoltre, l'area di lavoro precedente è configurata per query personalizzate, avvisi o API REST, è necessario riconfigurarli per l'area di lavoro di Azure Sentinel se si desidera utilizzarli per Azure Information Protection. Non è necessaria alcuna riconfigurazione per i client e i servizi che usano Azure Information Protection.

## <a name="prerequisites"></a>Prerequisiti

- Uno dei seguenti ruoli di amministratore Azure AD per il tenant: Azure Information Protection amministratore, amministratore della sicurezza o amministratore globale.
    
    > [!NOTE]
    > Non è possibile usare il ruolo di amministratore Azure Information Protection se il tenant si trova nella [piattaforma di etichettatura unificata](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).

- Autorizzazioni per la lettura e la scrittura nell'area di lavoro Log Analytics usata per Sentinel e Azure Information Protection.

- Azure Information Protection è stato aggiunto al portale di Azure. Per informazioni su questo passaggio, vedere [aggiungere Azure Information Protection al portale di Azure](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Connetti a Azure Information Protection

Utilizzare le istruzioni seguenti se non è stata configurata un'area di lavoro di Log Analytics per Azure Information Protection oppure è necessario modificare l'area di lavoro in cui sono archiviate le informazioni di registrazione del Azure Information Protection. 

1. In Sentinel di Azure selezionare **connettori dati**, quindi **Azure Information Protection**.

2. Nel pannello **Azure Information Protection** selezionare **Apri pagina connettore**.

3. Nel pannello successivo, nella sezione **configurazione** selezionare **Azure Information Protection** per passare a **Azure Information Protection Analytics**.

4. Nell'elenco delle aree di lavoro disponibili selezionare l'area di lavoro attualmente in uso per Azure Sentinel. Se si seleziona un'area di lavoro diversa, i dati di report di Azure Information Protection non saranno disponibili per il servizio Sentinel di Azure.

5. Dopo aver selezionato un'area di lavoro, fare clic su **OK** . lo **stato** del connettore ora passa a **connesso**.

6. I dati di report di Azure Information Protection vengono archiviati nella tabella **InformationProtectionLogs_CL** all'interno dell'area di lavoro selezionata. 
    
    Per usare lo schema pertinente in monitoraggio di Azure per i dati di report, cercare **InformationProtectionEvents**. Per informazioni su queste funzioni evento, vedere la sezione [riferimento allo schema descrittivo per le funzioni evento](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) dalla documentazione Azure Information Protection.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Information Protection ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
