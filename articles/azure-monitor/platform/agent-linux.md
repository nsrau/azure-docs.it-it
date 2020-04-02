---
title: Connettere i computer Linux a Monitor di Azure Documenti Microsoft
description: Questo articolo descrive come connettere computer Linux ospitati in altri cloud o locali a Monitoraggio di Azure con l'agente Log Analytics per Linux.This article describes how to connect Linux computers hosted in other clouds or on-premises to Azure Monitor with the Log Analytics agent for Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: aa2356901403c7a63aa4aa96dcb38f9c0c971e58
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528356"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Connettere computer Linux a Monitoraggio di AzureConnect Linux computers to Azure Monitor

Per monitorare e gestire le macchine virtuali o i computer fisici nel data center locale o in un altro ambiente cloud con Monitoraggio di Azure, è necessario distribuire l'agente Log Analytics e configurarlo per la creazione di report in un'area di lavoro di Log Analytics.In order to monitor and manage virtual machines or physical computers in your local datacenter or other cloud environment with Azure Monitor, you need to deploy the Log Analytics agent and configure it to report to a Log Analytics workspace. L'agente supporta anche il ruolo di lavoro ibrido per runbook per Automazione di Azure.

L'agente Log Analytics per Linux può essere installato utilizzando uno dei metodi descritti di seguito. I dettagli sull'uso di ogni metodo sono disponibili più avanti nell'articolo.

* [Scaricare e installare manualmente](#install-the-agent-manually) l'agente. Questa operazione è necessaria quando il computer Linux non ha accesso a Internet e comunicherà con Monitoraggio di Azure o Automazione di Azure tramite il [gateway log Analytics](gateway.md). 
* [Installare l'agente per Linux usando uno script wrapper](#install-the-agent-using-wrapper-script) ospitato in GitHub.Install the agent for Linux using a wrapper-script hosted on GitHub. Questo è il metodo consigliato per installare e aggiornare l'agente quando il computer dispone di connettività con Internet, direttamente o tramite un server proxy.

Per comprendere la configurazione supportata, vedere i [sistemi operativi Linux supportati](log-analytics-agent.md#supported-linux-operating-systems) e la [configurazione del firewall di rete](log-analytics-agent.md#firewall-requirements).

>[!NOTE]
>L'agente di Log Analytics per Linux non può essere configurato per inviare report a più di un'area di lavoro Log Analytics. Può essere configurato solo per creare report solo per un gruppo di gestione di System Center Operations Manager e un'area di lavoro di Log Analytics contemporaneamente o singolarmente.

## <a name="agent-install-package"></a>Pacchetto di installazione dell'agente

L'agente log Analytics per Linux è composto da più pacchetti. Il file di rilascio contiene i seguenti pacchetti, `--extract` che sono disponibili eseguendo il bundle della shell con il parametro:

**Pacchetto** | **Version** | **Descrizione**
----------- | ----------- | --------------
omsagent | 1.12.15 | Agente di Log Analytics per Linux
omsconfig | 1.1.1 | Agente di configurazione per l'agente di Log Analytics
omi | 1.6.3 | Open Management Infrastructure (OMI): un server CIM leggero. *Si noti che OMI richiede l'accesso root per eseguire un processo cron necessario per il funzionamento del servizio*
scx | 1.6.3 | Provider OMI CIM per metriche delle prestazioni del sistema operativo
apache-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server HTTP Apache per OMI. Installato solo se viene rilevato il server HTTP Apache.
mysql-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server MySQL per OMI. Installato solo se viene rilevato il server MySQL/MariaDB.
docker-cimprov | 1.0.0 | Provider Docker per OMI. Installato solo se viene rilevato Docker.

### <a name="agent-installation-details"></a>Dettagli di installazione dell'agente

Dopo aver installato l'agente di Log Analytics per i pacchetti Linux, vengono applicate le seguenti modifiche di configurazione aggiuntive a livello di sistema. Questi elementi vengono rimossi quando viene disinstallato il pacchetto omsagent.

* Viene creato un utente senza privilegi denominato `omsagent` . Il daemon viene eseguito con questa credenziale. 
* Un file di *inclusione* `/etc/sudoers.d/omsagent`sudoers viene creato in . Ciò `omsagent` autorizza a riavviare i daemons syslog e omsagent. Se le direttive sudo *include* non sono supportate nella versione installata di sudo, queste voci verranno scritte in `/etc/sudoers`.
* La configurazione di SysLog viene modificata in modo da inoltrare un sottoinsieme di eventi all'agente. Per ulteriori informazioni, vedere [Configurare la raccolta dati Syslog](data-sources-syslog.md).

In un computer Linux monitorato, `omsagent`l'agente è elencato come . `omsconfig`è l'agente di Log Analytics per l'agente di configurazione Linux che cerca la nuova configurazione lato portale ogni 5 minuti. La configurazione nuova e aggiornata viene applicata ai file di configurazione dell'agente che si trovano in `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## <a name="obtain-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro

Prima di installare l'agente di Log Analytics per Linux, sono necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics. Queste informazioni sono necessarie durante l'installazione dell'agente per configurarlo correttamente e garantire che possa comunicare correttamente con Monitoraggio di Azure.This information is required during setup of the agent to properly configure it and ensure it can successfully communicate with Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Nell'angolo superiore sinistro del portale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere **Log Analytics**. Mentre si digita, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.

2. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro creata in precedenza. Potrebbe essere stata denominata **DefaultLAWorkspace**.

3. Selezionare **Impostazioni avanzate**:

    ![Menu Impostazioni avanzate per l'area di lavoro Log Analytics nel portale di Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selezionare **Origini connesse**, quindi **Server Linux**.

5. Il valore a destra di **ID area di lavoro** e **Chiave primaria**. Copiare e incollare entrambi i valori nell'editor predefinito.

## <a name="install-the-agent-manually"></a>Installare l'agente manualmente

L'agente log Analytics per Linux viene fornito in un pacchetto di script della shell autoestraente e installabile. Questo bundle contiene pacchetti Debian e RPM per ognuno dei componenti dell'agente e può essere installato direttamente o estratto per recuperare i singoli pacchetti. Viene fornito un pacchetto per x64 e uno per le architetture x86. 

> [!NOTE]
> Per le macchine virtuali di Azure, è consigliabile installare l'agente in esse usando l'estensione della macchina virtuale di Azure Log Analytics per Linux.For Azure VMs, we recommend you install the agent on them using the [Azure Log Analytics VM extension](../../virtual-machines/extensions/oms-linux.md) for Linux. 

1. [Scaricare](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) e trasferire il pacchetto appropriato (x64 o x86) nella macchina virtuale Linux o nel computer fisico, usando scp/sftp.

2. Installare il pacchetto `--install` utilizzando l'argomento . Per eseguire l'onboarding in un'area di lavoro di Log Analytics durante l'installazione, specificare i `-w <WorkspaceID>` parametri e `-s <workspaceKey>` copiati in precedenza.

    >[!NOTE]
    >È necessario utilizzare `--upgrade` l'argomento se sono installati pacchetti dipendenti come omi, scx, omsconfig o le versioni precedenti, come avviene se l'agente System Center Operations Manager per Linux è già installato. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Per configurare l'agente Linux per l'installazione e la connessione a un'area di lavoro di Log Analytics tramite un gateway di Log Analytics, eseguire il comando seguente specificando i parametri proxy, ID area di lavoro e chiave dell'area di lavoro. Questa configurazione può essere specificata nella `-p [protocol://][user:password@]proxyhost[:port]`riga di comando includendo . La proprietà *proxyhost* accetta un nome di dominio completo o un indirizzo IP del server gateway di Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se è richiesta l'autenticazione, è necessario specificare il nome utente e la password. Ad esempio: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Per configurare il computer Linux per la connessione a un'area di lavoro di Log Analytics nel cloud di Azure per enti pubblici, eseguire il comando seguente specificando l'ID dell'area di lavoro e la chiave primaria copiati in precedenza.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se si desidera installare i pacchetti agente e configurarlo per la creazione di report in un'area di lavoro di Log Analytics specifica in un secondo momento, eseguire il comando seguente:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se si desidera estrarre i pacchetti dell'agente dal pacchetto senza installare l'agente, eseguire il comando seguente:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Installare l'agente utilizzando lo script wrapper

I passaggi seguenti consentono di configurare la configurazione dell'agente per Log Analytics nel cloud di Azure e Azure per enti pubblici usando lo script wrapper per i computer Linux in grado di comunicare direttamente o tramite un server proxy per scaricare l'agente ospitato in GitHub e installare l'agente.  

Se il computer Linux deve comunicare tramite un server proxy a Log Analytics, questa configurazione può essere specificata nella riga di comando includendo `-p [protocol://][user:password@]proxyhost[:port]`. La *protocol* proprietà protocol `http` `https`accetta o , e la proprietà *proxyhost* accetta un nome di dominio completo o un indirizzo IP del server proxy. 

Ad esempio: `https://proxy01.contoso.com:30443`

Se in entrambi i casi è richiesta l'autenticazione, è necessario specificare il nome utente e la password. Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

1. Per configurare il computer Linux per la connessione a un'area di lavoro di Log Analytics, eseguire il comando seguente specificando l'ID dell'area di lavoro e la chiave primaria. Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Il comando seguente include il parametro `-p` del proxy e la sintassi di esempio per i casi in cui il server proxy richiede l'autenticazione:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Per configurare il computer Linux per connettersi all'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, eseguire il comando seguente specificando l'ID e la chiave primaria dell'area di lavoro copiati in precedenza. Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Il comando seguente include il parametro `-p` del proxy e la sintassi di esempio per i casi in cui il server proxy richiede l'autenticazione:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Riavviare l'agente eseguendo il comando seguente: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Eseguire l'aggiornamento da una versione precedente

L'aggiornamento da una versione precedente, a partire dalla versione 1.0.0-47, è supportato in ogni versione. Eseguire l'installazione `--upgrade` con il parametro per aggiornare tutti i componenti dell'agente alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux](agent-manage.md) per informazioni su come riconfigurare, aggiornare o rimuovere l'agente dalla macchina virtuale.

- Se si verificano problemi di installazione o gestione dell'agente, vedere l'articolo relativo alla [risoluzione dei problemi dell'agente Linux](agent-linux-troubleshoot.md).
