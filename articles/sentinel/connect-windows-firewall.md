---
title: Connettere i dati di Windows Defender Firewall ad Azure Sentinel | Microsoft Docs
description: Abilitare il connettore Windows Firewall in Sentinel di Azure per trasmettere facilmente gli eventi firewall da computer Windows in cui è installato Log Analytics agenti.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: 5804dcc840eb666c1d43ea7d7ed7640b8f7ff371
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657443"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Connettere Windows Defender Firewall con sicurezza avanzata ad Azure Sentinel

[Windows Defender Firewall con connettore di sicurezza avanzata](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) consente a Azure Sentinel di inserire facilmente Windows Defender Firewall con log di sicurezza avanzati da qualsiasi computer Windows nell'area di lavoro. Questa connessione consente di visualizzare e analizzare gli eventi Windows Firewall nelle cartelle di lavoro, di usarli per la creazione di avvisi personalizzati e di incorporarli nelle indagini di sicurezza, consentendo di ottenere informazioni più dettagliate sulla rete dell'organizzazione e sul miglioramento delle funzionalità di sicurezza. 

La soluzione raccoglie gli eventi di Windows Firewall dai computer Windows in cui è installato un agente Log Analytics. 

> [!NOTE]
> - I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.
>
> - Se Azure Sentinel e Azure Defender (in precedenza il Centro sicurezza di Azure) vengono raccolti nella stessa area di lavoro, non è necessario abilitare la soluzione Windows Firewall tramite questo connettore. Se è stato abilitato comunque, non verrà generato alcun dato duplicato. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro a cui sono connessi i computer che si desidera monitorare.

- È necessario avere il ruolo **collaboratore log Analytics** nella soluzione SecurityInsights in tale area di lavoro, oltre ai ruoli di **Azure Sentinel** . [Altre informazioni](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Abilitare il connettore 

1. Nel portale di Azure Sentinel selezionare **connettori dati** dal menu di navigazione.

1. Selezionare **Windows Firewall** dalla raccolta di connettori e fare clic su **Apri pagina connettore**.

### <a name="instructions-tab"></a>Scheda istruzioni

Completare i passaggi seguenti nella scheda **Instructions (istruzioni** ).

- **Se i computer Windows sono in Azure, completare i passaggi seguenti:**

   1. Selezionare **Installa agente nella macchina virtuale Windows di Azure**.
   
   1. Fare clic sul collegamento **scarica & installa agente per macchine virtuali Windows di Azure >** visualizzato.
   
   1. Nell'elenco **macchine virtuali** selezionare il computer Windows di cui si vuole eseguire lo streaming in Sentinel di Azure. È possibile selezionare **Windows** nel filtro colonne sistema operativo per assicurarsi che vengano visualizzate solo le macchine virtuali Windows.
   
   1. Nella finestra che viene visualizzata per la macchina virtuale fare clic su **Connetti**.
   
   1. Tornare al riquadro **macchine virtuali** e ripetere i due passaggi precedenti per tutte le altre macchine virtuali a cui si vuole connettersi. Al termine, tornare al riquadro **Windows Firewall** .

- **Se il computer Windows non è una macchina virtuale di Azure, completare i passaggi seguenti:**
   
   1. Selezionare **Installa agente in un computer Windows non Azure**.
   
   1. Fare clic sul collegamento **scarica & installa agente per computer Windows non Azure >** visualizzato.
   
   1. Nel riquadro **Gestione agenti** selezionare **Scarica agente Windows (64 bit)** o **Scarica agente Windows (32 bit)**, in base alle esigenze.
   
   1. Copiare le stringhe di **ID dell'area di lavoro**, **chiave primaria**e **chiave secondaria** in un file di testo. Copiare il file e il file di installazione scaricato nel computer Windows. Eseguire il file di installazione e, quando richiesto, immettere le stringhe di ID e chiave nel file di testo durante l'installazione.
   
   1. Tornare al riquadro **Windows Firewall** .

Dopo aver completato i passaggi nella scheda **istruzioni** , fare clic su **Installa soluzione**.

### <a name="next-steps-tab"></a>Scheda passaggi successivi

- Per informazioni dettagliate sui dati del log di Windows Firewall, vedere le cartelle di lavoro consigliate e gli esempi di query disponibili in bundle con il connettore dati **Windows Firewall** .

- Per eseguire una query sui dati di Windows Firewall nei **log**, digitare **WindowsFirewall** nella finestra query.


## <a name="validate-connectivity"></a>Convalidare la connettività
 
Poiché i log di Windows Firewall vengono inviati ad Azure Sentinel solo quando il file di log locale raggiunge la capacità, lasciando il log con le dimensioni predefinite 4096 KB è molto probabile che si verifichi una latenza di raccolta elevata. È possibile ridurre la latenza abbassando le dimensioni del file di log. Vedere le istruzioni per [configurare il registro Windows Firewall](https://docs.microsoft.com/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log). Si noti che durante la definizione delle dimensioni minime del log (1 KB) si elimina praticamente la latenza di raccolta, potrebbe anche influire negativamente sulle prestazioni del computer locale. 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Windows Firewall ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

