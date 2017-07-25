---
title: Risoluzione dei problemi relativi a Diagnostica di Azure | Documentazione Microsoft
description: Risoluzione dei problemi per l&quot;uso di Diagnostica di Azure nelle macchine virtuali di Azure, in Service Fabric o in Servizi cloud.
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
ms.date: 03/28/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 0764b9f3ac262b7c65944d6e2c82490daefa54c3
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---
# <a name="azure-diagnostics-troubleshooting"></a>Risoluzione dei problemi di Diagnostica di Azure
Informazioni sulla risoluzione dei problemi relativi all'uso di Diagnostica di Azure. Per altre informazioni su Diagnostica di Azure, vedere [Panoramica di Diagnostica di Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Componenti logici
**Utilità di avvio del plug-in di diagnostica (DiagnosticsPluginLauncher.exe)**: avvia l'estensione Diagnostica di Azure. Svolge la funzione di processo del punto di ingresso.

**Plug-in di diagnostica (DiagnosticsPlugin.exe)**: processo principale avviato dall'utilità di avvio che configura l'agente di monitoraggio, lo avvia e ne gestisce il ciclo di vita. 

**Agente di monitoraggio (processi di MonAgent\*.exe)**: questi processi eseguono la maggior parte delle operazioni, ovvero monitoraggio, raccolta e trasferimento dei dati di diagnostica.  

## <a name="logartifact-paths"></a>Percorsi di log ed elementi
Di seguito sono elencati i percorsi di alcuni log ed elementi importanti. Nella parte rimanente del documento verrà fatto riferimento a questi elementi:
### <a name="cloud-services"></a>Servizi cloud
| Elemento | Path |
| --- | --- |
| **File di configurazione di Diagnostica di Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versione>\Config.txt |
| **File di log** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versione>\ |
| **Archivio locale dei dati di diagnostica** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<NomeRuolo>.DiagnosticStore\WAD0107\Tables |
| **File di configurazione dell'agente di monitoraggio** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<NomeRuolo>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacchetto dell'estensione di diagnostica di Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versione> |
| **Percorso dell'utilità di raccolta dei log** | %SystemDrive%\Packages\GuestAgent\ |

### <a name="virtual-machines"></a>Macchine virtuali
| Elemento | Path |
| --- | --- |
| **File di configurazione di Diagnostica di Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versione>\RuntimeSettings |
| **File di log** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versione>\Logs\ |
| **Archivio locale dei dati di diagnostica** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<VersioneDiagnostica>\WAD0107\Tables |
| **File di configurazione dell'agente di monitoraggio** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<VersioneDiagnostica>\WAD0107\Configuration\MaConfig.xml |
| **File di stato** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versione>\Status |
| **Pacchetto dell'estensione di diagnostica di Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<VersioneDiagnostica>|
| **Percorso dell'utilità di raccolta dei log** | C:\WindowsAzure\Packages |

## <a name="azure-diagnostics-is-not-starting"></a>Mancato avvio di Diagnostica Azure
Per informazioni sui motivi del mancato avvio della diagnostica, controllare i file **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** dal percorso dei file di log indicato in precedenza.  

L'ultima riga del file di log contiene il codice di uscita.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se si trova un codice di uscita **negativo**, fare riferimento alla [tabella dei codici di uscita](#azure-diagnostics-plugin-exit-codes) in [Riferimenti](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>I dati di diagnostica non vengono registrati in Archiviazione di Azure
Determinare se non vengono visualizzati tutti i dati o parte di essi.

### <a name="diagnostics-infrastructure-logs"></a>Log dell'infrastruttura di diagnostica
La diagnostica di Azure registra gli errori rilevati nei log dell'infrastruttura di diagnostica. Assicurarsi di aver abilitato ([Come?](#how-to-check-diagnostics-extension-configuration)) l'acquisizione dei log dell'infrastruttura di diagnostica nella configurazione ed esaminare rapidamente eventuali errori rilevanti visualizzati nella tabella `DiagnosticInfrastructureLogsTable` nell'account di archiviazione configurato.

### <a name="no-data-is-showing-up"></a>Non sono visualizzati dati
La causa più comune della mancanza di tutti i dati degli eventi è la definizione non corretta delle informazioni sull'account di archiviazione.

Soluzione: correggere la configurazione della diagnostica e reinstallare Diagnostica.

Se l'account di archiviazione è configurato correttamente, connettersi tramite desktop remoto al computer e assicurarsi che DiagnosticsPlugin.exe e MonAgentCore.exe siano in esecuzione. Se non sono in esecuzione, seguire le istruzioni in [Mancato avvio di Diagnostica Azure](#azure-diagnostics-is-not-starting). Se i processi sono in esecuzione, passare a [I dati vengono acquisiti in locale](#is-data-getting-captured-locally) e seguire le istruzioni rimanenti della guida.

### <a name="part-of-the-data-is-missing"></a>Alcuni dati sono mancanti
Alcuni dati sono presenti ma una parte dei dati è mancante. Ciò significa che la pipeline di raccolta dati/trasferimento è impostata correttamente. Attenersi alle istruzioni delle sezioni riportate di seguito per circoscrivere il problema:
#### <a name="is-collection-configured"></a>La raccolta è configurata: 
La configurazione della diagnostica include la parte che indica il tipo di dati specifico da raccogliere. [Rivedere la configurazione](#how-to-check-diagnostics-extension-configuration) per assicurarsi di non cercare dati che non sono stati configurati per la raccolta.
#### <a name="is-the-host-generating-data"></a>L'host genera i dati:
- **Contatori delle prestazioni**: aprire perfmon e controllare il contatore.
- **Log di traccia**: connettersi tramite desktop remoto alla macchina virtuale e aggiungere TextWriterTraceListener al file di configurazione dell'app.  Vedere http://msdn.microsoft.com/en-us/library/sk36c28t.aspx per impostare il listener di testo.  Verificare che l'elemento `<trace>` includa `<trace autoflush="true">`.<br />
Se i log di traccia non vengono generati, seguire le istruzioni in [Altre informazioni sui log di traccia mancanti](#more-about-trace-logs-missing).
 - **Tracce ETW**: connettersi tramite desktop remoto alla macchina virtuale e installare PerfView.  In PerfView eseguire File -> User Command -> Listen etwprovder1,etwprovider2 (File -> Comando utente -> Attesa etwprovder1,etwprovider2) e così via.  Si noti che nel comando di attesa viene fatta distinzione tra maiuscole e minuscole e non possono essere presenti spazi nell'elenco dei provider ETW separato da virgole.  Se il comando non viene eseguito è possibile fare clic sul pulsante 'Log' nella parte inferiore destra dello strumento Perfview per visualizzare i tentativi di esecuzione e i risultati.  Presupponendo che l'input sia corretto, viene visualizzata una nuova finestra e dopo pochi secondi saranno visibili le tracce ETW.
- **Log eventi**: connettersi tramite desktop remoto alla macchina virtuale. Aprire `Event Viewer` e assicurarsi che gli eventi siano presenti.
#### <a name="is-data-getting-captured-locally"></a>I dati vengono acquisiti in locale:
Assicurarsi quindi che i dati vengano acquisiti in locale.
I dati vengono archiviati in locale nei file `*.tsf` nell'[archivio locale per i dati di diagnostica](#log-artifacts-path). I diversi tipi di log vengono raccolti in file `.tsf` diversi. I nomi sono simili ai nomi delle tabelle nell'archiviazione di Azure. Ad esempio, `Performance Counters` viene raccolto in `PerformanceCountersTable.tsf`, i log eventi vengono raccolti in `WindowsEventLogsTable.tsf`. Usare le istruzioni nella sezione [Estrazione dei log locali](#local-log-extraction) per aprire i file di raccolta locali e assicurarsi che vengano raccolti su disco.

Se i log non vengono raccolti in locale ed è già stato verificato che l'host genera dati, è possibile che sia presente un problema di configurazione. Rivedere attentamente la configurazione della sezione appropriata. Rivedere anche la configurazione generata per l'agente di monitoraggio [MaConfig.xml](#log-artifacts-path) e assicurarsi che sia presente una sezione che descriva l'origine dei log e che non sia stata persa durante la conversione tra la configurazione di diagnostica di Azure e la configurazione dell'agente di monitoraggio.
#### <a name="is-data-getting-transferred"></a>I dati vengono trasferiti:
Se è stato verificato che i dati vengono acquisiti in locale e i dati non sono ancora visibili nell'account di archiviazione: 
- Innanzitutto verificare di aver specificato un account di archiviazione corretto e che non sia stato eseguito il rollover di chiavi e altri elementi per l'account di archiviazione specificato. Per i servizi cloud, a volte gli utenti non aggiornano `useDevelopmentStorage=true`.
- Se l'account di archiviazione specificato è corretto. Verificare che non siano presenti limitazioni di rete che non consentono ai componenti di raggiungere gli endpoint di archiviazione pubblici. Questa operazione può essere eseguita collegandosi tramite desktop remoto al computer e provando a scrivere nello stesso account di archiviazione.
- Infine, è possibile controllare gli errori segnalati dall'agente di monitoraggio. L'agente di monitoraggio scrive i log in `maeventtable.tsf` nell'[archivio locale dei dati di diagnostica](#log-artifacts-path). Seguire le istruzioni nella sezione [Estrazione dei log locali](#local-log-extraction) per aprire il file e verificare se sono presenti `errors` che indicano errori di lettura dei file locali o di scrittura nell'archiviazione.

### <a name="capturing--archiving-logs"></a>Acquisizione e archiviazione dei log
Sono stati eseguiti i passaggi descritti ma non è ancora stata individuata la causa del problema e si sta considerando la possibilità di rivolgersi al supporto tecnico. Il supporto tecnico potrebbe innanzitutto richiedere di raccogliere i registri del computer. È possibile risparmiare tempo eseguendo questa operazione autonomamente. Eseguire l'utilità `CollectGuestLogs.exe` nel [percorso di Raccolta di log](#log-artifacts-path). Verrà generato un file con estensione zip con tutti i log di Azure rilevanti nella stessa cartella.

## <a name="diagnostics-data-tables-not-found"></a>Tabelle dei dati di diagnostica non trovate
Le tabelle nell'archiviazione di Azure contenenti gli eventi ETW vengono denominate usando il codice seguente:

```C#
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
        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
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
Vengono generate 4 tabelle:

| Evento | Nome tabella |
| --- | --- |
| provider="prov1" &lt;Event id=v1" /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>Riferimenti

### <a name="how-to-check-diagnostics-extension-configuration"></a>Come controllare la configurazione dell'estensione di diagnostica
Il modo più semplice per controllare la configurazione dell'estensione consiste nel passare a http://resources.azure.com, quindi alla macchina virtuale o al servizio cloud in cui si vuole controllare l'estensione di diagnostica di Azure (IaaSDiagnostics/PaaDiagnostics).

In alternativa, connettersi tramite desktop remoto al computer ed esaminare il file di configurazione di Diagnostica di Azure descritto [qui](#log-artifacts-path) nella sezione appropriata.

In entrambi i casi cercare **Microsoft.Azure.Diagnostics** quindi cercare il campo **xmlCfg** o **WadCfg**. 

In caso di macchine virtuali se il campo WadCfg è presente, significa che il file di configurazione è in JSON. Se il campo xmlCfg è presente, significa che la configurazione è in formato XML e contiene la codifica Base64. Per visualizzare il codice XML caricato da Diagnostica è necessario [decodificare](http://www.bing.com/search?q=base64+decoder) il file.

Per il ruolo del servizio cloud, se si seleziona la configurazione dal disco, i dati sono codificati in base 64, pertanto sarà necessario [decodificarli](http://www.bing.com/search?q=base64+decoder) per visualizzare il codice XML caricato da Diagnostica.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Codici di uscita del plug-in Diagnostica di Azure
Il plug-in restituisce i seguenti codici di uscita:

| Codice di uscita | Descrizione |
| --- | --- |
| 0 |Completamento della procedura. |
| -1 |Errore generico. |
| -2 |Impossibile caricare il file rcf.<p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -3 |Impossibile caricare il file di configurazione di Diagnostica.<p><p>Soluzione: questo errore si verifica quando un file di configurazione non ha superato la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. |
| -4 |La directory delle risorse locali è già usata da un'altra istanza dell'agente di monitoraggio di Diagnostica.<p><p>Soluzione: specificare un valore diverso per **LocalResourceDirectory**. |
| -6 |L'utilità di avvio del plug-in dell'agente guest ha tentato di avviare Diagnostica con una riga di comando non valida.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -10 |Il plug-in di Diagnostica ha generato un'eccezione non gestita. |
| -11 |L'agente guest non è stato in grado di creare il processo responsabile dell'avvio e del monitoraggio dell'agente di monitoraggio.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.<p> |
| -101 |Argomenti non validi durante la chiamata del plug-in di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -102 |Il processo del plug-in non è in grado di inizializzarsi.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi. |
| -103 |Il processo del plug-in non è in grado di inizializzarsi. In particolare, non è in grado di creare l'oggetto logger.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi. |
| -104 |Impossibile caricare il file rcf fornito dall'agente guest.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -105 |Il plug-in di Diagnostica non è in grado di aprire il file di configurazione di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente, in modo non corretto, sulla macchina virtuale. |
| -106 |Impossibile leggere il file di configurazione di Diagnostica.<p><p>Soluzione: questo errore si verifica quando un file di configurazione non ha superato la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. Vedere [Come controllare la configurazione dell'estensione di diagnostica](#how-to-check-diagnostics-extension-configuration). |
| -107 |La directory delle risorse passata all'agente di monitoraggio non è valida.<p><p>Questo errore interno dovrebbe verificarsi solo se l'agente di monitoraggio viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p> |
| -108 |Impossibile convertire il file di configurazione di Diagnostica nel file di configurazione dell'agente di monitoraggio.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido. |
| -110 |Errore di configurazione generale di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido. |
| -111 |Impossibile avviare l'agente di monitoraggio.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti. |
| -112 |Errore generale: |

### <a name="local-log-extraction"></a>Estrazione dei log locali
L'agente di monitoraggio raccoglie log ed elementi come file `.tsf`. Il file `.tsf` non è leggibile ma è possibile convertirlo in `.csv` come illustrato di seguito: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Un nuovo file denominato `<relevantLogFile>.csv` verrà creato nello stesso percorso del file `.tsf` corrispondente.

**NOTA**: è necessario eseguire questa utilità solo nel file tsf principale, ad esempio PerformanceCountersTable.tsf. Gli altri file, ad esempio PerformanceCountersTables_\*\*001.tsf e PerformanceCountersTables_\*\*002.tsf, verranno elaborati automaticamente.

### <a name="more-about-trace-logs-missing"></a>Altre informazioni sui log di traccia mancanti

**Nota:** nella maggior parte dei casi ciò si applica solo ai servizi cloud, a meno che non sia stato configurato DiagnosticsMonitorTraceListener in un'applicazione in esecuzione nella macchina virtuale IaaS. 

- Verificare che DiagnosticMonitorTraceListener sia configurato in web.config o app.config.  DiagnosticMonitorTraceListener è configurato per impostazione predefinita nei progetti del servizio cloud, ma alcuni clienti hanno segnalato che in questo modo le istruzioni di traccia non verranno raccolte dalla diagnostica. 
- Se i log non vengono scritti dal metodo OnStart o Run, verificare che DiagnosticMonitorTraceListener si trovi in app.config.  Per impostazione predefinita DiagnosticMonitorTraceListener si trova in web.config, ma solo nel caso di codice eseguito in w3wp.exe. Per acquisire le tracce in esecuzione in WaIISHost.exe è necessario che si trovi in app.config.
- Assicurarsi di usare Diagnostics.Trace.TraceXXX anziché Diagnostics.Debug.WriteXXX.  Le istruzioni di debug verranno rimosse da una build di versione.
- Assicurarsi che il codice compilato includa le righe Diagnostics.Trace (usare Reflector, ildasm o ILSpy per verificare).  I comandi Diagnostics.Trace vengono rimossi dal file binario compilato a meno che non venga usato il simbolo di compilazione condizionale TRACE.  Questo problema è comune se si usa msbuild per la compilazione del progetto.

## <a name="known-issues-and-mitigations"></a>Problemi noti e prevenzione
Di seguito è riportato un elenco di problemi noti con le relative misure di prevenzione:

**1. Dipendenza di .NET 4.5:**

WAD presenta una dipendenza di runtime nel framework .NET 4.5 o versioni successive. Attualmente, in tutti i computer per i servizi cloud e in tutte le immagini di base di macchine virtuali di Azure ufficiali è installato .NET 4.5 o versioni successive. È possibile tuttavia che si verifichi la situazione in cui si tenti di eseguire WAD in un computer in cui non è installato .NET 4.5 o versioni successive. Ciò si verifica quando si crea il computer a partire da un'immagine o uno snapshot precedente o si usa un disco personalizzato.

Questa situazione si manifesta in genere come codice di uscita **255** durante l'esecuzione di DiagnosticsPluginLauncher.exe. L'errore si verifica a causa dell'eccezione non gestita: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Prevenzione:** installare .NET 4.5 o versione successiva nel computer.

**2. I dati dei contatori delle prestazioni sono disponibili nell'archiviazione ma non vengono visualizzati nel portale**

Il portale delle macchine virtuali visualizza determinati contatori delle prestazioni per impostazione predefinita. Se i contatori non sono visualizzati e si è certi che i dati vengano generati perché sono disponibili nell'archiviazione. Controllare:
- Se i dati nell'archiviazione hanno nomi di contatore in lingua inglese. Se i nomi dei contatori non sono in inglese, il grafico delle metriche del portale non sarà in grado di riconoscerli.
- Se si usano caratteri jolly (\*) nei nomi dei contatori delle prestazioni, il portale non sarà in grado di correlare il contatore configurato e il contatore raccolto.

**Prevenzione**: modificare la lingua del computer impostando l'inglese per gli account di sistema. Pannello di controllo -> Area geografica -> Opzioni di amministrazione -> Impostazioni copia -> deselezionare "Schermata iniziale e account di sistema" in modo che la lingua personalizzata non venga applicata all'account di sistema. Assicurarsi anche di non usare caratteri jolly per consentire al portale di rappresentare l'esperienza di utilizzo principale.

