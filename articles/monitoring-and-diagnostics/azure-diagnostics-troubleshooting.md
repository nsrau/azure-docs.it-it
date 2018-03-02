---
title: Risoluzione dei problemi relativi a Diagnostica di Azure | Documentazione Microsoft
description: Risoluzione dei problemi per l'uso di Diagnostica di Azure nelle macchine virtuali di Azure, in Service Fabric o in Servizi cloud.
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.openlocfilehash: e194c2898616d5a19782039d38592c59f6b0c576
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="azure-diagnostics-troubleshooting"></a>Risoluzione dei problemi di Diagnostica di Azure
Questo articolo contiene informazioni sulla risoluzione dei problemi relativi all'uso di Diagnostica di Azure. Per altre informazioni su Diagnostica di Azure, vedere la [panoramica di Diagnostica di Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Componenti logici
**Utilità di avvio del plug-in di diagnostica (DiagnosticsPluginLauncher.exe)**: avvia l'estensione Diagnostica di Azure. Svolge la funzione di processo del punto di ingresso.

**Plug-in di Diagnostica (DiagnosticsPlugin.exe)**: configura e avvia l'agente di monitoraggio e ne gestisce il ciclo di vita. È il principale processo avviato dall'utilità di avvio.

**Agente di monitoraggio (processi MonAgent\*.exe)**: monitora, raccoglie e trasferisce i dati di diagnostica.  

## <a name="logartifact-paths"></a>Percorsi di log ed elementi
Di seguito sono elencati i percorsi di alcuni log ed elementi importanti. Nel resto del documento verrà fatto riferimento a queste informazioni.

### <a name="azure-cloud-services"></a>Servizi cloud di Azure
| Elemento | path |
| --- | --- |
| **File di configurazione di Diagnostica di Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versione>\Config.txt |
| **File di log** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versione>\ |
| **Archivio locale dei dati di diagnostica** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<NomeRuolo>.DiagnosticStore\WAD0107\Tables |
| **File di configurazione dell'agente di monitoraggio** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<NomeRuolo>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacchetto dell'estensione Diagnostica di Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versione> |
| **Percorso dell'utilità di raccolta dei log** | %SystemDrive%\Packages\GuestAgent\ |
| **File di log MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Macchine virtuali
| Elemento | path |
| --- | --- |
| **File di configurazione di Diagnostica di Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versione>\RuntimeSettings |
| **File di log** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Archivio locale dei dati di diagnostica** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<VersioneDiagnostica>\WAD0107\Tables |
| **File di configurazione dell'agente di monitoraggio** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<VersioneDiagnostica>\WAD0107\Configuration\MaConfig.xml |
| **File di stato** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versione>\Status |
| **Pacchetto dell'estensione Diagnostica di Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<VersioneDiagnostica>|
| **Percorso dell'utilità di raccolta dei log** | C:\WindowsAzure\Packages |
| **File di log MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Mancata visualizzazione dei dati relativi alle metriche nel portale di Azure
Diagnostica di Azure offre dati relativi alle metriche che possono essere visualizzati nel portale di Azure. In caso di problemi con la visualizzazione dei dati nel portale, controllare la tabella WADMetrics\* nell'account di archiviazione di Diagnostica di Azure per verificare se siano presenti i record delle metriche corrispondenti. 

Il valore **PartitionKey** della tabella è l'ID risorsa, la macchina virtuale o il set di scalabilità di macchine virtuali. **RowKey** è il nome della metrica (detto anche nome del contatore delle prestazioni).

Se l'ID risorsa non è corretto, controllare in **Diagnostics** **Configuration** (Configurazione di diagnostica) > **Metriche** > **ResourceId** se l'ID risorsa è impostato correttamente.

Se non sono presenti dati per la metrica specifica, controllare in **Diagnostics Configuration** (Configurazione di diagnostica) > **PerformanceCounter** se la metrica (contatore delle prestazioni) è inclusa. Per impostazione predefinita sono abilitati i contatori seguenti:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET Applications(__Total__)\Requests/Sec
- \ASP.NET Applications(__Total__)\Errors Total/Sec
- \ASP.NET\Requests Queued
- \ASP.NET\Requests Rejected
- \Processor(w3wp)\% Processor Time
- \Process(w3wp)\Private Bytes
- \Process(WaIISHost)\% Processor Time
- \Process(WaIISHost)\Private Bytes
- \Process(WaWorkerHost)\% Processor Time
- \Process(WaWorkerHost)\Private Bytes
- \Memory\Page Faults/sec
- \.NET CLR Memory(_Global_)\% Time in GC
- \LogicalDisk(C:)\Disk Write Bytes/sec
- \LogicalDisk(C:)\Disk Read Bytes/sec
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk(D:)\Disk Read Bytes/sec

Se la configurazione è impostata correttamente ma i dati relativi alle metriche non vengono comunque visualizzati, usare le linee guida seguenti per risolvere il problema.


## <a name="azure-diagnostics-isnt-starting"></a>Mancato avvio di Diagnostica di Azure
Per informazioni sui motivi per cui non è stato possibile avviare Diagnostica di Azure, vedere i file **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** nel percorso dei file di log riportati in precedenza. 

Se questi log indicano `Monitoring Agent not reporting success after launch`, significa che si è verificato un errore di avvio di MonAgentHost.exe. Esaminare i log nel percorso indicato per `MonAgentHost log file` nella sezione precedente.

L'ultima riga del file di log contiene il codice di uscita.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se si trova un codice di uscita **negativo**, vedere la [tabella dei codici di uscita](#azure-diagnostics-plugin-exit-codes) nella sezione [Riferimenti](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Mancata registrazione dei dati di diagnostica in Archiviazione di Azure
Determinare se i dati non vengono visualizzati interamente o in parte.

### <a name="diagnostics-infrastructure-logs"></a>Log dell'infrastruttura di diagnostica
Diagnostica registra tutti gli errori nei log dell'infrastruttura di diagnostica. Verificare di aver abilitato l'[acquisizione dei log dell'infrastruttura di diagnostica nella configurazione](#how-to-check-diagnostics-extension-configuration). È quindi possibile cercare rapidamente gli errori pertinenti riportati nella tabella `DiagnosticInfrastructureLogsTable` nell'account di archiviazione configurato.

### <a name="no-data-is-appearing"></a>Nessun dato visualizzato
La causa più comune per cui non viene visualizzato alcun dato di eventi è la definizione non corretta delle informazioni dell'account di archiviazione.

Soluzione: correggere la configurazione della diagnostica e reinstallare Diagnostica.

Se l'account di archiviazione è configurato correttamente, accedere in remoto al computer e verificare che DiagnosticsPlugin.exe e MonAgentCore.exe siano in esecuzione. Se non sono in esecuzione, seguire la procedura illustrata in [Mancato avvio di Diagnostica di Azure](#azure-diagnostics-is-not-starting). 

Se i processi sono in esecuzione, passare a [I dati vengono acquisiti in locale?](#is-data-getting-captured-locally) e seguire le istruzioni riportate in tale sottoparagrafo.

### <a name="part-of-the-data-is-missing"></a>Alcuni dati sono mancanti
Se vengono visualizzati alcuni dati ma non tutti, la pipeline di raccolta/trasferimento dati è impostata correttamente. Seguire le istruzioni di questi sottoparagrafi per circoscrivere il problema.

#### <a name="is-the-collection-configured"></a>La raccolta è configurata? 
La configurazione di Diagnostica contiene istruzioni per la raccolta di un determinato tipo di dati. [Esaminare la configurazione](#how-to-check-diagnostics-extension-configuration) per assicurarsi di cercare solo i dati per cui è stata configurata la raccolta.

#### <a name="is-the-host-generating-data"></a>L'host genera dati?
- **Contatori delle prestazioni**: aprire perfmon e controllare il contatore.

- **Log di traccia**: accedere in remoto alla VM e aggiungere TextWriterTraceListener al file di configurazione dell'app.  Vedere http://msdn.microsoft.com/library/sk36c28t.aspx per impostare il listener di testo.  Verificare che l'elemento `<trace>` includa `<trace autoflush="true">`.<br />
Se non risulta che i log di traccia vengano generati, vedere [Altre informazioni sui log di traccia mancanti](#more-about-trace-logs-missing).

- **Tracce ETW**: accedere in remoto alla VM e installare PerfView.  In PerfView eseguire **File** > **User Command** > **Listen etwprovder1** > **etwprovider2** (File > Comando utente -> Ascolto etwprovder1 > etwprovider2) e così via. Nel **comando di ascolto** viene fatta distinzione tra maiuscole e minuscole e nell'elenco separato da virgole dei provider ETW non possono essere presenti spazi. Se l'esecuzione del comando ha esito negativo, è possibile selezionare il pulsante **Log** in basso a destra nello strumento Perfview per visualizzare i tentativi di esecuzione e i risultati.  Se l'input è corretto, verrà aperta una nuova finestra. Dopo pochi secondi inizieranno a essere visualizzate le tracce ETW.

- **Log eventi**: accedere in remoto alla VM. Aprire `Event Viewer` e quindi verificare che gli eventi siano presenti.

#### <a name="is-data-getting-captured-locally"></a>I dati vengono acquisiti in locale?
Verificare quindi che i dati vengano acquisiti in locale.
I dati vengono archiviati in locale nei file `*.tsf` nell'[archivio locale per i dati di diagnostica](#log-artifacts-path). I diversi tipi di log vengono raccolti in file `.tsf` diversi. I nomi sono simili a quelli delle tabelle in Archiviazione di Azure. 

Ad esempio, i log di `Performance Counters` vengono raccolti in `PerformanceCountersTable.tsf`, mentre i log eventi vengono raccolti in `WindowsEventLogsTable.tsf`. Usare le istruzioni riportate nella sezione [Estrazione dei log locali](#local-log-extraction) per aprire i file di raccolta locali e verificare che vengano raccolti su disco.

Se non risulta che i log vengano raccolti in locale e si è già verificato che l'host genera dati, è probabile che sia presente un problema di configurazione. Esaminare attentamente la configurazione. 

Analizzare anche la configurazione generata per il file [MaConfig.xml](#log-artifacts-path) dell'agente di monitoraggio. Verificare che sia presente una sezione che descrive l'origine di log pertinente e quindi che non sia andata persa nella conversione tra la configurazione di Diagnostica e la configurazione dell'agente di monitoraggio.

#### <a name="is-data-getting-transferred"></a>I dati vengono trasferiti?
Se è stato verificato che i dati vengono acquisiti in locale ma non vengono comunque visualizzati nell'account di archiviazione, seguire questa procedura: 

- Verificare di aver specificato un account di archiviazione corretto e che non sia stato eseguito il rollover di chiavi per l'account di archiviazione indicato. Per Servizi cloud di Azure, si riscontra talvolta che gli utenti non aggiornano `useDevelopmentStorage=true`.

- Verificare che l'account di archiviazione specificato sia corretto. Assicurarsi che non siano presenti restrizioni di rete che impediscono ai componenti di raggiungere gli endpoint di archiviazione pubblici. Questa operazione può essere eseguita accedendo in remoto al computer e provando quindi a scrivere nello stesso account di archiviazione.

- È infine possibile esaminare gli errori segnalati dall'agente di monitoraggio. L'agente di monitoraggio scrive i log in `maeventtable.tsf`, che si trova nell'[archivio locale dei dati di diagnostica](#log-artifacts-path). Per aprire questo file, seguire le istruzioni riportate nella sezione [Estrazione dei log locali](#local-log-extraction). Provare quindi a determinare se siano presenti `errors` che indicano errori di lettura nei file locali o scrittura nelle risorse di archiviazione.

### <a name="capturing-and-archiving-logs"></a>Acquisizione e archiviazione dei log
Se si intende contattare il supporto tecnico, considerare che per prima cosa potrebbe essere richiesto di raccogliere i log del computer. È possibile risparmiare tempo eseguendo questa operazione autonomamente. Eseguire `CollectGuestLogs.exe` nel [percorso dell'utilità di raccolta dei log](#log-artifacts-path). Verrà generato un file ZIP con tutti i log di Azure pertinenti nella stessa cartella.

## <a name="diagnostics-data-tables-not-found"></a>Tabelle dei dati di diagnostica non trovate
Le tabelle in Archiviazione di Azure contenenti gli eventi ETW vengono denominate usando il codice seguente:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Di seguito è fornito un esempio: 

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Questo codice genera quattro tabelle:

| Event | Nome tabella |
| --- | --- |
| provider="prov1" &lt;Event id=v1" /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>Riferimenti

### <a name="how-to-check-diagnostics-extension-configuration"></a>Come controllare la configurazione dell'estensione Diagnostica
Il modo più semplice per controllare la configurazione dell'estensione consiste nel passare ad [Azure Resource Explorer](http://resources.azure.com) e quindi alla macchina virtuale o al servizio cloud in cui si trova l'estensione Diagnostica di Azure (IaaSDiagnostics/PaasDiagnostics).

In alternativa, connettersi tramite desktop remoto al computer ed esaminare il file di configurazione di Diagnostica di Azure descritto nella sezione [Percorsi di log ed elementi](#log-artifacts-path).

In entrambi i casi cercare **Microsoft.Azure.Diagnostics** e quindi il campo **xmlCfg** o **WadCfg**. 

Se si esegue la ricerca su una macchina virtuale ed è presente il campo **WadCfg**, la configurazione è in formato JSON. Se è presente il campo **xmlCfg**, la configurazione è in formato XML con codifica Base64. Per visualizzare il codice XML caricato da Diagnostica è necessario [decodificare](http://www.bing.com/search?q=base64+decoder) il file.

Per il ruolo del servizio cloud, se si seleziona la configurazione dal disco, ai dati è applicata la codifica Base64 ed è quindi necessario [decodificarli](http://www.bing.com/search?q=base64+decoder) per visualizzare il codice XML caricato da Diagnostica.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Codici di uscita del plug-in di Diagnostica di Azure
Il plug-in restituisce i seguenti codici di uscita:

| Codice di uscita | DESCRIZIONE |
| --- | --- |
| 0 |Completamento della procedura. |
| -1 |Errore generico. |
| -2 |Impossibile caricare il file rcf.<p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente in modo non corretto sulla VM. |
| -3 |Impossibile caricare il file di configurazione di Diagnostica.<p><p>Soluzione: questo errore si verifica quando un file di configurazione non ha superato la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. |
| -4 |La directory delle risorse locali è già usata da un'altra istanza dell'agente di monitoraggio di Diagnostica.<p><p>Soluzione: specificare un valore diverso per **LocalResourceDirectory**. |
| -6 |L'utilità di avvio del plug-in dell'agente guest ha tentato di avviare Diagnostica con una riga di comando non valida.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente in modo non corretto sulla VM. |
| -10 |Il plug-in di Diagnostica ha generato un'eccezione non gestita. |
| -11 |L'agente guest non è stato in grado di creare il processo responsabile dell'avvio e del monitoraggio dell'agente di monitoraggio.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.<p> |
| -101 |Argomenti non validi durante la chiamata del plug-in di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente in modo non corretto sulla VM. |
| -102 |Il processo del plug-in non è in grado di inizializzarsi.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi. |
| -103 |Il processo del plug-in non è in grado di inizializzarsi. In particolare, non riesce a creare l'oggetto logger.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi. |
| -104 |Impossibile caricare il file rcf fornito dall'agente guest.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente in modo non corretto sulla VM. |
| -105 |Il plug-in di Diagnostica non è in grado di aprire il file di configurazione di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente in modo non corretto sulla VM. |
| -106 |Impossibile leggere il file di configurazione di Diagnostica.<p><p>Questo errore si verifica quando un file di configurazione non supera la convalida dello schema. <br><br>Soluzione: specificare un file di configurazione conforme allo schema. Per altre informazioni, vedere [Come controllare la configurazione dell'estensione Diagnostica](#how-to-check-diagnostics-extension-configuration). |
| -107 |La directory delle risorse passata all'agente di monitoraggio non è valida.<p><p>Questo errore interno dovrebbe verificarsi solo se l'agente di monitoraggio viene richiamato manualmente in modo non corretto sulla VM.</p> |
| -108 |Impossibile convertire il file di configurazione di Diagnostica nel file di configurazione dell'agente di monitoraggio.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido. |
| -110 |Errore di configurazione generale di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido. |
| -111 |Impossibile avviare l'agente di monitoraggio.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti. |
| -112 |Errore generale: |

### <a name="local-log-extraction"></a>Estrazione dei log locali
L'agente di monitoraggio raccoglie log ed elementi come file `.tsf`. Il file con estensione `.tsf` non è leggibile ma può essere convertito in `.csv` come illustrato di seguito: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Un nuovo file denominato `<relevantLogFile>.csv` verrà creato nello stesso percorso del file con estensione `.tsf` corrispondente.

>[!NOTE] 
> È necessario eseguire questa utilità solo per il file con estensione tsf principale, ad esempio PerformanceCountersTable.tsf. I file associati, ad esempio PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf e così via, verranno elaborati automaticamente.

### <a name="more-about-missing-trace-logs"></a>Altre informazioni sui log di traccia mancanti 

>[!NOTE]
> Le informazioni seguenti si applicano prevalentemente a Servizi cloud di Azure, a meno che non si sia configurato DiagnosticsMonitorTraceListener in un'applicazione eseguita nella VM IaaS. 

- Verificare che in web.config o app.config sia configurato **DiagnosticMonitorTraceListener**.  Nei progetti di servizi cloud è configurato per impostazione predefinita, ma viene impostato come commento da alcuni clienti e in questo caso le istruzioni di traccia non verranno raccolte da Diagnostica. 

- Se i log non vengono scritti dal metodo **OnStart** o **Run**, verificare che in app.config sia presente **DiagnosticMonitorTraceListener**.  Per impostazione predefinita si trova in web.config, ma ciò si applica solo al codice eseguito in w3wp.exe. Per acquisire le tracce in esecuzione in WaIISHost.exe è necessario che sia presente in app.config.

- Verificare di usare **Diagnostics.Trace.TraceXXX** anziché **Diagnostics.Debug.WriteXXX**. Le istruzioni di debug vengono rimosse da una build di versione.

- Verificare che il codice compilato includa effettivamente le **righe Diagnostics.Trace** (per la verifica usare Reflector, ildasm o ILSpy). I comandi **Diagnostics.Trace** vengono rimossi dal file binario compilato a meno che non venga usato il simbolo di compilazione condizionale TRACE. Questo problema comune si verifica quando si usa msbuild per compilare un progetto.   

## <a name="known-issues-and-mitigations"></a>Problemi noti e procedure di prevenzione
Di seguito è riportato un elenco di problemi noti con le relative misure di prevenzione:

**1. Dipendenza da .NET 4.5**

L'estensione Diagnostica di Microsoft Azure presenta una dipendenza di runtime da .NET Framework 4.5 o versione successiva. Al momento della stesura di questo articolo, in tutti i computer sottoposti a provisioning per Servizi cloud di Azure e in tutte le immagini ufficiali basate su macchine virtuali di Azure è installato .NET 4.5 o versione successiva. 

È comunque possibile che si provi a eseguire l'estensione Diagnostica di Microsoft Azure in un computer in cui non è presente .NET 4.5 o versione successiva. Ciò si verifica quando si crea il computer da un'immagine o uno snapshot meno recente o si usa un disco personalizzato.

Questa situazione si manifesta in genere con un codice di uscita **255** durante l'esecuzione di **DiagnosticsPluginLauncher.exe**. L'errore è causato dall'eccezione non gestita seguente: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Prevenzione:** installare .NET 4.5 o versione successiva nel computer.

**2. I dati dei contatori delle prestazioni sono disponibili nella risorsa di archiviazione ma non vengono visualizzati nel portale**

Nell'esperienza del portale per le macchine virtuali vengono visualizzati per impostazione predefinita determinati contatori delle prestazioni. Se i contatori non vengono visualizzati e si è certi che i dati vengano generati perché sono disponibili nella risorsa di archiviazione, controllare quanto segue:

- Se i dati nella risorsa di archiviazione contengono i nomi dei contatori in lingua inglese. Se i nomi dei contatori non sono in inglese, il grafico delle metriche del portale non riesce a riconoscerli.

- Se si usano caratteri jolly (\*) nei nomi dei contatori delle prestazioni, il portale non può correlare il contatore configurato e il contatore raccolto.

**Prevenzione**: modificare la lingua del computer impostando l'inglese per gli account di sistema. A tale scopo, selezionare **Pannello di controllo** > **Area geografica** > **Opzioni di amministrazione** > **Copia impostazioni**. Deselezionare quindi **Schermata iniziale e account di sistema** affinché la lingua personalizzata non venga applicata all'account di sistema. Assicurarsi anche di non usare caratteri jolly se si vuole usare il portale come esperienza di utilizzo principale.
