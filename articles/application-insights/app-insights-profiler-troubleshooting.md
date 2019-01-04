---
title: Risolvere i problemi con Azure Application Insights Profiler | Microsoft Docs
description: Questa pagina include le procedure di risoluzione dei problemi e le informazioni per aiutare gli sviluppatori che hanno problemi ad abilitare o a usare Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 7b7aad2cb8aa9b4faeada795f20c818995f62fb6
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720440"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Risolvere i problemi di abilitazione o visualizzazione di Application Insights Profiler

## <a id="troubleshooting"></a>Risoluzione dei problemi generali

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>I profili vengono caricati solo se sono presenti richieste all'applicazione mentre il profiler è in esecuzione
Application Insights Profiler raccoglie i dati del profiler per due minuti ogni ora o quando viene scelto il pulsante [**Profile now**](app-insights-profiler-settings.md?toc=/azure/azure-monitor/toc.json) (Profila ora) nella pagina **Configura Application Insights Profiler**. I dati di profilatura vengono tuttavia caricati solo quando possono essere collegati a una richiesta avvenuta durante l'esecuzione del profiler. 

Il profiler scrive i messaggi di traccia e gli eventi personalizzati nella risorsa di Application Insights. È possibile usare tali eventi per vedere in che modo viene eseguito il profiler. Se si ritiene che il profiler debba essere in esecuzione e acquisire le tracce ma nella pagina Prestazioni le tracce non sono visualizzate, è possibile controllare l'esecuzione del profiler:

1. Cercare i messaggi di traccia e gli eventi personalizzati inviati dal profiler alla risorsa di Application Insights. È possibile usare questa stringa di ricerca per trovare i dati rilevanti:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Lo screenshot seguente riporta un esempio derivante da due ricerche da due diverse risorse di intelligenza artificiale. 
    
    * L'esempio a sinistra riguarda un'applicazione che non riceve richieste durante l'esecuzione del profiler. Si può vedere il messaggio che indica l'annullamento del caricamento a causa dell'assenza di attività. 

    * Nell'esempio a destra si può vedere che il profiler è stato avviato e ha inviato eventi personalizzati in seguito al rilevamento di richieste durante l'esecuzione del profiler. La presenza dell'evento personalizzato ServiceProfilerSample indica che il profiler ha collegato una traccia a una richiesta ed è possibile visualizzare la traccia nella pagina Prestazioni di Application Insights.

    * Se non sono visibili i dati di telemetria, il profiler non è in esecuzione. Potrebbe essere necessario leggere le sezioni di risoluzione dei problemi per il tipo di app specifico in uso più avanti in questo documento.  

     ![Cercare i dati di telemetria del profiler][profiler-search-telemetry]

1. Se si sono verificate richieste durante l'esecuzione del profiler, verificare che tali richieste siano gestite dalla parte dell'applicazione per cui il profiler è abilitato. In alcuni casi le applicazioni sono costituite da più componenti, ma Profiler è abilitato solo per alcuni componenti, non tutti. La pagina Configura Application Insights Profiler mostra i componenti che hanno caricato tracce.

### <a name="other-things-to-check"></a>Altri aspetti da controllare:
* L'app viene eseguita su .NET Framework 4.6.
* Se l'app Web è un'applicazione ASP.NET Core, deve eseguire almeno ASP.NET Core 2.0.
* Se i dati che si sta tentando di visualizzare sono antecedenti a un paio di settimane, è possibile limitare il filtro temporale e riprovare. Le tracce vengono eliminate dopo sette giorni.
* Verificare che un firewall o i proxy non abbiano bloccato l'accesso a https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Doppio conteggio dei thread in parallelo

In alcuni casi la metrica del tempo totale nel visualizzatore dello stack supera la durata della richiesta.

Questa situazione può verificarsi quando due o più thread sono associati a una richiesta e operano in parallelo. In questo caso, il tempo totale di thread è superiore al tempo trascorso. Un thread può essere in attesa del completamento dell'altro. Il visualizzatore tenta di rilevare questa situazione e omette l'attesa non interessante, ma sbaglia mostrando troppe informazioni anziché limitarsi alle informazioni potenzialmente critiche.

Quando sono presenti thread in parallelo nelle analisi, determinare quali thread sono in attesa per poter verificare il percorso critico per la richiesta. Nella maggior parte dei casi, il thread che entra rapidamente in uno stato di attesa è semplicemente in attesa di altri thread. Concentrarsi sugli altri thread e ignorare il tempo nei thread in attesa.

### <a name="error-report-in-the-profile-viewer"></a>Report degli errori nel visualizzatore profili
Inviare un ticket di supporto nel portale. Verificare di includere l'ID di correlazione dal messaggio di errore.

## <a name="troubleshooting-profiler-on-app-services"></a>Risoluzione dei problemi relativi a Profiler nei Servizi app
### <a name="for-the-profiler-to-work-properly"></a>Per il corretto funzionamento del profiler:
* Il piano di servizio dell'app Web deve essere di livello Basic o superiore.
* Application Insights deve essere abilitato per l'app Web.
* L'impostazione **APPINSIGHTS_INSTRUMENTATIONKEY** nell'app Web deve essere configurata con la stessa chiave di strumentazione usata da Application Insights SDK.
* L'impostazione **APPINSIGHTS_PROFILERFEATURE_VERSION** nell'app Web deve essere definita e impostata su 1.0.0.
* Per l'app Web deve essere definita l'impostazione dell'app **DiagnosticServices_EXTENSION_VERSION** con il valore impostato su ~3.
* Il processo Web **ApplicationInsightsProfiler3** deve essere in esecuzione. È possibile controllare il processo Web passando a [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)e aprendo il **Dashboard di Processi Web** nel menu Strumenti. Come si può vedere negli screenshot seguenti, se si fa clic sul collegamento ApplicationInsightsProfiler2, è possibile visualizzare i dettagli del processo Web, incluso il log.

    Ecco il collegamento da selezionare per visualizzare i dettagli del processo Web: 

    ![profiler-webjob]

    Ecco la pagina che mostra i dettagli. È possibile scaricare il log e inviarlo al team del supporto tecnico Microsoft se non si riesce a capire il motivo per cui il profiler non funziona.
    
    ![profiler-webjob-log]

### <a name="manual-installation"></a>Installazione manuale

Quando si configura Profiler, vengono apportati alcuni aggiornamenti alle impostazioni dell'app Web. È possibile applicare gli aggiornamenti manualmente, se l'ambiente lo richiede Ad esempio nel caso in cui l'applicazione sia in esecuzione in un ambiente di App Web per PowerApps.

1. Nel riquadro **Web App Control** (Controllo app Web) aprire **Impostazioni**.
1. Impostare la **versione di .NET Framework** su **v4.6**.
1. Attivare **Always On**.
1. Aggiungere l'impostazione dell'app **APPINSIGHTS_INSTRUMENTATIONKEY** e impostare il valore sulla stessa chiave di strumentazione usata dall'SDK.
1. Aggiungere l'impostazione dell'app **APPINSIGHTS_PROFILERFEATURE_VERSION** e impostare il valore su 1.0.0.
1. Aggiungere l'impostazione dell'app **DiagnosticServices_EXTENSION_VERSION** e impostare il valore su ~3.

### <a name="too-many-active-profiling-sessions"></a>Troppe sessioni di profilatura attive

È attualmente possibile abilitare Profiler su un massimo di quattro app Web e slot di distribuzione di Azure in esecuzione nello stesso piano di servizio. Se sono presenti più app Web rispetto a quelle eseguite in un piano di servizio app, è possibile visualizzare un'eccezione Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException generata dal profiler. Il profiler, eseguito separatamente per ogni app Web, prova ad avviare una sessione ETW per ogni app. Tuttavia, il numero delle sessioni ETW che possono essere attive contemporaneamente è limitato. Se il processo Web di Profiler segnala un numero eccessivo di sessioni di profilatura attive, spostare alcune app Web in un altro piano di servizio.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Errore di distribuzione: Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se si intende ridistribuire l'app Web a una risorsa di App Web con Profiler abilitato, è possibile che venga visualizzato il messaggio seguente:

*Directory non vuota 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

Questo errore si verifica se si esegue Distribuzione Web da script o dalla pipeline di distribuzione di Azure DevOps. Per risolvere questo problema, aggiungere i parametri di distribuzione seguenti all'attività Distribuzione Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Questi parametri eliminano la cartella usata da Application Insights Profiler e sbloccano il processo di ridistribuzione. Non influiscono sull'istanza di Profiler attualmente in esecuzione.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Come è possibile verificare se il Application Insights Profiler è in esecuzione?

Profiler viene eseguito come processo Web continuo nell'app Web. È possibile aprire la risorsa dell'app Web nel [portale di Azure](https://portal.azure.com). Nel riquadro **WebJobs** controllare lo stato di **ApplicationInsightsProfiler**. Se non è in esecuzione, aprire **Log** per altre informazioni.

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>Risoluzione dei problemi relativi a Profiler e di Diagnostica di Microsoft Azure (WAD)

Per verificare se il profiler è configurato correttamente da WAD, occorre controllare tre aspetti. In primo luogo, è necessario controllare che i contenuti della configurazione di WAD distribuita siano quelli previsti. In secondo luogo, è necessario controllare che WAD passi la chiave di strumentazione corretta nella riga di comando del profiler. In terzo luogo, è possibile controllare il file di log del profiler per vedere se il profiler è stato eseguito ma si è verificato un errore. 

Per verificare le impostazioni usate per configurare WAD, è necessario accedere alla macchina virtuale e aprire il file di log in questo percorso: 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
In questo file cercare la stringa "WadCfg", dove si trovano le impostazioni che sono state passate alla macchina virtuale per configurare WAD. È possibile verificare se la chiave di strumentazione usata dal sink del profiler è corretta.

Si può quindi controllare la riga di comando usata per avviare il profiler. Il file seguente contiene gli argomenti usati per avviare il profiler.
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
Verificare che la chiave di strumentazione nella riga di comando del profiler sia corretta. 

Nel percorso disponibile nel file config.json summenzionato controllare quindi il file di log del profiler. Questo file include le informazioni di debug che indicano le impostazioni usate dal profiler e i messaggi di errore e di stato del profiler. Se il profiler è in esecuzione mentre l'applicazione riceve richieste, verrà visualizzato questo messaggio: Activity detected from iKey (Rilevata attività da chiave di strumentazione). Poco prima di caricare la traccia, verrà visualizzato questo messaggio: Start to upload trace (Avviare caricamento della traccia). 


[profiler-search-telemetry]:./media/app-insights-profiler/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/app-insights-profiler/Profiler-webjob.png
[profiler-webjob-log]:./media/app-insights-profiler/Profiler-webjob-log.png








