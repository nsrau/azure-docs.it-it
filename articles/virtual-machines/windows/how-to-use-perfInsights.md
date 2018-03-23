---
title: Come usare PerfInsights in Microsoft Azure| Microsoft Docs
description: Informazioni su come usare PerfInsights per risolvere i problemi delle prestazioni delle VM Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: ee8334cbe9256b7a5ecd5e96afa2f15d6389afa8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2018
---
# <a name="how-to-use-perfinsights"></a>Come usare PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) è uno strumento Self-help che raccoglie e analizza i dati di diagnostica e fornisce un report utile per risolvere i problemi relativi alle prestazioni delle macchine virtuali Windows in Azure. PerfInsights può essere eseguito nelle macchine virtuali come strumento autonomo o direttamente dal portale installando l'[estensione per macchine virtuali Diagnostica prestazioni di Azure](performance-diagnostics-vm-extension.md).

Se si verificano problemi di prestazioni delle macchine virtuali, eseguire lo strumento prima di contattare l'assistenza.

## <a name="supported-troubleshooting-scenarios"></a>Scenari per la risoluzione dei problemi supportati

PerfInsights può raccogliere e analizzare diversi tipi di informazioni. Le sezioni seguenti trattano scenari comuni.

### <a name="collect-basic-configuration"></a>Raccolta della configurazione di base 

Questo scenario consente di raccogliere la configurazione dei dischi e altre importanti informazioni, tra cui:

-   Log eventi

-   Stato della rete per tutte le connessioni in ingresso e in uscita

-   Impostazioni di configurazione della rete e del firewall

-   Elenco di attività per tutte le applicazioni attualmente in esecuzione nel sistema

-   Impostazioni di configurazione del database di Microsoft SQL Server (se la VM viene identificata come server che esegue SQL Server)

-   Contatori di affidabilità di archiviazione

-   Importanti hotfix di Windows

-   Driver di filtro installati

Si tratta di una raccolta passiva di informazioni che non influirà sul sistema. 

>[!Note]
>Questo scenario viene automaticamente incluso in tutti gli scenari seguenti:

### <a name="benchmarking"></a>Benchmarking

Questo scenario esegue il test di benchmark [Diskspd](https://github.com/Microsoft/diskspd), ovvero delle operazioni di I/O al secondo e di Mbps, per tutte le unità collegate alla macchina virtuale. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della macchina virtuale.
>

### <a name="slow-vm-analysis"></a>Analisi di una macchina virtuale lenta 

Questo scenario esegue una traccia dei [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando i contatori specificati nel file RuleEngineConfig.json. Se la macchina virtuale viene identificata come server che esegue SQL Server, viene eseguita una traccia del contatore delle prestazioni. Questa operazione viene eseguita usando i contatori che si trovano nel file RuleEngineConfig.json. Lo scenario include anche i dati di diagnostica delle prestazioni.

### <a name="azure-files-analysis"></a>Analisi di File di Azure 

Questo scenario esegue una speciale acquisizione dei contatori delle prestazioni insieme a una traccia di rete. L'acquisizione include tutti i contatori delle condivisioni client Server Message Block, ovvero SMB. I seguenti sono alcuni dei principali contatori delle prestazioni Condivisioni client SMB che fanno parte dell'acquisizione:

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

Quando si esegue un'analisi personalizzata di una macchina virtuale lenta, l'utente deve selezionare le tracce da eseguire in parallelo. Se si vuole, è possibile eseguirle tutte (Performance Counter, Xperf, Network e StorPort).  

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della macchina virtuale.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Tipo di informazioni raccolte da PerfInsights

Vengono raccolte informazioni sulla macchina virtuale Windows, sulla configurazione dei dischi o dei pool di archiviazione, sui contatori delle prestazioni, sui log e su diverse tracce, a seconda dello scenario delle prestazioni usato. La tabella seguente contiene informazioni dettagliate:

|Dati raccolti                              |  |  | Scenari delle prestazioni |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Raccolta della configurazione di base | Benchmarking | Analisi di una macchina virtuale lenta | Analisi di File di Azure | Analisi personalizzata di una macchina virtuale lenta |
| Informazioni dei registri eventi       | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Informazioni di sistema                | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Mapping del volume                        | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Mapping del disco                          | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Attività in esecuzione                     | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Contatori di affidabilità di archiviazione      | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Informazioni sull'archiviazione               | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Output di fsutil                     | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Informazioni sul driver filtro                | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Output di netstat                    | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Network configuration             | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Configurazione del firewall            | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Configurazione di SQL Server          | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Tracce di diagnostica delle prestazioni *  | Sì                        | Sì                                | Sì                      | Sì                  | Sì                  |
| Traccia del contatore delle prestazioni **      |                            |                                    | Sì                      |                      | Sì                  |
| Traccia del contatore SMB **              |                            |                                    |                          | Sì                  |                      |
| Traccia del contatore SQL Server **       |                            |                                    | Sì                      |                      | Sì                  |
| Traccia di XPerf                       |                            |                                    |                          |                      | Sì                  |
| Traccia di StorPort                    |                            |                                    |                          |                      | Sì                  |
| Traccia di rete                     |                            |                                    |                          | Sì                  | Sì                  |
| Traccia del benchmark Diskspd ***       |                            | Sì                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Traccia di diagnostica delle prestazioni (*)

Esegue in background un motore basato su regole per raccogliere dati e diagnosticare problemi di prestazioni in corso. Sono attualmente supportate le regole seguenti:

- Regola HighCpuUsage: rileva i periodi di uso elevato della CPU e visualizza i principali consumer di uso della CPU in questi periodi.
- Regola HighDiskUsage: rileva i periodi di uso elevato dei dischi nei dischi fisici e visualizza i principali consumer di uso dei dischi in questi periodi.
- Regola HighResolutionDiskMetric: visualizza le metriche relative a operazioni di I/O al secondo, velocità effettiva e latenza di I/O ogni 50 millisecondi per ogni disco fisico. Consente di identificare rapidamente i periodi di limitazione.
- Regola HighMemoryUsage: rileva i periodi di utilizzo della memoria elevato e visualizza i principali consumer di utilizzo della memoria.

> [!NOTE] 
> Sono attualmente supportate le versioni di Windows che includono .NET Framework 3.5 o versioni successive.

### <a name="performance-counter-trace-"></a>Traccia del contatore delle prestazioni (**)

Raccoglie i contatori delle prestazioni seguenti:

- \Processo, \Processore, \Memoria, \Thread, \Disco fisico e \Disco logico
- \Cache\Pagine dirty, \Cache\Scritture Lazy scaricate su disco/sec, \Server\Errori in pool non di paging, Errori e \Server\Errori in pool di paging
- Contatori selezionati in \Interfaccia di rete, \IPv4\Datagrammi, \IPv6\Datagrammi, \TCPv4\Segmenti, \TCPv6\Segmenti, \Scheda di rete, \Piattaforma filtro Windows versione 4\Pacchetti, \Piattaforma filtro Windows versione 6\Pacchetti, \UDPv4\Datagrammi, \UDPv6\Datagrammi, \TCPv4\Connessione, \TCPv6\Connessione, \Criteri QoS di rete\Pacchetti, \Attività scheda di interfaccia di rete per processore e \Microsoft Winsock Base Service Provider

#### <a name="for-sql-server-instances"></a>Per le istanze di SQL Server
- \SQL Server:Gestione buffer, \SQLServer:Statistiche del pool di risorse, \SQLServer:Statistiche SQL\
- \SQLServer:Locks, \SQLServer:General Statistics
- \SQLServer:Metodi di accesso

#### <a name="for-azure-files"></a>Per File di Azure
\Condivisioni client SMB

### <a name="diskspd-benchmark-trace-"></a>Traccia del benchmark Diskspd (***)
Il carico di lavoro di I/O Diskspd testa (disco del sistema operativo [scrittura] e unità di pool [lettura/scrittura])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Eseguire lo strumento PerfInsights nella macchina virtuale

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Informazioni importanti prima di eseguire lo strumento 

#### <a name="tool-requirements"></a>Requisiti dello strumento

-  Questo strumento deve essere eseguito nella macchina virtuale in cui si è verificato il problema di prestazioni. 

-  Sono supportati i sistemi operativi seguenti: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Possibili problemi quando si esegue lo strumento nelle macchine virtuali di produzione

-  Per lo scenario di benchmarking o per quello di analisi personalizzata di una macchina virtuale lenta configurato per l'uso di XPerf o Diskspd, lo strumento può influire negativamente sulle prestazioni della macchina virtuale. Questi scenari non devono essere eseguiti in un ambiente di produzione attivo.

-  Per lo scenario di benchmarking o per quello di analisi personalizzata di una macchina virtuale lenta configurato per l'uso di Diskspd, verificare che non siano presenti interferenze di attività in background con il carico di lavoro di I/O.

-  Per impostazione predefinita, lo strumento usa l'unità di archiviazione temporanea per raccogliere i dati. Se la traccia rimane abilitata per un periodo più lungo, la quantità di dati raccolti potrebbe essere rilevante. Ciò può ridurre la disponibilità di spazio nel disco temporaneo e può influire sulle applicazioni che si basano su questa unità.

### <a name="how-do-i-run-perfinsights"></a>Come si esegue PerfInsights 

È possibile eseguire PerfInsights su una macchina virtuale installando l'[estensione della macchina virtuale Diagnostica delle prestazioni di Azure](performance-diagnostics-vm-extension.md). È anche possibile eseguirlo come strumento autonomo. 

**Installare ed eseguire PerfInsights dal portale di Azure**

Per altre informazioni su questa opzione, vedere [Installare l'estensione della macchina virtuale Diagnostica delle prestazioni di Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Eseguire PerfInsights in modalità autonoma**

Per eseguire lo strumento PerfInsights, seguire questa procedura:


1. Scaricare [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Sbloccare il file PerfInsights.zip Per eseguire questa operazione fare clic con il pulsante destro del mouse sul file PerfInsights.zip e selezionare **Proprietà**. Nella scheda **Generale** selezionare **Sblocca** e quindi fare clic su **OK**. In tal modo, lo strumento viene eseguito senza altre richieste di sicurezza.  

    ![Screenshot delle proprietà di PerfInsights con l'opzione Sblocca evidenziata](media/how-to-use-perfInsights/unlock-file.png)

3.  Espandere il file PerfInsights.zip compresso nell'unità temporanea. Per impostazione predefinita, in genere è l'unità D. 

4.  Aprire il prompt dei comandi di Windows come amministratore e quindi eseguire PerfInsights.exe per visualizzare i parametri disponibili della riga di comando.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Screenshot dell'output della riga di comando di PerfInsights](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    La sintassi di base per eseguire gli scenari di PerfInsights è la seguente:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    È possibile usare l'esempio seguente per eseguire lo scenario di analisi di una macchina virtuale lenta per 5 minuti:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    È possibile usare l'esempio seguente per eseguire lo scenario personalizzato con le tracce dei contatori Xperf e Performance per 5 minuti:
    
    ```
    PerfInsights /run custom xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    È possibile cercare tutti gli scenari e le opzioni disponibili tramite il comando **/list**:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Prima di eseguire uno scenario, PerfInsights chiede all'utente di acconsentire a condividere le informazioni di diagnostica e di accettare il contratto di licenza. Per ignorare queste richieste di conferma, usare l'opzione **/AcceptDisclaimerAndShareDiagnostics**. 
    >
    >Se si ha un ticket di supporto attivo con Microsoft e PerfInsights è in esecuzione su richiesta del tecnico del supporto, specificare il numero del ticket tramite l'opzione **/sr**.
    >
    >Per impostazione predefinita, PerfInsights proverà a eseguire l'aggiornamento alla versione più recente, se disponibile. Per ignorare l'aggiornamento automatico, usare il parametro **/SkipAutoUpdate** o **/sau**.  
    >
    >Se l'opzione relativa alla durata **/d** non è specificata, PerfInsights chiederà all'utente di riprodurre il problema durante l'esecuzione dello scenario di analisi della macchina virtuale lenta, di quello di File di Azure e di quello personalizzato. 

Al termine delle operazioni o delle tracce, viene visualizzato un nuovo file in D:\\log\_collection o nell'unità temporanea. Il nome del file è **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip.** È possibile inviare il file all'agente di supporto per l'analisi oppure aprire il report all'interno del file zip per esaminare i risultati e i consigli.

## <a name="review-the-diagnostics-report"></a>Esaminare il rapporto di diagnostica

Nel file **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip,** è possibile trovare un report HTML che elenca in dettaglio i risultati di PerfInsights. Per esaminare il report, espandere il file **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip** e quindi aprire il file **PerfInsights Report.html**.

Selezionare la scheda **Risultati**.

![Screenshot del report di PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![Screenshot del report di PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> I risultati classificati come critici sono problemi noti che possono causare problemi di prestazioni. I risultati classificati come importanti rappresentano configurazioni non ottimali che non necessariamente causano problemi di prestazioni. I risultati classificati come informativi sono solo istruzioni informative.

Esaminare gli elementi consigliati e i collegamenti per tutti i risultati critici e importanti. Informazioni su come questi possono influenzare le prestazioni e sulle procedure consigliate per configurazioni ottimizzate per le prestazioni.

### <a name="storage-tab"></a>Scheda Archiviazione

Nella sezione **Risultati** vengono visualizzate i risultati e le raccomandazioni inerenti all'archiviazione.

Le sezioni **Disk Map** (Mapping del disco) e **Volume Map** (Mapping del volume) descrivono la correlazione tra i volumi logici e i dischi fisici.

Dal punto di vista del disco fisico, ovvero Disk Map (Mapping del disco), la tabella mostra tutti i volumi logici in esecuzione sul disco. Nell'esempio seguente **PhysicalDrive2** esegue due volumi logici creati su più partizioni, J e H:

![Screenshot della scheda del disco](media/how-to-use-perfInsights/disktab.png)

Dal punto di vista del volume, ovvero Volume Map (Mapping del volume), le tabelle mostrano tutti i dischi fisici in ogni volume logico. Si noti che per i dischi RAID/dinamici è possibile eseguire un volume logico su più dischi fisici. Nell'esempio seguente *C:\\mount* è un punto di montaggio configurato come *SpannedDisk* nei dischi fisici 2 e 3:

![Screenshot della scheda del volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Scheda SQL

Se la macchina virtuale di destinazione ospita istanze di SQL Server, nel report viene visualizzata una scheda aggiuntiva denominata **SQL**:

![Screenshot della scheda SQL](media/how-to-use-perfInsights/sqltab.png)

Questa sezione contiene una scheda **Risultati** e altre schede per ogni istanza di SQL Server ospitata nella macchina virtuale.

La scheda **Risultati** contiene un elenco di tutti i problemi di prestazioni correlati a SQL rilevati, con i relativi consigli.

Nell'esempio seguente viene visualizzato **PhysicalDrive0** che esegue l'unità C, perché entrambi i file **modeldev** e **modellog** si trovano nell'unità C e sono di tipo diverso, ad esempio file di dati e log delle transazioni rispettivamente.

![Screenshot delle informazioni del log](media/how-to-use-perfInsights/loginfo.png)

Le schede per istanze specifiche di SQL Server contengono una sezione generale che mostra informazioni di base sull'istanza selezionata. Le schede contengono anche sezioni aggiuntive per le informazioni avanzate, tra cui impostazioni, configurazioni e opzioni utente.

### <a name="diagnostic-tab"></a>Scheda Diagnostica
La scheda **Diagnostica** contiene informazioni sui principali consumer di CPU, dischi e memoria nel computer per la durata dell'esecuzione di PerfInsights. È possibile trovare anche informazioni sulle patch critiche non installate nel sistema, l'elenco delle attività e importanti eventi di sistema. 

## <a name="references-to-the-external-tools-used"></a>Informazioni di riferimento sugli strumenti esterni usati

### <a name="diskspd"></a>Diskspd

Diskspd è un generatore di carico di archiviazione e uno strumento di test delle prestazioni di Microsoft. Per altre informazioni, vedere [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf è uno strumento da riga di comando per acquisire tracce da Windows Performance Toolkit. Per altre informazioni, vedere [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/) (Windows Performance Toolkit - Xperf).

## <a name="next-steps"></a>Passaggi successivi

È possibile caricare i log di diagnostica e i report nel supporto tecnico Microsoft per eseguire altre analisi. Il supporto potrebbe richiedere all'utente di trasmettere l'output generato da PerfInsights per facilitare il processo di risoluzione dei problemi.

Lo screenshot seguente mostra un messaggio simile a quello che l'utente potrebbe ricevere:

![Screenshot del messaggio di esempio del supporto tecnico Microsoft](media/how-to-use-perfInsights/supportemail.png)

Seguire le istruzioni nel messaggio per accedere all'area di lavoro di trasferimento del file. Per maggiore sicurezza, l'utente dovrà cambiare la password al primo uso.

Dopo aver effettuato l'accesso, verrà visualizzata una finestra di dialogo per caricare il file **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip** raccolto da PerfInsights.
