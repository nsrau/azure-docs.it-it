---
title: Connettere i dati di Windows Firewall ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Windows Firewall ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588060"
---
# <a name="connect-windows-firewall"></a>Connettere Windows Firewall



Il connettore Windows Firewall consente di connettere facilmente i log di Windows Firewall, se sono connessi all'area di lavoro di Azure Sentinel. Questa connessione consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. In questo modo è possibile ottenere informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. La soluzione raccoglie gli eventi di Windows Firewall dai computer Windows in cui è installato un agente Log Analytics. 


> [!NOTE]
> - I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.
> - Se Sentinel di Azure e il Centro sicurezza di Azure vengono raccolti nella stessa area di lavoro, non è necessario abilitare la soluzione Windows Firewall tramite questo connettore. Se è stato abilitato comunque, non verrà generato alcun dato duplicato. 

## <a name="enable-the-connector"></a>Abilitare il connettore 

1. Nel portale di Azure Sentinel selezionare **connettori dati** e quindi fare clic sul riquadro **Windows Firewall** . 
1.  Se i computer Windows sono in Azure:
    1. Fare clic su **Installa agente nella macchina virtuale Windows di Azure**.
    1. Nell'elenco **macchine virtuali** selezionare il computer Windows di cui si vuole eseguire lo streaming in Sentinel di Azure. Assicurarsi che si tratta di una macchina virtuale Windows.
    1. Nella finestra che viene visualizzata per la macchina virtuale fare clic su **Connetti**.  
    1. Fare clic su **Abilita** nella finestra **connettore Windows Firewall** . 

2. Se il computer Windows non è una macchina virtuale di Azure:
    1. Fare clic su **Installa agente in computer non Azure**.
    1. Nella finestra **agente diretto** selezionare **Scarica agente Windows (64 bit)** o **scarica agente Windows (bit 32)**.
    1. Installare l'agente nel computer Windows. Copiare l' **ID dell'area di lavoro**, la **chiave primaria**e la **chiave secondaria** e usarli quando richiesto durante l'installazione.

4. Consente di selezionare i tipi di dati di cui si desidera eseguire il flusso.
5. Fare clic su **Installa soluzione**.
6. Per utilizzare lo schema pertinente in Log Analytics per Windows Firewall, cercare **SecurityEvent**.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Windows Firewall ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

