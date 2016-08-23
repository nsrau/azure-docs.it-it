<properties
   pageTitle="Creare e gestire un cluster autonomo di Azure Service Fabric | Microsoft Azure"
   description="Informazioni su come creare e gestire un cluster di Azure Service Fabric su qualsiasi macchina (fisica o virtuale) che esegue Windows Server, che sia locale o in qualsiasi cloud."
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
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>  


# Creare e gestire un cluster eseguito in Windows Server

Azure Service Fabric permette di creare cluster Service Fabric su qualsiasi macchina virtuale o computer con Windows Server in esecuzione. In questo modo è possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente che contenga un set di computer Windows Server interconnessi, in locale o con qualsiasi provider di cloud. Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric, denominato pacchetto autonomo per Windows Server.

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
Prima di creare un cluster di Service Fabric sui propri computer, è possibile stabilire da quali tipi di errore il cluster non deve essere compromesso. Ad esempio, sono necessarie linee di alimentazione o connessioni Internet separate per queste macchine? È necessario inoltre considerare la sicurezza fisica di tali macchine. Dove si trova fisicamente la macchina e chi deve accedervi? Dopo aver preso queste decisioni, è possibile eseguire il mapping logico delle macchine ai vari domini di errore (per altre informazioni, vedere di seguito). Il coinvolgimento della pianificazione dell'infrastruttura per i cluster di produzione sarà superiore rispetto ai cluster di test.

<a id="preparemachines"></a>
### Passaggio 2: preparare le macchine con i prerequisiti
Prerequisiti per ogni macchina da aggiungere al cluster:

- Consigliati minimo 2 GB di memoria
- Connettività di rete: verificare che le macchine siano su una rete o su più reti protette.
- Windows Server 2012 R2 o Windows Server 2012 (per questo componente è necessaria l'installazione di [KB2858668](https://support.microsoft.com/kb/2858668)).
- [.NET Framework 4.5.1 o versione successiva](https://www.microsoft.com/download/details.aspx?id=40773), installazione completa.
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- L'amministratore del cluster che distribuisce e configura il cluster deve disporre dei [privilegi di amministratore](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) in ogni computer.
- Il [servizio RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve essere eseguito in tutti i computer.

### Passaggio 3: determinare le dimensioni iniziali del cluster
In ogni nodo di un cluster di Service Fabric autonomo è distribuito il runtime di Service Fabric. Tutti i nodi sono membri del cluster. In una distribuzione di produzione tipica è presente un nodo per istanza (fisica o virtuale) del sistema operativo. La dimensione del cluster è determinata dalle esigenze aziendali, anche se è necessario disporre di una dimensione minima del cluster di tre nodi (macchine/VM). A scopi di sviluppo, è possibile configurare più di un nodo in una macchina specifica. In un ambiente di produzione, il Service Fabric supporta solo un nodo per ogni macchina virtuale o fisica.

### Passaggio 4: identificazione del numero di domini di errore e di aggiornamento
Un **dominio di errore** è un'unità fisica di errore ed è direttamente correlato all'infrastruttura fisica nei data center. Un dominio di errore comprende componenti hardware (computer, commutatori, rete e altro) che condividono un singolo punto di guasto. Sebbene non sia presente una mappatura 1:1 tra domini di errore e rack, ogni rack può essere considerato in senso lato un dominio di errore. Quando si esaminano i nodi nel cluster, è consigliabile distribuire i nodi tra gli ultimi tre domini di errore.

Quando si specificano domini di errore nel file *ClusterConfig.json*, è possibile scegliere il nome di ogni dominio di errore. Il Service Fabric supporta i domini di errore gerarchici, in modo che possano rispecchiare la topologia infrastrutturale. Di seguito sono riportati esempi di domini di errore validi:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **dominio di aggiornamento** è un'unità logica di nodi. Durante gli aggiornamenti orchestrati di Service Fabric (di applicazione o cluster), tutti i nodi in un dominio di aggiornamento vengono disattivati per eseguire l'aggiornamento, mentre i nodi in altri domini di aggiornamento rimangono disponibili per gestire le richieste. L'aggiornamento del firmware sulle macchine non rispetta i domini di aggiornamento ed è pertanto necessario eseguirli su una macchina alla volta.

Il metodo più semplice per descrivere questi concetti è considerare i domini di errore come unità degli errori imprevisti e i domini di aggiornamento come unità della manutenzione pianificata.

Quando si specificano domini di aggiornamento nel file *ClusterConfig.json*, è possibile scegliere il nome di ogni dominio di aggiornamento. Di seguito sono riportati esempi validi:

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
|**NodeTypes**|I tipi di nodo permettono di separare i nodi del cluster in diversi gruppi. Un cluster deve avere almeno un NodeType. Tutti i nodi in un gruppo possiedono le seguenti caratteristiche comuni. <br> **Nome**: nome del tipo di nodo. <br>**Porte di endpoint**: endpoint con nomi diversi (porte) associati a questo tipo di nodo. È possibile usare qualsiasi numero di porta desiderato, purché non entri in conflitto con qualsiasi altro nel manifesto e non sia già utilizzato da un'altra applicazione eseguita nel computer/VM. <br> **Proprietà di posizionamento**: descrivono proprietà per questo tipo di nodo da usare come vincoli di posizionamento per i servizi di sistema o i servizi dell'utente. Queste proprietà sono coppie chiave/valore definite dall'utente che forniscono metadati aggiuntivi per un determinato nodo. Le proprietà del nodo possono includere ad esempio la presenza o meno di un disco rigido o di una scheda grafica, il numero di spindle nel disco rigido, le memorie centrali e altre proprietà fisiche. <br>**Capacità**: le capacità del nodo definiscono il nome e la quantità di una particolare risorsa utilizzabile da parte di un determinato nodo. Ad esempio, un nodo può definire la propria capacità per una metrica denominata "MemoryInMb" con un valore predefinito di 2048 MB di memoria disponibile. Queste capacità vengono usate in fase di esecuzione per garantire che i servizi che richiedono una determinata quantità di risorse vengano inseriti nei nodi in cui tali risorse sono disponibili nelle quantità richieste.<br>**IsPrimary**: se sono definiti più NodeType, verificare che solo uno sia impostato come primario, in cui vengono eseguiti i servizi di sistema, con il valore *true*. Tutti gli altri tipi di nodo devono essere impostati sul valore *false*|
|**Nodi**|Questi sono i dettagli per ciascun nodo che farà parte del cluster (tipo di nodo, nome del nodo, indirizzo IP, dominio di errore e dominio di aggiornamento del nodo). I computer in cui si vuole creare il cluster devono essere elencati in questa sezione con il relativo indirizzo IP. <br> Se si usa lo stesso indirizzo IP per tutti i nodi, viene creato un cluster di una casella che può essere usato per scopi di test. Non utilizzare cluster di una casella per la distribuzione dei carichi di lavoro di produzione.|

### Passaggio 2: esecuzione dello script di creazione del cluster
Dopo aver modificato la configurazione del cluster nel documento JSON e aver aggiunto tutte le informazioni relative ai nodi, eseguire lo script di PowerShell *CreateServiceFabricCluster.ps1* per la creazione del cluster dalla cartella del pacchetto e passare il percorso del file di configurazione JSON e il percorso del file CAB del pacchetto.

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina sulla quale viene eseguito questo script può far parte o meno del cluster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] I log di distribuzione sono disponibili localmente nel computer o nella VM in cui si esegue lo script CreateServiceFabricCluster di PowerShell, in una sottocartella denominata "DeploymentTraces" all'interno della cartella da cui è stato eseguito il comando di PowerShell. Per verificare se Service Fabric è stato distribuito correttamente in un computer, si possono cercare i file installati nella directory C:\\ProgramData e i processi FabricHost.exe e Fabric.exe in esecuzione in Gestione attività.

### Passaggio 3: connettersi al cluster
È ora possibile connettersi al cluster con Service Fabric Explorer direttamente da uno dei computer con http://localhost:19080/Explorer/index.html oppure in remoto con http://<*IndirizzoIPComputer*>:19080/Explorer/index.html

## Aggiungere e rimuovere nodi nel cluster

È possibile aggiungere o rimuovere nodi nel cluster di Service Fabric autonomo al variare delle esigenze aziendali. Per i passaggi dettagliati, leggere le informazioni sull'[aggiunta e rimozione di nodi in un cluster di Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).


## Rimuovere il cluster

Per rimuovere un cluster, eseguire lo script *RemoveServiceFabricCluster.ps1* di Powershell dalla cartella del pacchetto e passare il percorso del file di configurazione JSON e il percorso del file CAB del pacchetto.

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. Il computer in cui viene eseguito lo script può far parte o meno del cluster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```


## Passaggi successivi
- [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
- [Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Creare un cluster di Service Fabric autonomo con VM di Azure che eseguono Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0810_2016-->