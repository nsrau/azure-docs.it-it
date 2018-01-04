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
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>Come usare PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) è uno script automatico che consente di raccogliere informazioni diagnostiche. Viene inoltre eseguito carichi dei / o e fornisce un report di analisi per la risoluzione dei problemi di prestazioni di macchina virtuale di Windows in Azure. Questo è possibile eseguire sulle macchine virtuali come script autonomo o direttamente dal portale installando [estensione VM diagnostica delle prestazioni di Azure](performance-diagnostics-vm-extension.md).

Se si verificano problemi di prestazioni con le macchine virtuali, prima di contattare il supporto, eseguire lo script.

## <a name="supported-troubleshooting-scenarios"></a>Scenari per la risoluzione dei problemi supportati

PerfInsights possono raccogliere e analizzare i diversi tipi di informazioni. Le sezioni seguenti illustrano scenari comuni.

### <a name="collect-basic-configuration"></a>Raccogli configurazione di base 

Questo scenario raccoglie la configurazione del disco e altre informazioni importanti, tra cui:

-   Log eventi

-   Stato della rete per tutte le connessioni in ingresso e in uscita

-   Impostazioni di configurazione della rete e del firewall

-   Elenco di attività per tutte le applicazioni attualmente in esecuzione nel sistema

-   File di informazioni creato da msinfo32 per la macchina virtuale

-   Impostazioni di configurazione del database di Microsoft SQL Server (se la VM viene identificata come server che esegue SQL Server)

-   Contatori di affidabilità di archiviazione

-   Importanti hotfix di Windows

-   Driver di filtro installati

Si tratta di una raccolta passiva di informazioni che non influirà sul sistema. 

>[!Note]
>Questo scenario viene automaticamente incluso in tutti gli scenari seguenti.

### <a name="benchmarking"></a>Benchmarking

Questo scenario viene eseguito il [Diskspd](https://github.com/Microsoft/diskspd) prova comparativa (IOPS e MBPS) per tutte le unità collegate alla macchina virtuale. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito su un sistema di produzione in tempo reale. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro che è causato da un test di benchmark o di traccia può influire negativamente sulle prestazioni della macchina virtuale.
>

### <a name="slow-vm-analysis"></a>Analisi di una macchina virtuale lenta 

Questo scenario esegue una traccia dei [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando i contatori specificati nel file Generalcounters.txt. Se la macchina virtuale viene identificata come un server che esegue SQL Server, viene eseguita una traccia del contatore delle prestazioni. Esegue l'operazione utilizzando i contatori che si trovano nel file Sqlcounters.txt e include anche i dati di diagnostica delle prestazioni.

### <a name="slow-vm-analysis-and-benchmarking"></a>Analisi di una macchina virtuale lenta e test di benchmark

Questo scenario viene eseguito un [contatore delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) traccia seguita da un [Diskspd](https://github.com/Microsoft/diskspd) test di benchmark. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito su un sistema di produzione in tempo reale. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro che è causato da un test di benchmark o di traccia può influire negativamente sulle prestazioni della macchina virtuale.
>

### <a name="azure-files-analysis"></a>Analisi di File di Azure 

Questo scenario esegue una speciale acquisizione dei contatori delle prestazioni insieme a una traccia di rete. L'acquisizione include tutti i contatori delle condivisioni del client di Server Message Block (SMB). I seguenti sono alcuni dei principali contatori delle prestazioni Condivisioni client SMB che fanno parte dell'acquisizione:

| **Tipo**     | **Contatore Condivisioni client SMB** |
|--------------|-------------------------------|
| IOPS         | Richieste dati/sec             |
|              | Richieste di lettura/sec             |
|              | Richieste di scrittura/sec            |
| Latenza      | Media secondi/richiesta dati         |
|              | Media secondi/lettura                 |
|              | Media secondi/scrittura                |
| Dimensioni I/O      | Avg. Bytes/Data Request (Media byte/richiesta dati)       |
|              | Avg. byte/Lettura               |
|              | Media byte/Scrittura              |
| Velocità effettiva   | Byte dati/sec                |
|              | Byte letti/sec                |
|              | Byte scritti/sec               |
| Lunghezza coda | Avg. media coda lettura        |
|              | Avg. media coda scrittura       |
|              | Lunghezza media coda dati        |

### <a name="custom-slow-vm-analysis"></a>Analisi personalizzata di una macchina virtuale lenta 

Quando si esegue un'analisi di macchina virtuale personalizzata lenta, selezionare le tracce da eseguire in parallelo. È possibile eseguirli tutti (contatore delle prestazioni, Xperf, rete e StorPort) se si desidera. Al termine dell'analisi, lo strumento viene eseguito il benchmark di Diskspd, se è selezionato. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito su un sistema di produzione in tempo reale. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro che è causato da un test di benchmark o di traccia può influire negativamente sulle prestazioni della macchina virtuale.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Tipo di informazioni raccolte dallo script

Informazioni sulla macchina virtuale di Windows, dischi o configurazione di pool di archiviazione, i contatori delle prestazioni, log e varie le tracce vengono raccolte. Dipende lo scenario di prestazioni in uso. Nella tabella seguente vengono fornite informazioni dettagliate:

|Dati raccolti                              |  |  | Scenari di prestazioni |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Raccogli configurazione di base | Benchmarking | Analisi di una macchina virtuale lenta | Analisi di una macchina virtuale lenta e test di benchmark | Analisi di File di Azure | Analisi personalizzata di una macchina virtuale lenta |
| Informazioni dai registri eventi      | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni di sistema               | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Mappa di volume                       | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Mappa di disco                         | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Attività in esecuzione                    | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Contatori di affidabilità di archiviazione     | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni sull'archiviazione              | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Output di fsutil                    | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni sul driver di filtro               | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Output di netstat                   | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Network configuration            | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Configurazione del firewall           | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Configurazione di SQL Server         | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Le tracce di diagnostica delle prestazioni * | Sì                        | Sì                                | Sì                      |                                | Sì                  | Sì                  |
| Traccia del contatore delle prestazioni * *     |                            |                                    |                          |                                |                      | Sì                  |
| Traccia contatore SMB * *             |                            |                                    |                          |                                | Sì                  |                      |
| Traccia del contatore SQL Server * *      |                            |                                    |                          |                                |                      | Sì                  |
| Traccia Xperf                      |                            |                                    |                          |                                |                      | Sì                  |
| Traccia StorPort                   |                            |                                    |                          |                                |                      | Sì                  |
| Traccia di rete                    |                            |                                    |                          |                                | Sì                  | Sì                  |
| Traccia benchmark Diskspd * * *      |                            | Sì                                |                          | Sì                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Traccia di diagnostica delle prestazioni (*)

Esegue in background un motore basato su regole per raccogliere dati e diagnosticare problemi di prestazioni in corso. Sono attualmente supportate le regole seguenti:

- Regola HighCpuUsage: rileva i periodi di utilizzo elevati della CPU e Mostra i consumer di utilizzo della CPU superiore durante tali periodi.
- Regola HighDiskUsage: rileva i periodi di utilizzo del disco nei dischi fisici e Mostra il primo disco consumer utilizzo durante tali periodi.
- Regola HighResolutionDiskMetric: Mostra le metriche di latenza IOPS, velocità effettiva e i/o per 50 millisecondi per ogni disco fisico. Consente di identificare rapidamente i periodi di limitazione.
- Regola HighMemoryUsage: rileva i periodi di utilizzo della memoria elevato e visualizza i principali consumer di utilizzo della memoria.

> [!NOTE] 
> Sono attualmente supportate le versioni di Windows che includono .NET Framework 3.5 o versioni successive.

### <a name="performance-counter-trace-"></a>Traccia del contatore delle prestazioni (*)

Raccoglie i contatori delle prestazioni seguenti:

- \Processo \Processore, \Memory., \Thread, \PhysicalDisk e \LogicalDisk
- \Cache\Dirty \Cache\Lazy scrittura scaricamenti/sec, pagine \Server\Pool non di paging, errori e gli errori di \Server\Pool di paging
- Seleziona contatori in \Network interfaccia \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ QoS Policy\Packets di rete e attività di Smart Card dell'interfaccia di rete di \Per processore \Microsoft Winsock base Service Provider

#### <a name="for-sql-server-instances"></a>Per le istanze di SQL Server
- \SQL server: Buffer Manager e statistiche del Pool di \SQLServer:Resource \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General Statistics
- \SQLServer:Metodi di accesso

#### <a name="for-azure-files"></a>Per File di Azure
\Condivisioni client SMB

### <a name="diskspd-benchmark-trace-"></a>Traccia di benchmark di Diskspd (*)
Test di carico di lavoro dei / o di Diskspd (disco del sistema operativo [write] e le unità pool [lettura/scrittura])

## <a name="run-the-perfinsights-script-on-your-vm"></a>Eseguire lo script PerfInsights nella VM

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Informazioni importanti prima di eseguire lo script 

#### <a name="script-requirements"></a>Requisiti di script

-  Questo script deve essere eseguito nella VM che presenta il problema relativo alle prestazioni. 

-  Sono supportati i seguenti sistemi operativi: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>Possibili problemi quando si esegue lo script in produzione, le macchine virtuali

-  Per tutti gli scenari di benchmark o lo scenario "Analisi VM lenta personalizzata" che è configurato per utilizzare Xperf o Diskspd, lo script potrebbe influire negativamente sulle prestazioni della macchina virtuale. Non eseguire questi scenari in un ambiente di produzione.

-  Per tutti gli scenari di benchmark o lo scenario "Analisi VM lenta personalizzata" che è configurato per l'utilizzo di Diskspd, verificare che nessuna altra attività di background interferisce con il carico di lavoro dei / o.

-  Per impostazione predefinita, lo script usa l'unità di archiviazione temporanea per raccogliere i dati. Se la traccia rimane abilitata per un periodo più lungo, la quantità di dati raccolti potrebbe essere rilevante. Questo può ridurre la disponibilità di spazio su disco temporaneo e può pertanto influire sulle applicazioni che si basano su questa unità.

### <a name="how-do-i-run-perfinsights"></a>Come si esegue PerfInsights 

È possibile eseguire PerfInsights in una macchina virtuale installando [estensione VM diagnostica delle prestazioni di Azure](performance-diagnostics-vm-extension.md). È possibile anche eseguire come script autonomo. 

**Installare ed eseguire PerfInsights dal portale di Azure**

Per ulteriori informazioni su questa opzione, vedere [estensione VM diagnostica di installare Azure prestazioni](performance-diagnostics-vm-extension.md#install-the-extension).  

**Eseguire lo script di PerfInsights in modalità autonoma**

Per eseguire lo script di PerfInsights, seguire questa procedura:


1. Scaricare [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Sbloccare il file PerfInsights.zip A tale scopo, il file PerfInsights.zip e scegliere **proprietà**. Nel **generale** , selezionare **Unblock**, quindi selezionare **OK**. In questo modo si garantisce che lo script viene eseguito senza alcuna protezione aggiuntiva richiesto.  

    ![Schermata di PerfInsights le proprietà, con sblocco evidenziato](media/how-to-use-perfInsights/unlock-file.png)

3.  Espandere il file compresso PerfInsights.zip nell'unità temporanea (per impostazione predefinita, si tratta in genere l'unità D). Il file compresso conterrà i file e le cartelle seguenti:

    ![Schermata di file nella cartella zip](media/how-to-use-perfInsights/file-folder.png)

4.  Aprire Windows PowerShell come amministratore e quindi eseguire lo script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Potrebbe essere necessario immettere "y" per confermare che si desidera modificare i criteri di esecuzione.

    Nel **nota e fornire il consenso** nella finestra di dialogo è possibile condividere le informazioni di diagnostica con il supporto Microsoft. È anche necessario dare il consenso al contratto di licenza per continuare. Effettuare le selezioni desiderate e quindi selezionare **Esegui Script**.

    ![Schermata di notifica e consenso la finestra di dialogo](media/how-to-use-perfInsights/disclaimer.png)

5.  Inviare il numero di case, se disponibile, quando si esegue lo script. Selezionare **OK**.
    
    ![Schermata della finestra di dialogo ID supporto](media/how-to-use-perfInsights/enter-support-number.png)

6.  Selezionare l'unità di archiviazione temporanea. Lo script può rilevare automaticamente la lettera di unità dell'unità. Se si verificano problemi in questa fase, potrebbe venire chiesto di selezionare l'unità. L'unità predefinita è D. I log generati vengono archiviati in questo caso, nel registro\_cartella insieme. Dopo aver immesso o accettare la lettera di unità, selezionare **OK**.

    ![Schermata della finestra di dialogo unità temporanea](media/how-to-use-perfInsights/enter-drive.png)

7.  Selezionare uno scenario di risoluzione dei problemi dall'elenco fornito.

       ![Schermata di elenco degli scenari di risoluzione dei problemi](media/how-to-use-perfInsights/select-scenarios.png)

È anche possibile eseguire PerfInsights senza l'interfaccia utente. Il comando seguente esegue il "analisi VM lenta" Risoluzione dei problemi relativi a uno scenario senza l'interfaccia utente. Viene chiesto il consenso per la stessa dichiarazione di non responsabilità e le stesse condizioni di licenza citate nel passaggio 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Per eseguire PerfInsights in modalità non interattiva, usare il parametro **-AcceptDisclaimerAndShareDiagnostics**. Ad esempio, usare il comando seguente:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Come risolvere i problemi durante l'esecuzione dello script

Se lo script termina in modo anomalo, è possibile tornare a uno stato coerente eseguendo lo script con l'opzione di pulizia, come indicato di seguito:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Se si verificano problemi durante il rilevamento automatico dell'unità temporanea, potrebbe venire chiesto di selezionare l'unità. L'unità predefinita è D.

![Schermata della finestra di dialogo unità temporanea](media/how-to-use-perfInsights/enter-drive.png)

Lo script disinstalla gli strumenti dell'utilità e rimuove le cartelle temporanee.

### <a name="troubleshoot-other-script-issues"></a>Risoluzione di altri problemi di script 

Se si verificano problemi quando si esegue lo script, premere Ctrl + C per interrompere lo script. Se si continua a generare un errore di script anche dopo diversi tentativi, eseguire lo script in modalità di debug tramite il "-Debug" opzione parametro all'avvio.

Quando si verifica l'errore, copiare l'output completo della console di PowerShell e inviarlo all'agente del supporto tecnico Microsoft che sta prestando assistenza nella risoluzione del problema.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Come eseguire lo script in modalità "Analisi VM lenta personalizzati"

Selezionando il **analysis VM lenta personalizzata**, è possibile abilitare più tracce in parallelo (per selezionare più tracce, utilizzare il tasto MAIUSC):

![Schermata di elenco di scenari](media/how-to-use-perfInsights/select-scenario.png)

Quando si seleziona la traccia del contatore delle prestazioni, Xperf traccia, traccia di rete o scenari di traccia Storport, seguono le istruzioni nelle finestre di dialogo successive. Provare a riprodurre il problema di rallentamento delle prestazioni dopo l'avvio di tracce.

Viene avviata una traccia tramite la finestra di dialogo seguenti:

![La finestra di dialogo schermata di avvio contatore traccia delle prestazioni](media/how-to-use-perfInsights/start-trace-message.png)

Per arrestare le tracce, è necessario confermare il comando in una seconda finestra di dialogo.

![La finestra di dialogo schermata di arresto delle prestazioni del contatore traccia](media/how-to-use-perfInsights/stop-trace-message.png)
![schermata di arresto di tutte le tracce la finestra di dialogo](media/how-to-use-perfInsights/ok-trace-message.png)

Al termine delle operazioni o le tracce, un nuovo file viene visualizzato nell'unità d:\\log\_raccolta (o unità temporanea). Il nome del file è **CollectedData\_AAAA-MM-GG\_hh\_mm\_ss.zip.** È possibile inviare il file per l'agente di supporto per l'analisi.

## <a name="review-the-diagnostics-report"></a>Esaminare il rapporto di diagnostica

All'interno di **CollectedData\_AAAA-MM-GG\_hh\_mm\_ss.zip** file, è possibile trovare un rapporto HTML che descrive in dettaglio i risultati ottenuti da PerfInsights. Per esaminare il report, espandere il file **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip** e quindi aprire il file **PerfInsights Report.html**.

Selezionare la scheda **Risultati**.

![Schermata di Report PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![schermata di PerfInsights Report](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Alcuni problemi che potrebbero causare problemi di prestazioni noti conclusivo classificati come importanti. I risultati classificati come importanti rappresentano configurazioni non ottimali che non necessariamente causano problemi di prestazioni. I risultati classificati come informativi sono solo istruzioni informative.

Esaminare le indicazioni e i collegamenti per tutti i dati raccolti critici e importanti. Informazioni su come possono influenzare le prestazioni e sulle procedure consigliate per ottimizzare le prestazioni delle configurazioni.

### <a name="storage-tab"></a>Scheda Archiviazione

Nella sezione **Risultati** vengono visualizzate i risultati e le raccomandazioni inerenti all'archiviazione.

Il **disco mappa** e **Volume mappa** sezioni descrivono come logica volumi e fisica dischi sono correlati tra loro.

Nella prospettiva del disco fisico (disco Map), la tabella mostra tutti i volumi logici che sono in esecuzione sul disco. Nell'esempio seguente, **PhysicalDrive2** esegue due volumi logici creati in più partizioni (J e H):

![Schermata della scheda disco](media/how-to-use-perfInsights/disktab.png)

Nella prospettiva del volume (Volume Map), le tabelle mostrano tutti i dischi fisici in ogni volume logico. Si noti che per i dischi RAID/dinamico, è possibile eseguire un volume logico su più dischi fisici. Nell'esempio seguente, *c:\\montare* configurato come un punto di montaggio *SpannedDisk* su dischi fisici, 2 e 3:

![Schermata della scheda volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Scheda SQL

Se la macchina virtuale di destinazione contiene tutte le istanze di SQL Server, vedrai una scheda aggiuntiva nel report, denominato **SQL**:

![Scheda schermata di SQL](media/how-to-use-perfInsights/sqltab.png)

In questa sezione contiene un **conclusioni** scheda e le schede aggiuntive per ognuna delle istanze di SQL Server ospitate nella macchina virtuale.

Il **conclusioni** scheda contiene un elenco di tutto il codice SQL trovati, insieme a indicazioni di problemi di prestazioni correlati.

Nell'esempio seguente, **Unitàfisica0** (in esecuzione nell'unità C) viene visualizzato. Infatti, sia il **modeldev** e **modellog** file si trovano nell'unità C e sono di tipo diverso (ad esempio dati file di log delle transazioni, rispettivamente).

![Schermata di informazioni di log](media/how-to-use-perfInsights/loginfo.png)

Le schede per istanze specifiche di SQL Server contengono una sezione generale che consente di visualizzare le informazioni di base nell'istanza selezionata. Le schede contengono anche le sezioni aggiuntive per le informazioni avanzate, incluse le impostazioni, le configurazioni e le opzioni utente.

### <a name="diagnostic-tab"></a>Scheda Diagnostica
Il **diagnostica** scheda contiene informazioni sulle principali consumer di CPU, disco e memoria del computer per la durata dell'esecuzione di PerfInsights. È inoltre possibile trovare informazioni sulle patch critiche che il sistema potrebbe essere mancante, l'elenco attività e gli eventi di sistema importanti. 

## <a name="references-to-the-external-tools-used"></a>Informazioni di riferimento sugli strumenti esterni usati

### <a name="diskspd"></a>Diskspd

Diskspd è un carico generatore e prestazioni test strumento di archiviazione di Microsoft. Per altre informazioni, vedere [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf è uno strumento da riga di comando per acquisire tracce da Windows Performance Toolkit. Per altre informazioni, vedere [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/) (Windows Performance Toolkit - Xperf).

## <a name="next-steps"></a>Passaggi successivi

È possibile caricare i log di diagnostica e i report per il supporto Microsoft per un'ulteriore analisi. Supporto potrebbe richiedere che si trasmette l'output generato da PerfInsights per semplificare il processo di risoluzione dei problemi.

La schermata seguente mostra come è possibile ricevere un messaggio:

![Schermata del messaggio di esempio dal supporto Microsoft](media/how-to-use-perfInsights/supportemail.png)

Seguire le istruzioni nel messaggio per l'area di lavoro di trasferimento di file di accesso. Per una maggiore sicurezza, è necessario modificare la password al primo utilizzo.

Dopo l'accesso, si troverà una finestra di dialogo per caricare il **CollectedData\_AAAA-MM-GG\_hh\_mm\_ss.zip** file raccolti con PerfInsights.
