---
title: Connettere i dati syslog ad Azure Sentinel Preview | Microsoft Docs
description: Informazioni su come connettere i dati syslog a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: fef9fa128d2ebb84fb82579f254735fdb9aa7ee2
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881075"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connettere la soluzione esterna usando syslog

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile connettere qualsiasi appliance locale che supporta syslog in Azure Sentinel. Questa operazione viene eseguita usando un agente basato su un computer Linux tra il dispositivo e la sentinella di Azure. Se il computer Linux è in Azure, è possibile trasmettere i log dal dispositivo o dall'applicazione a un'area di lavoro dedicata creata in Azure e connetterla. Se il computer Linux non è in Azure, è possibile trasmettere i log dall'appliance a una macchina virtuale locale dedicata o a un computer in cui si installa l'agente per Linux. 

> [!NOTE]
> Se il dispositivo supporta syslog CEF, la connessione è più completa ed è consigliabile scegliere questa opzione e seguire le istruzioni in [connessione dei dati da CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Funzionamento

Syslog è un protocollo di registrazione di eventi comunemente usato in Linux. Le applicazioni inviano messaggi che possono essere archiviati nel computer locale o recapitati a un agente di raccolta di Syslog. Quando è installato, l'agente di Log Analytics per Linux configura il daemon Syslog locale per inoltrare i messaggi all'agente. Quest'ultimo invia quindi il messaggio a Monitoraggio di Azure, dove viene creato un record corrispondente.

Per altre informazioni, vedere [origini dati syslog in monitoraggio di Azure](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> L'agente può raccogliere log da più origini, ma deve essere installato nel computer proxy dedicato.

## <a name="connect-your-syslog-appliance"></a>Connettere l'appliance syslog

1. Nel portale di Azure Sentinel selezionare **connettori dati** e selezionare la riga **syslog** nella tabella e nel riquadro syslog a destra fare clic su **Apri pagina connettore**.
2. Se il computer Linux si trova in Azure, selezionare **scaricare e installare l'agente nella macchina virtuale Linux di Azure**. Nella finestra macchine virtuali selezionare i computer in cui si desidera installare l'agente e fare clic su **Connetti** nella parte superiore.
1. Se il computer Linux non è incluso in Azure, selezionare **scaricare e installare l'agente in una macchina Linux non Azure**. Nella finestra **agente diretto** copiare il comando in **Scarica e caricare l'agente per Linux** ed eseguirlo nel computer. 
1. In **configurare i log da connettere** nella finestra di configurazione del connettore syslog, seguire le istruzioni:
    1. Fare clic sul collegamento per **aprire la configurazione delle impostazioni avanzate dell'area di lavoro**. 
    1. Selezionare **dati**, seguito da **syslog**.
    1. Quindi, nella tabella impostare le funzionalità che si desidera vengano raccolte da syslog. È necessario aggiungere o selezionare le funzionalità incluse nell'appliance syslog nelle intestazioni dei log. È possibile visualizzare questa configurazione nell'appliance syslog in syslog-d nella cartella:/etc/rsyslog.d/Security-Config-omsagent.conf e in r-syslog in/etc/syslog-ng/Security-Config-omsagent.conf. 
       > [!NOTE]
       > Se si seleziona la casella di controllo per **applicare la configurazione seguente alle macchine virtuali**, questa configurazione verrà applicata a tutti i computer Linux connessi all'area di lavoro. È possibile visualizzare questa configurazione nel computer syslog in 
1. Fare clic su **premere qui per aprire il pannello di configurazione**.
1. Selezionare **dati** e quindi **syslog**.
   - Verificare che ogni funzionalità inviata da syslog si trovi nella tabella. Per ogni struttura che si intende monitorare, impostare una gravità. Fare clic su **Apply**.
1. Nel computer syslog assicurarsi di inviare tali strutture. 

1. Per usare lo schema pertinente in Log Analytics per i log syslog, cercare **syslog**.
1. È possibile usare la funzione kusto descritta in [uso di funzioni nelle query log di monitoraggio di Azure](../azure-monitor/log-query/functions.md) per analizzare i messaggi syslog e quindi salvarli come una nuova funzione log Analytics e quindi usare la funzione come nuovo tipo di dati.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le appliance syslog locali ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
