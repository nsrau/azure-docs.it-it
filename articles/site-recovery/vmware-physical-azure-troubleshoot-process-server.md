---
title: Risolvere i problemi del server di elaborazione di Microsoft Azure Site Recovery
description: Questo articolo descrive come risolvere i problemi relativi al server di elaborazione di Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 4816b597d66aea3bbe7f834004f924b5108de939
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499756"
---
# <a name="troubleshoot-the-process-server"></a>Risolvere i problemi del server di elaborazione

Il server di elaborazione di [Site Recovery](site-recovery-overview.md) viene usato per configurare il ripristino di emergenza per i server fisici e le macchine virtuali VMware locali. Questo articolo illustra come risolvere i problemi relativi al server di elaborazione, inclusi i problemi di connettività e di replica.

[Altre informazioni](vmware-physical-azure-config-process-server-overview.md) sul server di elaborazione.

## <a name="before-you-start"></a>Prima di iniziare

Prima di avviare la risoluzione dei problemi:

1. Acquisire familiarità con le procedure di [monitoraggio dei server di elaborazione](vmware-physical-azure-monitor-process-server.md).
2. Esaminare le procedure consigliate descritte di seguito.
3. Assicurarsi di seguire le [considerazioni sulla capacità](site-recovery-plan-capacity-vmware.md#capacity-considerations) e usare le linee guida per il ridimensionamento del [server di configurazione](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) o dei [server di elaborazione autonomi](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Procedure consigliate per la distribuzione dei server di elaborazione

Per ottenere prestazioni ottimali dei server di elaborazione, di seguito sono riepilogate alcune procedure consigliate generali.

**Procedura consigliata** | **Dettagli**
--- |---
**Utilizzo** | Verificare che il server di configurazione e il server di elaborazione autonomo vengano usati solo per lo scopo designato. Non eseguire nient'altro sul server.
**Indirizzo IP** | Verificare che il server di elaborazione disponga di un indirizzo IPv4 statico e che NAT non sia configurato sul server.
**Controllare l'utilizzo di memoria e CPU** |Mantenere l'utilizzo di CPU e memoria al di sotto del 70%.
**Verificare lo spazio disponibile** | Lo spazio disponibile si riferisce allo spazio su disco della cache nel server di elaborazione. I dati di replica vengono memorizzati nella cache prima di essere caricati in Azure.<br/><br/> Tenere libero uno spazio superiore al 25%. Se lo spazio disponibile scende sotto il 20%, la replica viene limitata per le macchine virtuali replicate associate al server di elaborazione.

## <a name="check-process-server-health"></a>Controllare l'integrità del server di elaborazione

Il primo passaggio di risoluzione dei problemi consiste nel controllare l'integrità e lo stato del server di elaborazione. A questo scopo occorre esaminare tutti gli avvisi, verificare che i servizi necessari siano in esecuzione e verificare che sia presente un heartbeat dal server di elaborazione. Questi passaggi sono riepilogati nel diagramma seguente, seguiti da procedure per facilitarne l'esecuzione.

![Risolvere i problemi di integrità dei server di elaborazione](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passaggio 1: Risolvere i problemi relativi agli avvisi sull'integrità dei server di elaborazione

Il server di elaborazione genera un certo numero di avvisi di integrità. Questi avvisi, insieme alle azioni consigliate, sono riepilogati nella tabella seguente.

**Tipo di avviso** | **Errore** | **Risolvere i problemi**
--- | --- | --- 
![Healthy][green] | nessuno  | Il server di elaborazione è connesso e integro.
![Avviso][yellow] | I servizi specificati non sono in esecuzione. | 1. Controllare che i servizi siano in esecuzione.<br/> 2. Se i servizi sono in esecuzione come previsto, seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Avviso][yellow]  | Utilizzo della CPU > 80% negli ultimi 15 minuti. | 1. Non aggiungere nuove macchine virtuali.<br/>2. Verificare che il numero di macchine virtuali che usano il server di elaborazione rientri nei [limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations) e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Critico][red] |  Utilizzo della CPU > 95% negli ultimi 15 minuti. | 1. Non aggiungere nuove macchine virtuali.<br/>2. Verificare che il numero di macchine virtuali che usano il server di elaborazione rientri nei [limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations) e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).<br/> 4. Se il problema persiste, eseguire [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) per la replica di macchine virtuali VMware/server fisici.
![Avviso][yellow] | Utilizzo della memoria > 80% negli ultimi 15 minuti. |  1. Non aggiungere nuove macchine virtuali.<br/>2. Verificare che il numero di macchine virtuali che usano il server di elaborazione rientri nei [limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations) e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni associate all'avviso.<br/> 4. Se il problema persiste, seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Critico][red] | Utilizzo della memoria > 95% negli ultimi 15 minuti. | 1. Non aggiungere nuove macchine virtuali e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/> 2. Seguire le istruzioni associate all'avviso.<br/> 3. 4. Se il problema persiste, seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).<br/> 4. Se il problema persiste, eseguire [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) per i problemi di replica di macchine virtuali VMware/server fisici.
![Avviso][yellow] | Spazio disponibile nella cartella cache < 30% negli ultimi 15 minuti. | 1. Non aggiungere nuove macchine virtuali e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>2. Verificare che il numero di VM che usano il server di elaborazione sia allineato alle [linee guida](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Critico][red] |  Spazio disponibile < 25% negli ultimi 15 minuti. | 1. Seguire le istruzioni associate all'avviso relativo al problema.<br/> 2. 3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).<br/> 3. Se il problema persiste, eseguire [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) per la replica di macchine virtuali VMware/server fisici.
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti o più. Il servizio tmansvc non sta comunicando con il server di configurazione. | 1. Verificare che il server di elaborazione sia operativo.<br/> 2. Verificare che il servizio tmansvc sia in esecuzione nel server di elaborazione.<br/> 3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).


![Chiave tabella](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passaggio 2: Controllare i servizi del server di elaborazione

Nella tabella seguente sono riepilogati i servizi che devono essere in esecuzione nel server di elaborazione. Questi servizi variano leggermente in base alla modalità di distribuzione del server di elaborazione. 

Per tutti i servizi tranne l'agente di Servizi di ripristino di Microsoft Azure (obengine), verificare che Tipo di avvio sia impostato su **Automatico** o **Automatico (avvio ritardato)** .
 
**Distribuzione** | **Servizi in esecuzione**
--- | ---
**Server di elaborazione nel server di configurazione** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; servizio di caricamento log (LogUpload); servizio dell'applicazione InMage Scout; agente di Servizi di ripristino di Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; servizio Pubblicazione sul Web (W3SVC); MySQL; servizio Microsoft Azure Site Recovery (dra)
**Server di elaborazione in esecuzione come server autonomo** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; servizio di caricamento log (LogUpload); servizio dell'applicazione InMage Scout; agente di Servizi di ripristino di Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc
**Server di elaborazione distribuito in Azure per il failback** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; servizio di caricamento log (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passaggio 3: Controllare l'heartbeat del server di elaborazione

In assenza di heartbeat dal server di elaborazione (codice errore 806), eseguire le operazioni seguenti:

1. Verificare che la VM del server di elaborazione sia operativa.
2. Verificare la presenza di errori in questi log.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Controllare lo stato di connettività e replica

 Spesso gli errori di replica iniziale e in corso sono causati da problemi di connettività tra le macchine virtuali di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. Questi passaggi sono riepilogati nel diagramma seguente, seguiti da procedure per facilitarne l'esecuzione.

![Diagramma di flusso che illustra i passaggi per risolvere i problemi di connettività e replica.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passaggio 4: Verificare la sincronizzazione dell'ora nella macchina virtuale di origine

Assicurarsi che la data e l'ora di sistema della macchina virtuale replicata siano sincronizzate. [Altre informazioni](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passaggio 5: Verificare il software antivirus nella macchina virtuale di origine

Verificare che nessun software antivirus nella macchina virtuale replicata blocchi Site Recovery. Se si vuole escludere Site Recovery dai programmi antivirus, vedere [questo articolo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passaggio 6: Verificare la connettività dalla macchina virtuale di origine


1. Se necessario, installare il [client Telnet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) nella macchina virtuale di origine. Non usare Ping.
2. Dalla macchina virtuale di origine effettuare il ping del server di elaborazione sulla porta HTTPS con Telnet. Per impostazione predefinita, la porta 9443 è la porta HTTPS per il traffico di replica.

    `telnet <process server IP address> <port>`

3. Verificare se la connessione è riuscita.


**Connettività** | **Dettagli** | **Azione**
--- | --- | ---
**Connessione riuscita** | Telnet mostra una schermata vuota e il server di elaborazione è raggiungibile. | Non è necessaria alcuna azione.
**Connessione non riuscita** | Non è possibile connettersi. | Verificare che la porta in ingresso 9443 sia consentita sul server di elaborazione, ad esempio in presenza di una rete perimetrale. Verificare di nuovo la connettività.
**Connessione parzialmente riuscita** | È possibile connettersi, ma la macchina virtuale di origine segnala che il server di elaborazione è irraggiungibile. | Continuare con la procedura di risoluzione dei problemi successiva.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passaggio 7: Risolvere i problemi relativi a un server di elaborazione non raggiungibile

Se il server di elaborazione non è raggiungibile dalla macchina virtuale di origine, viene visualizzato l'errore 78186. Se l'errore non viene risolto, i punti di ripristino coerenti con l'applicazione e quelli coerenti con l'arresto anomalo del sistema non verranno generati nel modo previsto.

Risolvere i problemi controllando se la macchina virtuale di origine è in grado di raggiungere l'indirizzo IP del server di elaborazione, quindi eseguire lo strumento cxpsclient nella macchina virtuale di origine per verificare la connessione end-to-end.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verificare la connessione IP nel server di elaborazione

Se l'esecuzione di Telnet riesce ma la macchina virtuale di origine segnala che il server di elaborazione non è raggiungibile, verificare se è possibile raggiungere l'indirizzo IP del server di elaborazione.

1. In un Web browser provare a raggiungere l'indirizzo IP https://<PS_IP>:<PS_Porta_Dati>/.
2. Se il controllo indica un errore di certificato HTTPS, è normale. Se si ignora l'errore, si dovrebbe vedere un errore 400 - Richiesta non valida. Questo significa che il server non è in grado di gestire la richiesta del browser e che la connessione HTTPS standard funziona correttamente.
3. Se questo controllo non funziona, prendere nota del messaggio di errore del browser. Ad esempio, un errore 407 indica un problema con l'autenticazione proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Controllare la connessione con cxpsclient

Si può anche eseguire lo strumento cxpsclient per verificare la connessione end-to-end.

1. Eseguire lo strumento nel modo seguente:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Nel server di elaborazione controllare i log generati in queste cartelle:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verificare la presenza di errori di caricamento nei log delle VM di origine (errore 78028)

Se un problema nelle macchine virtuali di origine blocca i caricamenti di dati nel servizio di elaborazione, i punti di ripristino coerenti con l'applicazione e quelli coerenti con l'arresto anomalo del sistema potrebbero non essere generati. 

1. Per risolvere gli errori di caricamento in rete, è possibile cercare gli errori in questo log:

    C:\Programmi (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Usare le restanti procedure descritte in questo articolo per risolvere i problemi di caricamento dei dati.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passaggio 8: Controllare se il server di elaborazione sta eseguendo il push dei dati

Controllare se il server di elaborazione esegue attivamente il push dei dati in Azure.

  1. Nel server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC+ESC).
  2. Selezionare la scheda **Prestazioni** > **Apri Monitoraggio risorse**.
  3. Nella pagina **Monitoraggio risorse** selezionare la scheda **Rete**. In **Processi con attività di rete** controllare se cbengine.exe sta inviando attivamente volumi di dati di grandi dimensioni.

       ![Screenshot che mostra un numero elevato di volumi in processi con attività di rete.](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe non invia una quantità notevole di dati, completare i passaggi illustrati nelle sezioni seguenti.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passaggio 9: Verificare la connessione del server di elaborazione ad Archiviazione BLOB di Azure

1. In Monitoraggio risorse selezionare **cbengine.exe**.
2. In **Connessioni TCP** verificare se vi è connettività tra il server di elaborazione e Archiviazione di Azure.

  ![Screenshot che mostra la connettività tra cbengine.exe e l'URL di archiviazione BLOB di Azure.](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verificare i servizi

Se non vi è connettività tra il server di elaborazione e l'URL di Archiviazione BLOB di Azure, verificare che i servizi siano in esecuzione.

1. Nel Pannello di controllo selezionare **Servizi**.
2. Verificare che i servizi seguenti siano in esecuzione:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Agente di Servizi di ripristino di Microsoft Azure
    - Servizio Microsoft Azure Site Recovery
    - tmansvc

3. Avviare o riavviare qualsiasi servizio che non sia in esecuzione.
4. Verificare che il server di elaborazione sia connesso e raggiungibile. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passaggio 10: Verificare la connessione del server di elaborazione all'indirizzo IP pubblico di Azure

1. Nel server di elaborazione, in **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, aprire il file CBEngineCurr.errlog più recente.
2. Nel file cercare **443** o la stringa **connection attempt failed** (tentativo di connessione non riuscito).

  ![Log degli errori nella cartella Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. In caso di problemi, individuare l'indirizzo IP pubblico di Azure nel file CBEngineCurr.currLog usando la porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Nella riga di comando del server di elaborazione usare Telnet per effettuare il ping dell'indirizzo IP pubblico di Azure.
6. Se la connessione non riesce, seguire la procedura successiva.

## <a name="step-11-check-process-server-firewall-settings"></a>Passaggio 11: Controllare le impostazioni del firewall del server di elaborazione 

Verificare se il firewall basato su indirizzo IP nel server di elaborazione blocca l'accesso.

1. Per le regole del firewall basato su indirizzi IP:

    a) Scaricare l'elenco completo degli [intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Aggiungere gli intervalli di indirizzi IP alla configurazione del firewall per assicurarsi che il firewall consenta le comunicazioni con Azure (e con la porta HTTPS predefinita 443).

    c) Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (area usata per il controllo di accesso e la gestione delle identità).

2. Per i firewall basati su URL, aggiungere gli URL elencati nella tabella seguente per la configurazione del firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Passaggio 12: Verificare le impostazioni proxy del server di elaborazione 

1. Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS. Controllare il valore fornito durante la configurazione del server di configurazione nella chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Verificare che le stesse impostazioni siano usate dall'agente di Azure Site Recovery per l'invio dei dati.

    a) Cercare **Backup di Microsoft Azure**.

    b) Aprire **Backup di Microsoft Azure** e selezionare **Azione** > **Modifica proprietà**.

    c) Nella scheda **Configurazione proxy** l'indirizzo del proxy dovrebbe essere uguale all'indirizzo del proxy che viene visualizzato nelle impostazioni del Registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.

## <a name="step-13-check-bandwidth"></a>Passaggio 13: Controllare la larghezza di banda

Aumentare la larghezza di banda tra il server di elaborazione e Azure e controllare se il problema persiste.


## <a name="next-steps"></a>Passaggi successivi

Per richiedere maggiore assistenza, pubblicare una domanda sulla [pagina delle domande di Domande e risposte per Azure Site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
