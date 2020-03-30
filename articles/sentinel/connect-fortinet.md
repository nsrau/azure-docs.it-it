---
title: Connettere i dati Fortinet ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati Fortinet ad Azure Sentinel.Learn how to connect Fortinet data to Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588196"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Connettere Fortinet ad Azure SentinelConnect Fortinet to Azure Sentinel



Questo articolo illustra come connettere l'appliance Fortinet ad Azure Sentinel.This article explains how to connect your Fortinet appliance to Azure Sentinel. Il connettore dati Fortinet consente di connettere facilmente i log Fortinet con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'uso di Fortinet in Azure Sentinel fornirà maggiori informazioni sull'utilizzo di Internet dell'organizzazione e ne migliorerà le funzionalità operative di sicurezza. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Inoltrare i registri Fortinet all'agente Syslog

Configurare Fortinet per inoltrare i messaggi Syslog in formato CEF all'area di lavoro di Azure tramite l'agente Syslog.

1. Aprire l'interfaccia della riga di comando sull'appliance Fortinet ed eseguire i seguenti comandi:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Sostituire **l'indirizzo IP** del server con l'indirizzo IP dell'agente.
    - Impostare la **porta syslog** su **514** o la porta impostata sull'agente.
    - Per abilitare il formato CEF nelle prime versioni di FortiOS, potrebbe essere necessario eseguire il set di comandi **csv disable**.
 
   > [!NOTE] 
   > Per ulteriori informazioni, visitare la [raccolta documenti Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selezionare la versione in uso e utilizzare il **manuale** e **il riferimento**ai messaggi di registrazione .

1. Per usare lo schema pertinente in Azure Monitor `CommonSecurityLog`Log Analytics per gli eventi Fortinet, cercare .

1. Procedere al [passaggio 3: Convalidare la connettività](connect-cef-verify.md).


## <a name="next-steps"></a>Passaggi successivi
In this article, you learned how to connect Fortinet appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


