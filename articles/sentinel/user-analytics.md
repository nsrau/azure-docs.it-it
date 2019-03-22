---
title: Analizzare gli utenti con analitica utente disponibile in anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come analizzare gli utenti con analitica utente in Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246322"
---
# <a name="investigate-users-with-user-analytics"></a>Analizzare gli utenti con analitica utente

> [!IMPORTANT]
> Sentinel Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gli utenti sono entità che si desidera monitorare attentamente. Per aiutarti a ottenere informazioni approfondite agli utenti, Sentinel Azure si integra perfettamente con Azure Advanced Threat Protection. Questa integrazione consente di analizzare il comportamento dell'utente e classificare gli utenti che è necessario esaminare in primo luogo, in base alle avvisi e di modelli di attività sospette in Azure Sentinel e Microsoft 365.

Azure Sentinel arricchisce i dati utente con analitica di Microsoft 365 per abilitare l'analisi analitica e rischio dell'utente completa per tutti gli utenti in Azure Active Directory. 

## <a name="how-it-works"></a>Funzionamento

1. Base di protezione della regole di analisi, quando una corrispondenza viene rilevato, Sentinel Azure invia avvisi ad Azure ATP.
1. Azure ATP controlla quali entità utente sono correlati agli avvisi e calcola la priorità di indagine per tali utenti.
3. Azure ATP quindi di ricalcolare il punteggio degli utenti dopo che sono state aggiunte con dati provenienti da regole analitica per Sentinel di Azure.


## <a name="prerequisites"></a>Prerequisiti

- Una licenza di Azure Advanced Threat Protection 
- Per abilitare la funzionalità, sono necessarie autorizzazioni di amministratore globale del tenant in cui è installato Azure Sentinel

> [!NOTE]
> - Per ogni tenant di Azure ATP, è possibile connettersi un'istanza di Azure Sentinel.
> - Analitica utente è attualmente disponibile solo per gli utenti di Azure Active Directory. 

## <a name="enable-user-analytics"></a>Abilitare analitica utente

1. Per abilitare analitica utente in Azure Sentinel, nel portale passare al **analitica utente** e fare clic su **abilitare**. Invia informazioni sull'area di lavoro ad Azure ATP.

1. Quindi, consente ad Azure ATP. Sotto **estensioni di sicurezza** nel **Sentinel di Microsoft Azure** scheda, fare clic sui **+ plus** per aggiungere e quindi selezionare l'area di lavoro. 
1. Fare clic su **Connetti**.

## <a name="investigate-a-user"></a>Provare a utilizzare un utente

1. Nel menu Azure Sentinel **analitica utente**, esaminare l'elenco di utenti in base alla loro priorità di indagine. Il punteggio è basato su Azure Sentinel avvisi e avvisi di Microsoft 365.

2. Cercare un utente che si desidera analizzare. Fare clic sull'utente per accedere alla pagina dell'utente. Esaminare le attività dell'utente e gli avvisi, nel corso del tempo nella sequenza temporale. È possibile visualizzare tutte le attività di Microsoft 365 e Azure Sentinel. È anche possibile raggiungere la pagina utente eseguire il drill-in di utenti all'interno di un case.
      
    ![Utenti](./media/user-analytics/user-investigation.png)

 
3. Per funzionare correttamente, è necessario impostare correttamente le [regola di avviso personalizzata](tutorial-detect-threats.md) per eseguire il mapping gli identificatori utente più adatto per il **account** entità.

    - Per gli eventi di Windows, eseguire il mapping **conto** per il **SID**
    - I dati di Azure AD (che possono essere trovati in molti log di attività di Azure) o dati di Office 365, eseguire il mapping il **Account** proprietà per il **GUID**, o la **User Principal Name**. 

   ![Query](./media/user-analytics/query-window.png)



Ad esempio:  
> [!NOTE]
> Nei log di attività dell'attività di Azure, all'entità chiamante include il nome UPN.

1. Questa query esegue la ricerca per la creazione di un numero anomalo delle risorse o le attività di distribuzione nel Log attività di Azure.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. Nella regola di avviso personalizzata, eseguire il mapping di **conto** proprietà **chiamante**.
   
   ![Query](./media/user-analytics/query-window.png)

3. Esaminare l'utente nella finestra di analisi dell'utente. Per consigli su come analizzare gli utenti, vedere [esercitazione: Provare a utilizzare un utente](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come connettere il provider di Intelligence sulle minacce per Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti.

- Per iniziare a usare Azure Sentinel, è necessaria una sottoscrizione a Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Informazioni su come [caricare i dati in Azure Sentinel](quickstart-onboard.md), e [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
