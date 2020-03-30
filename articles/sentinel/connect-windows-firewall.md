---
title: Connettere i dati del firewall di Windows ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati del firewall di Windows ad Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588060"
---
# <a name="connect-windows-firewall"></a>Connettere Windows Firewall



Il connettore windows firewall consente di connettere facilmente i log dei firewall di Windows, se sono connessi all'area di lavoro di Azure Sentinel.The Windows firewall connector allows you to easily connect your Windows firewalls logs, if they are connected to your Azure Sentinel workspace. Questa connessione consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Ciò offre maggiori informazioni sulla rete dell'organizzazione e migliora le funzionalità di gestione della sicurezza. La soluzione raccoglie gli eventi del firewall di Windows dai computer Windows in cui è installato un agente di Log Analytics.The solution collects Windows firewall events from the Windows machines on which a Log Analytics agent is installed. 


> [!NOTE]
> - I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.
> - Se Azure Sentinel e il Centro sicurezza di Azure vengono raccolti nella stessa area di lavoro, non è necessario abilitare la soluzione Windows Firewall tramite questo connettore. Se è stato abilitato comunque, non causerà dati duplicati. 

## <a name="enable-the-connector"></a>Abilitare il connettore 

1. Nel portale di Azure Sentinel selezionare Connettori dati e quindi fare clic sul riquadro Windows Firewall.In the Azure Sentinel portal, select **Data connectors** and then click on the **Windows firewall** tile. 
1.  Se i computer Windows sono in Azure:If your Windows machines are in Azure:
    1. Fare clic su **Installa agente nella macchina virtuale Windows di Azure.**
    1. Nell'elenco **Macchine virtuali** selezionare la macchina Windows che si vuole trasmettere in Azure Sentinel. Assicurarsi che si tratti di una macchina virtuale Windows.Make sure This is a Windows VM.
    1. Nella finestra visualizzata per la macchina virtuale fare clic su **Connetti**.  
    1. Fare clic su **Attiva** nella finestra del **connettore di Windows Firewall.** 

2. Se il computer Windows non è una macchina virtuale di Azure:If your Windows machine is not an Azure VM:
    1. Fare clic su **Installa agente in computer non Azure**.
    1. Nella finestra **Agente diretto** selezionare Scarica agente **Windows (64 bit)** o **Scarica agente Windows (32 bit)**.
    1. Installare l'agente nel computer Windows. Copiare **l'ID area di lavoro,** la chiave **primaria**e la **chiave secondaria** e utilizzarli quando richiesto durante l'installazione.

4. Selezionare i tipi di dati che si desidera trasmettere in streaming.
5. Fare clic su **Installa soluzione**.
6. Per utilizzare lo schema pertinente in Log Analytics per il firewall di Windows, cercare **SecurityEvent**.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere voluti fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Windows firewall to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

