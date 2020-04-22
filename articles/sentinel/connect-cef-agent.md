---
title: Distribuire il server d'inoltro del log per connettere i dati CEF ad Azure Sentinel . Documenti Microsoft
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
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731651"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Passaggio 1: Distribuire il server d'inoltro del logStep 1: Deploy the log forwarder


In questo passaggio verrà designata e configurata la macchina Linux che invierà i log dalla soluzione di sicurezza all'area di lavoro di Azure Sentinel.In this step, you will designate and configure the Linux machine that will forward the logs from your security solution to your Azure Sentinel workspace. Questa macchina può essere una macchina fisica o virtuale nell'ambiente locale, in una macchina virtuale di Azure o in una macchina virtuale in un altro cloud. Utilizzando il collegamento fornito, verrà eseguito uno script sul computer designato che esegue le attività seguenti:
- Installa l'agente di Log Analytics per Linux (noto anche come agente OMS) e lo configura per gli scopi seguenti:
    - ascolto di messaggi CEF dal daemon Linux Syslog incorporato sulla porta TCP 25226
    - invio sicuro dei messaggi tramite TLS all'area di lavoro di Azure Sentinel, dove vengono analizzati e arricchiti

- Configura il daemon Syslog Linux incorporato (rsyslog.d/syslog-ng) per i seguenti scopi:
    - ascolto dei messaggi Syslog dalle soluzioni di sicurezza sulla porta TCP 514
    - inoltrare solo i messaggi che identifica come CEF all'agente di Log Analytics su localhost utilizzando la porta TCP 25226
 
## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di autorizzazioni elevate (sudo) sul computer Linux designato.
- È necessario disporre di python installato sulla macchina Linux.<br>Utilizzare `python -version` il comando per controllare.
- Il computer Linux non deve essere connesso ad alcuna area di lavoro di Azure prima di installare l'agente log Analytics.The Linux machine must not be connected to any Azure workspaces before you install the Log Analytics agent.

## <a name="run-the-deployment-script"></a>Eseguire lo script di distribuzione
 
1. Dal menu di spostamento di Azure Sentinel fare clic su **Connettori dati**. Nell'elenco dei connettori fare clic sul riquadro **Formato evento comune (CEF, Common Event Format)** e quindi sul pulsante **Apri pagina connettore** in basso a destra. 

1. In **1.2 Installare l'agente**di raccolta CEF nel computer Linux , copiare il collegamento fornito in Eseguire lo script seguente per installare e applicare **l'agente**di raccolta CEF o dal testo seguente:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Durante l'esecuzione dello script, verificare di non ricevere messaggi di errore o di avviso.

Continuare con [il passaggio 2: Configurare la soluzione di sicurezza per inoltrare i messaggi CEF.](connect-cef-solution-config.md)

## <a name="deployment-script-explained"></a>Spiegazione dello script di distribuzione

Di seguito è riportata una descrizione comando per comando delle azioni dello script di distribuzione.

Scegliere un daemon syslog per visualizzare la descrizione appropriata.

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **Download e installazione dell'agente di Log Analytics:**

    - Scarica lo script di installazione per l'agente Linux di Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installa l'agente di Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurazione del daemon Syslog:**

    1. Apre la porta 514 per la `/etc/rsyslog.conf`comunicazione TCP utilizzando il file di configurazione syslog .

    1. Configura il daemon per inoltrare i messaggi CEF all'agente di Log Analytics sulla `security-config-omsagent.conf` porta TCP 25226, inserendo un file di configurazione speciale nella directory `/etc/rsyslog.d/`di daemon syslog .

        Contenuto del `security-config-omsagent.conf` file:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Riavvio del daemon Syslog**

    `service rsyslog restart`

1. **Impostazione della configurazione dell'agente di Log Analytics per l'ascolto sulla porta 25226 e l'inoltro dei messaggi CEF ad Azure SentinelSetting the Log Analytics agent configuration to listen on port 25226 and forward CEF messages to Azure Sentinel**

    1. Scarica la configurazione dal repository GitHub dell'agente di Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Riavvia l'agente di Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **Download e installazione dell'agente di Log Analytics:**

    - Scarica lo script di installazione per l'agente Linux di Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installa l'agente di Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configurazione del daemon Syslog:**

    1. Apre la porta 514 per la `/etc/syslog-ng/syslog-ng.conf`comunicazione TCP utilizzando il file di configurazione syslog .

    1. Configura il daemon per inoltrare i messaggi CEF all'agente di Log Analytics sulla `security-config-omsagent.conf` porta TCP 25226, inserendo un file di configurazione speciale nella directory `/etc/syslog-ng/conf.d/`di daemon syslog .

        Contenuto del `security-config-omsagent.conf` file:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Riavvio del daemon Syslog**

    `service syslog-ng restart`

1. **Impostazione della configurazione dell'agente di Log Analytics per l'ascolto sulla porta 25226 e l'inoltro dei messaggi CEF ad Azure SentinelSetting the Log Analytics agent configuration to listen on port 25226 and forward CEF messages to Azure Sentinel**

    1. Scarica la configurazione dal repository GitHub dell'agente di Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Riavvia l'agente di Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to deploy the Log Analytics agent to connect CEF appliances to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).

