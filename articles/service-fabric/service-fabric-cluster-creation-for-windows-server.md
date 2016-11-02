<properties
   pageTitle="Creare e gestire un cluster autonomo di Azure Service Fabric | Microsoft Azure"
   description="Creare e gestire un cluster di Azure Service Fabric su qualsiasi macchina (fisica o virtuale) che esegue Windows Server, sia locale che nel cloud."
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
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Creare e gestire un cluster eseguito in Windows Server

Azure Service Fabric consente di creare cluster Service Fabric su qualsiasi macchina virtuale o computer che esegue Windows Server. In questo modo è possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente che contenga un set di computer Windows Server interconnessi, in locale o con qualsiasi provider di cloud. Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric, denominato pacchetto autonomo per Windows Server.

Questo articolo illustra la procedura per creare un cluster con il pacchetto autonomo per Service Fabric in locale, anche se può essere facilmente adattato a qualsiasi altro ambiente, ad esempio altri provider di cloud.

>[AZURE.NOTE] Il pacchetto autonomo per Windows Server può contenere funzionalità che al momento sono disponibili solo in anteprima e non sono supportate per l'uso commerciale. Per visualizzare l'elenco delle funzionalità che sono in anteprima, vedere "Funzionalità di anteprima incluse in questo pacchetto". È anche possibile [scaricare una copia del contratto di licenza](http://go.microsoft.com/fwlink/?LinkID=733084) ora.


<a id="getsupport"></a>
## <a name="get-support-for-the-service-fabric-standalone-package"></a>Ottenere supporto per il pacchetto autonomo Service Fabric

- Porre domande alla community sul pacchetto autonomo Service Fabric per Windows Server nel [forum di Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).

- Aprire un ticket per ottenere il [supporto professionale per Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146 ).  Altre informazioni sul [supporto professionale Microsoft](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).

<a id="downloadpackage"></a>
## <a name="download-the-service-fabric-standalone-package"></a>Download del pacchetto autonomo Service Fabric


[Scaricare il pacchetto autonomo per Service Fabric per Windows Server 2012 R2](http://go.microsoft.com/fwlink/?LinkId=730690), denominato Microsoft.Azure.ServiceFabric.WindowsServer.&lt;versione&gt;.zip.


Nel pacchetto di download sono disponibili i seguenti file:

|**Nome file**|**Descrizione breve**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|File CAB contenente i file binari distribuiti in ogni computer del cluster.|
|ClusterConfig.Unsecure.DevCluster.json|File di configurazione del cluster di esempio contenente le impostazioni per un cluster di sviluppo a tre nodi non protetto con singolo computer (o macchina virtuale), con le informazioni per ogni nodo incluso nel cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|File di configurazione del cluster di esempio contenente le impostazioni per un cluster non protetto con più computer o macchine virtuali, con le informazioni per ogni computer incluso nel cluster.|
|ClusterConfig.Windows.DevCluster.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster di sviluppo a tre nodi non protetto con singolo computer (o macchina virtuale), con le informazioni per ogni nodo incluso nel cluster. Il cluster è protetto con [identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster protetto con più computer o macchine virtuali, che usa la funzionalità di sicurezza di Windows, con le informazioni per ogni computer incluso nel cluster protetto. Il cluster è protetto con [identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster di sviluppo a tre nodi non protetto con singolo computer (o macchina virtuale), con le informazioni per ogni nodo presente nel cluster. Il cluster è protetto tramite certificati X.509 di Windows.|
|ClusterConfig.x509.MultiMachine.json|File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster sicuro con più computer o macchine virtuali, con le informazioni per ogni nodo del cluster sicuro. Il cluster è protetto tramite certificati X.509 di Windows.|
|EULA_ENU.txt|Condizioni di licenza per l'uso del pacchetto autonomo Microsoft Azure Service Fabric per Windows Server. È possibile [scaricare una copia del contratto di licenza](http://go.microsoft.com/fwlink/?LinkID=733084) ora.|
|Readme.txt|Collegamento alle note sulla versione e alle istruzioni di installazione base. Si tratta di un sottoinsieme delle istruzioni disponibili in questo documento.|
|CreateServiceFabricCluster.ps1|Script di PowerShell che crea il cluster usando le impostazioni di ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script di PowerShell che rimuove un cluster usando le impostazioni di ClusterConfig.json.|
|ThirdPartyNotice.rtf |Informativa sul software di terze parti presente nel pacchetto.|
|AddNode.ps1|Script di PowerShell per l'aggiunta di un nodo a un cluster distribuito esistente.|
|RemoveNode.ps1|Script di PowerShell per la rimozione di un nodo da un cluster distribuito esistente.|
|CleanFabric.ps1|Script di PowerShell per rimuovere un'installazione autonoma di Service Fabric dal computer in uso. Le installazioni MSI precedenti devono essere rimosse usando i programmi di disinstallazione associati.|
|TestConfiguration.ps1|Script di PowerShell per l'analisi dell'infrastruttura specificata in cluster.json.|


## <a name="plan-and-prepare-your-cluster-deployment"></a>Pianificare e preparare la distribuzione del cluster
I passaggi seguenti devono essere eseguiti prima di creare il cluster.

### <a name="step-1:-plan-your-cluster-infrastructure"></a>Passaggio 1: pianificazione dell’infrastruttura del cluster
Prima di creare un cluster di Service Fabric sui propri computer, è possibile stabilire da quali tipi di errore il cluster non deve essere compromesso. Ad esempio, sono necessarie linee di alimentazione o connessioni Internet separate per queste macchine? È necessario inoltre considerare la sicurezza fisica di tali macchine. Dove si trovano le macchine e chi ha bisogno di accedervi? Dopo aver preso queste decisioni, è possibile eseguire il mapping logico delle macchine ai vari domini di errore (vedere il passaggio 4). La pianificazione dell'infrastruttura per i cluster di produzione è più complicata rispetto ai cluster di test.

<a id="preparemachines"></a>
### <a name="step-2:-prepare-the-machines-to-meet-the-prerequisites"></a>Passaggio 2: preparare le macchine con i prerequisiti
Prerequisiti per ogni macchina da aggiungere al cluster:

- Consigliati minimo 16 GB di RAM.
- Consigliati minimo 40 GB di spazio disponibile su disco.
- Consigliata una CPU 4 core o superiore.
- Connessione a una o più reti protette per tutte le macchine.
- Windows Server 2012 R2 o Windows Server 2012 (è necessaria l'installazione di [KB2858668](https://support.microsoft.com/kb/2858668) ).
- [.NET Framework 4.5.1 o versione successiva](https://www.microsoft.com/download/details.aspx?id=40773), installazione completa.
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- Il [servizio RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve essere eseguito in tutti i computer.

L'amministratore del cluster che distribuisce e configura il cluster deve disporre dei [privilegi di amministratore](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) in ogni computer. Non è possibile installare Service Fabric in un controller di dominio.

### <a name="step-3:-determine-the-initial-cluster-size"></a>Passaggio 3: determinare le dimensioni iniziali del cluster
In ogni nodo di un cluster di Service Fabric autonomo è distribuito il runtime di Service Fabric. Tutti i nodi sono membri del cluster. In una distribuzione di produzione tipica è presente un nodo per istanza (fisica o virtuale) del sistema operativo. La dimensione del cluster viene determinata in base alle esigenze aziendali. È tuttavia necessario avere una dimensione minima del cluster di tre nodi (computer o macchine virtuali).
A scopi di sviluppo è possibile configurare più di un nodo in una macchina specifica. In un ambiente di produzione, il Service Fabric supporta solo un nodo per ogni macchina virtuale o fisica.

### <a name="step-4:-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Passaggio 4: identificazione del numero di domini di errore e di aggiornamento
Un *dominio di errore* è un'unità fisica di errore ed è direttamente correlato all'infrastruttura fisica nei data center. È costituito da componenti hardware (computer, commutatori, rete e altro) che condividono un singolo punto di guasto. Sebbene non sia presente una mappatura 1:1 tra domini di errore e rack, ogni rack può essere considerato in senso lato un dominio di errore. Quando si esaminano i nodi nel cluster è consigliabile distribuire i nodi tra gli ultimi tre domini di errore.

Quando si specificano domini di errore nel file ClusterConfig.json, è possibile scegliere il nome di ogni dominio di errore. Il Service Fabric supporta i domini di errore gerarchici, in modo che possano rispecchiare la topologia infrastrutturale.  Di seguito sono riportati esempi di domini di errore validi:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un *dominio di aggiornamento* è un'unità logica di nodi. Durante gli aggiornamenti orchestrati di Service Fabric (di applicazione o cluster), tutti i nodi in un dominio di aggiornamento vengono disattivati per eseguire l'aggiornamento, mentre i nodi in altri domini di aggiornamento rimangono disponibili per gestire le richieste. Gli aggiornamenti del firmware sulle macchine non rispettano i domini di aggiornamento ed è pertanto necessario eseguirli su una macchina alla volta.

Il metodo più semplice per descrivere questi concetti è considerare i domini di errore come unità degli errori imprevisti e i domini di aggiornamento come unità della manutenzione pianificata.

Quando si specificano domini di aggiornamento nel file ClusterConfig.json è possibile scegliere il nome di ogni dominio di aggiornamento. Di seguito sono riportati esempi di nomi validi:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Per informazioni più dettagliate sui domini di aggiornamento e di errore, vedere [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="step-5:-download-the-service-fabric-standalone-package-for-windows-server"></a>Passaggio 5: scaricare il pacchetto autonomo Service Fabric per Windows Server
[Scaricare il pacchetto autonomo Service Fabric per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimerlo in un computer di distribuzione non appartenente al cluster o in uno dei computer che faranno parte del cluster. È possibile rinominare la cartella non compressa `Microsoft.Azure.ServiceFabric.WindowsServer`.

<a id="createcluster"></a>
## <a name="create-your-cluster"></a>Creazione del cluster

Dopo aver eseguito le operazioni di pianificazione e preparazione, è possibile creare il cluster.

### <a name="step-1:-modify-cluster-configuration"></a>Passaggio 1: modificare la configurazione del cluster
Il cluster è descritto in ClusterConfig.json. Per informazioni dettagliate sulle sezioni di tale file, vedere [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md).
Aprire uno dei file ClusterConfig.json del pacchetto scaricato e modificare le impostazioni seguenti:

|**Impostazioni di configurazione**|**Descrizione**|
|-----------------------|--------------------------|
|**NodeTypes**|I tipi di nodo permettono di separare i nodi del cluster in diversi gruppi. Un cluster deve avere almeno un NodeType. Tutti i nodi in un gruppo possiedono le seguenti caratteristiche comuni:  <br> **Nome**: il nome del tipo di nodo. <br>**Porte di endpoint**: endpoint con nomi diversi (porte) associati a questo tipo di nodo. È possibile usare qualsiasi numero di porta che non sia in conflitto con qualsiasi altro elemento nel manifesto e non venga già usato da un'altra applicazione eseguita nel computer o nella macchina virtuale. <br> **Proprietà di posizionamento**: descrivono proprietà per questo tipo di nodo usate come vincoli di posizionamento per i servizi di sistema o i servizi dell'utente. Queste proprietà sono coppie chiave/valore definite dall'utente che forniscono metadati aggiuntivi per un determinato nodo. Le proprietà del nodo possono includere ad esempio la presenza di un disco rigido o di una scheda grafica nel nodo, il numero di spindle nel disco rigido, le memorie centrali e altre proprietà fisiche. <br> **Capacità**: le capacità del nodo definiscono il nome e la quantità di una risorsa disponibile per l'uso da parte di un determinato nodo. Ad esempio, un nodo può definire la propria capacità per una metrica denominata "MemoryInMb" con un valore predefinito di 2048 MB di memoria disponibile. Queste capacità vengono usate in fase di esecuzione per garantire che i servizi che richiedono una determinata quantità di risorse vengano inseriti nei nodi in cui tali risorse sono disponibili nelle quantità richieste.<br>**IsPrimary**: se sono definiti più tipi di nodo, verificare che solo uno sia impostato come primario, con il valore *true*, ovvero quello in cui vengono eseguiti i servizi di sistema. Tutti gli altri tipi di nodo devono essere impostati sul valore *false*.
|**Nodi**|Questi sono i dettagli per ciascun nodo che fa parte del cluster (tipo di nodo, nome del nodo, indirizzo IP, dominio di errore e dominio di aggiornamento del nodo). I computer in cui si vuole creare il cluster devono essere elencati in questa sezione con il relativo indirizzo IP. <br>  Se si usa lo stesso indirizzo IP per tutti i nodi, viene creato un cluster di una casella che può essere usato per scopi di test. Non usare cluster di una casella per la distribuzione dei carichi di lavoro di produzione.|

### <a name="step-2:-run-the-testconfiguration-script"></a>Passaggio 2: Eseguire lo script TestConfiguration

Lo script TestConfiguration esegue un test dell'infrastruttura definita in cluster.json per verificare che le autorizzazioni necessarie siano assegnate, che le macchine siano connesse tra loro e che siano definiti altri attributi, in modo che la distribuzione venga eseguita correttamente. Si tratta fondamentalmente di una versione minima di Best Practices Analyzer. In futuro verranno aggiunte periodicamente a questo strumento altre convalide per renderlo più solido.

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True


```

### <a name="step-3:-run-the-create-cluster-script"></a>Passaggio 3: Esecuzione dello script di creazione del cluster
Dopo aver modificato la configurazione del cluster nel documento JSON e aver aggiunto tutte le informazioni relative ai nodi, eseguire lo script di PowerShell per la creazione del cluster *CreateServiceFabricCluster.ps1* dalla cartella del pacchetto e passare il percorso del file di configurazione JSON. Al termine dell'operazione accettare il contratto di licenza.

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] I log di distribuzione sono disponibili localmente nel computer o nella macchina virtuale in cui si esegue lo script CreateServiceFabricCluster di PowerShell. Si trovano in una sottocartella denominata DeploymentTraces all'interno della cartella da cui è stato eseguito il comando di PowerShell. Per verificare se Service Fabric è stato distribuito correttamente in un computer, si possono cercare i file installati nella directory C:\ProgramData e i processi FabricHost.exe e Fabric.exe in esecuzione in Gestione attività.

### <a name="step-4:-connect-to-the-cluster"></a>Passaggio 4: Connettersi al cluster

Per connettersi a un cluster protetto, vedere la sezione relativa alla [connessione di Service Fabric a un cluster protetto](service-fabric-connect-to-secure-cluster.md).

Per connettersi a un cluster non protetto, eseguire il seguente comando di PowerShell:

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5:-bring-up-service-fabric-explorer"></a>Passaggio 5: Visualizzare Service Fabric Explorer

È ora possibile connettersi al cluster con Service Fabric Explorer direttamente da uno dei computer con http://localhost:19080/Explorer/index.html o in modalità remota con http://<*IPAddressofaMachine*>:19080/Explorer/index.html.



## <a name="add-and-remove-nodes"></a>Aggiungere e rimuovere ruoli

È possibile aggiungere o rimuovere nodi nel cluster di Service Fabric autonomo al variare delle esigenze aziendali. Per i passaggi dettagliati, leggere le informazioni su [aggiunta e rimozione di nodi in un cluster Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).


## <a name="remove-a-cluster"></a>Rimuovere un cluster

Per rimuovere un cluster, eseguire lo script *RemoveServiceFabricCluster.ps1* di Powershell dalla cartella del pacchetto e passare il percorso del file di configurazione JSON. Se necessario, è anche possibile specificare un percorso per il log del processo di eliminazione.

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>
## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dati di telemetria raccolti e come rifiutarli esplicitamente

Per impostazione predefinita, il prodotto raccoglie i dati di telemetria sull'utilizzo di Service Fabric per migliorarlo. Best Practice Analyzer, che viene eseguito durante la configurazione, controlla la connettività a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se non è raggiungibile, la configurazione non riesce, a meno che non si rifiutino esplicitamente i dati di telemetria.

  1. La pipeline di telemetria cerca di caricare i dati seguenti in [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) una volta al giorno. Si tratta di un tentativo di caricamento che non influisce sulla funzionalità del cluster. I dati di telemetria vengono inviati solo dal nodo che esegue la gestione failover primaria. Nessun altro nodo invia dati di telemetria.

  2. La telemetria è costituita dagli elementi seguenti:

-            Numero di servizi
-            Numero di elementi ServiceTypes
-            Numero di elementi Applications
-            Numero di elementi ApplicationUpgrades
-            Numero di elementi FailoverUnits
-            Numero di elementi InBuildFailoverUnits
-            Numero di elementi UnhealthyFailoverUnits
-            Numero di elementi Replicas
-            Numero di elementi InBuildReplicas
-            Numero di elementi StandByReplicas
-            Numero di elementi OfflineReplicas
-            CommonQueueLength
-            QueryQueueLength
-            FailoverUnitQueueLength
-            CommitQueueLength
-            Numero di elementi Nodes
-            IsContextComplete: True/False
-            ClusterId: GUID generato casualmente per ogni cluster
-            ServiceFabricVersion
-             Indirizzo IP della macchina virtuale o del computer da cui vengono caricati i dati di telemetria


Per disabilitare la telemetria, aggiungere quanto segue all'elemento *properties* nel file di configurazione del cluster *enableTelemetry: false*.

<a id="previewfeatures"></a>
## <a name="preview-features-included-in-this-package"></a>Funzionalità di anteprima incluse in questo pacchetto

Nessuna.

>[AZURE.NOTE] Con la nuova [versione GA del cluster autonomo per Windows Server (versione 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/) è possibile aggiornare il cluster per le versioni future, manualmente o automaticamente. Poiché questa funzionalità non è disponibile nelle versioni di anteprima, è necessario creare un cluster usando la versione GA e migrare i dati e le applicazioni dal cluster di anteprima. Saranno presto disponibili altri dettagli su questa funzionalità.


## <a name="next-steps"></a>Passaggi successivi
- [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
- [Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Creare un cluster di Service Fabric autonomo con VM di Azure che eseguono Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Area attendibile]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png



<!--HONumber=Oct16_HO2-->


