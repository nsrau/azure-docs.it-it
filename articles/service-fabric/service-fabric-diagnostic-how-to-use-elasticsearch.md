<properties
   pageTitle="Uso di ElasticSearch come archivio di traccia dell'applicazione dell'infrastruttura di servizi | Microsoft Azure"
   description="Descrive in che modo le applicazioni dell'infrastruttura di servizi possono usare ElasticSearch e Kibana per archiviare, indicizzare ed eseguire ricerche nelle tracce (log) dell'applicazione."
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
   ms.date="11/02/2015"
   ms.author="karolz@microsoft.com"/>

# Uso di ElasticSearch come archivio di traccia dell'applicazione dell'infrastruttura di servizi
## Introduzione
Questo articolo descrive in che modo le applicazioni dell'[infrastruttura di servizi](http://azure.microsoft.com/documentation/services/service-fabric/) possono usare **ElasticSearch** e **Kibana** per l'archiviazione della traccia dell'applicazione, l'indicizzazione e la ricerca. [ElasticSearch](https://www.elastic.co/guide/index.html) è un motore di ricerca e analisi open source, distribuito e scalabile in tempo reale, particolarmente adatto per questa attività, che può essere installato in macchine virtuali Windows o Linux che eseguono Microsoft Azure. ElasticSearch può elaborare in modo molto efficiente le tracce *strutturate* creare con tecnologie come **Event Tracing for Windows (ETW)**.

ETW viene usato dal runtime dell'infrastruttura di servizi per l'origine delle informazioni di diagnostica (tracce) ed è il metodo consigliato per le applicazioni dell'infrastruttura di servizi anche per l'origine delle relative informazioni di diagnostica. Ciò consente la correlazione tra le tracce fornite dal runtime e dall'applicazione e facilita la risoluzione dei problemi. I modelli di progetto dell'infrastruttura di servizi in Visual Studio include un'API di registrazione, basata sulla classe .NET **EventSource**, che genera tracce ETW per impostazione predefinita. Per una panoramica generale della traccia di applicazioni di Service Fabric tramite ETW, vedere [questo articolo](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Per visualizzare le tracce in ElasticSearch, queste devono essere acquisite nei nodi del cluster dell'infrastruttura di servizi in tempo reale, mentre è in esecuzione l'applicazione, e inviati all'endpoint ElasticSearch. Per l'acquisizione di tracce sono disponibili due opzioni principali:

+ **Acquisizione di tracce in-process** L'applicazione, o più precisamente il processo del servizio, è responsabile dell'invio di dati di diagnostica all'archivio di traccia (ElasticSearch).

+ **Acquisizione di tracce out-of-process** Un agente separato acquisisce le tracce da processi di servizio e le invia all'archivio di traccia.

La parte restante dell'articolo descrive come configurare ElasticSearch in Azure, illustra i vantaggi e gli svantaggi di entrambe le opzioni di acquisizione e spiega come configurare un servizio dell'infrastruttura per inviare dati a ElasticSearch.


## Configurazione di Elasticsearch in Azure
Il modo più semplice per configurare il servizio ElasticSearch in Azure consiste nell'usare [**modelli ARM di Azure**](../resource-group-overview.md). Un [modello ARM di avvio rapido per ElasticSearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) è disponibile dal repository di modelli di avvio rapido di Azure. Questo modello usa account di archiviazione distinti per unità di scala (gruppi di nodi) e può effettuare il provisioning di nodi client e server distinti con configurazioni diverse e un numero variabile di dischi dati collegati.

Questo articolo userà un altro modello denominato **ES MultiNode** dal [ramo ELK di Microsoft Patterns & Practices](https://github.com/mspnp/semantic-logging/tree/elk/). Questo modello è più semplice da usare e crea un cluster ElasticSearch protetto dall'autenticazione di base HTTP per impostazione predefinita. Prima di procedere, scaricare il [repository "elk" Microsoft P & P](https://github.com/mspnp/semantic-logging/tree/elk/) da GitHub nel computer locale, clonando il repository o scaricando un file ZIP. Il modello ES-MultiNode si trova nella cartella con lo stesso nome.
>[AZURE.NOTE]Il modello ES-MultiNode e gli script associati supportano attualmente ElasticSearch versione 1.7. Il supporto per ElasticSearch 2.0 verrà aggiunta in una data successiva.

### Preparazione di un computer per l'esecuzione degli script di installazione di ElasticSearch
Il modo più semplice per usare il modello ES-MultiNode consiste nell'eseguire uno script di PowerShell fornito denominato `CreateElasticSearchCluster`. Per usare questo script è necessario installare i moduli di Azure PowerShell e uno strumento denominato openssl. Quest'ultimo è necessario per la creazione di una chiave SSH che può essere usata per amministrare il cluster ElasticSearch in modalità remota.

Nota: lo script `CreateElasticSearchCluster` è progettato per semplificare l'uso del modello ES-MultiNode da un computer Windows. È possibile usare il modello in un computer non Windows, ma questo scenario non rientra nell'ambito di questo articolo.

1. Se non è già stato installato, installare i [**moduli di Azure PowerSell**](http://go.microsoft.com/fwlink/p/?linkid=320376). Quando richiesto, fare clic su Esegui, quindi su Installa.
>[AZURE.NOTE]Con la versione di Azure PowerShell 1.0, Azure PowerShell è sottoposto a una modifica sostanziale. CreateElasticSearchCluster è progettato attualmente per funzionare con Azure PowerShell 0.9.8 e non supporta Azure PowerShell 1.0 Preview. In una data successiva verrà fornito uno script compatibile con Azure PowerShell 1.0.

2. Lo strumento **openssl** è incluso nella distribuzione di [**Git per Windows**](http://www.git-scm.com/downloads). Se non è già stato fatto, installare [Git per Windows](http://www.git-scm.com/downloads), accettando le opzioni di installazione predefinite.

3. Supponendo che Git sia già installato, ma non incluso nel percorso di sistema, aprire la finestra di Microsoft Azure PowerShell ed eseguire i comandi seguenti:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Sostituire `<Git installation folder>` con il percorso Git nel computer locale. Il percorso predefinito è *"C:\\Programmi\\Git"*. Si noti il carattere punto e virgola all'inizio del primo percorso.

4. Assicurarsi di essere connessi ad Azure tramite il cmdlet [*Add-AzureAccount*](https://msdn.microsoft.com/library/azure/dn790372.aspx) e di avere selezionato la sottoscrizione da usare per creare il cluster ElasticSearch ([*Select-AzureSubscription*](https://msdn.microsoft.com/library/azure/dn790367.aspx)).

5. Se non è già stato fatto, passare dalla directory corrente alla cartella ES-MultiNode.

### Esecuzione dello script CreateElasticSearchCluster
Prima di eseguire lo script, aprire il file `azuredeploy-parameters.json` e verificare o fornire i valori per parametri dello script. Vengono forniti i parametri seguenti:

|Nome parametro |Descrizione|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Questo è il nome che verrà usato per creare il nome DNS visibile pubblicamente per il cluster ElasticSearch, aggiungendo il dominio dell'area di Azure al nome fornito. Ad esempio, se il valore del parametro è "myBigCluster" e l'area di Azure scelta è Stati Uniti occidentali, il nome DNS risultante per il cluster sarà myBigCluster.westus.cloudapp.azure.com. <br /><br />Questo nome viene usato anche come radice per i nomi di molti elementi associati al cluster ElasticSearch, ad esempio i nomi dei nodi dati.|
|storageAccountPrefix |Prefisso per gli account di archiviazione che verranno creati per il cluster ElasticSearch. <br /><br /> La versione corrente del modello usa un account di archiviazione condivisa, ma che potrebbe cambiare in futuro.|
|adminUsername |Nome dell'account amministratore per la gestione del cluster ElasticSearch. Le chiavi SSH corrispondenti saranno generate automaticamente.|
|dataNodeCount |Numero dei nodi nel cluster ElasticSearch. La versione corrente dello script non fa distinzione tra nodi dati e di query, tutti i nodi eseguiranno entrambi i ruoli.|
|dataDiskSize |Dimensioni dei dischi dati (in GB) che saranno allocati per ogni nodo dati. Ogni nodo riceverà 4 dischi dati, dedicati esclusivamente al servizio ElasticSearch.|
|region |Nome dell'area di Azure in cui dovrà trovarsi il cluster ElasticSearch.|
|esClusterName |Nome interno del cluster ElasticSearch. <br /><br />Non è necessario modificare l'impostzione predefinita di questo valore, a meno che non si preveda di eseguire più di un cluster ElasticSearch nella stessa rete virtuale, una configurazione che attualmente non è supportata dal modello ES-MultiNode.|
|esUserName esPassword |Credenziali per l'utente che sarà configurato per l'accesso al cluster ES (soggetto all'autenticazione di base HTTP).|

A questo punto è possibile eseguire lo script. Eseguire questo comando: ```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
``` dove `<es-group-name>` è il nome del gruppo di risorse di Azure che conterrà tutte le risorse cluster.

>[AZURE.NOTE]Se il cmdlet Test-AzureResourceGroup restituisce un messaggio NullReferenceException, si è dimenticato di accedere ad Azure (`Add-AzureAccount`).

Se viene restituito un errore dell'esecuzione dello script e si stabilisce che l'errore è stato causato da un valore di parametro del modello errato, correggere il file dei parametri ed eseguire di nuovo lo script con il nome di un gruppo di risorse diverso. È anche possibile riutilizzare lo stesso nome di gruppo di risorse e impostare lo script di pulizia quello precedente aggiungendo il parametro `-RemoveExistingResourceGroup` alla chiamata allo script.

### Risultato dell'esecuzione dello script CreateElasticSearchCluster
Dopo l'esecuzione dello script `CreateElasticSearchCluster` verranno creati gli elementi principali seguenti. Per chiarezza, si supponga che sia stato usato "myBigCluster" come valore del parametro `dnsNameForLoadBalancerIP` e che l'area di cui è stato creato il cluster sia Stati Uniti occidentali.

|Elemento|Nome, percorso e note|
|----------------------------------|----------------------------------|
|Chiave SSH per l'amministrazione remota |File myBigCluster.key, nella directory da cui è stato eseguito CreateElasticSearchCluster. <br /><br />Questa è la chiave che può essere usata per connettersi al nodo amministratore e, tramite questo nodo, ai nodi dati del cluster.|
|Nodo amministratore |myBigCluster admin.westus.cloudapp.azure.com <br /><br />È una VM dedicata per l'amministrazione remota del cluster ElasticSearch, l'unica che consenta connessioni SSH esterne. Viene eseguita nella stessa rete virtuale di tutti i nodi del cluster ElasticSearch, ma non esegue servizi ElasticSearch.|
|Nodi dati |myBigCluster1 … myBigCluster*N* <br /><br />odi dati che eseguono servizi ElasticSearch e Kibana. È possibile connettersi tramite SSH a ogni nodo, ma solo tramite il nodo amministratore.|
|Cluster ElasticSearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Si tratta l'endpoint primario per il cluster ElasticSearch (notare il suffisso /es). È protetto mediante l'autenticazione HTTP di base. Le credenziali sono state specificate con i parametri esUserName/esPassword del modello ES-MultiNode. Nel cluster è anche installato il plug-in head (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) per l'amministrazione di base del cluster.|
|Servizio Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Il servizio Kibana è configurato per visualizzare i dati del cluster ElasticSearch creato. È protetto con le stesse credenziali di autenticazione del cluster stesso.|

## Acquisizione di tracce in-process e out-of-process
Nell'introduzione sono state citate accennato due modalità fondamentali per la raccolta dei dati di diagnostica: in-process e out-of-process. Ognuna presenta vantaggi e svantaggi.

I vantaggi dell'**acquisizione di tracce in-process** includono:

1. *Facilità di configurazione e distribuzione*

    * La configurazione della raccolta dati di diagnostica è solo una parte della configurazione dell'applicazione ed è facile mantenerla sempre "sincronizzata" con il resto dell'applicazione.

    * È facile ottenere la configurazione per ogni applicazione o servizio.

    * L'acquisizione di tracce out-of-process richiede in genere una distribuzione separata e la configurazione dell'agente di diagnostica, un'attività amministrative aggiuntiva e una fonte di errori. Spesso la tecnologia specifica dell'agente consente solo un'istanza dell'agente stesso per ogni macchina virtuale (nodo), ovvero la configurazione per la raccolta della configurazione di diagnostica viene condivisa tra tutte le applicazioni e tutti i servizi in esecuzione nel nodo.

2. *Flessibilità*

    * L'applicazione può inviare i dati ogni volta che è necessario, purché sia disponibile una libreria client che supporta il sistema di archiviazione dati di destinazione. Nuovi sink possono essere aggiunti secondo le esigenze.

    * È possibile implementare regole di acquisizione, filtro e aggregazione dati complesse.

    * L'acquisizione di tracce out-of-process è spesso limitata dai sink di dati supportati dall'agente. Alcuni agenti sono estendibili.

3. *Accesso ai dati applicazione interni e contesto*

    * Il sottosistema di diagnostica in esecuzione nel processo dell'applicazione o del servizio può facilmente aumentare le tracce con informazioni contestuali.

    * Nell'approccio out-of-process i dati devono essere inviati a un agente tramite un meccanismo di comunicazione interprocesso, ad esempio Event Tracing for Windows (ETW). Questo approccio può imporre limitazioni aggiuntive.

I vantaggi dell'**acquisizione di tracce out-of-process** includono:

1. *Possibilità di monitorare l'applicazione e raccogliere dump di arresto anomalo del sistema*

    * L'acquisizione di tracce in-process può non riuscire se l'applicazione si avvia o di arresta in modo anomalo. Un agente indipendente ha maggiori possibilità di acquisire informazioni fondamentali sulla risoluzione dei problemi.<br /><br />

2. *Maturità, affidabilità e prestazioni comprovate*

    * Un agente sviluppato dal fornitore della piattaforma, ad esempio, l'agente Diagnostica di Microsoft Azure, è stato sottoposto a test rigorosi e protezione avanzata.

    * È necessario prestare attenzione con l'acquisizione di tracce in-process, per garantire che l'attività di invio dei dati di diagnostica da un processo dell'applicazione non interferisca con le attività principali dell'applicazione e non introduca problemi di temporizzazione o prestazioni. Un agente indipendente in esecuzione è meno soggetto a questi problemi e in genere è progettato appositamente per limitare l'impatto sul sistema.

Naturalmente è possibile combinare e trarre vantaggio da entrambi gli approcci. In realtà potrebbe essere la soluzione migliore per molte applicazioni.

Questo articolo userà la **libreria Microsoft.Diagnostic.Listeners** e l'acquisizione di tracce in-process per l'invio di dati da un'applicazione dell'infrastruttura di servizi al cluster ElasticSearch.

## Uso della libreria Listeners per inviare dati di diagnostica a ElasticSearch
La libreria Microsoft.Diagnostic.Listeners fa parte dell'applicazione dell'infrastruttura di esempio Party Cluster. Per usarla:

1. Scaricare l'[esempio Party Cluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) da GitHub.

2. Copiare i progetti Microsoft.Diagnostics.Listeners and Microsoft.Diagnostics.Listeners.Fabric (le cartelle intere) dalla directory dell'esempio Party Cluster nella cartella della soluzione dell'applicazione che dovrà inviare dati a ElasticSearch.

3. Aprire la soluzione di destinazione, fare clic con il pulsante destro del mouse sul nodo della soluzione in Esplora soluzioni e scegliere "Aggiungi progetto esistente". Aggiungere il progetto Microsoft.Diagnostics.Listeners alla soluzione. Ripetere lo stesso passaggio per il progetto Microsoft.Diagnostics.Listeners.Fabric.

4. Aggiungere un riferimento al progetto dai progetti di servizio per i due progetti aggiunti, ovvero ogni servizio che dovrà inviare dati a ElasticSearch deve fare riferimento a Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric.

    ![Riferimenti di progetto alle librerie Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric][1]

### Anteprima di novembre 2015 dell'infrastruttura di servizi e del pacchetto NuGet Microsoft.Diagnostics.Tracing
Le applicazioni compilate con l'anteprima di novembre 2015 dell'infrastruttura di servizi sono destinate a **.NET Framework 4.5.1**, perché questa è la versione più recente di .NET Framework supportata da Azure al momento del rilascio dell'anteprima. Purtroppo questa versione del framework non include alcune API EventListener richieste dalla libreria Microsoft.Diagnostics.Listeners. Poiché EventSource, il componente che costituisce la base per la registrazione delle API nelle applicazioni dell'infrastruttura, ed EventListener sono strettamente collegati, ogni progetto che usa la libreria Microsoft.Diagnostics.Listeners deve usare un'implementazione alternativa di EventSource, ad esempio quella fornita nel **pacchetto NuGet Microsoft.Diagnostics.Tracing** creato da Microsoft. Questo pacchetto è completamente compatibile con EventSource incluso nel framework, quindi non dovrebbero essere necessarie modifiche al codice oltre a quelle riguardanti lo spazio dei nomi a cui viene fatto riferimento.

Per iniziare a usare l'implementazione Microsoft.Diagnostics.Tracing della classe EventSource, seguire questi passaggi per ogni progetto di servizio che deve inviare dati a ElasticSearch:

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere "Gestisci pacchetti NuGet".

2. Passare all'origine del pacchetto nuget.org, se non è già selezionata, e cercare "Microsoft.Diagnostics.Tracing".

3. Installare il pacchetto `Microsoft.Diagnostics.Tracing.EventSource` e le relative dipendenze.

4. Aprire il file ServiceEventSource.cs o ActorEventSource.cs nel progetto di servizio e sostituire la direttiva `using System.Diagnostics.Tracing` all'inizio del file con la direttiva `using Microsoft.Diagnostics.Tracing`.

Questi passaggi non saranno necessari una volta che **.NET Framework 4.6** sarà supportato da Microsoft Azure.

### Configurazione e creazione dell'istanza del listener ElasticSearch
Il passaggio finale necessario per inviare i dati di diagnostica a ElasticSearch consiste nel creare un'istanza di `ElasticSearchListener` e configurarlo con i dati di connessione a ElasticSearch. Il listener acquisisce automaticamente tutti gli eventi generati tramite le classi EventSource definite nel progetto di servizio. Deve essere attivo nel corso della durata del servizio, quindi la posizione migliore in cui crearlo è il codice di inizializzazione del servizio. Ecco come può apparire il codice di inizializzazione per un servizio senza stato dopo le modifiche necessarie. Le aggiunte sono evidenziate nei commenti a partire da `****`:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {

                    // **** Instantiate ElasticSearchListener
                    var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                    ElasticSearchListener esListener = null;
                    if (configProvider.HasConfiguration)
                    {
                        esListener = new ElasticSearchListener(configProvider);
                    }

                    // This is the name of the ServiceType that is registered with FabricRuntime.
                    // This name must match the name defined in the ServiceManifest. If you change
                    // this name, please change the name of the ServiceType in the ServiceManifest.
                    fabricRuntime.RegisterServiceType("Stateless1Type", typeof(Stateless1));

                    ServiceEventSource.Current.ServiceTypeRegistered(
						Process.GetCurrentProcess().Id,
						typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);

                    // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                    GC.KeepAlive(esListener);

                }
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

Dati di connessione di ElasticSearch devono essere inseriti in una sezione separata nel file di configurazione del servizio (PackageRoot\\Config\\Settings.xml). Il nome della sezione deve corrispondere al valore passato al costruttore `FabricConfigurationProvider`, ad esempio:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
I valori di `serviceUri`, `userName` e `password` corrispondono rispettivamente all'indirizzo dell'endpoint del cluster ElasticSearch e al nome utente password di ElasticSearch. `indexNamePrefix` è il prefisso per gli indici di ElasticSearch. La libreria Microsoft.Diagnostics.Listeners crea quotidianamente un nuovo indice per i propri dati.

### Verifica
È tutto. Ora ogni volta che il servizio viene eseguito, inizierà a inviare tracce al servizio ElasticSearch specificato nella configurazione. È possibile verificare questa configurazione aprendo l'interfaccia utente di Kibana associata all'istanza ElasticSearch di destinazione, in questo esempio l'indirizzo della pagina sarà http://myBigCluster.westus.cloudapp.azure.com/, e controllare che gli indici con il prefisso del nome scelto per l'istanza `ElasticSearchListener` vengano effettivamente creati e popolato con dati.

![Eventi di Kibana che mostrano l'applicazione PartyCluster][2]

## Passaggi successivi
- [Altre informazioni sulla diagnostica e il monitoraggio di un servizio di infrastruttura di servizi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_1217_2015-->