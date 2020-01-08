---
title: Connettere i dati di Fortinet ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Fortinet a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 3ed83f794cdb92f709cbf5c0ea236a5a9b4c187b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610540"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Connettere Fortinet ad Azure Sentinel



Questo articolo illustra come connettere il dispositivo Fortinet ad Azure Sentinel. Fortinet Data Connector consente di connettere facilmente i log Fortinet con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di Fortinet in Sentinel di Azure offrirà maggiori informazioni sull'uso di Internet dell'organizzazione e migliorerà le funzionalità di sicurezza. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Inviare i log Fortinet all'agente syslog

Configurare Fortinet per l'invio dei messaggi syslog in formato CEF all'area di lavoro di Azure tramite l'agente syslog.

1. Aprire l'interfaccia della riga di comando nel dispositivo Fortinet ed eseguire i comandi seguenti:

        config log syslogd setting
        set format cef
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Sostituire l' **indirizzo IP** del server con l'indirizzo IP dell'agente.
    - Impostare la **porta syslog** su **514** o la porta impostata sull'agente.
    - Per abilitare il formato CEF nelle prime versioni di FortiOS, potrebbe essere necessario eseguire il set di comandi **Disable CSV**.
 
   > [!NOTE] 
   > Per ulteriori informazioni, visitare la [raccolta documenti Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selezionare la versione in uso e usare il **manuale** e il **riferimento ai messaggi di log**.

1. Per usare lo schema pertinente in monitoraggio di Azure Log Analytics per gli eventi Fortinet, cercare `CommonSecurityLog`.

1. Continuare con il [passaggio 3: convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come connettere le appliance Fortinet ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.


