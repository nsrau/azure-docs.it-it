---
title: Risolvere i problemi relativi a applicazione Azure Profiler Insights
description: Questo articolo presenta procedure di risoluzione dei problemi e informazioni utili per gli sviluppatori che hanno difficoltà ad abilitare o a usare Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aa9b186e74ed3b8fe5496afd5b21c54f50537d5f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049778"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Risolvere i problemi di abilitazione o visualizzazione di Application Insights Profiler

> [!CAUTION]
> È presente un bug che esegue Profiler per le app ASP.NET Core in app Azure servizio. È stata rilevata una correzione, ma la distribuzione a livello mondiale sarà necessaria alcune settimane. Per risolvere il problema, è possibile aggiungere l'SDK Application Insights all'applicazione con le istruzioni riportate [qui](./asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Risoluzione dei problemi generali

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>I profili vengono caricati solo se sono presenti richieste all'applicazione mentre Profiler è in esecuzione

Azure Application Insights Profiler raccoglie dati di profilatura per due minuti ogni ora. Raccoglie i dati anche quando si seleziona il pulsante **Profila ora** nel riquadro **Configura Application Insights Profiler**. I dati di profilatura vengono tuttavia caricati solo quando possono essere collegati a una richiesta avvenuta durante l'esecuzione di Profiler. 

Profiler scrive i messaggi di analisi e gli eventi personalizzati nella risorsa di Application Insights. È possibile usare questi eventi per monitorare l'esecuzione di Profiler. Se si ritiene che Profiler debba essere in esecuzione e acquisire le analisi ma nel riquadro **Prestazioni** le analisi non sono visualizzate, è possibile controllare l'esecuzione di Profiler:

1. Cercare i messaggi di analisi e gli eventi personalizzati inviati da Profiler alla risorsa di Application Insights. È possibile usare questa stringa di ricerca per trovare i dati rilevanti:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    L'immagine seguente illustra due esempi di ricerche da due risorse di Application Insights: 
    
   * Come si può vedere a sinistra, l'applicazione non riceve richieste durante l'esecuzione di Profiler. Il messaggio spiega che il caricamento è stato annullato a causa dell'assenza di attività. 

   * Come si può vedere a destra, Profiler è stato avviato e ha inviato eventi personalizzati a seguito di richieste avvenute durante l'esecuzione. La visualizzazione dell'evento personalizzato ServiceProfilerSample indica che Profiler ha collegato un'analisi a una richiesta. È possibile visualizzare l'analisi nel riquadro **Prestazioni di Application Insights**.

     Se non vengono visualizzati dati di telemetria, Profiler non è in esecuzione. Per risolvere il problema, vedere le sezioni relative alla risoluzione dei problemi per il tipo di app specifico più avanti in questo articolo.  

     ![Cercare dati di telemetria di Profiler][profiler-search-telemetry]

1. Se ci sono state richieste durante l'esecuzione di Profiler, verificare che tali richieste siano gestite dalla parte dell'applicazione per cui Profiler è abilitato. Anche se in alcuni casi le applicazioni sono costituite da più componenti, Profiler è abilitato solo per alcuni di questi. Il riquadro **Configura Application Insights Profiler** visualizza i componenti che hanno caricato analisi.

### <a name="other-things-to-check"></a>Altri aspetti da controllare
* Assicurarsi che l'app sia in esecuzione in .NET Framework 4.6.
* Se l'app Web è un'applicazione ASP.NET Core, deve eseguire almeno ASP.NET Core 2.0.
* Se i dati che si sta tentando di visualizzare sono antecedenti a un paio di settimane, limitare il filtro temporale e riprovare. Le tracce vengono eliminate dopo sette giorni.
* Assicurarsi che un firewall o i proxy non abbiano bloccato l'accesso a https://gateway.azureserviceprofiler.net.
* Profiler non è supportato nei piani di servizio app gratuiti o condivisi. Se si usa uno di questi piani, provare a scalare fino a uno dei piani di base e Profiler dovrebbe iniziare a funzionare.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Doppio conteggio in thread paralleli

In alcuni casi la metrica del tempo totale nel visualizzatore dello stack supera la durata della richiesta.

Questa situazione può verificarsi quando a una richiesta sono associati due o più thread che operano in parallelo. In questo caso, il tempo totale di thread è superiore al tempo trascorso. Un thread può essere in attesa del completamento dell'altro. Il visualizzatore prova a rilevare questa situazione e omette l'attesa non interessante. Ciò facendo, sbaglia visualizzando troppe informazioni anziché omettendo informazioni potenzialmente critiche.

Quando nelle analisi sono presenti thread in parallelo, verificare il percorso critico per la richiesta individuando i thread in attesa. Un thread che passa rapidamente in uno stato di attesa è in genere in attesa di altri thread. Concentrarsi sugli altri thread e ignorare il tempo nei thread in attesa.

### <a name="error-report-in-the-profile-viewer"></a>Report degli errori nel visualizzatore profili
Inviare un ticket di supporto nel portale. Verificare di includere l'ID di correlazione dal messaggio di errore.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Risolvere i problemi di Profiler nel Servizio app di Azure
Per il corretto funzionamento di Profiler:
* Il piano di servizio dell'app Web deve essere di livello Basic o superiore.
* Application Insights deve essere abilitato per l'app Web.
* L'app Web deve avere le impostazioni dell'app seguenti:

    |Impostazione app    | Valore    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey della risorsa di Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Il processo Web **ApplicationInsightsProfiler3** deve essere in esecuzione. Per controllare il processo Web:
   1. Passare a [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. Scegliere **WebJobs Dashboard** (Dashboard processi Web) dal menu **Tools** (Strumenti).  
      Verrà visualizzato il riquadro **WebJobs** (Processi Web). 
   
      ![profiler-webjob]   
   
   1. Per visualizzare i dettagli del processo Web, incluso il log, selezionare il collegamento **ApplicationInsightsProfiler3** .  
     Verrà visualizzato il riquadro **Continuous WebJob Details** (Dettagli processo Web continuo).

      ![profiler-webjob-log]

Se non si riesce a capire perché Profiler non funziona, è possibile scaricare il log e inviarlo al team per assistenza serviceprofilerhelp@microsoft.com . 
    
### <a name="manual-installation"></a>Installazione manuale

Quando si configura Profiler, vengono apportati alcuni aggiornamenti alle impostazioni dell'app Web. Se l'ambiente lo richiede, è possibile applicare gli aggiornamenti manualmente. Ad esempio nel caso in cui l'applicazione sia in esecuzione in un ambiente di App Web per PowerApps. Per applicare gli aggiornamenti manualmente:

1. Nel riquadro **controllo app Web** aprire **Impostazioni**.

1. Impostare la **versione di .NET Framework** su **v 4.6**.

1. Attivare **** Always On****.
1. Creare le impostazioni dell'app:

    |Impostazione app    | Valore    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey della risorsa di Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Troppe sessioni di profilatura attive

È attualmente possibile abilitare Profiler su un massimo di quattro app Web e slot di distribuzione di Azure in esecuzione nello stesso piano di servizio. Se in un solo piano di servizio app sono in esecuzione più di quattro app Web, Profiler può generare un'eccezione *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler, eseguito separatamente per ogni app Web, prova ad avviare una sessione di ETW (Event Tracing for Windows) per ogni app. Il numero di sessioni ETW che possono essere attive contemporaneamente, tuttavia, è limitato. Se il processo Web di Profiler segnala un numero eccessivo di sessioni di profilatura attive, spostare alcune app Web in un altro piano di servizio.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Errore di distribuzione: Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se si sta ridistribuendo l'app Web a una risorsa di App Web con Profiler abilitato, può essere visualizzato il messaggio seguente:

*Directory non vuota ' d: \\ Home \\ site \\ wwwroot \\ App_Data \\ Jobs '*

Questo errore si verifica se si esegue Distribuzione Web da script o dalla pipeline di distribuzione di Azure DevOps. Per risolvere questo problema, aggiungere i parametri di distribuzione seguenti all'attività Distribuzione Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Questi parametri eliminano la cartella usata da Application Insights Profiler e sbloccano il processo di ridistribuzione. Non influiscono sull'istanza di Profiler attualmente in esecuzione.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Come è possibile verificare se il Application Insights Profiler è in esecuzione?

Profiler viene eseguito come processo Web continuo nell'app Web. È possibile aprire la risorsa dell'app Web nel [portale di Azure](https://portal.azure.com). Nel riquadro **WebJobs** controllare lo stato di **ApplicationInsightsProfiler**. Se non è in esecuzione, aprire **Log** per altre informazioni.

## <a name="troubleshoot-vms-and-cloud-services"></a>Risolvere i problemi relativi alle macchine virtuali e ai servizi cloud

>**Il bug nel profiler fornito in WAD per i servizi cloud è stato risolto.** La versione più recente di WAD (1.12.2.0) per i servizi cloud funziona con tutte le versioni recenti di App Insights SDK. Gli host del servizio cloud eseguiranno l'aggiornamento automatico di WAD, ma non è immediato. Per forzare un aggiornamento, è possibile ridistribuire il servizio o riavviare il nodo.

Per verificare se Profiler è stato configurato correttamente da Diagnostica di Azure, eseguire le tre operazioni seguenti: 
1. Prima di tutto controllare che i contenuti della configurazione di Diagnostica di Azure distribuiti siano quelli previsti. 

1. In secondo luogo, assicurarsi che Diagnostica di Azure passi la chiave di strumentazione corretta nella riga di comando di Profiler. 

1. In terzo luogo, controllare nel file di log di Profiler se quest'ultimo è stato eseguito ma ha restituito un errore. 

Per controllare le impostazioni usate per configurare Diagnostica di Azure:

1. Accedere alla macchina virtuale (VM), quindi aprire il file di log in questo percorso. La versione del plug-in può essere più recente nel computer.
    
    Per le macchine virtuali:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Per Servizi cloud:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. Cercare nel file la stringa **WadCfg** per trovare le impostazioni passate alla macchina virtuale per configurare Diagnostica di Azure. È possibile verificare se la chiave di strumentazione usata dal sink di Profiler è corretta.

1. Controllare la riga di comando usata per avviare Profiler. Gli argomenti utilizzati per avviare profiler si trovano nel file seguente. (L'unità potrebbe essere c: o d: e la directory potrebbe essere nascosta).

    Per le macchine virtuali:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    per i servizi cloud:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Assicurarsi che la chiave di strumentazione nella riga di comando di Profiler sia corretta. 

1. Utilizzando il percorso trovato nella *config.js* precedente nel file, controllare il file di log del profiler, denominato **bootstrapn. log**. che visualizza le informazioni di debug che indicano le impostazioni usate da Profiler, nonché i messaggi di errore e di stato di Profiler.  

    Per le macchine virtuali, il file è in genere il seguente:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Per Servizi cloud:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Se Profiler è in esecuzione mentre l'applicazione riceve le richieste, viene visualizzato il messaggio seguente: *attività rilevata da Ikey*. 

    Quando viene caricata la traccia, viene visualizzato il messaggio seguente: *inizia a caricare la traccia*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Modificare le regole proxy o firewall di rete

Se l'applicazione si connette a Internet tramite un proxy o un firewall, potrebbe essere necessario modificare le regole per consentire all'applicazione di comunicare con il servizio Application Insights Profiler. Gli IP usati da Application Insights Profiler sono inclusi nel tag del servizio di monitoraggio di Azure.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png
