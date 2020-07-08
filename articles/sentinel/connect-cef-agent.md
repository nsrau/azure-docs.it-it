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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 502fbe3bc7b1de2038bc444ae5daf180cfc80203
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85298991"
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
- È necessario che Python sia installato nel computer Linux.<br>Usare il `python -version` comando per controllare.
- Il computer Linux non deve essere connesso ad alcuna area di lavoro di Azure prima di installare l'agente di Log Analytics.

## <a name="run-the-deployment-script"></a>Eseguire lo script di distribuzione
 
1. Dal menu di navigazione di Azure Sentinel, fare clic su **connettori dati**. Dall'elenco dei connettori, fare clic sul riquadro **CEF (Common Event Format)** e quindi sul pulsante **Apri pagina connettore** in basso a destra. 

1. In **1,2 installare l'agente di raccolta CEF nel computer Linux**, copiare il collegamento fornito in **eseguire lo script seguente per installare e applicare l'agente di raccolta CEF**oppure dal testo seguente:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Mentre lo script è in esecuzione, assicurarsi che non vengano visualizzati messaggi di errore o di avviso.

Continuare con [il passaggio 2: configurare la soluzione di sicurezza per l'invio dei messaggi CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Spiegazione dello script di distribuzione

Di seguito è riportata una descrizione comando per comando delle azioni dello script di distribuzione.

Scegliere un daemon syslog per visualizzare la descrizione appropriata.

# <a name="rsyslog-daemon"></a>[daemon rsyslog](#tab/rsyslog)

1. **Download e installazione dell'agente di Log Analytics:**

    - Scarica lo script di installazione per l'agente Linux Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installa l'agente di Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurazione del daemon syslog:**

    1. Apre la porta 514 per la comunicazione TCP usando il file di configurazione Syslog `/etc/rsyslog.conf` .

    1. Configura il daemon per l'invio dei messaggi CEF all'agente di Log Analytics sulla porta TCP 25226, inserendo un file di configurazione speciale `security-config-omsagent.conf` nella directory del daemon syslog `/etc/rsyslog.d/` .

        Contenuto del `security-config-omsagent.conf` file:

            :rawmsg, regex, "CEF"|"ASA"
            *.* @@127.0.0.1:25226

1. **Riavvio del daemon syslog**

    `service rsyslog restart`

1. **Impostazione della configurazione dell'agente di Log Analytics per l'ascolto sulla porta 25226 e l'invio dei messaggi CEF ad Azure Sentinel**

    1. Scarica la configurazione dal repository GitHub dell'agente Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Riavvia l'agente di Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[daemon syslog-ng](#tab/syslogng)

1. **Download e installazione dell'agente di Log Analytics:**

    - Scarica lo script di installazione per l'agente Linux Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installa l'agente di Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurazione del daemon syslog:**

    1. Apre la porta 514 per la comunicazione TCP usando il file di configurazione Syslog `/etc/syslog-ng/syslog-ng.conf` .

    1. Configura il daemon per l'invio dei messaggi CEF all'agente di Log Analytics sulla porta TCP 25226, inserendo un file di configurazione speciale `security-config-omsagent.conf` nella directory del daemon syslog `/etc/syslog-ng/conf.d/` .

        Contenuto del `security-config-omsagent.conf` file:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Riavvio del daemon syslog**

    `service syslog-ng restart`

1. **Impostazione della configurazione dell'agente di Log Analytics per l'ascolto sulla porta 25226 e l'invio dei messaggi CEF ad Azure Sentinel**

    1. Scarica la configurazione dal repository GitHub dell'agente Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Riavvia l'agente di Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come distribuire l'agente di Log Analytics per connettere le appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

