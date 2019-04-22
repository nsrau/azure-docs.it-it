---
title: Raccogliere avvisi di Nagios e Zabbix in Monitoraggio di Azure | Microsoft Docs
description: Nagios e Zabbix sono strumenti di monitoraggio open source. È possibile raccogliere avvisi da questi strumenti in Monitoraggio di Azure per analizzarli insieme ad avvisi provenienti da altre origini.  Questo articolo descrive come configurare l'agente di Log Analytics per Linux per raccogliere avvisi da questi sistemi.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 0ed6747573edf4c059eb29d28107a22706c52856
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426190"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Raccogliere avvisi da Nagios e Zabbix in Monitoraggio di Azure tramite l'agente di Log Analytics per Linux 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) e [Zabbix](https://www.zabbix.com/) sono strumenti di monitoraggio open source. È possibile raccogliere avvisi da questi strumenti in Monitoraggio di Azure per analizzarli insieme ai dati di log provenienti da altre origini.  Questo articolo descrive come configurare l'agente di Log Analytics per Linux per raccogliere avvisi da questi sistemi.


> [!NOTE]
> [Gli avvisi creati da Monitoraggio di Azure](alerts-overview.md) vengono archiviati separatamente dai dati di log e non sono accessibili dalle query di log.

 
## <a name="prerequisites"></a>Prerequisiti
L'agente di Log Analytics per Linux supporta la raccolta di avvisi di Nagios fino alla versione 4.2.x e di Zabbix fino alla versione 2.x.

## <a name="configure-alert-collection"></a>Configurare la raccolta di avvisi

### <a name="configuring-nagios-alert-collection"></a>Configurazione della raccolta di avvisi Nagios
Per raccogliere avvisi, seguire questa procedura nel server Nagios.

1. Concedere all'utente **omsagent** l'accesso in lettura al file di log Nagios `/var/log/nagios/nagios.log`. Supponendo che il file nagios.log sia di proprietà del gruppo `nagios`, è possibile aggiungere l'utente **omsagent** al gruppo **nagios**. 

    sudo usermod -a -G nagios omsagent

2.  Modificare il file di configurazione in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Assicurarsi che le voci seguenti siano presenti e non impostate come commento:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Riavviare il daemon omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Configurazione della raccolta di avvisi Zabbix
Per raccogliere avvisi da un server Zabbix, è necessario specificare un utente e una password come *testo non crittografato*.  Sebbene non sia la condizione ideale, è consigliabile creare un utente Zabbix con autorizzazioni di sola lettura per rilevare gli avvisi pertinenti.

Per raccogliere avvisi nel server Nagios, seguire questa procedura.

1. Modificare il file di configurazione in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Assicurarsi che le voci seguenti siano presenti e non impostate come commento.  Modificare il nome utente e la password con valori appropriati per l'ambiente Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Riavviare il daemon omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Record di avvisi
È possibile recuperare record di avvisi da Nagios e Zabbix usando [query di log](../log-query/log-query-overview.md) in Monitoraggio di Azure.

### <a name="nagios-alert-records"></a>Record di avvisi Nagios

Nei record di avvisi raccolti da Nagios, la proprietà **Tipo** è impostata su **Avviso** e **SourceSystem** su **Nagios**.  Includono le proprietà elencate nella tabella seguente.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| `Type` |*Avviso* |
| `SourceSystem` |*Nagios* |
| `AlertName` |Nome dell'avviso. |
| `AlertDescription` | Descrizione dell'avviso. |
| `AlertState` | Stato del servizio o dell'host.<br><br>OK<br>WARNING<br>UP<br>DOWN |
| `HostName` | Nome dell'host che ha creato l'avviso. |
| `PriorityNumber` | Livello di priorità dell'avviso. |
| `StateType` | Tipo di stato dell'avviso.<br><br>SOFT: problema che non è stato ricontrollato.<br>HARD: problema che è stata ricontrollato un determinato numero di volte.  |
| `TimeGenerated` |Data e ora in cui è stato creato l'avviso. |


### <a name="zabbix-alert-records"></a>Record di avvisi Zabbix
Nei record di avvisi raccolti da Zabbix, la proprietà **Tipo** è impostata su **Avviso** e **SourceSystem** su **Zabbix**.  Includono le proprietà elencate nella tabella seguente.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| `Type` |*Avviso* |
| `SourceSystem` |*Zabbix* |
| `AlertName` | Nome dell'avviso. |
| `AlertPriority` | Gravità dell'avviso<br><br>non classificata<br>Informazioni<br>Avviso<br>average<br>elevata<br>emergenza  |
| `AlertState` | Stato dell'avviso.<br><br>0: stato aggiornato.<br>1: stato sconosciuto.  |
| `AlertTypeNumber` | Specifica se l'avviso può generare più eventi relativi a problemi.<br><br>0: stato aggiornato.<br>1: stato sconosciuto.    |
| `Comments` | Commenti aggiuntivi per l'avviso. |
| `HostName` | Nome dell'host che ha creato l'avviso. |
| `PriorityNumber` | Valore che indica il livello di gravità dell'avviso.<br><br>0: non classificata<br>1: informazioni<br>2: avviso<br>3: media<br>4: elevata<br>5: emergenza |
| `TimeGenerated` |Data e ora in cui è stato creato l'avviso. |
| `TimeLastModified` |Data e ora in cui è stato modificato lo stato dell'avviso per l'ultima volta. |


## <a name="next-steps"></a>Passaggi successivi
* Informazioni sugli [avvisi](alerts-overview.md) in Monitoraggio di Azure.
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 
