---
title: Conservazione e archiviazione dei dati in Azure Application Insights | Microsoft Docs
description: Informativa sulla conservazione e sulla privacy
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mbullwin
ms.openlocfilehash: df441a55ef4a9a40fe4defcabca5f667eeddbf29
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207303"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Raccolta, conservazione e archiviazione di dati in Application Insights

Quando si installa [applicazione Azure Insights][start] SDK nell'app, vengono inviati i dati di telemetria sull'app al cloud. Naturalmente, gli sviluppatori responsabili vogliono sapere esattamente quali dati vengono inviati, cosa accade ai dati e come possono mantenerne il controllo. In particolare, devono sapere se possono essere inviati dati sensibili, dove vengono archiviati e quale livello di sicurezza viene applicato. 

Innanzitutto, chiariamo alcuni aspetti:

* È improbabile che i moduli di telemetria standard che seguono un comportamento predefinito possano inviare dati sensibili al servizio. I dati di telemetria riguardano metriche di carico, prestazioni e utilizzo, report di eccezioni e altri dati di diagnostica. I principali dati utente visibili nei report di diagnostica sono URL, ma l'app non deve in ogni caso inserire dati sensibili in testo normale in un URL.
* È possibile scrivere codice che invia dati di telemetria personalizzati aggiuntivi per agevolare la diagnostica e il monitoraggio dell'utilizzo. Questa flessibilità è un'eccellente funzionalità di Application Insights. Sarebbe possibile, per errore, scrivere il codice in modo che includa dati personali e altri dati sensibili. Se quindi l'applicazione usa questo tipo di dati, è consigliabile applicare un processo di revisione completo a tutto il codice creato.
* Durante lo sviluppo e il test dell'app, è facile controllare ciò che viene inviato dall’SDK. I dati vengono visualizzati nelle finestre di output del debug dell’IDE e del browser. 
* I dati vengono archiviati nei server di [Microsoft Azure](https://azure.com) negli Stati Uniti o in Europa. L'app può essere eseguita ovunque. Azure offre [processi di sicurezza efficaci e soddisfa una vasta gamma di standard di conformità](https://azure.microsoft.com/support/trust-center/). Solo lo sviluppatore dell’app e il team designato hanno accesso ai dati. Il personale Microsoft può avere accesso limitato ai dati solo in determinate circostanze con il consenso dello sviluppatore. È crittografato in transito e inattivo.

Nella parte restante di questo articolo verranno elaborate ulteriormente queste risposte. Questa parte è progettata per essere indipendente dal resto, pertanto è possibile mostrarla ai colleghi che non fanno parte del proprio team.

## <a name="what-is-application-insights"></a>Informazioni su Azure Application Insights
[Applicazione Azure Insights][start] è un servizio fornito da Microsoft che consente di migliorare le prestazioni e l'usabilità dell'applicazione Live. Esegue il monitoraggio dell'applicazione per tutto il tempo che è in esecuzione, sia durante il test che dopo la pubblicazione o la distribuzione. Application Insights crea grafici e tabelle che illustrano, ad esempio, in quali ore del giorno si ottengono più utenti, i tempi di risposta dell'app e come funzionano i servizi esterni da cui dipende. Se sono presenti arresti anomali del sistema, errori o problemi di prestazioni, è possibile cercare i dati di telemetria in dettaglio per diagnosticare la causa. Inoltre, il servizio invierà messaggi di posta elettronica in caso di modifiche della disponibilità e delle prestazioni dell'app.

Per ottenere questa funzionalità, installare Application Insights SDK nell'applicazione, che diventa parte del codice. Quando l'app è in esecuzione, l’SDK monitora il funzionamento e invia i dati di telemetria al servizio Application Insights. Si tratta di un servizio cloud ospitato da [Microsoft Azure](https://azure.com). Application Insights funziona tuttavia per tutte le applicazioni, non solo quelle ospitate in Azure.

Il servizio Application Insights archivia e analizza i dati di telemetria. Per visualizzare l'analisi o eseguire una ricerca nei dati di telemetria archiviati, accedere con il proprio account Azure e aprire la risorsa Application Insights per l'applicazione. È anche possibile condividere l'accesso ai dati con altri membri del team o con determinati sottoscrittori di Azure.

È possibile fare in modo che i dati vengano esportati dal servizio Application Insights, ad esempio in un database o in strumenti esterni. A ogni strumento è possibile assegnare una chiave speciale ottenuta dal servizio, che può essere revocata se necessario. 

Sono disponibili Application Insights SDK per una gamma di tipi di applicazioni: servizi Web ospitati nei server Java EE o ASP.NET o in Azure; client Web, ovvero il codice in esecuzione in una pagina Web; app desktop e servizi; app per dispositivi, ad esempio Windows Phone, iOS e Android. Tutti inviano i dati di telemetria allo stesso servizio.

## <a name="what-data-does-it-collect"></a>Quali dati raccoglie?
### <a name="how-is-the-data-is-collected"></a>Come vengono raccolti?
Esistono tre origini dati:

* L'SDK, che viene integrato nell'app [in fase di sviluppo](../../azure-monitor/app/asp-net.md) o [in esecuzione](../../azure-monitor/app/monitor-performance-live-website-now.md). Sono disponibili diversi SDK per diversi tipi di applicazione. È inoltre disponibile un [SDK per le pagine Web](../../azure-monitor/app/javascript.md), che viene caricato nel browser dell'utente finale insieme alla pagina.
  
  * Ogni SDK include un numero di [moduli](../../azure-monitor/app/configuration-with-applicationinsights-config.md), che utilizzano tecniche diverse per raccogliere tipi diversi di dati di telemetria.
  * Se si installa l’SDK in fase di sviluppo, è possibile utilizzare l'API per inviare dati di telemetria personalizzati, oltre ai moduli standard. Tali dati di telemetria personalizzati possono includere qualsiasi tipo di dati si voglia inviare.
* In alcuni server Web sono disponibili anche agenti che vengono eseguiti insieme all'applicazione e inviano dati di telemetria su CPU, memoria e occupazione della rete. Ad esempio, macchine virtuali di Azure, host Docker e [server Java EE](../../azure-monitor/app/java-agent.md) possono disporre di tali agenti.
* [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) sono processi eseguiti da Microsoft che inviano richieste all'app Web a intervalli regolari. I risultati vengono inviati al servizio Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Quali tipi di dati vengono raccolti?
Le categorie principali sono:

* [Dati di telemetria del server Web](../../azure-monitor/app/asp-net.md): richieste HTTP,  URI, tempo impiegato per elaborare la richiesta, codice di risposta, indirizzo IP del client, ID sessione.
* [Pagine Web](../../azure-monitor/app/javascript.md): numero di pagine, utenti e sessioni, tempo di caricamento della pagina, eccezioni, chiamate AJAX.
* Contatori delle prestazioni: memoria, CPU, IO, occupazione della rete.
* Contesto client e server: sistema operativo, impostazioni locali, tipo di dispositivo, browser, risoluzione dello schermo.
* [Eccezioni](../../azure-monitor/app/asp-net-exceptions.md) e arresti anomali: **dump dello stack**, ID compilazione, tipo di CPU. 
* [Dipendenze](../../azure-monitor/app/asp-net-dependencies.md): chiamate ai servizi esterni, ad esempio REST, SQL, AJAX; URI o stringa di connessione, durata, esito positivo, comando.
* [Test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) : durata del test e passaggi, risposte.
* [Log di traccia](../../azure-monitor/app/asp-net-trace-logs.md) e [telemetria personalizzata](../../azure-monitor/app/api-custom-events-metrics.md) - **tutto ciò che viene codificato nei log o nella telemetria**.

[Maggiori dettagli](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Come è possibile verificare cosa viene raccolto?
Se si sviluppa l'app con Visual Studio, eseguire l'app in modalità di debug (F5). I dati di telemetria vengono visualizzati nella finestra di output. Da qui, è possibile copiarli e formattarli come JSON per semplificare l'ispezione. 

![](./media/data-retention-privacy/06-vs.png)

È inoltre disponibile una visualizzazione più leggibile nella finestra di diagnostica.

Per le pagine Web, aprire la finestra di debug del browser.

![Premere F12 e aprire la scheda Rete.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>È possibile scrivere codice per filtrare i dati di telemetria prima che vengano inviati?
Questo sarebbe possibile scrivendo un [plug-in del processore di telemetria](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Per quanto tempo vengono conservati i dati?
I punti dati non elaborati, ovvero gli elementi su cui è possibile eseguire query in Analytics e ispezionare nella ricerca, vengono conservati per un massimo di 730 giorni. È possibile [selezionare una durata di conservazione](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) di 30, 60, 90, 120, 180, 270, 365, 550 o 730 giorni. Se è necessario conservare i dati più di 730 giorni, è possibile usare l' [esportazione continua](../../azure-monitor/app/export-telemetry.md) per copiarli in un account di archiviazione durante l'inserimento dei dati. 

I dati conservati per più di 90 giorni comporteranno addebiti aggiuntivi. Scopri di più sui prezzi di Application Insights nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

I dati aggregati, ovvero conteggi, medie e altri dati statistici visualizzati in Esplora metriche, vengono conservati con livello di dettaglio di 1 minuto per 90 giorni.

Gli [snapshot di debug](../../azure-monitor/app/snapshot-debugger.md) vengono archiviati per quindici giorni. I criteri di conservazione sono impostati per ogni singola applicazione. Se è necessario aumentare questo valore, è possibile richiedere un aumento aprendo un caso di supporto nel portale di Azure.

## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
I dati sono visibili all'utente e, se si usa un account aziendale, ai membri del team dell'utente. 

Possono essere esportati dall'utente e dai membri del team e possono essere copiati in altri percorsi e passati ad altri utenti.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>In che modo Microsoft usa le informazioni inviate dall'app ad Application Insights?
Microsoft usa i dati solo al fine di fornire il servizio all'utente.

## <a name="where-is-the-data-held"></a>Dove vengono archiviati i dati?
* È possibile selezionare la località quando si crea una nuova risorsa di Application Insights. Per altre informazioni sulla disponibilità Application Insights per area, vedere [qui](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Significa che l'app deve essere ospitata negli Stati Uniti, in Europa o in Asia sud-orientale?
* No. L'applicazione può essere eseguita ovunque, nel proprio host locale o nel cloud.

## <a name="how-secure-is-my-data"></a>Quanto sono sicuri i dati?
Application Insights è un servizio di Azure. I criteri di sicurezza sono descritti nel [white paper su sicurezza, privacy e conformità di Azure](https://go.microsoft.com/fwlink/?linkid=392408).

I dati vengono archiviati nei server di Microsoft Azure. Per gli account nel portale di Azure, le restrizioni relative all'account sono illustrate nel [documento relativo a sicurezza, privacy e conformità di Azure](https://go.microsoft.com/fwlink/?linkid=392408).

L'accesso ai dati da parte del personale Microsoft è limitato. Il personale Microsoft può accedere ai dati solo previa autorizzazione dell'utente e se deve fornire supporto per l'uso di Application Insights. 

I dati in forma aggregata in tutte le applicazioni dei clienti, ad esempio le frequenze dei dati e le dimensioni medie delle tracce, vengono usati per migliorare Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>È possibile che i dati di telemetria di altri utenti interferiscano con i dati di Application Insights?
È possibile che altri utenti inviino dati di telemetria aggiuntivi all'account dell'utente usando la chiave di strumentazione, disponibile nel codice delle pagine Web. Con una quantità sufficiente di dati aggiuntivi, è possibile che le metriche dell'utente non rappresentino correttamente le prestazioni e l'utilizzo dell'app.

Se si condivide codice con altri progetti, è necessario ricordare di rimuovere la chiave di strumentazione.

## <a name="is-the-data-encrypted"></a>I dati vengono crittografati?
Tutti i dati vengono crittografati a riposo e vengono spostati tra i Data Center.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>I dati vengono crittografati durante il transito dall'applicazione ai server di Application Insights?
Sì, viene usato HTTPS per l'invio dei dati al portale da quasi tutti gli SDK, inclusi i server Web, i dispositivi e le pagine Web HTTPS. L'unica eccezione è costituita dai dati inviati da semplici pagine Web HTTP.

## <a name="does-the-sdk-create-temporary-local-storage"></a>L'SDK crea una risorsa di archiviazione locale temporanea?

Sì, alcuni canali di dati di telemetria manterranno i dati in locale se non è possibile raggiungere un endpoint. Per i framework e i canali di dati di telemetria interessati, vedere sotto.

I canali di dati di telemetria che usano l'archiviazione locale creano file temporanei nella directory TEMP o APPDATA che sono limitate all'account specifico che esegue l'applicazione. Ciò può verificarsi quando un endpoint è stato temporaneamente non disponibile o si raggiunge il limite della limitazione delle richieste. Una volta risolto il problema, il canale di telemetria riprenderà l'invio di tutti i dati nuovi e persistenti.

I dati salvati in modalità permanente non sono crittografati localmente. Se si tratta di un problema, esaminare i dati e limitare la raccolta di dati privati. (Vedere [Come esportare ed eliminare dati privati](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) per altre informazioni.)

Se un cliente deve configurare la directory con requisiti di sicurezza specifici, è possibile configurarla per framework. Assicurarsi che il processo che esegue l'applicazione abbia accesso in scrittura a questa directory, ma assicurarsi anche che la directory sia protetta per evitare la lettura da parte di utenti malintenzionati dei dati di telemetria.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` viene usato per i dati persistenti. Questo percorso non è configurabile dalla directory di configurazione e le autorizzazioni per accedere a questa cartella sono limitate all'utente specifico con le credenziali richieste. (Vedere [Implementazione](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Per impostazione predefinita `ServerTelemetryChannel` usa la cartella dei dati dell'app locale `%localAppData%\Microsoft\ApplicationInsights` o la cartella temp `%TMP%` dell'utente corrente. (Vedere [Implementazione](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84).)


Tramite il file di configurazione:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Tramite il codice:

- Rimuovere ServerTelemetryChannel dal file di configurazione
- Aggiungere questo frammento di codice alla configurazione:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Per impostazione predefinita `ServerTelemetryChannel` usa la cartella dei dati dell'app locale `%localAppData%\Microsoft\ApplicationInsights` o la cartella temp `%TMP%` dell'utente corrente. (Vedere [Implementazione](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84).) In un ambiente Linux, la risorsa di archiviazione locale verrà disabilitata, a meno che non sia specificata una cartella di archiviazione.

Il frammento di codice seguente illustra come impostare `ServerTelemetryChannel.StorageFolder` nel metodo `ConfigureServices()` della classe `Startup.cs`:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

Per altre informazioni vedere [Configurazione personalizzata di AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration). )

### <a name="nodejs"></a>Node.js

Per impostazione predefinita, `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` viene usato per i dati persistenti. Le autorizzazioni per accedere a questa cartella sono limitate all'utente corrente e agli amministratori. (Vedere [Implementazione](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts).)

Il prefisso della cartella `appInsights-node` può essere sostituito modificando il valore di runtime della variabile statica `Sender.TEMPDIR_PREFIX` presente in [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Come inviare i dati ad Application Insights con TLS 1.2

Perché i dati in transito verso gli endpoint di Application Insights siano sicuri, è consigliabile che le applicazioni dei clienti siano configurate per usare almeno il protocollo Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate** e il settore interromperà a breve il supporto per questi tipi precedenti di protocollo. 

Il [PCI Security Standards Council](https://www.pcisecuritystandards.org/) ha imposto il [30 giugno 2018 come scadenza](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) per disabilitare le versioni precedenti di TLS/SSL ed eseguire l'aggiornamento a protocolli più sicuri. Al termine del supporto legacy di Azur, non sarà possibile inviare i dati ad Application Insights se l'applicazione e/o i client non potranno comunicare tramite il protocollo TLS 1.2. L'approccio adottato per testare e convalidare il supporto TLS dell'applicazione varierà a seconda del sistema operativo/piattaforma nonché del linguaggio/framework usati dall'applicazione.

Non è consigliabile impostare in modo esplicito l'applicazione perché usi solo il protocollo TLS 1.2, a meno che non sia assolutamente necessario. Questa scelta potrebbe causare l'interruzione delle funzionalità di sicurezza a livello di piattaforma che consentono di rilevare automaticamente e sfruttare i vantaggi di protocolli più recenti e più sicuri quando saranno disponibili, ad esempio TLS 1.3. È consigliabile eseguire un controllo approfondito del codice dell'applicazione per verificare la presenza di hardcoding di versioni TLS/SSL specifiche.

### <a name="platformlanguage-specific-guidance"></a>Indicazioni specifiche su piattaforma e linguaggio

|Piattaforma/linguaggio | Supporto | Altre informazioni |
| --- | --- | --- |
| Servizi app di Azure  | Supportato, potrebbe essere necessaria la configurazione. | Supporto annunciato in aprile 2018. Leggere l'annuncio per [informazioni dettagliate sulla configurazione](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| App per le funzioni di Azure | Supportato, potrebbe essere necessaria la configurazione. | Supporto annunciato in aprile 2018. Leggere l'annuncio per [informazioni dettagliate sulla configurazione](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Supportato, la configurazione varia a seconda della versione. | Per informazioni dettagliate sulla configurazione di .NET 4.7 e versioni precedenti, fare riferimento a [queste istruzioni](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitoraggio stato | Supportato, è necessaria la configurazione | Status Monitor si basa sulla [configurazione del sistema operativo](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [configurazione .NET](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) per supportare il protocollo TLS 1.2.
|Node.js |  Supportato, nella versione 10.5.0, potrebbe essere necessaria la configurazione. | Usare la [documentazione ufficiale TLS/SSL di Node.js](https://nodejs.org/api/tls.html) per qualsiasi configurazione specifica dell'applicazione. |
|Java | Supportato, supporto JDK per TLS 1.2 aggiunto in [JDK 6 aggiornamento 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) e [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 usa [TLS 1.2 per impostazione predefinita](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Le distribuzioni Linux si basano generalmente su [OpenSSL](https://www.openssl.org) per supportare TLS 1.2.  | Controllare nel [log delle modifiche di OpenSSL](https://www.openssl.org/news/changelog.html) per assicurarsi che la versione di OpenSSL sia supportata.|
| Windows 8.0 - 10 | Supportato e abilitato per impostazione predefinita. | Assicurarsi che le [impostazioni predefinite](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) siano ancora in uso.  |
| Windows Server 2012 - 2016 | Supportato e abilitato per impostazione predefinita. | Assicurarsi che le [impostazioni predefinite](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) siano ancora in uso |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Supportato ma non abilitato per impostazione predefinita. | Vedere la pagina [Transport Layer Security (TLS) registry settings](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) (Impostazioni del Registro di sistema di Transport Layer Security (TLS)) per informazioni dettagliate su come eseguire l'abilitazione.  |
| Windows Server 2008 SP2 | Il supporto per TLS 1.2 richiede un aggiornamento. | Vedere [Aggiornamento per aggiungere il supporto per TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) in Windows Server 2008 SP2. |
|Windows Vista | Non supportato. | N/D

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Verificare la versione di OpenSSL in esecuzione nella distribuzione Linux

Per controllare quale versione di OpenSSL è stata installata, aprire il terminale ed eseguire il comando seguente:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Eseguire un test di transazione di TLS 1.2 in Linux

Per eseguire un test preliminare di base e verificare se il sistema Linux può comunicare tramite TLS 1.2, aprire il terminale ed eseguire il comando seguente:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dati personali archiviati in Application Insights

Questo argomento viene trattato in modo approfondito nell'[articolo sui dati personali di Application Insights](../../azure-monitor/platform/personal-data-mgmt.md).

#### <a name="can-my-users-turn-off-application-insights"></a>Gli utenti possono disattivare Application Insights?
Non direttamente. Non viene fornita alcuna opzione che gli utenti possono usare per disattivare Application Insights.

È tuttavia possibile implementare una funzionalità di questo tipo nell'applicazione. Tutti gli SDK includono un'impostazione dell'API che disattiva la raccolta di dati di telemetria. 

## <a name="data-sent-by-application-insights"></a>Dati inviati da Application Insights
Gli SDK sono diversi a seconda delle piattaforme e sono disponibili vari componenti da installare. (Vedere [Application Insights-Overview][start]). Ogni componente invia dati diversi.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classi di dati inviati nei diversi scenari

| Azione | Classi di dati raccolte (vedere la tabella seguente) |
| --- | --- |
| [Aggiungere Application Insights SDK a un progetto Web .NET][greenbrown] |ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Eccezioni**<br/>Sessione<br/>Utenti |
| [Installare Status Monitor in IIS][redfield] |Dependencies<br/>ServerContext<br/>Inferred<br/>Perf counters |
| [Aggiungere Application Insights SDK a un'app Web Java][java] |ServerContext<br/>Inferred<br/>Richiesta<br/>Sessione<br/>Utenti |
| [Aggiungere JavaScript SDK a una pagina Web][client] |ClientContext <br/>Inferred<br/>Pagina<br/>ClientPerf<br/>Ajax |
| [Definire le proprietà predefinite][apiproperties] |**Properties** in tutti gli eventi standard e personalizzati |
| [Chiama TrackMetric][api] |Valori numerici<br/>**Proprietà** |
| [Traccia di chiamata *][api] |Nome evento<br/>**Proprietà** |
| [Chiama Trackexception][api] |**Eccezioni**<br/>Dump dello stack<br/>**Proprietà** |
| SDK non riesce a raccogliere dati. Esempio: <br/> - non riesce ad accedere ai contatori delle prestazioni<br/> - si è verificata un'eccezione nell'inizializzatore della telemetria |Diagnostica di SDK |

Per gli [SDK per altre piattaforme][platforms], vedere i relativi documenti.

#### <a name="the-classes-of-collected-data"></a>Classi dei dati raccolti

| Classe di dati raccolti | Include (elenco non completo) |
| --- | --- |
| **Properties** |**Qualsiasi dato, in base al codice** |
| DeviceContext |ID, IP, impostazioni locali, modello dispositivo, rete, tipo di rete, nome OEM, risoluzione dello schermo, istanza del ruolo, nome ruolo, tipo di dispositivo. |
| ClientContext |Sistema operativo, impostazioni locali, lingua, rete, risoluzione della finestra. |
| Session |ID sessione |
| ServerContext |Nome computer, impostazioni locali, sistema operativo, dispositivo, sessione utente, contesto utente, operazione. |
| Inferred |Area geografica in base a indirizzo IP, timestamp, sistema operativo, browser. |
| metrics |Nome e valore della metrica. |
| Eventi |Nome e valore dell'evento. |
| PageViews |URL e nome della pagina o della schermata. |
| Client perf |URL/nome pagina, tempo di caricamento del browser. |
| Ajax |Chiamate HTTP dalla pagina Web al server |
| Requests |URL, durata, codice di risposta. |
| Dependencies |Tipo (SQL, HTTP, ...), stringa di connessione o URI, sincrono/asincrono, durata, esito positivo, istruzione SQL (con Monitoraggio stato) |
| **Eccezioni** |Tipo, **messaggio**, stack di chiamate, file di origine e numero di riga, ID thread. |
| Crashes |ID processo, ID processo padre, ID thread di arresto anomalo, patch applicazione, ID, compilazione, tipo di eccezione, indirizzo, motivo, simboli e registri offuscati, indirizzi di inizio e fine binari, nome e percorso binario, tipo di CPU |
| Trace |**Messaggio** e livello di gravità. |
| Perf counters |Tempo processore, memoria disponibile, frequenza di richieste, frequenza di eccezioni, byte privati del processo, velocità di I/O, durata richiesta, lunghezza coda richiesta. |
| Disponibilità |Codice di risposta del test Web, durata di ogni passo del test, nome del test, timestamp, esito positivo, tempo di risposta, posizione del test |
| Diagnostica di SDK |Messaggio di traccia o eccezione |

È possibile disattivare [alcuni dei dati modificando ApplicationInsights. config][config]

> [!NOTE]
> Il client IP viene utilizzato per dedurre la posizione geografica, tuttavia per impostazione predefinita i dati IP non vengono più memorizzati e tutti gli zeri vengono scritti nel campo associato. Per comprendere meglio la gestione dei dati personali si consiglia questo [articolo](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Se è necessario archiviare i dati degli indirizzi IP, l' [articolo raccolta indirizzi IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) illustra le opzioni disponibili.

## <a name="credits"></a>Riconoscimenti
Questo prodotto include dati GeoLite2 creati da MaxMind, disponibile nel sito [https://www.maxmind.com](https://www.maxmind.com).



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
