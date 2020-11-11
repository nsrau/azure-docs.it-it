---
title: Distribuire il server d'invio del log per connettere i dati CEF ad Azure Sentinel | Microsoft Docs
description: Informazioni su come distribuire l'agente per connettere i dati CEF ad Azure Sentinel.
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
ms.openlocfilehash: 2f90a7c954fc8930f69dfee3e7b56b394225405a
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516738"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Passaggio 1: distribuire il server d'avanzamento log


In questo passaggio verrà designato e configurato il computer Linux che eseguirà l'invio dei log dalla soluzione di sicurezza all'area di lavoro di Azure Sentinel. Questo computer può essere fisico o macchina virtuale nell'ambiente locale, in una VM di Azure o in una VM in un altro cloud. Utilizzando il collegamento fornito, viene eseguito uno script nel computer designato che esegue le attività seguenti:

- Installa l'agente di Log Analytics per Linux (noto anche come agente OMS) e lo configura per gli scopi seguenti:
    - ascolto dei messaggi CEF dal daemon syslog di Linux incorporato sulla porta TCP 25226
    - invio sicuro dei messaggi su TLS all'area di lavoro di Azure Sentinel, in cui vengono analizzati e arricchiti

- Configura il daemon syslog di Linux predefinito (rsyslog. d/syslog-ng) per gli scopi seguenti:
    - ascolto dei messaggi syslog dalle soluzioni di sicurezza sulla porta TCP 514
    - l'invio dei soli messaggi identificati come CEF all'agente Log Analytics su localhost utilizzando la porta TCP 25226
 
## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di autorizzazioni elevate (sudo) nel computer Linux designato.

- Nel computer Linux è necessario che sia installato **python 2,7** o **3** .<br>Usare il `python -version` comando per controllare.

- Il computer Linux non deve essere connesso ad alcuna area di lavoro di Azure prima di installare l'agente di Log Analytics.

- Potrebbero essere necessari l'ID dell'area di lavoro e la chiave primaria dell'area di lavoro in un determinato punto del processo. È possibile trovarli nella risorsa dell'area di lavoro in **Gestione agenti**.

## <a name="run-the-deployment-script"></a>Eseguire lo script di distribuzione
 
1. Dal menu di navigazione di Azure Sentinel, fare clic su **connettori dati**. Dall'elenco dei connettori, fare clic sul riquadro **CEF (Common Event Format)** e quindi sul pulsante **Apri pagina connettore** in basso a destra. 

1. In **1,2 installare l'agente di raccolta CEF nel computer Linux** , copiare il collegamento fornito in **eseguire lo script seguente per installare e applicare l'agente di raccolta CEF** oppure dal testo seguente (applicando l'ID dell'area di lavoro e la chiave primaria al posto dei segnaposto):

    ```bash
    sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
    ```

1. Mentre lo script è in esecuzione, assicurarsi che non vengano visualizzati messaggi di errore o di avviso.
    - È possibile che venga ricevuto un messaggio che indica di eseguire un comando per correggere un problema relativo al mapping del campo del *computer* . Per informazioni dettagliate, vedere la [spiegazione nello script di distribuzione](#mapping-command) .

> [!NOTE]
> **Uso dello stesso computer per l'invio di messaggi syslog *e* CEF normali**
>
> Se si prevede di usare questo computer di server d'accesso per l'invio dei [messaggi syslog](connect-syslog.md) e della CEF, per evitare la duplicazione degli eventi nelle tabelle syslog e CommonSecurityLog:
>
> 1. In ogni computer di origine che invia log al server d'installazione in formato CEF è necessario modificare il file di configurazione Syslog per rimuovere le funzionalità utilizzate per inviare i messaggi CEF. In questo modo, le funzionalità inviate in CEF non verranno inviate anche in syslog. Per istruzioni dettagliate su come eseguire questa operazione, vedere [configurare syslog nell'agente Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. È necessario eseguire il comando seguente in questi computer per disabilitare la sincronizzazione dell'agente con la configurazione di syslog in Sentinel di Azure. In questo modo si garantisce che la modifica della configurazione apportata nel passaggio precedente non venga sovrascritta.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Continuare con [il passaggio 2: configurare la soluzione di sicurezza per l'invio dei messaggi CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Spiegazione dello script di distribuzione

Di seguito è riportata una descrizione comando per comando delle azioni dello script di distribuzione.

Scegliere un daemon syslog per visualizzare la descrizione appropriata.

# <a name="rsyslog-daemon"></a>[daemon rsyslog](#tab/rsyslog)

1. **Download e installazione dell'agente di Log Analytics:**

    - Scarica lo script di installazione per l'agente Linux Log Analytics (OMS).

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Installa l'agente di Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Impostazione della configurazione dell'agente di Log Analytics per l'ascolto sulla porta 25226 e l'invio dei messaggi CEF ad Azure Sentinel:**

    - Scarica la configurazione dal repository GitHub dell'agente Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configurazione del daemon syslog:**

    - Apre la porta 514 per la comunicazione TCP usando il file di configurazione Syslog `/etc/rsyslog.conf` .

    - Configura il daemon per l'invio dei messaggi CEF all'agente di Log Analytics sulla porta TCP 25226, inserendo un file di configurazione speciale `security-config-omsagent.conf` nella directory del daemon syslog `/etc/rsyslog.d/` .

        Contenuto del `security-config-omsagent.conf` file:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Riavviare il daemon syslog e l'agente di Log Analytics:**

    - Riavvia il daemon rsyslog.
    
        ```bash
        service rsyslog restart
        ```

    - Riavvia l'agente Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Verifica del mapping del campo del *computer* come previsto:**

    - Garantisce che il campo *computer* nell'origine syslog sia mappato correttamente nell'agente di log Analytics, usando il comando seguente: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Se si verifica un problema con il mapping, lo script genera un messaggio di errore che indica di **eseguire manualmente il comando seguente** , applicando l'ID dell'area di lavoro al posto del segnaposto. Il comando garantirà il corretto mapping e riavvierà l'agente.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[daemon syslog-ng](#tab/syslogng)

1. **Download e installazione dell'agente di Log Analytics:**

    - Scarica lo script di installazione per l'agente Linux Log Analytics (OMS).

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Installa l'agente di Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Impostazione della configurazione dell'agente di Log Analytics per l'ascolto sulla porta 25226 e l'invio dei messaggi CEF ad Azure Sentinel:**

    - Scarica la configurazione dal repository GitHub dell'agente Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Configurazione del daemon syslog:**

    - Apre la porta 514 per la comunicazione TCP usando il file di configurazione Syslog `/etc/syslog-ng/syslog-ng.conf` .

    - Configura il daemon per l'invio dei messaggi CEF all'agente di Log Analytics sulla porta TCP 25226, inserendo un file di configurazione speciale `security-config-omsagent.conf` nella directory del daemon syslog `/etc/syslog-ng/conf.d/` .

        Contenuto del `security-config-omsagent.conf` file:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Riavviare il daemon syslog e l'agente di Log Analytics:**

    - Riavvia il daemon syslog-ng.
    
        ```bash
        service syslog-ng restart
        ```

    - Riavvia l'agente Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Verifica del mapping del campo del *computer* come previsto:**

    - Garantisce che il campo *computer* nell'origine syslog sia mappato correttamente nell'agente di log Analytics, usando il comando seguente: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Se si verifica un problema con il mapping, lo script genera un messaggio di errore che indica di **eseguire manualmente il comando seguente** , applicando l'ID dell'area di lavoro al posto del segnaposto. Il comando garantirà il corretto mapping e riavvierà l'agente.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come distribuire l'agente di Log Analytics per connettere le appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

