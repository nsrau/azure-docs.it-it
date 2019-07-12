---
title: Connetti i dati di Symantec ICDx all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere dati Symantec ICDx Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673743"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Connettersi all'appliance Symantec ICDx 

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDx connector consente di connettere facilmente tutti i log della soluzione sicurezza Symantec con la Sentinel di Azure, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questo ti offre informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. L'integrazione tra Symantec ICDx e Azure Sentinel vengono utilizzate le API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui in esecuzione Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configurare e connettersi Symantec ICDx 

Symantec ICDx può integrare ed esportare i log direttamente da Azure Sentinel.

1. Aprire la Console di gestione ICDx per aggiungere i server d'inoltro con Microsoft Azure Sentinel (Log Analitica).
2. Nella barra di spostamento ICDx, fare clic su **configurazione**. 
3. In cima il **Configuration** schermata, fare clic su **server d'inoltro**.
4. Sotto **i server d'inoltro**, accanto a Microsoft Azure Sentinel (Log Analitica), fare clic su **Add**. 
4. Nel **Microsoft Azure (Log Analitica) di Sentinel** finestra, fare clic su **Mostra avanzate**. 
5. Nella parte superiore di espansa della finestra di Microsoft Azure Sentinel (Log Analitica), eseguire le operazioni seguenti:
    -   **Nome**: Digitare un nome per il server d'inoltro con non più di 30 caratteri. Scegliere un nome univoco e significativo. Questo nome viene visualizzato nell'elenco dei server d'inoltro nel **Configuration** dello schermo e nei dashboard nel **Dashboard** dello schermo. Ad esempio:  Microsoft Azure Log Analitica orientale. Questo campo è obbligatorio.
    -   **Descrizione**: Digitare una descrizione per il server d'inoltro. Questa descrizione viene visualizzata anche nell'elenco dei server d'inoltro sul **configurazione** dello schermo. Include dettagli quali il tipo di eventi inoltrati e il gruppo che è necessario esaminare i dati.
    -   **Tipo di avvio**: Selezionare il metodo di avvio per la configurazione del server d'inoltro. Le opzioni disponibili sono manuali e automatici.<br>Il valore predefinito è automatico. 
6. Sotto **eventi**, eseguire le operazioni seguenti: 
    - **Origine**: Selezionare uno o più archivi da cui inoltrare gli eventi. È possibile selezionare archivi dell'agente di raccolta attivo, inclusi l'archivio comune, isolati (orfani) gli archivi dell'agente di raccolta (vale a dire, gli archivi per gli agenti di raccolta che sono stati eliminati), gli archivi ricevitore ICDx o l'archivio di sistema. <br>Il valore predefinito è un archivio comune.
      > [!NOTE]
      > Gli archivi ricevitore ICDx vengono elencati separatamente, in base al nome. 
 
    - **Filtro**: Aggiungere un filtro che specifica il subset di eventi da inoltrare. Eseguire una di queste operazioni:
        - Per selezionare una condizione di filtro, fare clic su un tipo, attributo, operatore e valore. 
        - Nel campo del filtro, esaminare la condizione di filtro. È possibile modificarlo direttamente nel campo o eliminarlo in base alle esigenze.
        - Fare clic su e o o da aggiungere alla condizione di filtro.
        - È anche possibile fare clic su query salvate per applicare una query salvata.
    - **Inclusi gli attributi**: Digitare l'elenco delimitato da virgole di attributi da includere nei dati inoltrati. Gli attributi inclusi hanno la precedenza sugli attributi esclusi.
    - **Attributi esclusi**: Digitare l'elenco delimitato da virgole di attributi da escludere dai dati inoltrati.
    - **Dimensioni batch**: Selezionare il numero di eventi da inviare al batch. Le opzioni sono 10, 50, 100, 500 e 1000.<br>Il valore predefinito è 100. 
    - **Limite di frequenza**: Selezionare la frequenza con cui vengono inoltrati gli eventi, espressa in eventi al secondo. Le opzioni disponibili sono illimitate, 500, 1000, 10000, 5000. <br> Il valore predefinito è 5000. 
7. Sotto **destinazione Azure**, eseguire le operazioni seguenti: 
    - **ID area di lavoro**: Incollare l'ID dell'area di lavoro dal codice seguente. Questo campo è obbligatorio.
    - **Chiave primaria**: Incollare la chiave primaria dal codice seguente. Questo campo è obbligatorio.
    - **Nome Log personalizzato**: Digitare il nome log personalizzato in Microsoft Azure portale Log Analitica area di lavoro a cui si desidera inoltrare gli eventi. Il valore predefinito è SymantecICDx. Questo campo è obbligatorio.
8. Fare clic su *salvare* per completare la configurazione di server d'inoltro. 
9. Per avviare il server d'inoltro, sotto **le opzioni**, fare clic su **ulteriori** e quindi **avviare**.
10. Per usare lo schema appropriato nel Log Analitica per gli eventi di Symantec ICDx, cercare **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero occorrere fino a 20 minuti fino a quando i log di avvio venga visualizzato nel Log Analitica. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Sentinel di Symantec ICDx. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

