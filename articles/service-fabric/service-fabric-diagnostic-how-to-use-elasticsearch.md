<properties
   pageTitle="Uso di ElasticSearch come archivio di traccia delle applicazioni di Service Fabric | Microsoft Azure"
   description="Descrive in che modo le applicazioni di Service Fabric possono usare ElasticSearch e Kibana per archiviare, indicizzare ed eseguire ricerche nelle tracce (log) delle applicazioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# Usare ElasticSearch come archivio di traccia delle applicazioni di Service Fabric
## Introduzione
Questo articolo descrive le modalità in cui le applicazioni di [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/) possono usare **ElasticSearch** e **Kibana** per l'archiviazione delle tracce, l'indicizzazione e la ricerca. [ElasticSearch](https://www.elastic.co/guide/index.html) è un motore di ricerca e analisi open source, distribuito e scalabile in tempo reale, nonché particolarmente adatto per questa attività, che può essere installato in macchine virtuali Windows o Linux con Microsoft Azure. Elasticsearch può elaborare in modo efficiente tracce *strutturate* create con tecnologie come **Event Tracing for Windows (ETW)**.

ETW viene usato dal runtime di Service Fabric per ottenere informazioni di diagnostica (tracce) ed è anche il metodo consigliato alle applicazioni di Service Fabric per ottenere le rispettive informazioni di diagnostica. L'uso dello stesso meccanismo consente la correlazione tra le tracce fornite dal runtime e dall'applicazione e facilita la risoluzione dei problemi. I modelli di progetto dell'infrastruttura di servizi in Visual Studio include un'API di registrazione, basata sulla classe .NET **EventSource**, che genera tracce ETW per impostazione predefinita. Per una panoramica generale sulla definizione delle tracce delle applicazioni di Service Fabric con ETW, vedere [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Per poter essere visualizzate in ElasticSearch, le tracce devono essere acquisite nei nodi del cluster di Service Fabric in tempo reale, mentre è in esecuzione l'applicazione, e inviate all'endpoint ElasticSearch. Per l'acquisizione di tracce sono disponibili due opzioni principali:

+ **Acquisizione di tracce in-process**  
L'applicazione, o più esattamente il processo del servizio, è responsabile dell'invio dei dati di diagnostica all'archivio di traccia (ElasticSearch).

+ **Acquisizione di tracce out-of-process**  
Un agente separato acquisisce le tracce dal processo (o dai processi) di servizio e le invia all'archivio di traccia.

Di seguito si descrive come configurare ElasticSearch in Azure, si illustrano i vantaggi e gli svantaggi di entrambe le opzioni di acquisizione e si spiega come configurare un servizio di Service Fabric per inviare dati a ElasticSearch.


## Configurare ElasticSearch in Azure
L'uso dei [**modelli di Gestione risorse di Azure**](../resource-group-overview.md) costituisce il modo più semplice per configurare il servizio ElasticSearch in Azure. Un [modello di avvio rapido di Gestione risorse di Azure per ElasticSearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) è disponibile nel repository dei modelli di avvio rapido di Azure. Questo modello usa account di archiviazione separati per le unità di scala (gruppi di nodi) e può effettuare il provisioning di nodi client e server distinti con configurazioni diverse e un numero variabile di dischi dati collegati.

In questo caso viene usato un altro modello, denominato **ES-MultiNode** dal [repository degli strumenti di diagnostica di Azure](https://github.com/Azure/azure-diagnostics-tools). Questo modello è più semplice da usare e crea un cluster Elasticsearch protetto dall'autenticazione di base HTTP. Prima di procedere, scaricare nel computer locale il repository disponibile in GitHub (clonando il repository o scaricando un file con estensione zip). Il modello ES-MultiNode si trova nella cartella con lo stesso nome.

### Preparare un computer per l'esecuzione degli script di installazione di ElasticSearch
Il modo più semplice per usare il modello ES-MultiNode è quello di eseguire uno script di Azure PowerShell fornito denominato `CreateElasticSearchCluster`. Per usare lo script, è necessario installare i moduli di PowerShell e uno strumento denominato **openssl**. indispensabile per la creazione di una chiave SSH che può essere usata per amministrare il cluster ElasticSearch in modalità remota.

Lo script `CreateElasticSearchCluster` è progettato per semplificare l'uso del modello ES-MultiNode da un computer Windows. È possibile usare il modello in un computer non Windows, ma questo scenario non rientra nell'ambito di questo articolo.

1. Se non sono già stati installati, installare i [**moduli di Azure PowerShell**](http://aka.ms/webpi-azps). Quando richiesto, fare clic su **Esegui** e quindi su **Installa**. È richiesto Azure PowerShell 1.3 o versioni successive.

2. Lo strumento **openssl** è incluso nella distribuzione di [**Git per Windows**](http://www.git-scm.com/downloads). Se non è già stato fatto, installare [Git per Windows](http://www.git-scm.com/downloads). accettando le opzioni di installazione predefinite.

3. Supponendo che Git sia già installato, ma non incluso nel percorso di sistema, aprire una finestra di Microsoft Azure PowerShell ed eseguire i comandi seguenti:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Sostituire `<Git installation folder>` con il percorso Git nel computer locale. Il percorso predefinito è **"C:\\Programmi\\Git"**. Si noti il carattere punto e virgola all'inizio del primo percorso.

4. Assicurarsi di essere connessi ad Azure (tramite il cmdlet [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx)) e di avere selezionato la sottoscrizione da usare per creare il cluster Elasticsearch. Per verificare che sia selezionata la sottoscrizione corretta, è possibile usare i cmdlet `Get-AzureRmContext` e `Get-AzureRmSubscription`.

5. Se non è già stato fatto, passare dalla directory corrente alla cartella ES-MultiNode.

### Eseguire lo script CreateElasticSearchCluster
Prima di eseguire lo script, aprire il file `azuredeploy-parameters.json` e verificare o specificare i valori per i parametri dello script. Vengono forniti i parametri seguenti:

|Nome parametro |Description|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Nome usato per creare il nome DNS visibile pubblicamente per il cluster Elasticsearch aggiungendo il dominio dell'area di Azure al nome fornito. Ad esempio, se il valore del parametro è "myBigCluster" e l'area di Azure scelta è Stati Uniti occidentali, il nome DNS risultante per il cluster sarà myBigCluster.westus.cloudapp.azure.com. <br /><br />Questo nome viene usato anche come radice per i nomi di molti elementi associati al cluster Elasticsearch, ad esempio i nomi dei nodi dati.|
|adminUsername |Nome dell'account amministratore per la gestione del cluster Elasticsearch; le chiavi SSH corrispondenti vengono generate automaticamente.|
|dataNodeCount |Numero di nodi nel cluster ElasticSearch. La versione corrente dello script non fa distinzione tra nodi dati e di query, tutti i nodi eseguiranno entrambi i ruoli. Il valore predefinito è 3 nodi.|
|dataDiskSize |Dimensioni dei dischi dati (in GB) allocati per ogni nodo dati. Ogni nodo riceve 4 dischi dati dedicati esclusivamente al servizio Elasticsearch.|
|region |Nome dell'area di Azure in cui dovrà trovarsi il cluster ElasticSearch.|
|esUserName |Nome utente configurato per l'accesso al cluster Elasticsearch (soggetto all'autenticazione HTTP di base). La password non fa parte del file dei parametri e deve essere specificata quando viene richiamato lo script `CreateElasticSearchCluster`.|
|vmSizeDataNodes |Dimensioni della macchina virtuale di Azure per i nodi del cluster ElasticSearch. Il valore predefinito è Standard\_D2.|

A questo punto è possibile eseguire lo script. Eseguire il comando seguente:

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

dove

|Nome del parametro di script |Descrizione|
|-----------------------  |--------------------------|
|`<es-group-name>` |Nome del gruppo di risorse di Azure che conterrà tutte le risorse cluster Elasticsearch.|
|`<azure-region>` |Nome dell'area di Azure in cui si deve creare il cluster Elasticsearch.|         
|`<es-password>` |Password per l'utente di Elasticsearch.|

>[AZURE.NOTE] Se il cmdlet Test-AzureResourceGroup restituisce un messaggio NullReferenceException, significa che non è ancora stato eseguito l'accesso ad Azure (`Add-AzureRmAccount`).

Se viene restituito un errore dell'esecuzione dello script e si stabilisce che l'errore è stato causato da un valore di parametro del modello errato, correggere il file dei parametri ed eseguire di nuovo lo script con il nome di un gruppo di risorse diverso. È anche possibile riusare lo stesso nome di gruppo di risorse e fare in modo che lo script elimini quello precedente aggiungendo il parametro `-RemoveExistingResourceGroup` alla chiamata dello script.

### Risultato dell'esecuzione dello script CreateElasticSearchCluster
Dopo aver eseguito lo script `CreateElasticSearchCluster` verranno creati gli elementi principali seguenti. In questo esempio si suppone che sia stato usato "myBigCluster" come valore del parametro `dnsNameForLoadBalancerIP` e che l'area in cui è stato creato il cluster sia Stati Uniti occidentali.

|Elemento|Nome, percorso e note|
|----------------------------------|----------------------------------|
|Chiave SSH per l'amministrazione remota |File myBigCluster.key, nella directory da cui è stato eseguito CreateElasticSearchCluster. <br /><br />Questo file di chiave può essere usato per connettersi al nodo amministratore e, tramite questo nodo, ai nodi dati del cluster.|
|Nodo amministratore |myBigCluster admin.westus.cloudapp.azure.com <br /><br />VM dedicata per l'amministrazione remota del cluster Elasticsearch, l'unica che consenta connessioni SSH esterne. Viene eseguita nella stessa rete virtuale di tutti i nodi del cluster Elasticsearch, ma non esegue servizi Elasticsearch.|
|Nodi dati |myBigCluster1 … myBigCluster*N* <br /><br />Nodi di dati che eseguono servizi Elasticsearch e Kibana. È possibile connettersi tramite SSH a ogni nodo, ma solo tramite il nodo amministratore.|
|Cluster Elasticsearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Endpoint primario per il cluster Elasticsearch (notare il suffisso /es). È protetto mediante l'autenticazione HTTP di base. Le credenziali sono i parametri esUserName/esPassword del modello ES-MultiNode precedentemente specificati. Nel cluster è anche installato il plug-in head (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) per l'amministrazione di base del cluster.|
|Servizio Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Il servizio Kibana è configurato per visualizzare i dati del cluster Elasticsearch creato. È protetto con le stesse credenziali di autenticazione del cluster stesso.|

## Acquisizione di tracce in-process e out-of-process
Nell'introduzione sono state citate le due principali modalità di raccolta dei dati di diagnostica: in-process e out-of-process. Ognuna presenta vantaggi e svantaggi.

I vantaggi dell'**acquisizione di tracce in-process** includono:

1. *Facilità di configurazione e distribuzione*

    * La configurazione della modalità di raccolta dei dati di diagnostica è solo una parte del processo di configurazione dell'applicazione ed è facile mantenerla sempre "sincronizzata" con il resto dell'applicazione.

    * È facile ottenere la configurazione per ogni applicazione o servizio.

    * L'acquisizione di tracce out-of-process richiede in genere una distribuzione separata e la configurazione dell'agente di diagnostica, che costituisce un'attività amministrativa aggiuntiva e, quindi, una potenziale fonte di errori. Nella maggior parte dei casi, la tecnologia specifica dell'agente consente una sola istanza dell'agente per ogni macchina virtuale (nodo). In questo modo, la configurazione relativa alla raccolta dei dati di diagnostica viene condivisa tra tutte le applicazioni e i servizi in esecuzione sul nodo.

2. *Flessibilità*

    * L'applicazione può inviare i dati ogni volta che è necessario, purché sia disponibile una libreria client che supporta il sistema di archiviazione dati di destinazione. Nuovi sink possono essere aggiunti secondo le esigenze.

    * È possibile implementare complesse regole di acquisizione, filtro e aggregazione dati.

    * L'acquisizione di tracce out-of-process è spesso limitata dai sink di dati supportati dall'agente. Alcuni agenti sono estendibili.

3. *Accesso al contesto e ai dati di applicazione interni*

    * Il sottosistema di diagnostica in esecuzione nel processo dell'applicazione o del servizio può facilmente aumentare le tracce con informazioni contestuali.

    * Nell'approccio out-of-process i dati devono essere inviati a un agente tramite un meccanismo di comunicazione interprocesso, ad esempio Event Tracing for Windows (ETW). Questo meccanismo può imporre limitazioni aggiuntive.

I vantaggi dell'**acquisizione di tracce out-of-process** includono:

1. *Possibilità di monitorare l'applicazione e raccogliere dump di arresto anomalo del sistema*

    * L'acquisizione di tracce in-process può non riuscire se l'applicazione si avvia o si arresta in modo anomalo. Un agente indipendente ha maggiori possibilità di acquisire informazioni cruciali sulla risoluzione dei problemi.<br /><br />

2. *Maturità, affidabilità e prestazioni comprovate*

    * Un agente sviluppato da un fornitore di piattaforme, ad esempio l'agente Diagnostica di Microsoft Azure, viene sottoposto a test rigorosi e avanzate misure di protezione.

    * Con l'acquisizione di tracce in-process, è necessario prestare attenzione per assicurarsi che l'attività di invio dei dati di diagnostica da parte di un processo dell'applicazione non interferisca con le attività principali dell'applicazione e non introduca problemi di temporizzazione o prestazioni. Un agente indipendente in esecuzione è meno soggetto a questi problemi ed è progettato appositamente per limitare l'impatto sul sistema.

È possibile combinare e trarre vantaggio da entrambi gli approcci. In realtà, potrebbe essere la soluzione migliore per molte applicazioni.

In questo caso viene usata la **libreria Microsoft.Diagnostic.Listeners** e l'acquisizione di tracce in-process per l'invio di dati da un'applicazione di Service Fabric a un cluster Elasticsearch.

## Usare la libreria Listeners per inviare dati di diagnostica a ElasticSearch
La libreria Microsoft.Diagnostic.Listeners fa parte dell'applicazione di Service Fabric di esempio PartyCluster. Per usarla:

1. Scaricare l'[esempio di Party Cluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) da GitHub.

2. Copiare i progetti Microsoft.Diagnostics.Listeners e Microsoft.Diagnostics.Listeners.Fabric (le cartelle intere) dalla directory di esempio PartyCluster alla cartella della soluzione relativa all'applicazione che dovrà inviare i dati a ElasticSearch.

3. Aprire la soluzione di destinazione, fare clic con il pulsante destro del mouse sul nodo della soluzione in Esplora soluzioni e scegliere **Aggiungi progetto esistente**. Aggiungere il progetto Microsoft.Diagnostics.Listeners alla soluzione. Ripetere lo stesso passaggio per il progetto Microsoft.Diagnostics.Listeners.Fabric.

4. Aggiungere un riferimento dai progetti di servizio ai due progetti aggiunti: ogni servizio che dovrà inviare dati a ElasticSearch deve fare riferimento a Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric.

    ![Riferimenti di progetto alle librerie Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric][1]

### Versione di disponibilità generale di Service Fabric e pacchetto NuGet Microsoft.Diagnostics.Tracing
Il framework di destinazione delle applicazioni compilate con la versione di disponibilità generale di Service Fabric (2.0.135, rilasciata il 31 marzo 2016) è **.NET Framework 4.5.2**. Questa è la versione più recente di .NET Framework supportata da Azure al momento del rilascio della versione di disponibilità generale. Questa versione del framework, purtroppo, non include alcune API EventListener richieste dalla libreria Microsoft.Diagnostics.Listeners. Poiché EventSource, il componente di base per la registrazione delle API in applicazioni di Service Fabric, ed EventListener sono strettamente collegati, ogni progetto che usa la libreria Microsoft.Diagnostics.Listeners deve usare anche un'implementazione alternativa di EventSource, inclusa nel **pacchetto NuGet Microsoft.Diagnostics.Tracing** creato da Microsoft. Questo pacchetto è totalmente compatibile con la versione di EventSource inclusa nel framework e quindi non dovrebbero essere necessarie modifiche al codice oltre a quelle riguardanti lo spazio dei nomi a cui viene fatto riferimento.

Per iniziare a usare l'implementazione Microsoft.Diagnostics.Tracing della classe EventSource, seguire questi passaggi per ogni progetto di servizio che deve inviare dati a ElasticSearch:

1. Fare clic con il pulsante destro del mouse sul progetto di servizio e scegliere **Gestisci pacchetti NuGet**.

2. Passare all'origine del pacchetto nuget.org, se non è già selezionato, e cercare "**Microsoft.Diagnostics.Tracing**".

3. Installare il pacchetto `Microsoft.Diagnostics.Tracing.EventSource` e le relative dipendenze.

4. Aprire il file **ServiceEventSource.cs** o **ActorEventSource.cs** nel progetto di servizio e sostituire la direttiva `using System.Diagnostics.Tracing` all'inizio del file con la direttiva `using Microsoft.Diagnostics.Tracing`.

Questi passaggi non saranno più necessari quando **.NET Framework 4.6** sarà supportato da Microsoft Azure.

### Creazione dell'istanza e configurazione del listener ElasticSearch
Il passaggio finale per l'invio dei dati di diagnostica a Elasticsearch consiste nel creare un'istanza di `ElasticSearchListener` e configurarla con i dati di connessione a Elasticsearch. Il listener acquisisce automaticamente tutti gli eventi generati tramite le classi EventSource definite nel progetto di servizio. Deve essere attivo nel corso della durata del servizio, quindi la posizione migliore in cui crearlo è il codice di inizializzazione del servizio. Ecco come può apparire il codice di inizializzazione per un servizio senza stato dopo le modifiche necessarie. Le aggiunte sono evidenziate nei commenti a partire da `****`:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

I dati di connessione di Elasticsearch devono essere inseriti in una sezione separata nel file di configurazione del servizio (**PackageRoot\\Config\\Settings.xml**). Il nome della sezione deve corrispondere al valore passato al costruttore `FabricConfigurationProvider`, ad esempio:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
I valori dei parametri `serviceUri`, `userName` e `password` corrispondono rispettivamente all'indirizzo dell'endpoint del cluster Elasticsearch e al nome utente e password di Elasticsearch. `indexNamePrefix` è il prefisso per gli indici di Elasticsearch; la libreria Microsoft.Diagnostics.Listeners crea quotidianamente un nuovo indice per i propri dati.

### Verifica
L'operazione è terminata. A questo punto, ogni volta che il servizio viene eseguito, inizia a inviare tracce al servizio Elasticsearch specificato nella configurazione. È possibile verificare questo aprendo la UI Kibana associata all'istanza di Elasticsearch di destinazione. Nel nostro esempio, l'indirizzo della pagina è http://myBigCluster.westus.cloudapp.azure.com/. Verificare che gli indici con il prefisso del nome scelto per l'`ElasticSearchListener` istanza siano stati effettivamente creati e popolati con i dati.

![Eventi di Kibana che mostrano l'applicazione PartyCluster][2]

## Passaggi successivi
- [Altre informazioni sulla diagnostica e il monitoraggio di un servizio di infrastruttura di servizi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_0817_2016-->