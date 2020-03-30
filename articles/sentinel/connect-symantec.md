---
title: Connettere i dati ICDx Symantec in Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati ICDx Symantec ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588094"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Collegare l'appliance Symantec ICDx 



Il connettore Symantec ICDx consente di connettere facilmente tutti i log delle soluzioni di sicurezza Symantec con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Ciò offre maggiori informazioni sulla rete dell'organizzazione e migliora le funzionalità di gestione della sicurezza. L'integrazione tra Symantec ICDx e Azure Sentinel utilizza l'API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configurare e collegare Symantec ICDx 

Symantec ICDx può integrare ed esportare i log direttamente in Azure Sentinel.

1. Aprire ICDx Management Console per aggiungere server d'inoltro di Microsoft Azure Sentinel (Log Analytics).
2. Sulla barra di spostamento ICDx fare clic su **Configurazione**. 
3. Nella parte superiore della schermata **Configurazione** fare clic su **Inoltratori**.
4. In **Server d'inoltro**, accanto a Sentinel di Microsoft Azure (Log Analytics), fare clic su **Aggiungi**. 
4. Nella finestra **Sentinel di Microsoft Azure (Log Analytics)** fare clic su **Mostra avanzate**. 
5. Nella parte superiore della finestra espansa in Microsoft Azure Sentinel (Log Analytics) eseguire le operazioni seguenti:
    -   **Nome**: Digitare un nome per il server d'inoltro con non più di 30 caratteri. Scegliere un nome univoco e significativo. Questo nome viene visualizzato nell'elenco dei server d'inoltro nella schermata **Configurazione** e nei dashboard nella **schermata Dashboard.** Ad esempio: Microsoft Azure Log Analytics East.For example: Microsoft Azure Log Analytics East. Questo campo è obbligatorio.
    -   **Descrizione**: Digitare una descrizione per il server d'inoltro. Questa descrizione viene visualizzata anche nell'elenco dei server d'inoltro nella schermata **Configurazione.** Includere dettagli quali il tipo di evento inoltrato e il gruppo che deve controllare i dati.
    -   **Tipo di avvio**: Selezionare il metodo di avvio per la configurazione del server d'inoltro. Le opzioni sono manuali e automatiche.<br>Il valore predefinito è Automatico. 
6. In **Eventi**eseguire le operazioni seguenti: 
    - **Origine**: Selezionare uno o più archivi da cui inoltrare gli eventi. È possibile selezionare gli archivi di raccoglitori attivi (incluso l'archivio comune), gli archivi dei raccoglitori orfani (ovvero gli archivi per gli agenti di raccolta eliminati), gli archivi dei ricevitori ICDx o l'archivio di sistema. <br>L'impostazione predefinita è Archivio comune.
      > [!NOTE]
      > Gli archivi dei ricevitori ICDx sono elencati separatamente, per nome. 
 
    - **Filtro**: Aggiunge un filtro che specifichi il sottoinsieme di eventi da inoltrare. Eseguire una delle operazioni seguenti:
        - Per selezionare una condizione di filtro, fare clic su Tipo, Attributo, Operatore e Valore. 
        - Nel campo Filtro esaminare la condizione di filtro. È possibile modificarlo direttamente nel campo o eliminarlo in base alle esigenze.
        - Fare clic su AND o OR per aggiungere alla condizione di filtro.
        - È inoltre possibile fare clic su Query salvate per applicare una query salvata.
    - **Attributi inclusi**: digitare l'elenco delimitato da virgole di attributi da includere nei dati inoltrati. Gli attributi inclusi hanno la precedenza sugli attributi esclusi.
    - **Attributi esclusi**: digitare l'elenco delimitato da virgole di attributi da escludere dai dati inoltrati.
    - **Dimensione batch**: Selezionare il numero di eventi da inviare per batch. Le opzioni disponibili sono 10, 50, 100, 500 e 1000.<br>Il valore predefinito è 100. 
    - **Limite di velocità**: Selezionare la frequenza con cui gli eventi vengono inoltrati, espressa come eventi al secondo. Le opzioni disponibili sono Unlimited, 500, 1000, 5000, 10000. <br> Il valore predefinito è 5000. 
7. In Destinazione Azure eseguire le operazioni seguenti:Under **Azure Destination**, do the following: 
    - **ID area di lavoro**: incollare l'ID dell'area di lavoro dal basso. Questo campo è obbligatorio.
    - **Chiave primaria**: Incollare la chiave primaria dal basso. Questo campo è obbligatorio.
    - **Nome log personalizzato:** digitare il nome del log personalizzato nell'area di lavoro Log Analytics del portale di Microsoft Azure a cui si intende inoltrare gli eventi. Il valore predefinito è SymantecICDx. Questo campo è obbligatorio.
8. Fare clic su *Salva* per completare la configurazione del server d'inoltro. 
9. Per avviare il server d'inoltro, in **Opzioni**fare clic su **Altro** e quindi **su Avvia**.
10. Per utilizzare lo schema pertinente in Log Analytics per gli eventi ICDx Symantec, cercare **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere voluti fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Symantec ICDx to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


