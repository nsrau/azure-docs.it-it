---
title: Connettere i computer Linux a monitoraggio di Azure | Microsoft Docs
description: Questo articolo descrive come connettere i computer Linux ospitati in altri cloud o in monitoraggio da sito locale ad Azure con l'agente di Log Analytics per Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 9807d6eeb07b953ab75b328ce64c5166ca52dd2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80637511"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Connettere i computer Linux a monitoraggio di Azure

Per monitorare e gestire le macchine virtuali o i computer fisici nel Data Center locale o in un altro ambiente cloud con monitoraggio di Azure, è necessario distribuire l'agente di Log Analytics e configurarlo per il report in un'area di lavoro di Log Analytics. L'agente supporta anche il ruolo di lavoro ibrido per runbook per Automazione di Azure.

È possibile installare l'agente di Log Analytics per Linux usando uno dei metodi seguenti. I dettagli sull'uso di ogni metodo sono disponibili più avanti nell'articolo.

* [Scaricare e installare manualmente](#install-the-agent-manually) l'agente. Questa operazione è necessaria quando il computer Linux non ha accesso a Internet e comunicherà con monitoraggio di Azure o automazione di Azure tramite il [gateway log Analytics](gateway.md). 
* [Installare l'agente per Linux usando uno script wrapper](#install-the-agent-using-wrapper-script) ospitato in GitHub. Si tratta del metodo consigliato per installare e aggiornare l'agente quando il computer dispone di connettività a Internet, direttamente o tramite un server proxy.

Per comprendere la configurazione supportata, vedere i [sistemi operativi Linux supportati](log-analytics-agent.md#supported-linux-operating-systems) e la [configurazione del firewall di rete](log-analytics-agent.md#network-requirements).

>[!NOTE]
>L'agente di Log Analytics per Linux non può essere configurato per inviare report a più di un'area di lavoro Log Analytics. Può essere configurata solo in modo da segnalare sia un gruppo di gestione System Center Operations Manager che Log Analytics area di lavoro contemporaneamente oppure singolarmente.

## <a name="agent-install-package"></a>Pacchetto di installazione agente

L'agente di Log Analytics per Linux è costituito da più pacchetti. Il file di versione contiene i pacchetti seguenti, disponibili eseguendo il bundle della shell con il `--extract` parametro:

**Package** | **Version** | **Descrizione**
----------- | ----------- | --------------
omsagent | 1.12.15 | Agente di Log Analytics per Linux
omsconfig | 1.1.1 | Agente di configurazione per l'agente di Log Analytics
omi | 1.6.3 | OMI (Open Management Infrastructure): un server CIM leggero. *Si noti che OMI richiede l'accesso alla radice per eseguire un processo cron necessario per il funzionamento del servizio*
scx | 1.6.3 | Provider OMI CIM per metriche delle prestazioni del sistema operativo
apache-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server HTTP Apache per OMI. Installato solo se viene rilevato il server HTTP Apache.
mysql-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server MySQL per OMI. Installato solo se viene rilevato il server MySQL/MariaDB.
docker-cimprov | 1.0.0 | Provider Docker per OMI. Installato solo se viene rilevato Docker.

### <a name="agent-installation-details"></a>Dettagli installazione agente

Dopo aver installato l'agente di Log Analytics per i pacchetti Linux, vengono applicate le seguenti modifiche di configurazione aggiuntive a livello di sistema. Questi elementi vengono rimossi quando viene disinstallato il pacchetto omsagent.

* Viene creato un utente senza privilegi denominato `omsagent` . Il daemon viene eseguito con questa credenziale. 
* Un file di *inclusione* sudoers viene creato in `/etc/sudoers.d/omsagent` . In questo modo si autorizza `omsagent` a riavviare i daemon syslog e omsagent. Se le direttive di *inclusione* sudo non sono supportate nella versione installata di sudo, queste voci verranno scritte in `/etc/sudoers` .
* La configurazione di SysLog viene modificata in modo da inoltrare un sottoinsieme di eventi all'agente. Per altre informazioni, vedere [configurare la raccolta dei dati syslog](data-sources-syslog.md).

In un computer Linux monitorato, l'agente viene elencato come `omsagent` . `omsconfig`è l'agente di configurazione di Log Analytics Agent per Linux che cerca la nuova configurazione lato portale ogni 5 minuti. La configurazione nuova e aggiornata viene applicata ai file di configurazione dell'agente presenti in `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="obtain-workspace-id-and-key"></a>Ottenere l'ID e la chiave dell'area di lavoro

Prima di installare l'agente di Log Analytics per Linux, sono necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro Log Analytics. Queste informazioni sono necessarie durante l'installazione dell'agente per configurarlo correttamente e verificare che possa comunicare correttamente con monitoraggio di Azure.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Nell'angolo superiore sinistro del portale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere **Log Analytics**. Mentre si digita, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.

2. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro creata in precedenza. Potrebbe essere stata denominata **DefaultLAWorkspace**.

3. Selezionare **Impostazioni avanzate**:

    ![Menu Impostazioni avanzate per l'area di lavoro Log Analytics nel portale di Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selezionare **Origini connesse**, quindi **Server Linux**.

5. Il valore a destra di **ID area di lavoro** e **Chiave primaria**. Copiare e incollare entrambi i valori nell'editor predefinito.

## <a name="install-the-agent-manually"></a>Installare l'agente manualmente

L'agente di Log Analytics per Linux viene fornito in un bundle di script della shell autoestraente e installabile. Questo bundle contiene pacchetti Debian e RPM per ognuno dei componenti dell'agente e può essere installato direttamente o estratto per recuperare i singoli pacchetti. Viene fornito un bundle per x64 e uno per le architetture x86. 

> [!NOTE]
> Per le macchine virtuali di Azure, è consigliabile installarvi l'agente usando l' [estensione VM log Analytics di Azure](../../virtual-machines/extensions/oms-linux.md) per Linux. 

1. [Scaricare](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) e trasferire il bundle appropriato (x64 o x86) nella VM Linux o nel computer fisico usando SCP/SFTP.

2. Installare il bundle usando l' `--install` argomento. Per eseguire l'onboarding in un'area di lavoro Log Analytics durante l'installazione, fornire i `-w <WorkspaceID>` `-s <workspaceKey>` parametri e copiati in precedenza.

    >[!NOTE]
    >È necessario usare l' `--upgrade` argomento se sono installati tutti i pacchetti dipendenti, ad esempio OMI, SCX, programma omsconfig o le versioni precedenti, come accade se System Center Operations Manager Agent per Linux è già installato. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Per configurare l'agente Linux per l'installazione e la connessione a un'area di lavoro di Log Analytics tramite un gateway di Log Analytics, eseguire il comando seguente specificando i parametri di proxy, ID area di lavoro e chiave dell'area di lavoro. Questa configurazione può essere specificata nella riga di comando includendo `-p [protocol://][user:password@]proxyhost[:port]` . La proprietà *proxyhost* accetta un nome di dominio completo o un indirizzo IP del server gateway log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se è richiesta l'autenticazione, è necessario specificare il nome utente e la password. Ad esempio: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Per configurare il computer Linux per la connessione a un'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, eseguire il comando seguente specificando l'ID e la chiave primaria dell'area di lavoro copiati in precedenza.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se si desidera installare i pacchetti dell'agente e configurarli in modo che riportino a una specifica area di lavoro Log Analytics in un secondo momento, eseguire il comando seguente:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se si desidera estrarre i pacchetti dell'agente dal bundle senza installare l'agente, eseguire il comando seguente:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Installare l'agente usando lo script wrapper

La procedura seguente consente di configurare l'installazione dell'agente per Log Analytics in Azure e nel cloud di Azure per enti pubblici usando lo script wrapper per computer Linux in grado di comunicare direttamente o tramite un server proxy per scaricare l'agente ospitato su GitHub e installare l'agente.  

Se il computer Linux deve comunicare tramite un server proxy per Log Analytics, questa configurazione può essere specificata nella riga di comando includendo `-p [protocol://][user:password@]proxyhost[:port]` . La proprietà del *protocollo* accetta `http` o `https` e la proprietà *proxyhost* accetta un nome di dominio completo o un indirizzo IP del server proxy. 

Ad esempio: `https://proxy01.contoso.com:30443`

Se l'autenticazione è necessaria in entrambi i casi, è necessario specificare il nome utente e la password. Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

1. Per configurare il computer Linux per la connessione a un'area di lavoro Log Analytics, eseguire il comando seguente specificando l'ID e la chiave primaria dell'area di lavoro. Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione.
    
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

L'aggiornamento da una versione precedente, a partire dalla versione 1.0.0-47, è supportato in ogni versione. Eseguire l'installazione con il `--upgrade` parametro per aggiornare tutti i componenti dell'agente alla versione più recente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle modalità di riconfigurazione, aggiornamento o rimozione dell'agente dalla macchina virtuale, rivedere [Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux](agent-manage.md).

- Se si verificano problemi di installazione o gestione dell'agente, vedere l'articolo relativo alla [risoluzione dei problemi dell'agente Linux](agent-linux-troubleshoot.md).
