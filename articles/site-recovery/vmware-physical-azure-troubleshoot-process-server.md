---
title: Risolvere i problemi relativi al server di processo di Azure Site RecoveryTroubleshoot the Azure Site Recovery process server
description: Questo articolo descrive come risolvere i problemi con il server di processo di Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256861"
---
# <a name="troubleshoot-the-process-server"></a>Risolvere i problemi relativi al server di elaborazione

Il server di elaborazione [di Site Recovery](site-recovery-overview.md) viene usato quando si configura il ripristino di emergenza in Azure per le macchine virtuali VMware locali e i server fisici. In questo articolo viene descritto come risolvere i problemi con il server di elaborazione, inclusi i problemi di replica e connettività.

[Ulteriori informazioni](vmware-physical-azure-config-process-server-overview.md) sul server di elaborazione.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare la risoluzione dei problemi:

1. Assicurarsi di comprendere come monitorare i [server di elaborazione](vmware-physical-azure-monitor-process-server.md).
2. Rivedere le procedure consigliate riportate di seguito.
3. Assicurarsi di seguire [le considerazioni sulla capacità](site-recovery-plan-capacity-vmware.md#capacity-considerations)e utilizzare le indicazioni sul ridimensionamento per il server di [configurazione](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) o i server di [elaborazione autonomi.](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)

## <a name="best-practices-for-process-server-deployment"></a>Procedure consigliate per la distribuzione di server di elaborazione

Per ottenere prestazioni ottimali dei server di elaborazione, sono state riepilogate alcune procedure consigliate generali.

**Procedura consigliata** | **Dettagli**
--- |---
**Utilizzo** | Assicurarsi che il server di configurazione/server di elaborazione autonomo venga utilizzato solo per lo scopo previsto. Non eseguire nient'altro sulla macchina.
**Indirizzo IP** | Assicurarsi che il server di elaborazione abbia un indirizzo IPv4 statico e che non sia configurato NAT.
**Controllare l'utilizzo della memoria/CPU** |Mantenere l'utilizzo della CPU e della memoria al di sotto del 70%.
**Garantire lo spazio libero** | Lo spazio libero si riferisce allo spazio su disco della cache nel server di elaborazione. I dati di replica vengono archiviati nella cache prima di essere caricati in Azure.Replication data is stored in the cache before it's uploaded to Azure.<br/><br/> Mantenere spazio libero superiore al 25%. Se scende al di sotto del 20%, la replica viene limitata per i computer replicati associati al server di elaborazione.

## <a name="check-process-server-health"></a>Controllare l'integrità del server di elaborazione

Il primo passaggio nella risoluzione dei problemi consiste nel verificare l'integrità e lo stato del server di elaborazione. A tale scopo, esaminare tutti gli avvisi, verificare che i servizi necessari siano in esecuzione e verificare che vi sia un heartbeat dal server di elaborazione. Questi passaggi sono riepilogati nel grafico seguente, seguito da procedure che consentono di eseguire i passaggi.

![Risolvere i problemi di integrità del server di elaborazioneTroubleshoot process server health](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passaggio 1: Risolvere i problemi relativi agli avvisi di integrità del server di elaborazioneStep 1: Troubleshoot process server health alerts

Il server di elaborazione genera una serie di avvisi di integrità. Questi avvisi e le azioni consigliate sono riepilogati nella tabella seguente.

**Tipo di avviso** | **Errore** | **Risolvere i problemi**
--- | --- | --- 
![Healthy][green] | nessuno  | Il server di elaborazione è connesso e integro.
![Avviso][yellow] | I servizi specificati non sono in esecuzione. | 1. Verificare che i servizi siano in esecuzione.<br/> 2. Se i servizi vengono eseguiti come previsto, seguire le istruzioni riportate di seguito per risolvere i problemi di [connettività e replica.](#check-connectivity-and-replication)
![Avviso][yellow]  | L'utilizzo della CPU > l'80% negli ultimi 15 minuti. | 1. Non aggiungere nuove macchine.<br/>2. Verificare che il numero di macchine virtuali che utilizzano il server di elaborazione sia allineato [ai limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considerare la possibilità di impostare un server di [elaborazione aggiuntivo.](vmware-azure-set-up-process-server-scale.md)<br/>3. Seguire le istruzioni riportate di seguito per risolvere i problemi di [connettività e replica.](#check-connectivity-and-replication)
![Critico][red] |  L'utilizzo della CPU > al 95% negli ultimi 15 minuti. | 1. Non aggiungere nuove macchine.<br/>2. Verificare che il numero di macchine virtuali che utilizzano il server di elaborazione sia allineato [ai limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considerare la possibilità di impostare un server di [elaborazione aggiuntivo.](vmware-azure-set-up-process-server-scale.md)<br/>3. Seguire le istruzioni riportate di seguito per risolvere i problemi di [connettività e replica.](#check-connectivity-and-replication)<br/> 4. Se il problema persiste, eseguire La replica di [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) for VMware/physical server.
![Avviso][yellow] | Utilizzo della memoria > 80% negli ultimi 15 minuti. |  1. Non aggiungere nuove macchine.<br/>2. Verificare che il numero di macchine virtuali che utilizzano il server di elaborazione sia allineato [ai limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considerare la possibilità di impostare un server di [elaborazione aggiuntivo.](vmware-azure-set-up-process-server-scale.md)<br/>3. Seguire le istruzioni associate all'avviso.<br/> 4. Se il problema persiste, seguire le istruzioni riportate di seguito per risolvere i problemi di [connettività e replica.](#check-connectivity-and-replication)
![Critico][red] | Utilizzo della memoria > 95% negli ultimi 15 minuti. | 1. Non aggiungere nuovi computer e prendere in considerazione la configurazione di un server di [elaborazione aggiuntivo.](vmware-azure-set-up-process-server-scale.md)<br/> 2. Seguire le istruzioni associate all'avviso.<br/> 3. 4. Se il problema persiste, seguire le istruzioni riportate di seguito per risolvere i problemi di [connettività e replica.](#check-connectivity-and-replication)<br/> 4. Se il problema persiste, eseguire [La pianificazione della distribuzione](https://aka.ms/asr-v2a-deployment-planner) per i problemi di replica del server VMware/fisico.
![Avviso][yellow] | Spazio libero cartella cache < 30% negli ultimi 15 minuti. | 1. Non aggiungere nuovi computer e prendere in considerazione la configurazione di un server di [elaborazione aggiuntivo.](vmware-azure-set-up-process-server-scale.md)<br/>2. Verificare che il numero di macchine virtuali che utilizzano il server di elaborazione sia allineato alle [linee guida.](site-recovery-plan-capacity-vmware.md#capacity-considerations)<br/> 3. Seguire le istruzioni riportate di seguito per risolvere i problemi di [connettività e replica.](#check-connectivity-and-replication)
![Critico][red] |  Spazio libero < 25% negli ultimi 15 minuti | 1. Seguire le istruzioni associate all'avviso relativo a questo problema.<br/> 2. 3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e replica.](#check-connectivity-and-replication)<br/> 3. Se il problema persiste, eseguire La replica di [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) for VMware/physical server.
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti o più. Il servizio tmansvs non comunica con il server di configurazione. | 1) Verificare che il server di elaborazione sia in esecuzione.<br/> 2. Verificare che tmassvc sia in esecuzione sul server di elaborazione.<br/> 3. Seguire le istruzioni riportate di seguito per risolvere i problemi di [connettività e replica.](#check-connectivity-and-replication)


![Chiave tabella](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passaggio 2: Controllare i servizi del server di elaborazioneStep 2: Check process server services

I servizi che devono essere in esecuzione nel server di elaborazione sono riepilogati nella tabella seguente. Esistono lievi differenze nei servizi, a seconda della modalità di distribuzione del server di elaborazione. 

Per tutti i servizi ad eccezione di Microsoft Azure Recovery Services Agent (obengine), verificare che StartType sia impostato su **Automatico** o **Automatico (avvio ritardato)**.
 
**Distribuzione** | **Servizi in esecuzione**
--- | ---
**Server di elaborazione nel server di configurazione** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Log Upload Service (LogUpload); Servizio di applicazione InMage Scout; Agente servizi di ripristino di Microsoft Azure (obengine); InMage Scout VX Agente-Sentinel/Avamposto (svagents); tmansvc; Servizio Pubblicazione sul Web (W3SVC); MySQL; Servizio Site Recovery di Microsoft Azure (dra)
**Server di elaborazione in esecuzione come server autonomo** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Log Upload Service (LogUpload); Servizio di applicazione InMage Scout; Agente servizi di ripristino di Microsoft Azure (obengine); InMage Scout VX Agente-Sentinel/Avamposto (svagents); tmansvc.
**Server di processo distribuito in Azure per il failbackProcess server deployed in Azure for failback** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Servizio di caricamento log (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passaggio 3: Controllare l'heartbeat del server di elaborazioneStep 3: Check the process server heartbeat

Se non è presente alcun heartbeat dal server di elaborazione (codice di errore 806), eseguire le operazioni seguenti:

1. Verificare che la macchina virtuale del server di elaborazione sia in esecuzione.
2. Verificare la presenza di errori in questi registri.

    C: .*monitor_protection*. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .

## <a name="check-connectivity-and-replication"></a>Controllare la connettività e la replica

 Gli errori di replica iniziali e in corso sono spesso causati da problemi di connettività tra i computer di origine e il server di elaborazione o tra il server di elaborazione e Azure.Initial and ongoing replication failures are often caused by connectivity issues between source machines and the process server, or between the process server and Azure. Questi passaggi sono riepilogati nel grafico seguente, seguito da procedure che consentono di eseguire i passaggi.

![Risolvere i problemi di connettività e replicaTroubleshoot connectivity and replication](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passaggio 4: Verificare la sincronizzazione dell'ora nel computer di origineStep 4: Verify time sync on source machine

Assicurarsi che la data/ora di sistema per la macchina replicata sia sincronizzata. [Ulteriori informazioni](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passaggio 5: Controllare il software antivirus nel computer di origineStep 5: Check anti-virus software on source machine

Verificare che nessun software antivirus sul computer replicato stia bloccando Site Recovery. Se è necessario escludere Site Recovery dai programmi antivirus, leggere [questo articolo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passaggio 6: Controllare la connettività dal computer di origineStep 6: Check connectivity from source machine


1. Installare il [client Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) nel computer di origine, se necessario. Non usare Ping.
2. Dal computer di origine, eseguire il ping del server di elaborazione sulla porta HTTPS con Telnet. Per impostazione predefinita 9443 è la porta HTTPS per il traffico di replica.

    `telnet <process server IP address> <port>`

3. Verificare se la connessione è stata stabilita correttamente.


**Connettività** | **Dettagli** | **Azione**
--- | --- | ---
**Successo** | Telnet mostra una schermata vuota e il server di elaborazione è raggiungibile. | Non sono necessarie ulteriori azioni.
**Infruttuoso** | Non è possibile connettersi | Assicurarsi che la porta in ingresso 9443 sia consentita nel server di elaborazione. Ad esempio, se si dispone di una rete perimetrale o di una subnet schermata. Controllare nuovamente la connettività.
**Parzialmente riuscito** | È possibile connettersi, ma il computer di origine segnala che il server di elaborazione non è raggiungibile. | Continuare con la procedura di risoluzione dei problemi successiva.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passaggio 7: Risolvere i problemi relativi a un server di elaborazione non raggiungibileStep 7: Troubleshoot an unreachable process server

Se il server di elaborazione non è raggiungibile dal computer di origine, verrà visualizzato l'errore 78186. Se non viene risolto, questo problema comporterà che i punti di ripristino coerenti con l'app e con l'arresto anomalo del sistema non vengano generati come previsto.

Risolvere i problemi controllando se il computer di origine può raggiungere l'indirizzo IP del server di elaborazione ed eseguire lo strumento cxpsclient nel computer di origine per verificare la connessione end-to-end.


### <a name="check-the-ip-connection-on-the-process-server"></a>Controllare la connessione IP sul server di elaborazione

Se telnet ha esito positivo ma il computer di origine segnala che il server di elaborazione non è raggiungibile, verificare se è possibile raggiungere l'indirizzo IP del server di elaborazione.

1. In un browser Web, provare a raggiungere l'indirizzo IP https://<PS_IP>:<PS_Data_Port>/.
2. Se questo controllo mostra un errore del certificato HTTPS, è normale. Se si ignora l'errore, verrà visualizzato un 400 - Richiesta non valida. Ciò significa che il server non può soddisfare la richiesta del browser e che la connessione HTTPS standard va bene.
3. Se questo controllo non funziona, prendere nota del messaggio di errore del browser. Ad esempio, un errore 407 indicherà un problema con l'autenticazione proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Controllare la connessione con cxpsclient

Inoltre, è possibile eseguire lo strumento cxpsclient per controllare la connessione end-to-end.

1. Eseguire lo strumento come segue:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Nel server di elaborazione, controllare i registri generati in queste cartelle:

    C: . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Controllare i log delle macchine virtuali di origine per gli errori di caricamento (errore 78028)Check source VM logs for upload failures (error 78028)

Problema con i caricamenti di dati bloccati dai computer di origine al servizio di processo può causare la generazione di punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app. 

1. Per risolvere gli errori di caricamento di rete, è possibile cercare gli errori in questo log:To troubleshoot network upload failures, you can look for errors in this log:

    File di programma (x86) di Microsoft Azure, agente, agente e svagents, file di dominio. 

2. Utilizzare il resto delle procedure descritte in questo articolo può essere utile per risolvere i problemi di caricamento dei dati.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passaggio 8: Verificare se il server di elaborazione esegue il push dei datiStep 8: Check whether the process server is pushing data

Controllare se il server di elaborazione esegue attivamente il push dei dati in Azure.

  1. Nel server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC+ESC).
  2. Selezionare la scheda **Prestazioni** > **Apri Monitoraggio risorse**.
  3. Nella pagina Monitoraggio risorse selezionare la scheda Rete.In **Resource Monitor** page, select the **Network** tab. In **Processi con attività**di rete verificare se cbengine.exe invia attivamente un grande volume di dati.

       ![Volumi in processi con attività di rete](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe non invia una quantità notevole di dati, completare i passaggi illustrati nelle sezioni seguenti.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passaggio 9: Controllare la connessione del server di elaborazione all'archiviazione BLOB di AzureStep 9: Check the process server connection to Azure blob storage

1. In Monitoraggio risorse selezionare **cbengine.exe**.
2. In Connessioni TCP verificare se è disponibile connettività dal server di elaborazione all'archiviazione di Azure.Under **TCP Connections**, check for whether there is connectivity from the process server to the Azure storage.

  ![Connettività tra cbengine.exe e l'URL di archiviazione BLOB di AzureConnectivity between cbengine.exe and the Azure Blob storage URL](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Controllare i servizi

Se non è disponibile alcuna connettività dal server di elaborazione all'URL di archiviazione BLOB di Azure, verificare che i servizi siano in esecuzione.

1. Nel Pannello di controllo selezionare **Servizi**.
2. Verificare che siano in esecuzione i servizi seguenti:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Agente di Servizi di ripristino di Microsoft Azure
    - Servizio Microsoft Azure Site Recovery
    - tmansvc

3. Avviare o riavviare qualsiasi servizio che non sia in esecuzione.
4. Verificare che il server di elaborazione sia connesso e raggiungibile. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passaggio 10: Controllare la connessione al server di elaborazione all'indirizzo IP pubblico di AzureStep 10: check the process server connection to Azure public IP address

1. Nel server di elaborazione, in **%programfiles%, Agente servizi di ripristino**di Microsoft Azure e Temp , aprire il file CBEngineCurr.errlog più recente.
2. Nel file cercare **443**o il tentativo di connessione della stringa **non è riuscito.**

  ![Registri degli errori nella cartella Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se si verificano problemi, trovare l'indirizzo IP pubblico di Azure nel file CBEngineCurr.currLog utilizzando la porta 443:If you see issues, located your Azure public IP address in the CBEngineCurr.currLog file by using port 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Nella riga di comando nel server di elaborazione usare Telnet per eseguire il ping dell'indirizzo IP pubblico di Azure.At the command line on the process server, use Telnet to ping your Azure public IP address.
6. Se non riesci a connetterti, segui la procedura successiva.

## <a name="step-11-check-process-server-firewall-settings"></a>Passaggio 11: Controllare le impostazioni del firewall del server di elaborazione. 

Verificare se il firewall basato su indirizzo IP sul server di elaborazione blocca l'accesso.

1. Per le regole del firewall basato su indirizzi IP:

    a) Scaricare l'elenco completo degli [intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653)del data center di Microsoft Azure .

    b) Aggiungere gli intervalli di indirizzi IP alla configurazione del firewall, per garantire che il firewall consenta la comunicazione con Azure (e alla porta HTTPS predefinita, 443).

    c) Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per l'area Azure West US (usata per il controllo degli accessi e la gestione delle identità).

2. Per i firewall basati su URL, aggiungere gli URL elencati nella tabella seguente alla configurazione del firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Passaggio 12: Verificare le impostazioni del proxy del server di elaborazioneStep 12: Verify process server proxy settings 

1. Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS. Controllare il valore fornito durante la configurazione del server di configurazione nella chiave del Registro di sistema **HKEY_LOCAL_MACHINE**SOFTWARE.
2. Verificare che le stesse impostazioni vengano usate dall'agente di Azure Site Recovery per inviare dati.

    a) Cercare **Backup di Microsoft Azure**.

    b) Aprire **Backup di Microsoft Azure**e selezionare Proprietà**modifica** **azione** > .

    c) Nella scheda **Configurazione proxy,** l'indirizzo proxy deve essere uguale all'indirizzo proxy visualizzato nelle impostazioni del Registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.

## <a name="step-13-check-bandwidth"></a>Passaggio 13: Controllare la larghezza di bandaStep 13: Check bandwidth

Aumentare la larghezza di banda tra il server di elaborazione e Azure e quindi verificare se il problema persiste.


## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
