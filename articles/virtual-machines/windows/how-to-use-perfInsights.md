---
title: Come usare PerfInsights in Microsoft Azure| Microsoft Docs
description: Informazioni su come usare PerfInsights per risolvere i problemi delle prestazioni delle VM Windows.
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/18/2017
ms.author: genli
ms.openlocfilehash: 8d66bbdf6f7153cf59af60051e54377f6eccdc3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-perfinsights"></a>Come usare PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) è uno script automatizzato che raccoglie utili informazioni di diagnostica, esegue carichi con stress di I/O e fornisce un report di analisi per risolvere i problemi di prestazioni delle VM Windows in Microsoft Azure. Può essere eseguito sulle macchine virtuali come script autonomo o direttamente dal portale installando l'[estensione della VM Diagnostica prestazioni di Azure](performance-diagnostics-vm-extension.md).

È consigliabile aprire questo script prima di aprire un ticket di supporto con Microsoft per problemi di prestazioni delle VM.

## <a name="supported-troubleshooting-scenarios"></a>Scenari per la risoluzione dei problemi supportati

PerfInsights può raccogliere e analizzare diversi tipi di informazioni che vengono raggruppate in scenari univoci.

### <a name="collect-disk-configuration"></a>Raccogliere la configurazione dei dischi 

Questo scenario raccoglie la configurazione dei dischi e altre importanti informazioni, inclusi gli elementi seguenti:

-   Log eventi

-   Stato della rete per tutte le connessioni in ingresso e in uscita

-   Impostazioni di configurazione della rete e del firewall

-   Elenco di attività per tutte le applicazioni attualmente in esecuzione nel sistema

-   File di informazioni creato da msinfo32 per la macchina virtuale (VM)

-   Impostazioni di configurazione del database di Microsoft SQL Server (se la VM viene identificata come server che esegue SQL Server)

-   Contatori di affidabilità di archiviazione

-   Importanti hotfix di Windows

-   Driver di filtro installati

Si tratta di una raccolta passiva di informazioni che non influirà sul sistema. 

>[!Note]
>Questo scenario viene automaticamente incluso in tutti gli scenari seguenti.

### <a name="benchmarkstorage-performance-test"></a>Test delle prestazioni dell'archiviazione o di benchmark

Questo scenario esegue il test di benchmark [diskspd](https://github.com/Microsoft/diskspd) (operazioni di I/O al secondo e Mbps) per tutte le unità collegate alla VM. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della VM.
>

### <a name="general-vm-slow-analysis"></a>Analisi generale della VM lenta 

Questo scenario esegue una traccia dei [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando i contatori specificati nel file Generalcounters.txt. Se la VM viene identificata come server che esegue SQL Server, esegue una traccia dei contatori delle prestazioni usando i contatori presenti nel file Sqlcounters.txt. Include anche i dati di diagnostica delle prestazioni.

### <a name="vm-slow-analysis-and-benchmark"></a>Analisi della VM lenta e benchmark

Questo scenario esegue una traccia dei [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) seguita da un test di benchmark [diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della VM.
>

### <a name="azure-files-analysis"></a>Analisi di File di Azure 

Questo scenario esegue una speciale acquisizione dei contatori delle prestazioni insieme a una traccia di rete. L'acquisizione include tutti i contatori "Condivisioni client SMB". I seguenti sono alcuni dei principali contatori delle prestazioni Condivisioni client SMB che fanno parte dell'acquisizione:

| **Tipo**     | **Contatore Condivisioni client SMB** |
|--------------|-------------------------------|
| IOPS         | Richieste dati/sec             |
|              | Richieste di lettura/sec             |
|              | Richieste di scrittura/sec            |
| Latency      | Media secondi/richiesta dati         |
|              | Media secondi/lettura                 |
|              | Media secondi/scrittura                |
| Dimensioni I/O      | Avg. Bytes/Data Request (Media byte/richiesta dati)       |
|              | Media byte/Lettura               |
|              | Media byte/Scrittura              |
| Velocità effettiva   | Byte dati/sec                |
|              | Byte letti/sec                |
|              | Byte scritti/sec               |
| Lunghezza coda | Lunghezza media coda lettura        |
|              | Lunghezza media coda scrittura       |
|              | Lunghezza media coda dati        |

### <a name="custom-configuration"></a>Configurazione personalizzata 

Quando si esegue una configurazione personalizzata, si eseguono tutte le tracce (diagnostica delle prestazioni, contatore delle prestazioni, Xperf, rete, StorPort) in parallelo, a seconda del numero di tracce diverse selezionate. Al termine dell'analisi, lo strumento esegue il benchmark diskspd, se selezionato. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della VM.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Tipo di informazioni raccolte dallo script

Vengono raccolte informazioni sulla VM Windows, sulla configurazione dei dischi o dei pool di archiviazione, sui contatori delle prestazioni, sui log e su diverse tracce a seconda dello scenario delle prestazioni usato:

|Dati raccolti                              |  |  | Scenari delle prestazioni |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Raccogliere la configurazione dei dischi | Test delle prestazioni dell'archiviazione o di benchmark | Analisi generale della VM lenta | Analisi della VM lenta e benchmark | Analisi di File di Azure | Configurazione personalizzata |
| Informazioni dai log eventi      | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni di sistema               | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Mapping del volume                       | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Mapping del disco                         | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Attività in esecuzione                    | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Contatori di affidabilità di archiviazione     | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni sull'archiviazione              | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Output di fsutil                    | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni sul driver di filtro               | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Output di netstat                   | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Network configuration            | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Configurazione del firewall           | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Configurazione di SQL Server         | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Tracce diagnostiche delle prestazioni * |                            |                                    | Sì                      |                                |                      | Sì                  |
| Traccia dei contatori delle prestazioni **     |                            |                                    |                          |                                |                      | Sì                  |
| Traccia del contatore SMB **             |                            |                                    |                          |                                | Sì                  |                      |
| Traccia del contatore SQL Server **      |                            |                                    |                          |                                |                      | Sì                  |
| Traccia di XPerf                      |                            |                                    |                          |                                |                      | Sì                  |
| Traccia di StorPort                   |                            |                                    |                          |                                |                      | Sì                  |
| Traccia di rete                    |                            |                                    |                          |                                | Sì                  | Sì                  |
| Traccia del benchmark diskspd ***      |                            | Sì                                |                          | Sì                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Traccia diagnostica delle prestazioni (*)

Esegue in background un motore basato su regole per raccogliere dati e diagnosticare problemi di prestazioni in corso. Sono attualmente supportate le regole seguenti:

- Regola HighCpuUsage: rileva i periodi di utilizzo della CPU elevato e visualizza i principali consumer di utilizzo della CPU.
- Regola HighDiskUsage: rileva i periodi di utilizzo dei dischi elevato nei dischi fisici e visualizza i principali consumer di utilizzo dei dischi.
- Regola HighResolutionDiskMetric: visualizza le metriche relative a operazioni di I/O al secondo, velocità effettiva e latenza di I/O ogni 50 millisecondi per ogni disco fisico. Consente di identificare rapidamente i periodi di limitazione.
- Regola HighMemoryUsage: rileva i periodi di utilizzo della memoria elevato e visualizza i principali consumer di utilizzo della memoria.

> [!NOTE] 
> Sono attualmente supportate le versioni di Windows che includono .NET Framework 3.5 o versioni successive.

### <a name="performance-counter-trace-"></a>Traccia dei contatori delle prestazioni (**)

Raccoglie i contatori delle prestazioni seguenti:

- \Processo, \Processore, \Memoria, \Thread, \Disco fisico, \Disco logico
- \Cache\Pagine dirty, \Cache\Scritture Lazy scaricate su disco/sec, \Server\Errori in pool non di paging, \Server\Errori in pool di paging
- Contatori selezionati in \Interfaccia di rete, \IPv4\Datagrams (Datagrammi), \IPv6\Datagrams (Datagrammi), \TCPv4\Segmenti, \TCPv6\Segmenti, \Scheda di rete, \Piattaforma filtro Windows versione 4\Pacchetti, \Piattaforma filtro Windows versione 6\Pacchetti, \UDPv4\Datagrams (Datagrammi), \UDPv6\Datagrams (Datagrammi), \TCPv4\Connessione, \TCPv6\Connessione, \Criteri QoS di rete\Pacchetti, \Attività scheda di interfaccia di rete per processore, \Microsoft Winsock Base Service Provider

#### <a name="for-sql-server-instances"></a>Per le istanze di SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats, \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General Statistics
- \SQLServer:Metodi di accesso

#### <a name="for-azure-files"></a>Per File di Azure
\Condivisioni client SMB

### <a name="diskspd-benchmark-trace-"></a>Traccia del benchmark diskspd (***)
Il carico di lavoro di I/O diskspd testa [disco del sistema operativo (scrittura) e unità di pool (lettura/scrittura)]

## <a name="run-the-perfinsights-on-your-vm"></a>Eseguire PerfInsights nella VM

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Informazioni importanti prima di eseguire lo script 

**Requisiti dello script**

1.  Questo script deve essere eseguito nella VM che presenta il problema relativo alle prestazioni. 

2.  Sono supportati i sistemi operativi seguenti: Windows Server 2008 R2, 2012, 2012 R2 e 2016, Windows 8.1 e Windows 10.

**Possibili problemi quando si esegue lo script nelle VM di produzione:**

1.  Lo script può influire negativamente sulle prestazioni della VM quando viene usato con lo scenario benchmark o personalizzato, configurato tramite XPerf o DiskSpd. Prestare attenzione quando si esegue lo script in un ambiente di produzione.

2.  Quando si usa lo script con lo scenario benchmark o personalizzato che viene configurato tramite DiskSpd, verificare che nessuna altra attività in background interferisca con il carico di lavoro di I/O nei dischi testati.

3.  Per impostazione predefinita, lo script usa l'unità di archiviazione temporanea per raccogliere i dati. Se la traccia rimane abilitata per un periodo più lungo, la quantità di dati raccolti potrebbe essere rilevante. Ciò può ridurre la disponibilità di spazio nel disco temporaneo, influendo così sulle applicazioni che si basano su questa unità.

### <a name="how-do-i-run-perfinsights"></a>Come si esegue PerfInsights 

È possibile eseguire PerfInsights su una macchina virtuale installando l'[estensione della VM Diagnostica prestazioni di Azure](performance-diagnostics-vm-extension.md) oppure eseguirlo come script autonomo. 

**Installare ed eseguire PerfInsights dal portale di Azure**

PerfInsights ora può essere eseguito usando un'estensione della VM denominata Estensione Diagnostica prestazioni di Azure. Per altre informazioni, vedere [Install Azure Performance Diagnostics extension](performance-diagnostics-vm-extension.md#install-the-extension) (Installare l'estensione Diagnostica prestazioni di Azure).  

**Eseguire lo script di PerfInsights in modalità autonoma**

Per eseguire lo script di PerfInsights, seguire questa procedura:


1. Scaricare [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Sbloccare il file PerfInsights.zip facendo clic con il pulsante destro del mouse sul file PerfInsights.zip e scegliendo **Proprietà**. Nella scheda **Generale** selezionare **Annulla blocco** e quindi fare clic su **OK**. In tal modo, lo script verrà eseguito senza altri prompt di sicurezza.  

    ![Sbloccare il file con estensione zip](media/how-to-use-perfInsights/unlock-file.png)

3.  Espandere il file PerfInsights.zip compresso nell'unità temporanea (per impostazione predefinita, in genere l'unità D). Il file compresso conterrà i file e le cartelle seguenti:

    ![File nella cartella ZIP](media/how-to-use-perfInsights/file-folder.png)

4.  Aprire Windows PowerShell come amministratore e quindi eseguire lo script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Potrebbe essere necessario immettere "y" se viene chiesto di confermare che si vuole modificare il criterio di esecuzione.

    Nella finestra di dialogo Dichiarazione di non responsabilità, è presente l'opzione per condividere le informazioni diagnostiche con il supporto tecnico Microsoft. È anche necessario dare il consenso al contratto di licenza per continuare. Verificare le selezioni e quindi fare clic su **Esegui script**.

    ![Finestra di dialogo Dichiarazione di non responsabilità](media/how-to-use-perfInsights/disclaimer.png)

5.  Quando si esegue lo script, se disponibile, inviare il numero di caso, che verrà usato per le statistiche Microsoft. Fare quindi clic su **OK**.
    
    ![Immettere l'ID di supporto](media/how-to-use-perfInsights/enter-support-number.png)

6.  Selezionare l'unità di archiviazione temporanea. Lo script può rilevare automaticamente la lettera di unità. Se si verificano problemi in questa fase, potrebbe venire chiesto di selezionare l'unità. L'unità predefinita è D. I log generati vengono archiviati qui, nella cartella log\_collection. Dopo avere immesso o accettato la lettera di unità, fare clic su **OK**.

    ![Immettere l'unità](media/how-to-use-perfInsights/enter-drive.png)

7.  Selezionare uno scenario di risoluzione dei problemi dall'elenco fornito.

       ![Selezionare gli scenari di supporto](media/how-to-use-perfInsights/select-scenarios.png)

8.  È anche possibile eseguire PerfInsights senza l'interfaccia utente.

    Il comando seguente esegue lo scenario di risoluzione dei problemi "Analisi generale della VM lenta" senza prompt dell'interfaccia utente o acquisizione di dati per 30 secondi. Viene chiesto il consenso per la stessa dichiarazione di non responsabilità e le stesse condizioni di licenza citate nel passaggio 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    Per eseguire PerfInsights in modalità non interattiva, usare il parametro **-AcceptDisclaimerAndShareDiagnostics**. Ad esempio, usare il comando seguente:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Come risolvere i problemi durante l'esecuzione dello script

Se lo script viene terminato in modo anomalo, è possibile pulire uno stato non coerente eseguendo lo script con l'opzione -Cleanup, come segue:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Se si verificano problemi durante il rilevamento automatico dell'unità temporanea, potrebbe venire chiesto di selezionare l'unità. L'unità predefinita è D.

![Immettere l'unità](media/how-to-use-perfInsights/enter-drive.png)

Lo script disinstalla gli strumenti dell'utilità e rimuove le cartelle temporanee.

### <a name="troubleshooting-other-script-issues"></a>Risoluzione di altri problemi di script 

Se si verificano problemi quando si esegue lo script, premere CTRL+C per interrompere l'esecuzione dello script. Per rimuovere gli oggetti temporanei, vedere la sezione "Pulizia dopo una terminazione anomala".

Se si continuano a verificare errori di script anche dopo più tentativi, è consigliabile eseguire lo script in "modalità di debug" usando l'opzione del parametro "-Debug" all'avvio.

Quando si verifica l'errore, copiare l'output completo della console di PowerShell e inviarlo all'agente del supporto tecnico Microsoft che sta prestando assistenza nella risoluzione del problema.

### <a name="how-do-i-run-the-script-in-custom-configuration-mode"></a>Come si esegue lo script nella modalità di configurazione personalizzata

Selezionando la configurazione **Personalizzata**, è possibile abilitare più tracce in parallelo. Usare MAIUSC per la selezione multipla:

![Selezionare gli scenari](media/how-to-use-perfInsights/select-scenario.png)

Quando si selezionano gli scenari di diagnostica delle prestazioni, traccia dei contatori delle prestazioni, traccia di XPerf, traccia di rete o traccia di StorPort, seguire le istruzioni delle finestre di dialogo e provare a riprodurre il problema del rallentamento delle prestazioni dopo avere avviato le tracce.

La finestra di dialogo seguente consente di avviare una traccia:

![Avviare una traccia](media/how-to-use-perfInsights/start-trace-message.png)

Per arrestare le tracce, è necessario confermare il comando in una seconda finestra di dialogo.

![Arrestare una traccia](media/how-to-use-perfInsights/stop-trace-message.png)
![Arrestare una traccia](media/how-to-use-perfInsights/ok-trace-message.png)

Quando le tracce o le operazioni vengono completate, viene generato un nuovo file in D:\\log\_collection (o nell'unità temporanea) denominato **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip.** È possibile inviare il file all'agente di supporto per l'analisi.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>Esaminare il report di diagnostica creato da PerfInsights

Nel file **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip,** generato da PerfInsights, è possibile trovare un report HTML che elenca in dettaglio i risultati di PerfInsights. Per esaminare il report, espandere il file **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip** e quindi aprire il file **PerfInsights Report.html**.

Selezionare la scheda **Risultati**.

![Scheda Risultati](media/how-to-use-perfInsights/findingtab.png)

**Note**

-   I messaggi in rosso sono problemi di configurazione noti che possono causare problemi di prestazioni.

-   I messaggi in giallo sono avvisi che rappresentano configurazioni non ottimali che non necessariamente causano problemi di prestazioni.

-   I messaggi in blu sono solo istruzioni informative.

Esaminare i collegamenti HTTP per tutti i messaggi di errore in rosso per ottenere informazioni più dettagliate sui risultati e sulle possibili conseguenze sulle prestazioni o sulle procedure consigliate per le configurazioni ottimizzate per le prestazioni.

### <a name="disk-configuration-tab"></a>Scheda Disk Configuration (Configurazione dei dischi)

La sezione **Panoramica** visualizza diverse viste della configurazione di archiviazione, incluse le informazioni provenienti da Diskpart e Spazi di archiviazione

Le sezioni **DiskMap** e **VolumeMap** descrivono da un duplice punto di vista la correlazione tra i volumi logici e i dischi fisici.

Dal punto di vista di PhysicalDisk (DiskMap), la tabella visualizza tutti i volumi logici in esecuzione sul disco. Nell'esempio seguente PhysicalDrive2 esegue due volumi logici creati in più partizioni (J e H):

![Scheda dati](media/how-to-use-perfInsights/disktab.png)

Dal punto di vista del volume (*VolumeMap*), le tabelle visualizzano tutti i dischi fisici in ogni volume logico. Si noti che per i dischi RAID/dinamici è possibile eseguire un volume logico in più dischi fisici. Nell'esempio seguente *C:\\mount* è un punto di montaggio configurato come *SpannedDisk* nei dischi fisici \#2 e \#3:

![Scheda Volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-server-tab"></a>Scheda SQL Server

Se la VM di destinazione ospita istanze di SQL Server, nel report viene visualizzata una scheda aggiuntiva denominata **SQL Server**:

![Scheda per SQL](media/how-to-use-perfInsights/sqltab.png)

Questa sezione contiene una scheda "Panoramica" e altre schede secondarie per ogni istanza di SQL Server ospitata nella VM.

La sezione "Panoramica" contiene un'utile tabella che riepiloga tutti i dischi fisici (dischi di sistema e dati) in esecuzione e contiene una combinazione di file di dati e di file di log delle transazioni.

Nell'esempio seguente viene visualizzato *PhysicalDrive0* (che esegue l'unità C) perché entrambi i file *modeldev* e *modellog* si trovano nell'unità C e sono di tipo diverso (rispettivamente file di dati e log delle transazioni):

![Informazioni sui log](media/how-to-use-perfInsights/loginfo.png)

Le schede specifiche delle istanze di SQL Server contengono una sezione generale che visualizza le informazioni di base sull'istanza selezionata e altre sezioni per le informazioni avanzate, incluse impostazioni, configurazioni e opzioni utente.

## <a name="references-to-the-external-tools-used"></a>Informazioni di riferimento sugli strumenti esterni usati

### <a name="diskspd"></a>Diskspd

DISKSPD è un generatore di carico di archiviazione e uno strumento di test delle prestazioni dei team di progettazione di Windows, Windows Server e dell'infrastruttura server cloud. Per altre informazioni, vedere [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

Xperf è uno strumento da riga di comando per acquisire tracce da Windows Performance Toolkit.

Per altre informazioni, vedere [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/) (Windows Performance Toolkit - Xperf).

## <a name="next-steps"></a>Passaggi successivi

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>Caricare log di diagnostica e report per il supporto tecnico Microsoft per ulteriori analisi

Quando si collabora con il personale del supporto tecnico Microsoft, potrebbe venire chiesto di trasmettere l'output generato da PerfInsights per facilitare il processo di risoluzione dei problemi.

L'agente del supporto creerà un'area di lavoro DTM e si riceverà un messaggio di posta elettronica che include un collegamento al portale DTM (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm), un ID utente univoco e una password.

Questo messaggio verrà inviato da **CTS Automated Diagnostics Services** (ctsadiag@microsoft.com).

![Esempio di messaggio](media/how-to-use-perfInsights/supportemail.png)

Per maggiore sicurezza, verrà chiesto di cambiare la password al primo uso.

Dopo l'accesso a DTM, verrà visualizzata una finestra di dialogo per caricare il file **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip** raccolto da PerfInsights.
