---
title: Convalidare la connettività ad Azure Sentinel Documenti Microsoft
description: Convalidare la connettività della soluzione di sicurezza per assicurarsi che i messaggi CEF vengano inoltrati ad Azure Sentinel.
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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731821"
---
# <a name="step-3-validate-connectivity"></a>Fase 3: Convalidare la connettività

Dopo aver distribuito il log forwarder (nel passaggio 1) e configurato la soluzione di sicurezza per inviare messaggi CEF (nel passaggio 2), seguire queste istruzioni per verificare la connettività tra la soluzione di sicurezza e Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di autorizzazioni elevate (sudo) nel computer del server d'inoltro log.

- È necessario che Python sia installato nel computer log forwarder.<br>
Utilizzare `python –version` il comando per controllare.

## <a name="how-to-validate-connectivity"></a>Come convalidare la connettività

1. Dal menu di navigazione di Azure Sentinel aprire **Registri**. Eseguire una query utilizzando lo schema **CommonSecurityLog** per verificare se si ricevono log dalla soluzione di sicurezza.<br>
Tenere presente che potrebbero essere decollati circa 20 minuti prima che i log inizino a essere visualizzati in **Log Analytics**. 

1. Se non viene visualizzato alcun risultato dalla query, verificare che gli eventi vengano generati dalla soluzione di sicurezza oppure provare a generarne alcuni e verificare che vengano inoltrati al computer server d'inoltro Syslog designato. 

1. Eseguire lo script seguente nel server d'inoltro log per verificare la connettività tra la soluzione di sicurezza, il server d'inoltro del log e Azure Sentinel. Questo script controlla che il daemon sia in ascolto sulle porte corrette, che l'inoltro sia configurato correttamente e che nulla stia bloccando la comunicazione tra il daemon e l'agente di Log Analytics. Invia inoltre messaggi fittizi 'TestCommonEventFormat' per verificare la connettività end-to-end. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Spiegazione dello script di convalida

Lo script di convalida esegue i seguenti controlli:

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. Verifica che il file<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    esiste ed è valido.

1. Verifica che il file includa il testo seguente:

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

1. Controlla se sono presenti miglioramenti della sicurezza nel computer che potrebbero bloccare il traffico di rete (ad esempio un firewall host).

1. Verifica che il daemon syslog (rsyslog) sia configurato correttamente per inviare messaggi che identifica come CEF (utilizzando un'espressione regolare) all'agente di Log Analytics sulla porta TCP 25226:

    - File di configurazione:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Verifica che il daemon syslog stia ricevendo dati sulla porta 514

1. Verifica che vengano stabilite le connessioni necessarie: tcp 514 per la ricezione dei dati, tcp 25226 per la comunicazione interna tra il daemon syslog e l'agente di Log Analytics

1. Invia i dati MOCK alla porta 514 su localhost. Questi dati devono essere osservabili nell'area di lavoro di Azure Sentinel eseguendo la query seguente:This data should be observable in the Azure Sentinel workspace by running the following query:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Verifica che il file<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    esiste ed è valido.

1. Verifica che il file includa il testo seguente:

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

1. Controlla se sono presenti miglioramenti della sicurezza nel computer che potrebbero bloccare il traffico di rete (ad esempio un firewall host).

1. Verifica che il daemon syslog (syslog-ng) sia configurato correttamente per inviare messaggi che identifica come CEF (utilizzando un'espressione regolare) all'agente di Log Analytics sulla porta TCP 25226:

    - File di configurazione:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Verifica che il daemon syslog stia ricevendo dati sulla porta 514

1. Verifica che vengano stabilite le connessioni necessarie: tcp 514 per la ricezione dei dati, tcp 25226 per la comunicazione interna tra il daemon syslog e l'agente di Log Analytics

1. Invia i dati MOCK alla porta 514 su localhost. Questi dati devono essere osservabili nell'area di lavoro di Azure Sentinel eseguendo la query seguente:This data should be observable in the Azure Sentinel workspace by running the following query:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect CEF appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

