<properties
   pageTitle="Creazione di un cluster Azure Service Fabric locale e multi-cloud | Microsoft Azure"
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
   ms.date="03/28/2016"
   ms.author="chackdan"/>


# Creazione di un cluster di Azure Service Fabric locale o nel cloud

Azure Service Fabric permette di creare cluster Service Fabric su qualsiasi macchina virtuale o computer con Windows Server in esecuzione. In questo modo, è possibile distribuire ed eseguire applicazioni del Service Fabric in qualsiasi ambiente in cui è presente un set interconnesso di computer Windows Server, in locale o con qualsiasi provider cloud. Il Service Fabric offre un pacchetto di installazione per la creazione di cluster Service Fabric.

Questo articolo illustra i passaggi per creare un cluster tramite il pacchetto autonomo per il Service Fabric locale, anche se può essere facilmente adattato a qualsiasi altro ambiente, ad esempio cloud.

## Pacchetto autonomo Service Fabric

Il pacchetto autonomo per le distribuzioni di Service Fabric per Windows Server 2012 R2 si chiama *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip* e può essere scaricato [qui](http://go.microsoft.com/fwlink/?LinkId=730690).

Nel pacchetto di download sono disponibili i seguenti file:

|**Nome file**|**Descrizione breve**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|Il file CAB che contiene i binari da distribuire su ogni macchina del cluster.|
|ClusterConfig.JSON|File di configurazione del cluster che contiene tutte le impostazioni per il cluster, incluse le informazioni per ogni macchina appartenente al cluster.|
|EULA.txt|Le condizioni di licenza per l'utilizzo del pacchetto autonomo del Service Fabric di Microsoft Azure Service Fabric. [Fare clic qui](http://go.microsoft.com/fwlink/?LinkID=733084) per scaricare una copia del contratto di licenza EULA ora.|
|Readme.txt|Collegamento alle note sulla versione e alle istruzioni di installazione base. Si tratta di un piccolo sottoinsieme delle istruzioni illustrate su questa pagina.|
|CreateServiceFabricCluster.ps1|Script di PowerShell che crea il cluster utilizzando le impostazioni nel file ClusterConfig.JSON.|
|RemoveServiceFabricCluster.ps1|Lo script di PowerShell per la rimozione del cluster utilizzando le impostazioni nel file ClusterConfig.JSON.|

## Pianificazione e preparazione per la distribuzione del cluster
I passaggi seguenti devono essere eseguiti prima di creare il cluster.

### Passaggio 1: pianificazione dell’infrastruttura del cluster
Si sta per creare un cluster Service Fabric sulle proprie macchine, quindi occorre stabilire a quali tipi di errore il cluster deve sopravvivere. Ad esempio, sono necessarie linee di alimentazione o connessioni Internet separate per queste macchine? Inoltre, occorre considerare anche la sicurezza fisica di tali macchine. Dove si trova fisicamente la macchina e chi deve accedervi? Dopo aver preso queste decisioni, si mappano logicamente le macchine sui vari domini di errore (vedere di seguito per ulteriori informazioni). L'infrastruttura di pianificazione per i cluster di produzione sarà molto più coinvolta rispetto a quella dei cluster di test.

### Passaggio 2: preparazione delle macchine per soddisfare i prerequisiti
Prerequisiti per ogni macchina da aggiungere al cluster:

- Consigliati minimo 2 GB di memoria
- Connettività di rete: verificare che le macchine siano su una rete o su più reti protette
- Windows Server 2012 R2 o Windows Server 2012 (è necessaria l’installazione di KB2858668 per questo componente).
- .NET Framework 4.5.1 o versione successiva, installazione completa
- Windows PowerShell 3.0
- Visual C++ 2012 (VC++ 11.0) Redistributable Package
- L'amministratore del cluster che distribuisce e configura il cluster deve avere privilegi da amministratore su ogni computer.

### Passaggio 3: determinare la dimensione iniziale del cluster
Ogni nodo è costituito da uno stack Service Fabric completo ed è un singolo membro del cluster Service Fabric. In una tipica distribuzione di Service Fabric è presente un nodo per ogni istanza del sistema operativo (fisica o virtuale). La dimensione del cluster è determinata dalle esigenze aziendali, anche se è necessario disporre di una dimensione minima del cluster di tre nodi (macchine/VM). A scopi di sviluppo, è possibile configurare più di un nodo in una macchina specifica. In un ambiente di produzione, il Service Fabric supporta solo un nodo per ogni macchina virtuale o fisica.

### Passaggio 4: identificazione del numero di domini di errore e di aggiornamento
Un **dominio di errore (FD)** è un'unità fisica di errore ed è direttamente correlato all'infrastruttura fisica nei data center. Un dominio di errore comprende componenti hardware (computer, interruttori e altro) che condividono un singolo punto di errore. Sebbene non sia presente una mappatura 1:1 tra domini di errore e i rack, ogni rack può essere considerato in senso lato un dominio di errore. Quando si esaminano i nodi nel cluster, è vivamente consigliato distribuire i nodi tra gli ultimi tre domini di errore.

Quando si specificano i domini di errore nel file *ClusterConfig.JSON*, è possibile scegliere il nome del dominio di errore. Il Service Fabric supporta i domini di errore gerarchici, in modo che possano rispecchiare la topologia infrastrutturale. Ad esempio, è consentito quanto segue:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Un **dominio di aggiornamento (UD)** è un'unità logica di nodi. Durante un aggiornamento orchestrato al Service Fabric (aggiornamento di un'applicazione o l'aggiornamento del cluster), tutti i nodi in un dominio di aggiornamento vengono portati a eseguire l'aggiornamento, mentre i nodi in altri domini di aggiornamento rimangono disponibili per soddisfare le richieste. L'aggiornamento del firmware sulle macchine non rispetta i domini di aggiornamento ed è pertanto necessario eseguirli su una macchina alla volta.

Il metodo più semplice per descrivere questi concetti è considerare i domini di errore come unità degli errori imprevisti e i domini di aggiornamento come unità della manutenzione pianificata.

Quando si specificano i domini di aggiornamento nel file *ClusterConfig.JSON*, è possibile scegliere il nome del dominio di aggiornamento. Ad esempio, sono ammessi tutti i seguenti domini:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

### Passaggio 5: download del pacchetto autonomo per il Service Fabric per Windows Server
[Scaricare il pacchetto autonomo per il Service Fabric per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimere il pacchetto su una macchina di distribuzione non appartenente al cluster o su una delle macchine che faranno parte del cluster.

## Creazione del cluster

Dopo aver seguito la procedura della sezione precedente di pianificazione e preparazione, è possibile creare il cluster.

### Passaggio 1: modificare la configurazione del cluster
Aprire il file *ClusterConfig.JSON* dal pacchetto scaricato. È possibile utilizzare qualsiasi editor per modificare le impostazioni seguenti:

|**Impostazioni di configurazione**|**Descrizione**|
|-----------------------|--------------------------|
|NodeTypes|I tipi di nodo permettono di separare i nodi del cluster in diversi gruppi. Un cluster deve avere almeno un NodeType. Tutti i nodi in un gruppo possiedono le seguenti caratteristiche comuni. <br> *Name*: nome del tipo di nodo. <br>*EndPoints*: endpoint con nomi diversi (porte) associati a questo tipo di nodo. È possibile utilizzare qualsiasi numero di porta richiesto, purché non sia in conflitto con altri elementi di questo manifesto e non sia già in uso da altri programmi sulla macchina/VM <br> *PlacementProperties*: descrivono le proprietà per questo tipo di nodo utilizzate poi come vincoli di posizionamento per servizi di sistema o i servizi correnti. Queste proprietà del nodo consentono di definire le coppie chiave/valore che forniscono metadati aggiuntivi per un determinato nodo. Tra le proprietà del nodo è possibile elencare la presenza o meno di un'unità disco rigido o di una scheda grafica, il numero di spindle del disco rigido, i core e altre proprietà fisiche. <br> *Capacità*: le capacità del nodo definiscono il nome e la quantità di una particolare risorsa utilizzabile da parte di un determinato nodo. Ad esempio, un nodo può definire la propria capacità per una metrica denominata "MemoryInMb" con un valore predefinito di 2048 MB di memoria disponibile. Queste capacità sono utilizzate in fase di esecuzione per garantire che i servizi che richiedono una determinata quantità di risorse vengano inseriti nei nodi con tali risorse a disposizione.|
|Nodi|I dettagli per ciascun nodo che farà parte del cluster (tipo di nodo, nome del nodo, indirizzo IP, dominio di errore e dominio di aggiornamento del nodo). Le macchine sulle quali deve essere creato il cluster devono essere elencate qui con il rispettivo indirizzo IP. <br> Se si utilizzano gli stessi indirizzi IP per tutti i nodi, viene creato un cluster da una casella, utilizzabile per scopi di test. Non utilizzare cluster di una casella per la distribuzione dei carichi di lavoro di produzione.|

### Passaggio 2: esecuzione dello script di creazione del cluster
Dopo aver modificato la configurazione del cluster nel documento JSON e aggiunto tutte le relative le informazioni sul nodo, eseguire lo script PowerShell di creazione del cluster dalla cartella del pacchetto e passare il percorso al file di configurazione e il percorso della radice del pacchetto.

Questo script può essere eseguito su qualsiasi macchina con accesso da amministratore a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina sulla quale viene eseguito questo script può far parte o meno del cluster.

```
C:\Microsoft.Azure.ServiceFabric.WindowsServer.5.0.135.9590> .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath C:\Microsoft.Azure.ServiceFabric.WindowsServer.5.0.135.9590\ClusterConfig.JSON -MicrosoftServiceFabricCabFilePath C:\Microsoft.Azure.ServiceFabric.WindowsServer.5.0.135.9590\MicrosoftAzureServiceFabric.cab
```

## Passaggi successivi

Dopo aver creato un cluster, occorre proteggerlo:
- [Sicurezza del cluster](service-fabric-cluster-security.md)

Leggere le seguenti informazioni introduttive sulla distribuzione e sullo sviluppo di un’app:
- [Service Fabric SDK e introduzione](service-fabric-get-started.md)
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

Per ulteriori informazioni sui cluster di Azure e sui cluster autonomi:
- [Panoramica della funzionalità di creazione di cluster autonomi e confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md)

<!---HONumber=AcomDC_0330_2016-->