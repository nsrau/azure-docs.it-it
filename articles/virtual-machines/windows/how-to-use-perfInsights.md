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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>Come usare PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) è uno script automatico che consente di raccogliere informazioni di diagnostica utili. Esegue anche carichi di stress di I/O e crea un report di analisi per facilitare la risoluzione dei problemi di prestazioni della macchina virtuale Windows in Azure. Può essere eseguito sulle macchine virtuali come script autonomo o direttamente dal portale installando l'[estensione della macchina virtuale Diagnostica delle prestazioni di Azure](performance-diagnostics-vm-extension.md).

Se si verificano problemi di prestazioni con le macchine virtuali, eseguire lo script prima di contattare il supporto.

## <a name="supported-troubleshooting-scenarios"></a>Scenari per la risoluzione dei problemi supportati

PerfInsights può raccogliere e analizzare diversi tipi di informazioni. Le sezioni seguenti trattano scenari comuni.

### <a name="collect-basic-configuration"></a>Raccolta della configurazione di base 

Questo scenario consente di raccogliere la configurazione dei dischi e altre importanti informazioni, tra cui:

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

Questo scenario esegue il test di benchmark [Diskspd](https://github.com/Microsoft/diskspd), ovvero delle operazioni di I/O al secondo e di Mbps, per tutte le unità collegate alla macchina virtuale. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della macchina virtuale.
>

### <a name="slow-vm-analysis"></a>Analisi di una macchina virtuale lenta 

Questo scenario esegue una traccia dei [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando i contatori specificati nel file Generalcounters.txt. Se la macchina virtuale viene identificata come server che esegue SQL Server, esegue una traccia del contatore delle prestazioni usando i contatori presenti nel file Sqlcounters.txt e include anche i dati di diagnostica delle prestazioni.

### <a name="slow-vm-analysis-and-benchmarking"></a>Analisi di una macchina virtuale lenta e test di benchmark

Questo scenario esegue una traccia del [contatore delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) seguita da un test di benchmark [Diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della macchina virtuale.
>

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

Quando si esegue un'analisi personalizzata di una macchina virtuale lenta, l'utente deve selezionare le tracce da eseguire in parallelo. Se lo si desidera è possibile eseguirle tutte, ad esempio contatore delle prestazioni, Xperf, rete e StorPort. Al termine dell'analisi, lo strumento esegue il benchmark Diskspd, se selezionato. 

> [!Note]
> Questo scenario può influire sul sistema e non deve essere eseguito in un sistema di produzione live. Se necessario, eseguire questo scenario in una finestra di manutenzione dedicata per evitare qualsiasi problema. Un maggiore carico di lavoro causato da un test di traccia o di benchmark può influire negativamente sulle prestazioni della macchina virtuale.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Tipo di informazioni raccolte dallo script

Vengono raccolte informazioni sulla macchina virtuale Windows, sulla configurazione dei dischi o dei pool di archiviazione, sui contatori delle prestazioni, sui log e su diverse tracce, a seconda dello scenario delle prestazioni usato. La tabella seguente contiene informazioni dettagliate:

|Dati raccolti                              |  |  | Scenari delle prestazioni |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Raccolta della configurazione di base | Benchmarking | Analisi di una macchina virtuale lenta | Analisi di una macchina virtuale lenta e test di benchmark | Analisi di File di Azure | Analisi personalizzata di una macchina virtuale lenta |
| Informazioni dei registri eventi      | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni di sistema               | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Mapping del volume                       | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Mapping del disco                         | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Attività in esecuzione                    | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Contatori di affidabilità di archiviazione     | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni sull'archiviazione              | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Output di fsutil                    | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Informazioni sul driver filtro               | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Output di netstat                   | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Network configuration            | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Configurazione del firewall           | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Configurazione di SQL Server         | Sì                        | Sì                                | Sì                      | Sì                            | Sì                  | Sì                  |
| Tracce di diagnostica delle prestazioni * | Sì                        | Sì                                | Sì                      |                                | Sì                  | Sì                  |
| Traccia del contatore delle prestazioni **     |                            |                                    |                          |                                |                      | Sì                  |
| Traccia del contatore SMB **             |                            |                                    |                          |                                | Sì                  |                      |
| Traccia del contatore SQL Server **      |                            |                                    |                          |                                |                      | Sì                  |
| Traccia di XPerf                      |                            |                                    |                          |                                |                      | Sì                  |
| Traccia di StorPort                   |                            |                                    |                          |                                |                      | Sì                  |
| Traccia di rete                    |                            |                                    |                          |                                | Sì                  | Sì                  |
| Traccia del benchmark Diskspd ***      |                            | Sì                                |                          | Sì                            |                      |                      |
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

## <a name="run-the-perfinsights-script-on-your-vm"></a>Eseguire lo script PerfInsights nella macchina virtuale

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Informazioni importanti prima di eseguire lo script 

#### <a name="script-requirements"></a>Requisiti dello script

-  Questo script deve essere eseguito nella VM che presenta il problema relativo alle prestazioni. 

-  Sono supportati i sistemi operativi seguenti: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>Possibili problemi quando si esegue lo script nelle macchine virtuali di produzione

-  Per gli scenari di benchmark o per lo scenario Analisi personalizzata di una macchina virtuale lenta configurati per usare XPerf o Diskspd, lo script può influire negativamente sulle prestazioni della macchina virtuale. Non eseguire questi scenari in un ambiente di produzione.

-  Per gli scenari di benchmark o per lo scenario Analisi personalizzata di una macchina virtuale lenta configurati per usare Diskspd, verificare che nessun'altra attività in background interferisca con il carico di lavoro di I/O.

-  Per impostazione predefinita, lo script usa l'unità di archiviazione temporanea per raccogliere i dati. Se la traccia rimane abilitata per un periodo più lungo, la quantità di dati raccolti potrebbe essere rilevante. Ciò può ridurre la disponibilità di spazio nel disco temporaneo e può influire sulle applicazioni che si basano su questa unità.

### <a name="how-do-i-run-perfinsights"></a>Come si esegue PerfInsights 

È possibile eseguire PerfInsights su una macchina virtuale installando l'[estensione della macchina virtuale Diagnostica delle prestazioni di Azure](performance-diagnostics-vm-extension.md). È anche possibile eseguirlo come script autonomo. 

**Installare ed eseguire PerfInsights dal portale di Azure**

Per altre informazioni su questa opzione, vedere [Installare l'estensione della macchina virtuale Diagnostica delle prestazioni di Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Eseguire lo script di PerfInsights in modalità autonoma**

Per eseguire lo script di PerfInsights, seguire questa procedura:


1. Scaricare [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Sbloccare il file PerfInsights.zip Per eseguire questa operazione fare clic con il pulsante destro del mouse sul file PerfInsights.zip e selezionare **Proprietà**. Nella scheda **Generale** selezionare **Sblocca** e quindi fare clic su **OK**. In tal modo, lo script viene eseguito senza altri prompt di sicurezza.  

    ![Screenshot delle proprietà di PerfInsights con l'opzione Sblocca evidenziata](media/how-to-use-perfInsights/unlock-file.png)

3.  Espandere il file PerfInsights.zip compresso nell'unità temporanea. Per impostazione predefinita, in genere è l'unità D. Il file compresso conterrà i file e le cartelle seguenti:

    ![Screenshot dei file nella cartella compressa](media/how-to-use-perfInsights/file-folder.png)

4.  Aprire Windows PowerShell come amministratore e quindi eseguire lo script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Potrebbe essere necessario immettere "y"per confermare che si desidera modificare i criteri di esecuzione.

    Nella finestra di dialogo **Notice and Consent** (Informativa e consenso) è presente l'opzione per condividere le informazioni di diagnostica con il supporto tecnico Microsoft. È anche necessario dare il consenso al contratto di licenza per continuare. Selezionare le caselle appropriate e quindi fare clic su **Esegui script**.

    ![Screenshot della finestra di dialogo Notice and Consent (Informativa e consenso)](media/how-to-use-perfInsights/disclaimer.png)

5.  Quando si esegue lo script, se disponibile, inviare il numero di caso. Selezionare **OK**.
    
    ![Screenshot della finestra di dialogo dell'ID di supporto](media/how-to-use-perfInsights/enter-support-number.png)

6.  Selezionare l'unità di archiviazione temporanea. Lo script può rilevare automaticamente la lettera di unità. Se si verificano problemi in questa fase, potrebbe venire chiesto di selezionare l'unità. L'unità predefinita è D. I log generati vengono archiviati qui, nella cartella log\_collection. Dopo avere immesso o accettato la lettera di unità, selezionare **OK**.

    ![Screenshot della finestra di dialogo dell'unità temporanea](media/how-to-use-perfInsights/enter-drive.png)

7.  Selezionare uno scenario di risoluzione dei problemi dall'elenco fornito.

       ![Screenshot dell'elenco di scenari per la risoluzione dei problemi](media/how-to-use-perfInsights/select-scenarios.png)

È anche possibile eseguire PerfInsights senza l'interfaccia utente. Il comando seguente esegue lo scenario di risoluzione dei problemi Analisi di una macchina virtuale lenta senza l'interfaccia utente. Viene chiesto il consenso per la stessa dichiarazione di non responsabilità e le stesse condizioni di licenza citate nel passaggio 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Per eseguire PerfInsights in modalità non interattiva, usare il parametro **-AcceptDisclaimerAndShareDiagnostics**. Ad esempio, usare il comando seguente:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Come risolvere i problemi durante l'esecuzione dello script

Se lo script viene terminato in modo anomalo, è possibile tornare a uno stato di coerenza eseguendo lo script con l'opzione di pulizia, come segue:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Se si verificano problemi durante il rilevamento automatico dell'unità temporanea, potrebbe venire chiesto di selezionare l'unità. L'unità predefinita è D.

![Screenshot della finestra di dialogo dell'unità temporanea](media/how-to-use-perfInsights/enter-drive.png)

Lo script disinstalla gli strumenti dell'utilità e rimuove le cartelle temporanee.

### <a name="troubleshoot-other-script-issues"></a>Risolvere altri problemi di script 

Se si verificano problemi quando si esegue lo script, premere CTRL+C per interromperlo. Se si continuano a verificare errori di script anche dopo più tentativi, eseguire lo script in modalità di debug usando l'opzione del parametro "-Debug" all'avvio.

Quando si verifica l'errore, copiare l'output completo della console di PowerShell e inviarlo all'agente del supporto tecnico Microsoft che sta prestando assistenza nella risoluzione del problema.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Come eseguire lo script in modalità Analisi personalizzata di una macchina virtuale lenta

Selezionando **Analisi personalizzata di una macchina virtuale lenta**, è possibile abilitare più tracce in parallelo. Usare il tasto MAIUSC per la selezione multipla delle tracce:

![Screenshot dell'elenco di scenari](media/how-to-use-perfInsights/select-scenario.png)

Quando si selezionano gli scenari Performance Counter Trace (Traccia contatore delle prestazioni), Xperf Trace (Traccia XPerf), Network Trace (Traccia di rete) o Storport Trace (Traccia StorPort), seguire le istruzioni delle finestre di dialogo successive. Provare a riprodurre il problema di rallentamento delle prestazioni dopo aver avviato le tracce.

La finestra di dialogo seguente consente di avviare una traccia:

![Screenshot della finestra di dialogo Start Performance Counter Trace (Avvia traccia contatore delle prestazioni)](media/how-to-use-perfInsights/start-trace-message.png)

Per arrestare le tracce, è necessario confermare il comando in una seconda finestra di dialogo.

![Screenshot della finestra di dialogo Stopping Performance Counter Trace (Arresto della traccia contatore delle prestazioni)](media/how-to-use-perfInsights/stop-trace-message.png)
![Screenshot della finestra di dialogo Stopping All traces (Arresto di tutte le tracce)](media/how-to-use-perfInsights/ok-trace-message.png)

Al termine delle operazioni o delle tracce, viene visualizzato un nuovo file in D:\\log\_collection o nell'unità temporanea. Il nome del file è **CollectedData\_aaaa-MM-gg\_hh\_mm\_ss.zip.** È possibile inviare il file all'agente di supporto per l'analisi.

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
