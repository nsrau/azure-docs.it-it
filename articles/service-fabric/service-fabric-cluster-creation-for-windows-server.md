<properties
   pageTitle="Creare un cluster di Azure Service Fabric locale o nel cloud | Microsoft Azure"
   description="Informazioni su come creare un cluster di Azure Service Fabric su qualsiasi macchina (fisica o virtuale) che esegue Windows Server, sia locale o nel cloud."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="chackdan"/>


# Creare un cluster in esecuzione in Windows Server

Azure Service Fabric permette di creare cluster Service Fabric su qualsiasi macchina virtuale o computer con Windows Server in esecuzione. In questo modo, è possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente in cui è presente un set interconnesso di computer Windows Server, in locale o con qualsiasi provider cloud. Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric, denominato pacchetto autonomo per Windows Server.

Questo articolo illustra la procedura per creare un cluster con il pacchetto autonomo per Service Fabric in locale, anche se può essere facilmente adattato a qualsiasi altro ambiente, ad esempio provider di cloud.

>[AZURE.NOTE] Il pacchetto autonomo per Windows Server è attualmente in anteprima limitata e non è supportato per carichi di lavoro di produzione. [Fare clic qui](http://go.microsoft.com/fwlink/?LinkID=733084) per scaricare una copia del contratto di licenza.

<a id="downloadpackage"></a>
## Download del pacchetto autonomo Service Fabric


[Scaricare il pacchetto autonomo per Service Fabric per Windows Server 2012 R2 e versioni successive](http://go.microsoft.com/fwlink/?LinkId=730690), denominato *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;versione&gt;.zip*.

Nel pacchetto di download sono disponibili i seguenti file:

|**Nome file**|**Descrizione breve**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|File CAB contenente i file binari distribuiti in ogni computer del cluster.|
|ClusterConfig.Unsecure.DevCluster.json|File di configurazione del cluster di esempio contenente le impostazioni per un cluster di sviluppo a tre nodi non sicuro con singolo computer o VM, con le informazioni per ogni nodo incluso nel cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|File di configurazione del cluster di esempio contenente le impostazioni per un cluster non sicuro con più computer o VM, con le informazioni per ogni computer incluso nel cluster.|
|ClusterConfig.Windows.DevCluster.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster di sviluppo a tre nodi sicuro con singolo computer o VM, con le informazioni per ogni nodo incluso nel cluster. Il cluster è protetto con [identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster sicuro con più computer o VM che usa la funzionalità di sicurezza di Windows, con le informazioni per ogni computer incluso nel cluster sicuro. Il cluster è protetto con [identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster di sviluppo a tre nodi sicuro con singolo computer o VM, con le informazioni per ogni nodo del cluster. Il cluster è protetto tramite certificati X.509 di Windows.|
|ClusterConfig.x509.MultiMachine.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster sicuro con più computer o VM, con le informazioni per ogni nodo del cluster sicuro. Il cluster è protetto tramite certificati X.509 di Windows.|
|EULA.txt|Condizioni di licenza per l'uso del pacchetto autonomo Microsoft Azure Service Fabric per Windows Server. Per scaricare una copia del contratto di licenza [fare clic qui](http://go.microsoft.com/fwlink/?LinkID=733084).|
|Readme.txt|Collegamento alle note sulla versione e alle istruzioni di installazione base. Si tratta di un sottoinsieme delle istruzioni disponibili in questa pagina.|
|CreateServiceFabricCluster.ps1|Script di PowerShell che crea il cluster usando le impostazioni del file ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script di PowerShell che rimuove un cluster usando le impostazioni di ClusterConfig.json.|
|AddNode.ps1|Script di PowerShell per l'aggiunta di un nodo a un cluster distribuito esistente.|
|RemoveNode.ps1|Script di PowerShell per la rimozione di un nodo da un cluster distribuito esistente.|


## Pianificare e preparare la distribuzione del cluster
I passaggi seguenti devono essere eseguiti prima di creare il cluster.

### Passaggio 1: pianificazione dell’infrastruttura del cluster
Prima di creare un cluster di Service Fabric sui propri computer, è necessario stabilire da quali tipi di errore il cluster non deve essere compromesso. Ad esempio, sono necessarie linee di alimentazione o connessioni Internet separate per queste macchine? Inoltre, occorre considerare anche la sicurezza fisica di tali macchine. Dove si trova fisicamente la macchina e chi deve accedervi? Dopo aver preso queste decisioni, eseguire il mapping logico delle macchine ai vari domini di errore (vedere di seguito per altre informazioni). Il coinvolgimento della pianificazione dell'infrastruttura per i cluster di produzione sarà superiore rispetto ai cluster di test.

### Passaggio 2: preparazione delle macchine per soddisfare i prerequisiti
Prerequisiti per ogni macchina da aggiungere al cluster:

- Consigliati minimo 2 GB di memoria
- Connettività di rete. Verificare che i computer si trovino in una o più reti protette
- Windows Server 2012 R2 o Windows Server 2012 (è necessaria l’installazione di KB2858668 per questo componente).
- .NET Framework 4.5.1 o versione successiva, installazione completa
- Windows PowerShell 3.0
- L'amministratore del cluster che distribuisce e configura il cluster deve avere privilegi di amministratore in ogni computer.
- Il servizio RemoteRegistry deve essere in esecuzione in tutti i computer.

### Passaggio 3: determinare le dimensioni iniziali del cluster
In ogni nodo è distribuito il runtime di Service Fabric. Tutti i nodi sono membri del cluster. In una distribuzione di produzione tipica è presente un nodo per istanza (fisica o virtuale) del sistema operativo. La dimensione del cluster è determinata dalle esigenze aziendali, anche se è necessario disporre di una dimensione minima del cluster di tre nodi (macchine/VM). A scopi di sviluppo, è possibile configurare più di un nodo in una macchina specifica. In un ambiente di produzione, il Service Fabric supporta solo un nodo per ogni macchina virtuale o fisica.

### Passaggio 4: identificazione del numero di domini di errore e di aggiornamento
Un **dominio di errore** è un'unità fisica di errore ed è direttamente correlato all'infrastruttura fisica nei data center. Un dominio di errore comprende componenti hardware (computer, commutatori, rete e altro) che condividono un singolo punto di guasto. Sebbene non sia presente una mappatura 1:1 tra domini di errore e rack, ogni rack può essere considerato in senso lato un dominio di errore. Quando si esaminano i nodi nel cluster, è consigliabile distribuire i nodi tra gli ultimi tre domini di errore.

Quando si specificano domini di errore nel file *ClusterConfig.json*, è necessario scegliere il nome di ogni dominio di errore. Il Service Fabric supporta i domini di errore gerarchici, in modo che possano rispecchiare la topologia infrastrutturale. Di seguito sono riportati esempi di domini di errore validi:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **dominio di aggiornamento** è un'unità logica di nodi. Durante gli aggiornamenti orchestrati di Service Fabric (di applicazione o cluster), tutti i nodi in un dominio di aggiornamento vengono disattivati per eseguire l'aggiornamento, mentre i nodi in altri domini di aggiornamento rimangono disponibili per gestire le richieste. L'aggiornamento del firmware sulle macchine non rispetta i domini di aggiornamento ed è pertanto necessario eseguirli su una macchina alla volta.

Il metodo più semplice per descrivere questi concetti è considerare i domini di errore come unità degli errori imprevisti e i domini di aggiornamento come unità della manutenzione pianificata.

Quando si specificano domini di aggiornamento nel file *ClusterConfig.json*, è necessario scegliere il nome di ogni dominio di aggiornamento. Di seguito sono riportati esempi validi:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Per informazioni più dettagliate sui domini di aggiornamento e di errore, vedere l'articolo [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Passaggio 5: scaricare il pacchetto autonomo Service Fabric per Windows Server
[Scaricare il pacchetto autonomo Service Fabric per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimerlo in un computer di distribuzione non appartenente al cluster o in uno dei computer che faranno parte del cluster.

<a id="createcluster"></a>
## Creazione del cluster

Dopo aver seguito la procedura della sezione precedente di pianificazione e preparazione, è possibile creare il cluster.

### Passaggio 1: modificare la configurazione del cluster
Il cluster è descritto in un file *ClusterConfig.json*. Per informazioni dettagliate sulle sezioni di tale file, vedere l'articolo [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md). Aprire uno dei file *ClusterConfig.json* del pacchetto scaricato e modificare le impostazioni seguenti:

|**Impostazioni di configurazione**|**Descrizione**|
|-----------------------|--------------------------|
|**NodeTypes**|I tipi di nodo permettono di separare i nodi del cluster in diversi gruppi. Un cluster deve avere almeno un NodeType. Tutti i nodi in un gruppo possiedono le seguenti caratteristiche comuni. <br> **Nome**: nome del tipo di nodo. <br>**Porte di endpoint**: endpoint con nomi diversi (porte) associati a questo tipo di nodo. È possibile usare qualsiasi numero di porta, purché non sia in conflitto con altri elementi di questo manifesto e non sia già usato da altre applicazioni nel computer o nella VM <br> **Proprietà di posizionamento**: descrivono le proprietà per questo tipo di nodo che verranno usate come vincoli di posizionamento per i servizi di sistema o i propri servizi. Queste proprietà sono coppie chiave/valore definite dall'utente che forniscono metadati aggiuntivi per un determinato nodo. Le proprietà del nodo possono includere ad esempio la presenza o meno di un disco rigido o di una scheda grafica, il numero di spindle nel disco rigido, le memorie centrali e altre proprietà fisiche. <br>**Capacità**: le capacità del nodo definiscono il nome e la quantità di una particolare risorsa utilizzabile da parte di un determinato nodo. Ad esempio, un nodo può definire la propria capacità per una metrica denominata "MemoryInMb" con un valore predefinito di 2048 MB di memoria disponibile. Queste capacità vengono usate in fase di esecuzione per garantire che i servizi che richiedono una determinata quantità di risorse vengano inseriti nei nodi in cui tali risorse rimangono disponibili.<br>**IsPrimary**: se sono definiti più NodeType, verificare che solo uno sia impostato come primario, in cui vengono eseguiti i servizi di sistema, con il valore *true*. Tutti gli altri tipi di nodo devono essere impostati sul valore *false*.|
|**Nodi**|Dettagli per ciascun nodo che farà parte del cluster (tipo di nodo, nome del nodo, indirizzo IP, dominio di errore e dominio di aggiornamento del nodo). I computer in cui si vuole creare il cluster devono essere elencati in questa sezione con il relativo indirizzo IP. <br> Se si usano gli stessi indirizzi IP per tutti i nodi, viene creato un cluster di una casella che può essere usato per scopi di test. Non utilizzare cluster di una casella per la distribuzione dei carichi di lavoro di produzione.|

### Passaggio 2: esecuzione dello script di creazione del cluster
Dopo aver modificato la configurazione del cluster nel documento JSON e aver aggiunto tutte le informazioni relative ai nodi, eseguire lo script di PowerShell *CreateServiceFabricCluster.ps1* per la creazione del cluster dalla cartella del pacchetto e passare il percorso del file di configurazione JSON e il percorso del file CAB del pacchetto.

Questo script può essere eseguito su qualsiasi macchina con accesso da amministratore a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina sulla quale viene eseguito questo script può far parte o meno del cluster.

```
#Create an unsecure local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecure multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] I log di distribuzione sono disponibili localmente nel computer o nella VM in cui si esegue lo script CreateServiceFabricCluster di PowerShell, in una sottocartella denominata "DeploymentTraces" all'interno della cartella da cui è stato eseguito il comando di PowerShell. Per verificare se Service Fabric è stato distribuito correttamente in un computer, si possono cercare i file installati nella directory C:\\ProgramData e i processi FabricHost.exe e Fabric.exe in esecuzione in Gestione attività.

### Passaggio 3: connettersi al cluster
È ora possibile connettersi al cluster con Service Fabric Explorer direttamente da uno dei computer con http://localhost:19080/Explorer/index.html oppure in remoto con http://<*IndirizzoIPComputer*>:19080/Explorer/index.html

## Aggiungere nodi al cluster

1. Preparare il computer o la VM da aggiungere al cluster (vedere il passaggio 2 della precedente sezione Pianificare e preparare la distribuzione del cluster).
2. Pianificare a quale dominio di errore e dominio di aggiornamento si aggiungerà il computer o la VM.
3. Copiare o [scaricare il pacchetto autonomo per Service Fabric per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimerlo nel computer o nella VM che si intende aggiungere al cluster.
4. Aprire un prompt amministratore di PowerShell e passare al percorso del pacchetto decompresso.
5. Eseguire lo script di PowerShell *AddNode.ps1* con i parametri che descrivono il nuovo nodo da aggiungere. L'esempio seguente aggiunge un nuovo nodo denominato VM5, con tipo NodeType0 e indirizzo IP 182.17.34.52, in UD1 e FD1. *ExistingClusterConnectionEndPoint* è un endpoint di connessione per un nodo già presente nel cluster esistente, per cui è possibile scegliere *qualsiasi* indirizzo IP di nodo nel cluster.

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## Rimuovere nodi dal cluster

1. Creare una connessione Desktop remoto (RDP) con il computer o la VM da rimuovere dal cluster.
2. Copiare o [scaricare il pacchetto autonomo per Service Fabric per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimerlo nel computer o nella VM che si intende aggiungere al cluster.
3. Aprire un prompt amministratore di PowerShell e passare al percorso del pacchetto decompresso.
4. Eseguire lo script *RemoveNode.ps1* di PowerShell. L'esempio seguente rimuove il nodo corrente dal cluster. *ExistingClusterConnectionEndPoint* è un endpoint di connessione per un nodo già presente nel cluster esistente, per cui è possibile scegliere *qualsiasi* indirizzo IP di nodo nel cluster.

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

## Rimuovere il cluster
Per rimuovere un cluster, eseguire lo script *RemoveServiceFabricCluster.ps1* di Powershell dalla cartella del pacchetto e passare il percorso del file di configurazione JSON e il percorso del file CAB del pacchetto.

Questo script può essere eseguito su qualsiasi macchina con accesso da amministratore a tutte le macchine elencate come nodi nel file di configurazione del cluster. Il computer in cui viene eseguito lo script può far parte o meno del cluster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

## Procedura: Creare un cluster a tre nodi con VM IaaS di Azure
La procedura seguente descrive come creare un cluster in VM IaaS di Azure usando il programma di installazione autonomo per Windows Server. Si noti che il runtime di Service Fabric in questo cluster IaaS è interamente gestito dall'utente, a differenza dei cluster creati tramite il portale di Azure che vengono aggiornati dal provider di risorse di Service Fabric.

1. Accedere al portale di Azure e creare una nuova VM Windows Server 2012 R2 Datacenter in un gruppo di risorse.
2. Aggiungere altre due VM Windows Server 2012 R2 Datacenter allo stesso gruppo di risorse. Assicurarsi che ogni VM abbia al momento della creazione lo stesso nome utente e la stessa password amministratore. Al termine della creazione, tutte e tre le VM dovrebbero essere visualizzate nella stessa rete virtuale.
3. Connettersi a ogni VM e disattivare Windows Firewall con il dashboard Server locale di Server Manager. Ciò garantisce la comunicazione del traffico di rete tra le macchine. In ogni macchina ottenere l'indirizzo IP aprendo un prompt dei comandi e digitando *ipconfig*. In alternativa, è possibile visualizzare l'indirizzo IP di ogni macchina selezionando la risorsa rete virtuale del gruppo di risorse nel portale di Azure.
4. Connettersi a una delle macchine e verificare di poter effettuare correttamente il ping delle altre due.
5. Connettersi a una delle VM, scaricare il pacchetto autonomo per Windows Server in una nuova cartella nella macchina e decomprimere il pacchetto.
6. Aprire il file *ClusterConfig.Unsecure.MultiMachine.json* nel Blocco note e quindi modificare ogni nodo con i tre indirizzi IP delle macchine, modificare il nome del cluster nella parte superiore e salvare il file. Di seguito è riportato un esempio parziale del manifesto del cluster, con gli indirizzi IP di ogni macchina.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Aprire una finestra di Powershell ISE e passare alla cartella in cui è stato scaricato e decompresso il pacchetto autonomo di installazione ed è stato salvato il file manifesto precedente. Eseguire il comando di PowerShell seguente.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Dovrebbero essere visualizzate l'esecuzione di Powershell, la connessione a ogni macchina e la creazione di un cluster. Dopo circa 1 minuto è possibile testare che il cluster sia operativo connettendosi a Service Fabric Explorer in uno degli indirizzi IP delle macchine, ad esempio http://10.7.0.5:19080/Explorer/index.html. Poiché si tratta di un cluster autonomo in VM IaaS, per rendere sicuro il cluster è necessario distribuire certificati nelle VM oppure configurare una delle macchine come controller Active Directory di Windows Server per l'autenticazione di Windows così come si farebbe in locale. Per la configurazione di cluster sicuri, vedere la sezione Passaggi successivi seguente.

## Passaggi successivi
- [Creare cluster autonomi di Service Fabric in Windows Server o Linux](service-fabric-deploy-anywhere.md)
- [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)

- [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0713_2016-->