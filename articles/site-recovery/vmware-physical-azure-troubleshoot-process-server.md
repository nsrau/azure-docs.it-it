---
title: Risolvere i problemi relativi al server di elaborazione Azure Site Recovery
description: Questo articolo descrive come risolvere i problemi relativi al server di elaborazione Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813417"
---
# <a name="troubleshoot-the-process-server"></a>Risolvere i problemi del server di elaborazione

Il server di elaborazione [Site Recovery](site-recovery-overview.md) viene usato quando si configura il ripristino di emergenza in Azure per le macchine virtuali VMware e i server fisici locali. Questo articolo descrive come risolvere i problemi relativi al server di elaborazione, inclusi i problemi di connettività e di replica.

[Altre](vmware-physical-azure-config-process-server-overview.md) informazioni sul server di elaborazione.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare la risoluzione dei problemi:

1. Assicurarsi di comprendere come monitorare i [server di elaborazione](vmware-physical-azure-monitor-process-server.md).
2. Esaminare le procedure consigliate seguenti.
3. Assicurarsi di seguire le [considerazioni sulla capacità](site-recovery-plan-capacity-vmware.md#capacity-considerations)e usare le linee guida per il ridimensionamento per il [server di configurazione](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) o i [server di elaborazione autonomi](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Procedure consigliate per la distribuzione del server di elaborazione

Per prestazioni ottimali dei server di elaborazione, abbiamo riepilogato alcune procedure consigliate generali.

**Procedura consigliata** | **Dettagli**
--- |---
**Utilizzo** | Verificare che il server di configurazione/server di elaborazione autonomo vengano utilizzati solo per lo scopo designato. Non eseguire altre operazioni nel computer.
**Indirizzo IP** | Verificare che il server di elaborazione disponga di un indirizzo IPv4 statico e che non sia stato configurato NAT.
**Controllare l'utilizzo della memoria/CPU** |Mantieni l'utilizzo della CPU e della memoria inferiore al 70%.
**Verificare lo spazio disponibile** | Lo spazio disponibile si riferisce allo spazio su disco della cache nel server di elaborazione. I dati di replica vengono archiviati nella cache prima che vengano caricati in Azure.<br/><br/> Conserva lo spazio disponibile oltre il 25%. Se scende al di sotto del 20%, la replica viene limitata per i computer replicati associati al server di elaborazione.

## <a name="check-process-server-health"></a>Controllare l'integrità del server di elaborazione

Il primo passaggio nella risoluzione dei problemi consiste nel controllare l'integrità e lo stato del server di elaborazione. A tale scopo, esaminare tutti gli avvisi, verificare che i servizi necessari siano in esecuzione e verificare che sia presente un heartbeat dal server di elaborazione. Questi passaggi sono riepilogati nell'immagine seguente, seguiti da procedure che consentono di eseguire la procedura.

![Risolvere i problemi di integrità del server di elaborazione](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passaggio 1: Risolvere gli avvisi di integrità del server di elaborazione

Il server di elaborazione genera un certo numero di avvisi sull'integrità. Questi avvisi e le azioni consigliate sono riepilogati nella tabella seguente.

**Tipo di avviso** | **Error (Errore) (Error (Errore)e)** | **Risolvere i problemi**
--- | --- | --- 
![Integra][green] | Nessuna  | Il server di elaborazione è connesso e integro.
![Avviso][yellow] | I servizi specificati non sono in esecuzione. | 1. Verificare che i servizi siano in esecuzione.<br/> 2. Se i servizi vengono eseguiti come previsto, seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Avviso][yellow]  | L'utilizzo della CPU > 80% per gli ultimi 15 minuti. | 1. Non aggiungere nuovi computer.<br/>2. Verificare che il numero di macchine virtuali che usano il server di elaborazione sia allineato ai [limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Critico][red] |  L'utilizzo della CPU > 95% per gli ultimi 15 minuti. | 1. Non aggiungere nuovi computer.<br/>2. Verificare che il numero di macchine virtuali che usano il server di elaborazione sia allineato ai [limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).<br/> 4. Se il problema persiste, eseguire il [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) per la replica di VMware/server fisici.
![Avviso][yellow] | Utilizzo memoria > 80% per gli ultimi 15 minuti. |  1. Non aggiungere nuovi computer.<br/>2. Verificare che il numero di macchine virtuali che usano il server di elaborazione sia allineato ai [limiti definiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considerare la possibilità di configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni associate all'avviso.<br/> 4. Se il problema persiste, seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Critico][red] | Utilizzo memoria > 95% per gli ultimi 15 minuti. | 1. Non aggiungere nuovi computer e considerare la configurazione di un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/> 2. Seguire le istruzioni associate all'avviso.<br/> 3. 4. Se il problema persiste, seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).<br/> 4. Se il problema persiste, eseguire il [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) per i problemi di replica di VMware/server fisici.
![Avviso][yellow] | Spazio disponibile cartella cache < 30% per gli ultimi 15 minuti. | 1. Non aggiungere nuovi computer e prendere in considerazione la configurazione di un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>2. Verificare che il numero di macchine virtuali che usano il server di elaborazione sia allineato alle [linee guida](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).
![Critico][red] |  Spazio disponibile < 25% per gli ultimi 15 minuti | 1. Seguire le istruzioni associate all'avviso relativo a questo problema.<br/> 2. 3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).<br/> 3. Se il problema persiste, eseguire il [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) per la replica di VMware/server fisici.
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti o più. Il servizio tmansvs non comunica con il server di configurazione. | 1) verificare che il server di elaborazione sia in esecuzione.<br/> 2. Verificare che tmassvc sia in esecuzione nel server di elaborazione.<br/> 3. Seguire le istruzioni riportate di seguito per [risolvere i problemi di connettività e di replica](#check-connectivity-and-replication).


![Chiave tabella](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passaggio 2: Controllare i servizi del server di elaborazione

I servizi che devono essere in esecuzione nel server di elaborazione sono riepilogati nella tabella seguente. Esistono piccole differenze nei servizi, a seconda della modalità di distribuzione del server di elaborazione. 

Per tutti i servizi ad eccezione dell'agente Servizi di ripristino di Microsoft Azure (obengine), verificare che StartType sia impostato su **automatico** o **automatico (avvio ritardato)** .
 
**Distribuzione** | **Esecuzione di servizi**
--- | ---
**Server di elaborazione nel server di configurazione** | ProcessServer ProcessServerMonitor; cxprocessserver Inmage PushInstall; Servizio di caricamento log (LogUpload); Servizio dell'applicazione Inmage Scout; Agente di Servizi di ripristino di Microsoft Azure (obengine); Inmage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc Servizio di pubblicazione World Wide Web (W3SVC); MySQL Servizio di Microsoft Azure Site Recovery (DRA)
**Server di elaborazione in esecuzione come server autonomo** | ProcessServer ProcessServerMonitor; cxprocessserver Inmage PushInstall; Servizio di caricamento log (LogUpload); Servizio dell'applicazione Inmage Scout; Agente di Servizi di ripristino di Microsoft Azure (obengine); Inmage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Server di elaborazione distribuito in Azure per il failback** | ProcessServer ProcessServerMonitor; cxprocessserver Inmage PushInstall; Servizio di caricamento log (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passaggio 3: Controllare l'heartbeat del server di elaborazione

Se non è presente alcun heartbeat dal server di elaborazione (codice errore 806), eseguire le operazioni seguenti:

1. Verificare che la macchina virtuale del server di elaborazione sia attiva e in esecuzione.
2. Verificare la presenza di errori nei log.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log C\ProgramData\ASR\home\svsystems\monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>Controllare la connettività e la replica

 Gli errori di replica iniziali e in corso sono spesso causati da problemi di connettività tra i computer di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. Questi passaggi sono riepilogati nell'immagine seguente, seguiti da procedure che consentono di eseguire la procedura.

![Risolvere i problemi di connettività e replica](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passaggio 4: Verificare la sincronizzazione dell'ora nel computer di origine

Assicurarsi che la data e l'ora di sistema per il computer replicato siano sincronizzate. [Altre informazioni](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passaggio 5: Verificare il software antivirus nel computer di origine

Verificare che nessun software antivirus nel computer replicato stia bloccando Site Recovery. Se è necessario escludere Site Recovery da programmi antivirus, vedere [questo articolo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passaggio 6: Verifica la connettività dal computer di origine


1. Se necessario, installare il [client Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) nella macchina di origine. Non usare ping.
2. Dal computer di origine, effettuare il ping del server di elaborazione sulla porta HTTPS con Telnet. Per impostazione predefinita, 9443 è la porta HTTPS per il traffico di replica.

    `telnet <process server IP address> <port>`

3. Verificare se la connessione ha avuto esito positivo.


**Connettività** | **Dettagli** | **Azione**
--- | --- | ---
**Successful** | Telnet Mostra una schermata vuota e il server di elaborazione è raggiungibile. | Non sono necessarie altre azioni.
**Infruttuoso** | Non è possibile connettersi | Verificare che la porta in ingresso 9443 sia consentita nel server di elaborazione. Ad esempio, se si dispone di una rete perimetrale o di una subnet schermata. Verificare di nuovo la connettività.
**Completato parzialmente** | È possibile connettersi, ma il computer di origine segnala che non è possibile raggiungere il server di elaborazione. | Continuare con la procedura successiva per la risoluzione dei problemi.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passaggio 7: Risolvere i problemi relativi a un server di elaborazione non raggiungibile

Se il server di elaborazione non è raggiungibile dalla macchina di origine, verrà visualizzato l'errore 78186. Se non viene risolto, questo problema causerà la generazione di punti di ripristino coerenti con l'app e di arresto anomalo del sistema.

Risolvere i problemi controllando se il computer di origine è in grado di raggiungere l'indirizzo IP del server di elaborazione ed eseguire lo strumento cxpsclient nella macchina di origine per verificare la connessione end-to-end.


### <a name="check-the-ip-connection-on-the-process-server"></a>Controllare la connessione IP nel server di elaborazione

Se Telnet ha esito positivo ma il computer di origine segnala che non è possibile raggiungere il server di elaborazione, verificare se è possibile raggiungere l'indirizzo IP del server di elaborazione.

1. In un Web browser provare a raggiungere l'indirizzo IP HTTPS://< PS_IP >: < PS_Data_Port >/.
2. Se questo controllo Mostra un errore di certificato HTTPS, questo è normale. Se si ignora l'errore, verrà visualizzata una richiesta 400-non valida. Ciò significa che il server non può gestire la richiesta del browser e che la connessione HTTPS standard è corretta.
3. Se il controllo non funziona, prendere nota del messaggio di errore del browser. Ad esempio, un errore 407 indicherà un problema con l'autenticazione proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Verificare la connessione con cxpsclient

Inoltre, è possibile eseguire lo strumento cxpsclient per verificare la connessione end-to-end.

1. Eseguire lo strumento come segue:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Nel server di elaborazione, controllare i log generati in queste cartelle:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Controllare i log delle VM di origine per gli errori di caricamento (errore 78028)

Il problema del caricamento dei dati bloccato dalle macchine di origine al servizio di elaborazione può comportare la generazione di punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app. 

1. Per risolvere gli errori di caricamento di rete, è possibile cercare gli errori in questo log:

    C:\Programmi (x86) \Microsoft Azure site Recovery\agent\svagents *. log 

2. Usare le altre procedure descritte in questo articolo consente di risolvere i problemi di caricamento dei dati.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passaggio 8: Controllare se il server di elaborazione sta eseguendo il push dei dati

Controllare se il server di elaborazione sta attivamente eseguendo il push dei dati in Azure.

  1. Nel server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC+ESC).
  2. Selezionare la scheda **prestazioni** > **aprire Monitoraggio risorse**.
  3. Nella pagina **monitoraggio risorse** selezionare la scheda **rete** . In **processi con attività di rete**controllare se cbengine. exe sta inviando attivamente un volume elevato di dati.

       ![Volumi in processi con attività di rete](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe non invia una quantità notevole di dati, completare i passaggi illustrati nelle sezioni seguenti.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passaggio 9: Controllare la connessione del server di elaborazione all'archivio BLOB di Azure

1. In Monitoraggio risorse selezionare **cbengine. exe**.
2. In **connessioni TCP**verificare se è presente la connettività tra il server di elaborazione e l'archiviazione di Azure.

  ![Connettività tra cbengine. exe e l'URL di archiviazione BLOB di Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Controllare i servizi

Se non esiste connettività dal server di elaborazione all'URL di archiviazione BLOB di Azure, verificare che i servizi siano in esecuzione.

1. Nel pannello di controllo selezionare **Servizi**.
2. Verificare che siano in esecuzione i seguenti servizi:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Agente di Servizi di ripristino di Microsoft Azure
    - Servizio Microsoft Azure Site Recovery
    - tmansvc

3. Avviare o riavviare qualsiasi servizio che non sia in esecuzione.
4. Verificare che il server di elaborazione sia connesso e raggiungibile. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passaggio 10: controllare la connessione del server di elaborazione all'indirizzo IP pubblico di Azure

1. Nel server di elaborazione, in **%Programmi%\Microsoft Azure Recovery Services Agent\Temp**, aprire il file CBEngineCurr. errlog più recente.
2. Nel file cercare **443**o per il tentativo di connessione alla stringa **non riuscito**.

  ![Log degli errori nella cartella Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se si verificano problemi, si trova l'indirizzo IP pubblico di Azure nel file CBEngineCurr. currLog usando la porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Nella riga di comando del server di elaborazione usare Telnet per effettuare il ping dell'indirizzo IP pubblico di Azure.
6. Se non è possibile connettersi, attenersi alla procedura successiva.

## <a name="step-11-check-process-server-firewall-settings"></a>Passaggio 11: Controllare le impostazioni del firewall del server di elaborazione. 

Verificare che il firewall basato su indirizzi IP nel server di elaborazione blocchi l'accesso.

1. Per le regole del firewall basate su indirizzi IP:

    a) scaricare l'elenco completo di [Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653).

    b) aggiungere gli intervalli di indirizzi IP alla configurazione del firewall, per assicurarsi che il firewall consenta la comunicazione con Azure (e la porta HTTPS predefinita, 443).

    c) consente gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per l'area degli Stati Uniti occidentali di Azure (usata per il controllo di accesso e la gestione delle identità).

2. Per i firewall basati su URL, aggiungere gli URL elencati nella tabella seguente alla configurazione del firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Passaggio 12: Verificare le impostazioni del proxy del server di elaborazione 

1. Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS. Controllare il valore specificato quando si configura il server di configurazione nella chiave del registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure sito Recovery\ProxySettings**.
2. Assicurarsi che le stesse impostazioni vengano usate dall'agente di Azure Site Recovery per inviare i dati.

    a) cercare **backup di Microsoft Azure**.

    b) aprire **backup di Microsoft Azure**e selezionare l' **azione** > **modifica proprietà**.

    c) nella scheda **configurazione proxy** , l'indirizzo proxy deve corrispondere all'indirizzo proxy visualizzato nelle impostazioni del registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.

## <a name="step-13-check-bandwidth"></a>Passaggio 13: Controllare la larghezza di banda

Aumentare la larghezza di banda tra il server di elaborazione e Azure, quindi controllare se il problema si verifica ancora.


## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
