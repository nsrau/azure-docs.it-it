---
title: Convalidare la connettività a Sentinel di Azure | Microsoft Docs
description: Convalidare la connettività della soluzione di sicurezza per assicurarsi che i messaggi CEF vengano inviati a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 643b28b2e88f233d2924270511d3c87fa4d9b767
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631631"
---
# <a name="step-3-validate-connectivity"></a>PASSAGGIO 3: convalidare la connettività

Dopo aver distribuito il server d'invio del log (nel passaggio 1) e aver configurato la soluzione di sicurezza per l'invio dei messaggi CEF (nel passaggio 2), seguire queste istruzioni per verificare la connettività tra la soluzione di sicurezza e Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di autorizzazioni elevate (sudo) nel computer del server d'accesso.

- È necessario che Python sia installato nel computer del server d'accesso.<br>
Usare il `python –version` comando per controllare.

## <a name="how-to-validate-connectivity"></a>Come convalidare la connettività

1. Dal menu di navigazione di Azure Sentinel aprire **log**. Eseguire una query usando lo schema **CommonSecurityLog** per verificare se si ricevono log dalla soluzione di sicurezza.<br>
Tenere presente che potrebbero essere necessari circa 20 minuti prima che i log inizino a essere visualizzati in **log Analytics**. 

1. Se non vengono visualizzati risultati dalla query, verificare che gli eventi vengano generati dalla soluzione di sicurezza oppure provare a generarne alcuni e verificare che siano in fase di invio al computer del server d'avanzamento syslog designato. 

1. Eseguire lo script seguente nel server d'avvio del log per verificare la connettività tra la soluzione di sicurezza, il server d'avvio del log e la sentinella di Azure. Questo script verifica che il daemon sia in ascolto sulle porte corrette, che l'invio sia configurato correttamente e che nulla blocchi la comunicazione tra il daemon e l'agente di Log Analytics. Invia anche i messaggi fittizi ' TestCommonEventFormat ' per controllare la connettività end-to-end. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Spiegazione dello script di convalida

Lo script di convalida esegue i controlli seguenti:

# <a name="rsyslog-daemon"></a>[daemon rsyslog](#tab/rsyslog)

1. Verifica che il file<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    esiste e è valido.

1. Verifica che il file includa il testo seguente:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Verifica che l'analisi di Cisco ASA per gli eventi del firewall sia configurata come previsto:

    ```bash
    sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" 
        /opt/microsoft/omsagent/plugin/security_lib.rb && 
        sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verifica che il campo *computer* nell'origine syslog sia mappato correttamente nell'agente di log Analytics:

    ```bash
    sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
        -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
        filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Controlla se nel computer sono presenti miglioramenti della sicurezza che potrebbero bloccare il traffico di rete, ad esempio un firewall host.

1. Verifica che il daemon syslog (rsyslog) sia configurato correttamente per l'invio di messaggi (che identifica come CEF) all'agente Log Analytics sulla porta TCP 25226:

    - File di configurazione: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Riavvia il daemon syslog e l'agente di Log Analytics:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verifica che siano stabilite le connessioni necessarie: TCP 514 per la ricezione di dati, TCP 25226 per la comunicazione interna tra il daemon syslog e l'agente di Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Verifica che il daemon syslog riceva dati sulla porta 514 e che l'agente riceva i dati sulla porta 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Invia dati fittizi alla porta 514 in localhost. Questi dati devono essere osservabili nell'area di lavoro di Azure Sentinel eseguendo la query seguente:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[daemon syslog-ng](#tab/syslogng)

1. Verifica che il file<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    esiste e è valido.

1. Verifica che il file includa il testo seguente:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Verifica che l'analisi di Cisco ASA per gli eventi del firewall sia configurata come previsto:

    ```bash
    sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" 
        /opt/microsoft/omsagent/plugin/security_lib.rb && 
        sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verifica che il campo *computer* nell'origine syslog sia mappato correttamente nell'agente di log Analytics:

    ```bash
    sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
        -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
        filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Controlla se nel computer sono presenti miglioramenti della sicurezza che potrebbero bloccare il traffico di rete, ad esempio un firewall host.

1. Verifica che il daemon syslog (syslog-ng) sia configurato correttamente per inviare messaggi identificati come CEF (usando un'espressione regolare) all'agente Log Analytics sulla porta TCP 25226:

    - File di configurazione: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Riavvia il daemon syslog e l'agente di Log Analytics:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Verifica che siano stabilite le connessioni necessarie: TCP 514 per la ricezione di dati, TCP 25226 per la comunicazione interna tra il daemon syslog e l'agente di Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Verifica che il daemon syslog riceva dati sulla porta 514 e che l'agente riceva i dati sulla porta 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Invia dati fittizi alla porta 514 in localhost. Questi dati devono essere osservabili nell'area di lavoro di Azure Sentinel eseguendo la query seguente:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

