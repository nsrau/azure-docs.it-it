---
title: Connettere computer Linux ad Azure Log Analytics | Microsoft Docs
description: In questo articolo viene descritto come connettere i computer Linux ospitati in Azure, in altri cloud o in locale a Log Analytics con l'agente di Operations Management Suite per Linux.
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: magoedte
ms.openlocfilehash: 56c666d1a18937df21a6aca8acde87beda1cad8e
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Connettere computer Linux a Log Analytics 

Con Azure Log Analytics è possibile raccogliere e usare i dati generati dai computer Linux e da soluzioni contenitore come Docker, che si trovano nel data center locale, come macchine virtuali o server fisici, macchine virtuali in un servizio ospitato nel cloud come Amazon Web Services (AWS) o Microsoft Azure. È anche possibile usare soluzioni di gestione disponibili in [Automazione di Azure](../automation/automation-intro.md), ad esempio Rilevamento modifiche per identificare le modifiche alla configurazione e Gestione aggiornamenti per gestire gli aggiornamenti software, per una gestione proattiva del ciclo di vita delle VM Linux. 

L'agente di Operations Management Suite per Linux comunica in uscita con i servizi Log Analytics e Automazione di Azure sulla porta TCP 443 e, se il computer si connette a un firewall o a un server proxy per comunicare in Internet, vedere [Configurazione dell'agente per l'uso con un server proxy o un gateway OMS](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) per comprendere quali modifiche alla configurazione è necessario applicare.  Se si esegue il monitoraggio del computer con System Center 2016 - Operations Manager oppure Operations Manager 2012 R2, è possibile usare una configurazione multihomed con il servizio Log Analytics per raccogliere i dati e inoltrarli al servizio, mantenendo il monitoraggio di Operations Manager.  I computer Linux monitorati da un gruppo di gestione di Operations Manager integrato in Log Analytics (attualmente indicato come Operations Management Suite nella console di Operations Manager) non ricevono la configurazione per le origini dati e inoltrano i dati raccolti grazie al gruppo di gestione.  L'agente OMS non può essere configurato per inviare report a più di un'area di lavoro di Log Analytics.  

Se i criteri di sicurezza IT non consentono ai computer nella rete di connettersi a Internet, è possibile configurare l'agente per la connessione al gateway OMS, per ricevere informazioni di configurazione e inviare i dati raccolti a seconda della soluzione abilitata. Per altre informazioni e procedure per la configurazione dell'agente Linux OMS per la comunicazione tramite un gateway OMS con i servizi, vedere [Connettere computer a OMS usando il gateway OMS](log-analytics-oms-gateway.md).  

Il diagramma seguente illustra la connessione tra i computer Linux gestiti dall'agente e Log Analytics, incluse la direzione e le porte.

![Diagramma della comunicazione degli agenti diretti con Servizi di Azure](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Requisiti di sistema
Prima di iniziare, esaminare i dettagli seguenti per verificare che i prerequisiti siano soddisfatti.

### <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati
Le distribuzioni Linux seguenti sono supportate ufficialmente.  È tuttavia possibile che l'agente OMS per Linux sia eseguito in altre distribuzioni non elencate.

* Amazon Linux da 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

### <a name="network"></a>Rete
La tabella seguente contiene un elenco delle informazioni di configurazione del proxy e del firewall necessarie all'agente Linux per comunicare con Log Analytics e Automazione di Azure. Il traffico in uscita dalla rete viene indirizzato al servizio. 

|Risorsa agente| Porte |  
|------|---------|  
|*.ods.opinsights.azure.com | Porta 443|   
|*.oms.opinsights.azure.com | Porta 443|   
|*.blob.core.windows.net/ | Porta 443|   
|*.azure-automation.net | Porta 443|  

### <a name="package-requirements"></a>Requisiti dei pacchetti

 **Pacchetto obbligatorio**   | **Descrizione**   | **Versione minima**
--------------------- | --------------------- | -------------------
Glibc | Libreria GNU C   | 2.5-12 
Openssl | Librerie OpenSSL | 0.9.8e o 1.0
Curl | Client Web cURL | 7.15.5
Python-ctypes | | 
PAM | Moduli di autenticazione modulare | 

> [!NOTE]
>  Per raccogliere i messaggi SysLog, è necessario rsyslog o syslog-ng. Il daemon SysLog predefinito nella versione 5 di Red Hat Enterprise Linux, CentOS e nella versione Oracle Linux (sysklog) non è supportato per la raccolta di eventi SysLog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario che il daemon rsyslog sia installato e configurato per sostituire sysklog. 

L'agente include più pacchetti. Il file della versione rilasciata contiene i pacchetti seguenti, disponibili eseguendo il bundle della shell con `--extract`:

**Pacchetto** | **Versione** | **Descrizione**
----------- | ----------- | --------------
omsagent | 1.4.0 | Agente Operations Management Suite per Linux
omsconfig | 1.1.1 | Agente di configurazione per l'agente OMS
omi | 1.2.0 | Open Management Infrastructure (OMI) - server CIM leggero
scx | 1.6.3 | Provider OMI CIM per metriche delle prestazioni del sistema operativo
apache-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server HTTP Apache per OMI. Installato se viene rilevato il server HTTP Apache.
mysql-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server MySQL per OMI. Installato se viene rilevato il server MySQL/MariaDB.
docker-cimprov | 1.0.0 | Provider Docker per OMI. Installato se viene rilevato Docker.

### <a name="compatibility-with-system-center-operations-manager"></a>Compatibilità con System Center Operations Manager
L'agente OMS per Linux condivide file binari dell'agente con l'agente System Center Operations Manager. Se si installa l'agente di OMS per Linux in un sistema attualmente gestito da Operations Manager, i pacchetti OMI e SCX nel computer vengono aggiornati a una versione più recente. In questa versione gli agenti OMS e System Center 2016 - Operations Manager/Operations Manager 2012 R2 per Linux sono compatibili. 

> [!NOTE]
> System Center 2012 SP1 e le versioni precedenti non sono attualmente compatibili o supportati con l'agente OMS per Linux.<br>
> Se l'agente OMS per Linux viene installato in un computer attualmente non monitorato da Operations Manager e successivamente si vuole monitorare il computer con Operations Manager, è necessario modificare la [configurazione di OMI](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) prima di individuare il computer. **Questo passaggio *non* è necessario se l'agente Operations Manager viene installato prima dell'agente OMS per Linux.**

### <a name="system-configuration-changes"></a>Modifiche alla configurazione di sistema
Dopo l'installazione dei pacchetti dell'agente OMS per Linux, vengono applicate le modifiche di configurazione aggiuntive seguenti a livello di sistema. Questi elementi vengono rimossi quando viene disinstallato il pacchetto omsagent.

* Viene creato un utente senza privilegi denominato `omsagent` . Si tratta dell'account usato per l'esecuzione del daemon omsagent.
* Viene creato un file "include" suoders in /etc/sudoers.d/omsagent. Questo autorizza omsagent a riavviare i daemon SysLog e omsagent. Se le direttive "include" sudo non sono supportate nella versione di sudo installata, queste voci vengono scritte in /etc/sudoers.
* La configurazione di SysLog viene modificata in modo da inoltrare un sottoinsieme di eventi all'agente. Per altre informazioni, vedere la sezione **Configurazione della raccolta di dati** più avanti.

### <a name="upgrade-from-a-previous-release"></a>Eseguire l'aggiornamento da una versione precedente
L'aggiornamento da versioni precedenti alla 1.0.0-47 è supportato in questa versione. L'esecuzione dell'installazione con il comando `--upgrade` comporta l'aggiornamento di tutti i componenti dell'agente alla versione più recente.

## <a name="installing-the-agent"></a>Installazione dell'agente

In questa sezione viene descritto come installare manualmente l'agente di Operations Management Suite per Linux con un bundle, che contiene i pacchetti Debian e RPM per ciascuno dei componenti dell'agente.  Può essere installato direttamente o estratto per recuperare i singoli pacchetti.  Se si prevede di installare l'agente in una VM Linux di Azure, vedere l'argomento seguente [Raccogliere dati sulle macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md) per informazioni su come installare l'agente usando l'estensione VM di Log Analytics.  Eseguire i passaggi della sezione *Abilitare l'estensione macchina virtuale di Log Analytics*.  Per i computer Linux ospitati nell'ambiente in uso, è possibile semplificare il processo di installazione con un metodo di script descritto nell'articolo [Raccogliere dati dai computer Linux ospitati nell'ambiente in uso](log-analytics-quick-collect-linux-computer.md).  

> [!NOTE]
> Mentre i due articoli citati in precedenza sono destinati ai neofiti di Log Analytics che necessitano di iniziare subito a usare il servizio, la procedura di configurazione del computer è importante.  Se è già presente un'area di lavoro e si intende connettere il computer Linux, selezionare un'area di lavoro esistente, se si tratta di una macchina virtuale Linux di Azure, oppure copiare l'ID dell'area di lavoro e la chiave da passare allo script, se si tratta di un computer ospitato all'esterno di Azure.  

Prima di installare l'agente OMS per Linux, sono necessari l'ID e la chiave dell'area di lavoro per l'area di lavoro di Log Analytics.  

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
3. Nell'elenco delle aree di lavoro di Log Analytics selezionare quella che si intende associare al computer.
3. Selezionare **Impostazioni avanzate**.<br><br> ![Impostazioni avanzate di Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selezionare **Origini connesse**, quindi **Server Linux**.   
5. Il valore a destra di **ID area di lavoro** e **Chiave primaria**. Copiare e incollare entrambi i valori nell'editor predefinito.  
6. Scaricare la versione più recente di [agente OMS per Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) o [agente OMS per Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) da GitHub.  
7. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
8. Installare il bundle usando l'argomento `--install` o `--upgrade`. 

    > [!NOTE]
    > Se sono installati pacchetti esistenti, ad esempio quando l'agente System Center Operations Manager per Linux è già installato, usare l'argomento `--upgrade`. Per connettersi a Operations Management Suite durante l'installazione, fornire i parametri `-w <WorkspaceID>` e `-s <Shared Key>`.


#### <a name="to-install-and-onboard-directly"></a>Per eseguire installazione e onboarding direttamente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Per aggiornare il pacchetto dell'agente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Per eseguire installazione e onboarding in un'area di lavoro nel cloud US Government
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>Configurazione dell'agente per l'uso con un server proxy o un gateway OMS
L'agente di Operations Management Suite per Linux supporta la comunicazione tramite un server proxy o un gateway OMS con il servizio Log Analytics usando il protocollo HTTPS.  Sono supportate sia l'autenticazione anonima che quella di base (nome utente/password).  

### <a name="proxy-configuration"></a>Configurazione proxy
Il valore di configurazione proxy ha la sintassi seguente:

`[protocol://][user:password@]proxyhost[:port]`

Proprietà|Descrizione
-|-
Protocol|https
user|Nome utente facoltativo per l'autenticazione proxy
password|Password facoltativa per l'autenticazione proxy
proxyhost|Indirizzo o FQDN del server proxy/gateway OMS
port|Numero di porta facoltativo del server proxy/gateway OMS

Ad esempio: `https://user01:password@proxy01.contoso.com:30443`

È possibile specificare il server proxy durante l'installazione o modificando il file di configurazione proxy.conf dopo l'installazione.   

### <a name="specify-proxy-configuration-during-installation"></a>Specificare la configurazione proxy durante l'installazione
L'argomento `-p` o `--proxy` per il bundle di installazione di omsagent specifica la configurazione proxy da usare. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Definire la configurazione proxy in un file
La configurazione del proxy può essere impostata nei file `/etc/opt/microsoft/omsagent/proxy.conf` e `/etc/opt/microsoft/omsagent/conf/proxy.conf `. I file possono essere creati o modificati direttamente, ma le autorizzazioni devono essere aggiornate per concedere all'utente omiuser l'autorizzazione di lettura per i file. ad esempio:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>Rimozione della configurazione proxy
Per rimuovere una configurazione proxy definita in precedenza e ripristinare la connettività diretta, rimuovere il file proxy.conf:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-log-analytics"></a>Onboarding con Log Analytics
Se non sono stati forniti chiave e ID dell'area di lavoro durante l'installazione del bundle, l'agente dovrà essere successivamente registrato con Log Analytics.

### <a name="onboarding-using-the-command-line"></a>Onboarding usando la riga di comando
Eseguire il comando omsadmin.sh fornendo chiave e ID dell'area di lavoro. Questo comando deve essere eseguito come comando radice (con elevazione sudo):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="register-using-a-file"></a>Registrare con un file
1.  Creare il file `/etc/omsagent-onboard.conf`. Il file deve essere leggibile e scrivibile per la radice.
`sudo vi /etc/omsagent-onboard.conf`
2.  Inserire le righe seguenti nel file con la chiave condivisa e l'ID dell'area di lavoro:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Eseguire questo comando per la registrazione con Log Analytics: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  Il file viene eliminato al completamento dell'onboarding.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Consentire all'agente OMS per Linux di inviare segnalazioni a System Center Operations Manager
Seguire questa procedura per configurare l'agente OMS per Linux per inviare segnalazioni a un gruppo di gestione di System Center Operations Manager.  

1. Modificare il file `/etc/opt/omi/conf/omiserver.conf`
2. Assicurarsi che la riga che inizia con **httpsport=** definisca la porta 1270. Ad esempio: `httpsport=1270`
3. Riavviare il server OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>Log dell'agente
I log per l'agente OMS per Linux sono disponibili in: `/var/opt/microsoft/omsagent/<workspace id>/log/`. I log per il programma omsconfig (configurazione dell'agente) sono disponibili in: `/var/opt/microsoft/omsconfig/log/`. I log per i componenti OMI e SCX (che forniscono i dati delle metriche delle prestazioni) sono disponibili in:`/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>Configurazione di rotazione del log
La configurazione di rotazione del log per omsagent è disponibile in: `/etc/logrotate.d/omsagent-<workspace id>`

Le impostazioni predefinite sono le seguenti: 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>Disinstallazione dell'agente OMS per Linux
I pacchetti dell'agente possono essere disinstallati eseguendo il file SH con l'argomento `--purge`, che rimuove completamente l'agente e la configurazione dal computer.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problema: Impossibile stabilire la connessione tramite proxy a Log Analytics

#### <a name="probable-causes"></a>Possibili cause
* Il proxy specificato durante l'onboarding è errato
* Gli endpoint dei servizi Log Analytics e Automazione di Azure non sono inclusi nell'elenco elementi consentiti nel data center 

#### <a name="resolutions"></a>Soluzioni
1. Eseguire di nuovo l'onboarding nel servizio Log Analytics con l'agente di Operations Management Suite per Linux usando il comando seguente con l'opzione `-v` abilitata. In questo modo l'output dettagliato dell'agente si connette tramite proxy al servizio OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Vedere la sezione [Configurazione dell'agente per l'uso con un server proxy o un gateway OMS](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) per verificare di aver configurato correttamente l'agente per la comunicazione tramite un server proxy.    
* Controllare che gli endpoint di servizio di Log Analytics seguenti siano presenti nell'elenco elementi consentiti:

    |Risorsa agente| Porte |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Porta 443|   
    |*.oms.opinsights.azure.com | Porta 443|   
    |ods.systemcenteradvisor.com | Porta 443|   
    |*.blob.core.windows.net/ | Porta 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Viene visualizzato un errore 403 durante il tentativo di onboarding

#### <a name="probable-causes"></a>Possibili cause
* Data e ora nel server Linux non sono corrette 
* L'ID e la chiave dell'area di lavoro usati non sono corretti

#### <a name="resolution"></a>Risoluzione

1. Controllare l'ora nel server Linux con il comando date. Se l'ora è sfasata di +/- 15 minuti rispetto all'ora corrente, l'onboarding ha esito negativo. Per risolvere il problema, aggiornare la data e/o il fuso orario del server Linux. 
2. Verificare di avere installato la versione più recente dell'agente OMS per Linux.  Ora la versione più recente invia una notifica all'utente se la differenza di tempo causa l'errore di onboarding.
3. Eseguire di nuovo l'onboarding usando l'ID area di lavoro corretto e la chiave dell'area di lavoro seguendo le istruzioni di installazione presentate anteriormente in questo argomento.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Viene visualizzato un errore 404 o 500 nel file di log subito dopo l'onboarding
Si tratta di un problema noto che si verifica durante il primo caricamento dei dati di Linux in un'area di lavoro di Log Analytics. Questo non influisce sui dati inviati o sull'esperienza d'uso del servizio.

### <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: Non vengono visualizzati dati nel portale di Azure

#### <a name="probable-causes"></a>Possibili cause

- L'onboarding nel servizio Log Analytics ha avuto esito negativo
- La connessione al servizio Log Analytics è bloccata
- Viene eseguito il backup dei dati dell'agente OMS per Linux

#### <a name="resolutions"></a>Soluzioni
1. Controllare che l'onboarding del servizio Log Analytics sia avvenuto correttamente verificando la presenza del file seguente: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Eseguire di nuovo l'onboarding usando le istruzioni della riga di comando `omsadmin.sh`
3. Se si usa un proxy, vedere i passaggi di risoluzione del proxy indicati in precedenza.
4. In alcuni casi, quando l'agente di Operations Management Suite per Linux non può comunicare con il servizio, i dati dell'agente vengono inseriti in una coda fino a raggiungere le dimensioni intere del buffer, ovvero 50 MB. L'agente OMS per Linux deve essere riavviato usando il comando seguente: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Il problema è stato risolto nell'agente versione 1.1.0-28 e versioni successive.

### <a name="issue-omsagent-creates-excessive-number-of-user-process-on-computer-and-never-terminates-them"></a>Problema: OMSAgent crea un numero eccessivo di processi utente nel computer e non li termina mai
Quando si abilitano soluzioni di gestione che supportano la gestione delle VM di Linux, avvia un numero di processi nell'agente Linux. Prima che il processo termini, però, un altro processo viene avviato a causa di un problema noto. 

#### <a name="resolutions"></a>Soluzioni
Per modificare il numero di processi utente che possono essere generati da OMSAgent, configurare l'agente usando omsadmin.sh.  Il numero di processi generati per impostazione predefinita è 75. Prima di modificare il limite, è consigliabile eseguire il comando seguente per vedere quanti processi di OMSAgent sono in esecuzione: `ps aux | grep -E '^omsagent' | wc -l`.  
Per verificare il limite attualmente impostato, eseguire il comando seguente: `cat /etc/security/limits.conf | grep -E '^omsagent'`

Usare i comandi seguenti per configurare un limite di processi personalizzati o ripristinare il valore predefinito.

1. Per impostare il limite di processi per OMSAgent: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -n <specific number limit>`.<br>Il limite minimo che può essere impostato è 5.  

2. Per ripristinare il valore predefinito del limite di processi per OMSAgent: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -N`

Verificare che la nuova impostazione sia stata applicata eseguendo il comando seguente: `cat /etc/security/limits.conf | grep -E '^omsagent'`.  Se non la nuova configurazione non è applicata, il limite impostato potrebbe avere un valore troppo basso.  
