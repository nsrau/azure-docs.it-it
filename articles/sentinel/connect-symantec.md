---
title: Connettere i dati di Symantec ICDx ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Symantec ICDx per connettere facilmente tutti i log della soluzione Symantec Security con Azure Sentinel.
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
ms.openlocfilehash: 67fc80b5f34cf3a98fd39ddc352cb2dd9a5e7151
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564925"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Connettere il dispositivo Symantec ICDx 



Symantec ICDx Connector consente di connettere facilmente tutti i log della soluzione Symantec Security con la sentinella di Azure, di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. In questo modo è possibile ottenere informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. L'integrazione tra Symantec ICDx e Azure Sentinel usa l'API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configurare e connettere Symantec ICDx 

Symantec ICDx può integrare ed esportare i log direttamente in Sentinel di Azure.

1. Aprire la console di gestione di ICDx per aggiungere Microsoft Azure server d'inoltri Sentinel (Log Analytics).
2. Sulla barra di spostamento di ICDx fare clic su **configurazione**. 
3. Nella parte superiore della schermata di **configurazione** fare clic su **server d'inoltri**.
4. In **Server**d'installazione, accanto a Microsoft Azure Sentinel (log Analytics), fare clic su **Aggiungi**. 
4. Nella finestra **Microsoft Azure Sentinel (log Analytics)** fare clic su **Mostra avanzate**. 
5. Nella parte superiore della finestra espansa a Microsoft Azure Sentinel (Log Analytics) eseguire le operazioni seguenti:
    -   **Nome**: digitare un nome per il server d'avanzamento che non contenga più di 30 caratteri. Scegliere un nome univoco e significativo. Questo nome viene visualizzato nell'elenco dei server d'inoltri nella schermata di **configurazione** e nei dashboard nella schermata del **Dashboard** . Ad esempio: Microsoft Azure Log Analytics East. Questo campo è obbligatorio.
    -   **Descrizione**: digitare una descrizione per il server d'avanzamento. Questa descrizione viene visualizzata anche nell'elenco dei server d'inoltri nella schermata di **configurazione** . Includere i dettagli, ad esempio il tipo di evento da trasmettere e il gruppo che deve ispezionare i dati.
    -   **Tipo di avvio**: selezionare il metodo di avvio per la configurazione del server d'avvio. Le opzioni disponibili sono manuale e automatico.<br>Il valore predefinito è Automatic. 
6. In **eventi**eseguire le operazioni seguenti: 
    - **Origine**: selezionare uno o più archivi da cui inviare gli eventi. È possibile selezionare gli archivi degli agenti di raccolta attivi (incluso l'archivio comune), gli archivi degli agenti di raccolta orfani (ovvero gli archivi per gli agenti di raccolta eliminati), gli archivi del ricevitore ICDx o l'archivio di sistema. <br>Il valore predefinito è archivio comune.
      > [!NOTE]
      > Gli archivi del ricevitore ICDx sono elencati separatamente, in base al nome. 
 
    - **Filter**: aggiungere un filtro che specifichi il subset di eventi da trasmettere. Eseguire una delle operazioni seguenti:
        - Per selezionare una condizione di filtro, fare clic su un tipo, un attributo, un operatore e un valore. 
        - Nel campo filtro esaminare la condizione di filtro. È possibile modificarlo direttamente nel campo o eliminarlo, se necessario.
        - Fare clic su AND o OR per aggiungere alla condizione di filtro.
        - È anche possibile fare clic su query salvate per applicare una query salvata.
    - **Attributi inclusi**: digitare l'elenco delimitato da virgole degli attributi da includere nei dati sottotrasmessi. Gli attributi inclusi hanno la precedenza sugli attributi esclusi.
    - **Attributi esclusi**: digitare l'elenco delimitato da virgole degli attributi da escludere dai dati da trasmettere.
    - **Dimensioni batch**: selezionare il numero di eventi da inviare per batch. Le opzioni disponibili sono 10, 50, 100, 500 e 1000.<br>Il valore predefinito è 100. 
    - **Limite di velocità**: selezionare la frequenza con cui vengono trasmessi gli eventi, espressi come eventi al secondo. Le opzioni disponibili sono illimitato, 500, 1000, 5000, 10000. <br> Il valore predefinito è 5000. 
7. In **destinazione di Azure**eseguire le operazioni seguenti: 
    - **ID area di lavoro**: incollare l'ID area di lavoro riportato di seguito. Questo campo è obbligatorio.
    - **Chiave primaria**: incollare la chiave primaria riportata di seguito. Questo campo è obbligatorio.
    - **Nome registro personalizzato**: digitare il nome del log personalizzato nell'area di lavoro portale di Microsoft Azure log Analytics a cui si intende eseguire il provisioning degli eventi. Il valore predefinito è SymantecICDx. Questo campo è obbligatorio.
8. Fare clic su *Salva* per completare la configurazione del server d'avanzamento. 
9. Per avviare il server d'avvio, in **Opzioni**fare clic su **altro** e quindi su **Avvia**.
10. Per usare lo schema pertinente in Log Analytics per gli eventi di Symantec ICDx, cercare **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Symantec ICDx ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


