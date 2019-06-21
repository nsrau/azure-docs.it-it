---
title: Connettere i dati di Windows firewall all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Windows firewall per Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 09e63612d6e0e70b1bb21c23b158f650d4c34080
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190592"
---
# <a name="connect-windows-firewall"></a>Connettere Windows Firewall

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il connettore di Windows firewall consente di connettere facilmente i log di firewall di Windows, se sono connessi all'area di lavoro Azure Sentinel. Questa connessione consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questo ti offre informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. La soluzione raccoglie gli eventi di Windows firewall dai computer Windows in cui è installato un agente di Log Analitica. 


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui in esecuzione Azure Sentinel.

## <a name="enable-the-connector"></a>Abilitare il connettore 

1. Nel portale di Azure Sentinel, selezionare **connettori di dati** e quindi fare clic sui **firewall Windows** riquadro. 
1.  Se i computer Windows in Azure:
    1. Fare clic su **installare l'agente nella macchina virtuale Windows Azure**.
    1. Nel **macchine virtuali** elencare, selezionare il computer Windows si intende trasmettere in Sentinel di Azure. Assicurarsi che si tratta di una VM Windows.
    1. Nella finestra che si apre per la macchina virtuale, fare clic su **Connect**.  
    1. Fare clic su **abilitare** nel **Windows firewall connettore** finestra. 

2. Se nel computer Windows non è una macchina virtuale di Azure:
    1. Fare clic su **installare l'agente in computer non Azure**.
    1. Nel **agente diretto** finestra, selezionare **dell'agente di Download di Windows (64 bit)** oppure **dell'agente di Download di Windows (32 bit)** .
    1. Installare l'agente nel computer Windows. Copia il **ID area di lavoro**, **chiave primaria**, e **chiave secondaria** e usarle quando richiesto durante l'installazione.

4. Selezionare i tipi di dati da trasmettere in streaming.
5. Fare clic su **installare soluzione**.
6. Per usare lo schema appropriato nel Log Analitica per il firewall di Windows, cercare **SecurityEvent**.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero occorrere fino a 20 minuti fino a quando i log di avvio venga visualizzato nel Log Analitica. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure Sentinel di firewall di Windows. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

