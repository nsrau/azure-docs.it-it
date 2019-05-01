---
title: Risolvere i problemi di server di elaborazione di Azure Site Recovery
description: Questo articolo descrive come risolvere i problemi con il server di elaborazione di Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: rayne
ms.openlocfilehash: 926e5b685369f8660daf6221f818734f6f12d2b5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928408"
---
# <a name="troubleshoot-the-process-server"></a>Risolvere i problemi del server di elaborazione

Il [Site Recovery](site-recovery-overview.md) server di elaborazione viene usato quando si configura il ripristino di emergenza in Azure per le macchine virtuali VMware locali e server fisici. Questo articolo descrive come risolvere i problemi con il server di elaborazione, inclusi i problemi di connettività e della replica.

[Altre informazioni](vmware-physical-azure-config-process-server-overview.md) sui server di elaborazione.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare la risoluzione dei problemi:

1. Assicurarsi di comprendere come [monitorare i server di elaborazione](vmware-physical-azure-monitor-process-server.md).
2. Esaminare le procedure consigliate seguenti.
3. Assicurarsi di seguire [considerazioni sulla capacità](site-recovery-plan-capacity-vmware.md#capacity-considerations)e utilizzare le linee guida di ridimensionamento per il [server di configurazione](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) oppure [server di elaborazione autonomi](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Le procedure consigliate per la distribuzione di server di elaborazione

Per prestazioni ottimali del server di elaborazione, è stata riepilogati un numero di procedure consigliate generali.

**Procedure consigliate** | **Dettagli**
--- |---
**Utilizzo** | Assicurarsi che il server di elaborazione/autonoma di server di configurazione vengono usati solo per lo scopo previsto. Non eseguire altre operazioni nel computer.
**Indirizzo IP** | Assicurarsi che il server di elaborazione ha un indirizzo IPv4 statico e non ha configurato NAT.
**Controllare l'utilizzo di memoria/CPU** |Mantenere l'utilizzo di CPU e memoria sotto il 70%.
**Verificare che lo spazio disponibile** | Spazio libero si intende lo spazio su disco della cache nel server di elaborazione. Dati di replica vengono archiviati nella cache prima che venga caricato in Azure.<br/><br/> Mantieni spazio di sopra del 25%. Se tale numero scende sotto il 20%, la replica è limitata per le macchine replicate associati con il server di elaborazione.

## <a name="check-process-server-health"></a>Controllare l'integrità del processo server

Il primo passaggio nella risoluzione dei problemi consiste per controllare l'integrità e lo stato del server di elaborazione. A tale scopo, esaminare tutti gli avvisi, verificare che i servizi necessari sono in esecuzione e verificare che sia presente un heartbeat dal server di elaborazione. Questi passaggi sono riepilogati nella seguente figura, seguito da procedure che consentono di eseguire la procedura.

![Risolvere i problemi di integrità del processo server](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passaggio 1: Risolvere gli avvisi sull'integrità di server di processo

Il server di elaborazione genera un numero di avvisi sull'integrità. Questi avvisi e le azioni consigliate, sono riepilogate nella tabella seguente.

**Tipo di avviso** | **Error (Errore) (Error (Errore)e)** | **Risolvere i problemi**
--- | --- | --- 
![Healthy][green] | Nessuna  | Server di elaborazione sia connesso e integro.
![Avviso][yellow] | Servizi specificati non sono in esecuzione. | 1. Verificare che i servizi siano in esecuzione.<br/> 2. Se i servizi sono eseguiti come previsto, seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).
![Avviso][yellow]  | CPU utilizzo > 80% negli ultimi 15 minuti. | 1. Non aggiungere nuovi computer.<br/>2. Verificare che il numero di macchine virtuali con il server di elaborazione viene allineato al [definiti limiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e provare a configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).
![Critico][red] |  CPU utilizzo > 95% negli ultimi 15 minuti. | 1. Non aggiungere nuovi computer.<br/>2. Verificare che il numero di macchine virtuali con il server di elaborazione viene allineato al [definiti limiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e provare a configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).<br/> 4. Se il problema persiste, eseguire la [Deployment Planner](http://aka.ms/asr-v2a-deployment-planner) per la replica di server VMware/computer fisici.
![Avviso][yellow] | Memoria utilizzo > 80% negli ultimi 15 minuti. |  1. Non aggiungere nuovi computer.<br/>2. Verificare che il numero di macchine virtuali con il server di elaborazione viene allineato al [definiti limiti](site-recovery-plan-capacity-vmware.md#capacity-considerations)e provare a configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>3. Seguire le istruzioni associate all'avviso.<br/> 4. Se il problema persiste, seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).
![Critico][red] | Memoria utilizzo > 95% per gli ultimi 15 minuti. | 1. Non aggiungere nuovi computer e prendere in considerazione configurazione di un' [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/> 2. Seguire le istruzioni associate all'avviso.<br/> 3. 4. Se il problema persiste, seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).<br/> 4. Se il problema persiste, eseguire la [Deployment Planner](http://aka.ms/asr-v2a-deployment-planner) per problemi di replica di server VMware/computer fisici.
![Avviso][yellow] | Cartella della cache spazio < 30% negli ultimi 15 minuti. | 1. Non aggiungere nuovi computer e provare a configurare un [server di elaborazione aggiuntivo](vmware-azure-set-up-process-server-scale.md).<br/>2. Verificare che il numero di macchine virtuali con il server di elaborazione viene allineato al [linee guida](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).
![Critico][red] |  Spazio libero < 25% per ultimi 15 minuti | 1. Seguire le istruzioni associate all'avviso per risolvere questo problema.<br/> 2. 3. Seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).<br/> 3. Se il problema persiste, eseguire la [Deployment Planner](http://aka.ms/asr-v2a-deployment-planner) per la replica di server VMware/computer fisici.
![Critico][red] | Nessun heartbeat dal server di elaborazione per 15 minuti o più. Il servizio tmansvs non comunica con il server di configurazione. | 1) verificare che il server di elaborazione è in esecuzione.<br/> 2. Verificare che il tmassvc sia in esecuzione nel server di elaborazione.<br/> 3. Seguire le istruzioni seguenti per [risolvere i problemi di connettività e della replica](#check-connectivity-and-replication).


![Chiave della tabella](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passaggio 2: Controllare i servizi del server di elaborazione

Nella tabella seguente sono riepilogati i servizi che devono essere in esecuzione nel server di elaborazione. Esistono piccole differenze nei servizi, a seconda del modo in cui viene distribuito il server di elaborazione. 

Per tutti i servizi, ad eccezione di Microsoft Azure Recovery Services Agent (obengine), verificare che sia impostato il StartType **automatici** oppure **automatico (avvio ritardato)**.
 
**Distribuzione** | **Servizi in esecuzione**
--- | ---
**Server di elaborazione nel server di configurazione** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Servizio di caricamento del log (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; World Wide Web (W3SVC); servizio di pubblicazione MySQL; Servizio Microsoft Azure Site Recovery (dra)
**Server di elaborazione in esecuzione come server autonomo** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Servizio di caricamento del log (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Server di elaborazione distribuito in Azure per il failback** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Servizio di caricamento del log (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passaggio 3: Controllo dell'heartbeat server di processo

Se è presente nessun heartbeat dal server di elaborazione (codice di errore da 806), eseguire le operazioni seguenti:

1. Verificare che il server di elaborazione della macchina virtuale sia in esecuzione.
2. Controllare i log degli errori.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Verificare la connettività e la replica

 Gli errori di replica iniziale e continuativa sono spesso causati da problemi di connettività tra macchine virtuali di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. Questi passaggi sono riepilogati nella seguente figura, seguito da procedure che consentono di eseguire la procedura.

![Risolvere i problemi di connettività e della replica](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passaggio 4: Verificare la sincronizzazione dell'ora nel computer di origine

Assicurarsi che la data/ora di sistema per il computer replicato è sincronizzata. [Altre informazioni](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passaggio 5: Controllare il software antivirus nel computer di origine

Verificare che nessun software antivirus sul computer replicati sia bloccate da Site Recovery. Se è necessario escludere Site Recovery da programmi antivirus, esaminare [questo articolo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passaggio 6: Verificare la connettività dal computer di origine


1. Installare il [client Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) nella macchina di origine se è necessario. Non usare il comando Ping.
2. Dalla macchina di origine, eseguire il ping del server di elaborazione sulla porta HTTPS con Telnet. Per impostazione predefinita 9443 è la porta HTTPS per il traffico di replica.

    `telnet <process server IP address> <port>`

3. Verificare se la connessione ha esito positivo.


**Connettività** | **Dettagli** | **Azione**
--- | --- | ---
**Successful** | Telnet Mostra una schermata vuota e il server di elaborazione è raggiungibile. | Richiesta alcuna ulteriore azione.
**Unsuccessful** | Non è possibile connettersi | Assicurarsi che sia consentita porta in ingresso 9443 nel server di elaborazione. Ad esempio, se si dispone di una rete perimetrale o in una rete perimetrale. Verificare la connettività anche in questo caso.
**Completato parzialmente** | È possibile connettersi, ma la macchina di origine segnala che il server di elaborazione non può essere raggiunto. | Continuare con la successiva procedura di risoluzione dei problemi.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passaggio 7: Risolvere i problemi di un server di elaborazione non raggiungibile

Se il server di elaborazione non è raggiungibile dalla macchina di origine, verrà visualizzato l'errore 78186. Se non risolti, si verificherà questo problema sia coerente con l'app e i punti di ripristino coerenti con l'arresto anomalo del sistema non vengono generati come previsto.

Risoluzione dei problemi, controllare se la macchina di origine possibile raggiungere l'indirizzo IP del server di elaborazione ed eseguire lo strumento cxpsclient nella macchina di origine, per verificare la connessione end-to-end.


### <a name="check-the-ip-connection-on-the-process-server"></a>Controllare la connessione IP nel server di elaborazione

Se telnet ha esito positivo, ma la macchina di origine indica che il server di elaborazione non può essere raggiunto, verificare se è possibile raggiungere l'indirizzo IP del server di elaborazione.

1. In un web browser, provare a raggiungere https://<PS_IP>:<PS_Data_Port>/ indirizzo IP.
2. Se questo controllo viene visualizzato un errore di certificato HTTPS, che è normale. Se si ignora l'errore, si dovrebbe vedere un 400 - richiesta non valida. Ciò significa che il server non può essere utilizzato nella richiesta del browser e che la connessione HTTPS standard sia accurata.
3. Se questo controllo non funziona, quindi prendere nota il messaggio di errore del browser. Ad esempio, un errore 407 indicherà un problema con l'autenticazione del proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Controllare la connessione con cxpsclient

Inoltre, è possibile eseguire lo strumento cxpsclient per verificare la connessione end-to-end.

1. Eseguire lo strumento come segue:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Nel server di elaborazione, controllare i log generati in queste cartelle:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Controllare i log di macchina virtuale di origine per caricare gli errori (errore 78028)

Problema di operazioni di caricamento dati bloccato dai computer di origine per il servizio processo può comportare entrambi i punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app non vengono generati. 

1. Per risolvere gli errori di caricamento di rete, è possibile cercare gli errori in questo file di registro:

    C:\Programmi\Microsoft file (x86) \Microsoft Azure Site Recovery\agent\svagents*.log 

2. Usare il resto delle procedure riportate in questo articolo può aiutare a risolvere i problemi di caricamento dati.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passaggio 8: Controllare se il server di elaborazione effettua il push dei dati

Controllare se il server di elaborazione è attivamente il push dei dati in Azure.

  1. Nel server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC+ESC).
  2. Selezionare il **Performance** tab > **Apri Monitoraggio risorse**.
  3. Nelle **Monitor risorse** pagina, selezionare la **rete** scheda. Sotto **processi con attività rete**, controllare se cbengine.exe sta inviando attivamente un vNotolume elevato dei dati.

       ![Volumi con i processi con attività di rete](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe non invia una quantità notevole di dati, completare i passaggi illustrati nelle sezioni seguenti.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passaggio 9: Verificare la connessione al processo server archiviazione blob di Azure

1. In Monitoraggio di risorse, selezionare **cbengine.exe**.
2. Sotto **le connessioni TCP**, verificare se vi sia connettività tra il server di elaborazione per l'archiviazione di Azure.

  ![Connettività tra cbengine.exe e l'URL di archiviazione Blob di Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verifica servizi

Se è presente alcuna connettività tra il server di elaborazione per l'URL di archiviazione blob di Azure, verificare che i servizi siano in esecuzione.

1. Nel Pannello di controllo, selezionare **Services**.
2. Verificare che i servizi seguenti siano in esecuzione:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Agente di Servizi di ripristino di Microsoft Azure
    - Servizio Microsoft Azure Site Recovery
    - tmansvc

3. Avviare o riavviare qualsiasi servizio che non sia in esecuzione.
4. Verificare che il server di elaborazione sia connesso e raggiungibile. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passaggio 10: verificare la connessione server di processo e indirizzo IP pubblico di Azure

1. Nel server di elaborazione, in **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, aprire il file più recente Cbenginecurr.
2. Nel file, cercare **443**, o per la stringa **tentativo di connessione non è stato possibile**.

  ![Log degli errori nella cartella Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se viene visualizzato problemi, che si trova l'indirizzo IP pubblico di Azure nel file Cbenginecurr file usando la porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Nella riga di comando nel server di elaborazione, usare Telnet per effettuare il ping di indirizzo IP pubblico di Azure.
6. Se non è possibile connettersi, seguire la procedura successiva.

## <a name="step-11-check-process-server-firewall-settings"></a>Passaggio 11: Controllare le impostazioni del processo server firewall. 

Controllare se il firewall basato su indirizzi IP nel server di elaborazione sta bloccando l'accesso.

1. Le regole del firewall basate sull'indirizzo IP:

    a) l'elenco completo dei download [intervalli IP dei data center Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) aggiungere gli intervalli di indirizzi IP alla configurazione del firewall, per garantire che il firewall consenta le comunicazioni in Azure (e alla porta HTTPS predefinito 443).

    c) Consenti intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per l'area di Azure Stati Uniti Occidentali (usati per la gestione di identità e controllo di accesso).

2. Per i firewall basato su URL e aggiungere gli URL elencati nella tabella seguente per la configurazione del firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Passaggio 12: Verificare le impostazioni di proxy server di processo 

1. Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS. Controllare il valore fornito quando si configura il server di configurazione nella chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Assicurarsi che le stesse impostazioni vengono usate dall'agente di Azure Site Recovery per l'invio dei dati.

    a) cercare **Backup di Microsoft Azure**.

    b) aprire **Backup di Microsoft Azure**e selezionare **azione** > **modificare le proprietà**.

    c) nella **configurazione del Proxy** scheda, l'indirizzo del proxy deve essere lo stesso indirizzo proxy che viene visualizzato nelle impostazioni del Registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.

## <a name="step-13-check-bandwidth"></a>Passaggio 13: Controllare la larghezza di banda

Aumentare la larghezza di banda tra il server di elaborazione e Azure e quindi verificare se il problema persiste.


## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png