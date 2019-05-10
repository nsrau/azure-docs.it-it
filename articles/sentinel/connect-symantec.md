---
title: Connetti i dati di Symantec ICDX all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere dati Symantec ICDX Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 0410b052f17a868aed70ce407b9c9fdefbe023df
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233617"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Connettersi all'appliance ICDX Symantec 

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Connettore di Symantec ICDX consente di connettere facilmente tutti i log della soluzione sicurezza Symantec con la Sentinel di Azure, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questo ti offre informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. L'integrazione tra ICDX Symantec e Azure Sentinel vengono utilizzate le API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui in esecuzione Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configurare e connettersi ICDX Symantec 

Symantec ICDX può integrare ed esportare i log direttamente da Azure Sentinel.

1. Aprire la Console di gestione ICDX.
2. Nel menu di spostamento a sinistra, selezionare **Configuration** e quindi la **server d'inoltro** scheda.
3. Nella riga Analitica di Log di Microsoft Azure, fare clic su **altre**, quindi su **modificare**. 
4. Nel **utilità di inoltro di Microsoft Azure Log Analitica** finestra, impostare le opzioni seguenti:
    - Lasciare l'impostazione predefinita, SymantecICDX nome Log personalizzato.
    - Copiare l'ID dell'area di lavoro e incollo qui il **identificatore di cliente** campo. Copia il **chiave primaria** e incollarlo nel campo della chiave condivisa. È possibile copiare questi valori dal portale di Azure Sentinel selezionando **connettori di dati** e quindi **Symantec ICDX**.
6. Per usare lo schema appropriato nel Log Analitica per gli eventi di Symantec ICDX, cercare **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero occorrere fino a 20 minuti fino a quando i log di avvio venga visualizzato nel Log Analitica. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Sentinel di ICDX Symantec. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

