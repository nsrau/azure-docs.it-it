<properties
   pageTitle="Panoramica della terminologia di Service Fabric | Microsoft Azure"
   description="Panoramica della terminologia di Service Fabric. Illustra i concetti chiave relativi alla terminologia e i termini usati nel resto della documentazione."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/05/2016"
   ms.author="ryanwi"/>

# Panoramica della terminologia di Service Fabric

Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Questa argomento illustra la terminologia usata da Service Fabric per agevolare la comprensione dei termini usati altrove nella documentazione.

## Concetti relativi all'infrastruttura
**Cluster**: un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. I cluster possono supportare migliaia di macchine.

**Nodo**: un computer o una macchina virtuale che fa parte di un cluster viene chiamato nodo. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento. Ogni computer o macchina virtuale ha un servizio di Windows ad avvio automatico, `FabricHost.exe`, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: `Fabric.exe` e `FabricGateway.exe`. Questi due eseguibili costituiscono il nodo. Negli scenari di test è possibile ospitare più nodi in un singolo PC o macchina virtuale eseguendo più istanze di `Fabric.exe` e `FabricGateway.exe`.

## Concetti relativi alle applicazioni
**Tipo di applicazione**: il nome o la versione assegnata a una raccolta di tipi di servizio. Queste informazioni sono definite in un file `ApplicationManifest.xml`, incorporato in una directory del pacchetto dell'applicazione che viene quindi copiato nell'archivio immagini del cluster di Service Fabric. È quindi possibile creare un'applicazione denominata da questo tipo di applicazione all'interno del cluster.

Per altre informazioni, leggere l'articolo [Modellare un'applicazione](service-fabric-application-model.md).

**Pacchetto dell'applicazione**: una directory del disco contenente il file `ApplicationManifest.xml` del tipo di applicazione. Questo file fa riferimento ai pacchetti del servizio per ogni servizio che costituisce il tipo di applicazione. I file nella directory del pacchetto dell'applicazione vengono copiati nell'archivio immagini del cluster di Service Fabric. Ad esempio, un pacchetto dell'applicazione per il tipo di applicazione posta elettronica può contenere un pacchetto del servizio di accodamento, un pacchetto del servizio front-end e un pacchetto del servizio di database.

**Applicazione denominata**: al termine della copia di un pacchetto dell'applicazione nell'archivio immagini, è possibile creare un'istanza dell'applicazione all'interno del cluster specificando il tipo di applicazione del pacchetto dell'applicazione, usando nome e versione corrispondenti. A ogni istanza del tipo di applicazione viene assegnato un nome URI simile al seguente: `"fabric:/MyNamedApp"`. All'interno di un cluster è possibile creare più applicazioni denominate da un singolo tipo di applicazione. È anche possibile creare applicazioni denominate da tipi di applicazione diversi. L'amministrazione e il controllo delle versioni sono gestiti in modo indipendente per ogni applicazione.

**Tipo di servizio**: il nome o la versione assegnata ai pacchetti di codice, ai pacchetti di dati e ai pacchetti di configurazione del servizio. Queste informazioni sono definite in un file `ServiceManifest.xml`, che è incorporato in una directory del pacchetto del servizio a cui a sua volta fa riferimento un file `ApplicationManifest.xml` del pacchetto dell'applicazione. All'interno del cluster, dopo aver creato un'applicazione denominata, è possibile creare un servizio denominato da uno dei tipi di servizio del tipo di applicazione. Il file `ServiceManifest.xml` del tipo di servizio descrive il servizio.

Per altre informazioni, leggere l'articolo [Modellare un'applicazione](service-fabric-application-model.md).

Sono disponibili due tipi di servizi:

- **Senza stato:** usare un servizio senza stato quando lo stato permanente del servizio è archiviato in un servizio di archiviazione esterno, ad esempio Archiviazione di Azure, database SQL di Azure o Azure DocumentDB. È consigliabile usare un servizio senza stato anche nei casi in cui il servizio non prevede alcun tipo di archivio permanente. Ad esempio, un servizio di calcolo in cui i valori sono passati al servizio, viene eseguito un calcolo usando tali valori e viene restituito un risultato.

- **Con stato:** usare un servizio con stato quando si vuole che Service Fabric gestisca lo stato del servizio tramite i modelli di programmazione Reliable Collections o Reliable Actors. Durante la creazione di un servizio denominato, è necessario specificare il numero di partizioni su cui si vuole distribuire lo stato (per la scalabilità) e il numero di volte in base a cui replicare lo stato tra i nodi (per l'affidabilità). Ogni servizio denominato ha un'unica replica primaria e più repliche secondarie. È possibile modificare lo stato del servizio denominato tramite la scrittura nella replica primaria. Service Fabric replica quindi lo stato su tutte le repliche secondarie per garantire che tutte le repliche siano sincronizzate. Quando si verifica un errore nella replica primaria, Service Fabric lo rileva automaticamente, alza di livello una delle repliche secondarie rendendola così la replica primaria e quindi crea una nuova replica secondaria.

**Pacchetto del servizio**: una directory del disco contenente il file `ServiceManifest.xml` del tipo di servizio. Questo file fa riferimento al codice, ai dati statici e ai pacchetti di configurazione per il tipo di servizio. Il file `ApplicationManifest.xml` del tipo di applicazione fa riferimento ai file nella directory del pacchetto del servizio. Ad esempio, un pacchetto del servizio può fare riferimento al codice, ai dati statici e ai pacchetti di configurazione che costituiscono un servizio di database.

**Servizio denominato**: dopo aver creato un'applicazione denominata, è possibile creare un'istanza di uno dei relativi tipi di servizio all'interno del cluster specificando il tipo di servizio, usando nome e versione corrispondenti. A ogni istanza del tipo di servizio viene assegnato un nome URI con un ambito definito dall'URI della relativa applicazione denominata. Se ad esempio si crea un servizio denominato "MyDatabase" all'interno di un'applicazione denominata "MyNamedApp", l'URI corrispondente sarà simile al seguente: `"fabric:/MyNamedApp/MyDatabase"`. All'interno di un'applicazione denominata è possibile creare vari servizi denominati. Ogni servizio denominato può avere uno schema di partizione e numeri di istanze/repliche specifici.

**Pacchetto di codice**: directory del disco contenente i file eseguibili del tipo di servizio, in genere file EXE/DLL. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di codice. Quando viene creato un servizio denominato, i file nel pacchetto di codice vengono copiati nei nodi che Service Fabric seleziona per eseguire il servizio e quindi viene avviata l'esecuzione del codice. Esistono due tipi di eseguibili di pacchetto di codice:

- **Eseguibili guest**: eseguibili che vengono eseguiti così come sono nel sistema operativo (Windows o Linux) host. Significa che questi eseguibili non indirizzano o non fanno riferimento ad alcun file del runtime di Service Fabric e che di conseguenza non usano i modelli di programmazione di Service Fabric. Questi eseguibili non riescono a usare alcune funzionalità di Service Fabric, ad esempio il servizio Naming per l'individuazione degli endpoint, quindi non possono segnalare le metriche di carico specifiche per ogni istanza del servizio.

- **Eseguibili host del servizio**: si tratta di eseguibili che usano i modelli di programmazione di Service Fabric tramite il collegamento ai file di runtime di Service Fabric. Parti di codice dell'eseguibile vengono quindi associate a Service Fabric per consentire la disponibilità di funzionalità aggiuntive. Un'istanza di servizio denominato può ad esempio registrare gli endpoint con il servizio Naming di Service Fabric e anche segnalare le metriche di carico.

**Pacchetto dati**: una directory del disco contenente file di dati di sola lettura statici, in genere file di foto, audio e video, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto dati. Quando viene creato un servizio denominato, i file nel pacchetto dati vengono copiati nei nodi che Service Fabric seleziona per eseguire il servizio e quindi viene avviata l'esecuzione del codice. Il codice può ora accedere ai file di dati.

**Pacchetto di configurazione**: una directory del disco contenente file di configurazione di sola lettura statici, in genere file di testo, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di configurazione. Quando viene creato un servizio denominato, i file nel pacchetto di configurazione vengono copiati nei nodi che Service Fabric seleziona per eseguire il servizio e quindi viene avviata l'esecuzione del codice. Il codice può ora accedere ai file di configurazione.

**Schema di partizione**: quando si crea un servizio denominato, è necessario specificare uno schema di partizione. I servizi con grandi quantità di stato suddividono i dati tra partizioni che li distribuiscono nei vari nodi del cluster. In questo modo è possibile ridimensionare lo stato del servizio denominato. All'interno di una partizione, per i servizi denominati senza stato sono presenti istanze mentre per i servizi denominati con stato sono presenti repliche. In genere i servizi denominati senza stato avranno sempre una partizione, dal momento che non hanno stato interno. Le istanze della partizione garantiscono la disponibilità. Se un'istanza presenta un errore, le altre continuano a funzionare normalmente e Service Fabric creerà una nuova istanza. I servizi denominati con stato gestiscono il proprio stato all'interno delle repliche e ogni partizione contiene un set di repliche dedicato con tutti gli stati sincronizzati. Se una replica presenta un errore, Service Fabric ne crea una nuova da quelle esistenti.

Per altre informazioni, vedere [Partizionare i servizi Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md).

## Servizi di sistema
In ogni cluster vengono creati alcuni servizi di sistema che forniscono le funzionalità della piattaforma di Service Fabric.

**Servizio Naming**: ogni cluster di Service Fabric ha un servizio Naming che risolve i nomi del servizio in una posizione nel cluster e consente agli utenti di gestire i nomi e le proprietà del servizio. È analogo a DNS (Domain Name Service) Internet per il cluster. Il servizio Naming consente ai client di comunicare in modo sicuro con qualsiasi nodo del cluster per risolvere un nome del servizio e la rispettiva posizione, ovvero per ottenere l'indirizzo IP effettivo del computer e la porta in cui è attualmente in esecuzione. L'uso delle API del client di comunicazione consente di sviluppare servizi e client in grado di risolvere il percorso di rete corrente, anche se le applicazioni vengono spostate all'interno del cluster, ad esempio a causa di errori, bilanciamento delle risorse o ridimensionamento del cluster.

Per altre informazioni sull'uso delle API di comunicazione del client e del servizio che interagiscono con il servizio Naming, vedere l'articolo [Comunicare con i servizi](service-fabric-connect-and-communicate-with-services.md).

**Servizio Image Store**: ogni cluster di Service Fabric ha un servizio Image Store in cui vengono conservati i pacchetti dell'applicazione distribuiti e con controllo delle versioni. È necessario copiare il contenuto di un pacchetto dell'applicazione nel servizio Image Store e quindi registrare il tipo dell'applicazione contenuto in quel pacchetto dell'applicazione. Dopo avere eseguito il provisioning del tipo di applicazione, è possibile creare applicazioni denominate di tale tipo. È possibile annullare la registrazione di un tipo di applicazione dal servizio Image Store solo dopo aver eliminato tutte le relative applicazioni denominate.

Per altre informazioni sulla distribuzione di applicazioni nel servizio Image Store, vedere [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md).

## Modelli di programmazione predefiniti
Sono disponibili modelli di programmazione di .NET Framework per creare servizi di Service Fabric:

**Reliable Services**: un'API che consente di creare servizi con e senza stato. I servizi con stato archiviano il proprio stato in Reliable Collections, ad esempio un dizionario o una coda. È anche possibile collegare un'ampia gamma di stack di comunicazione, ad esempio API Web e Windows Communication Foundation (WCF).

**Reliable Actors**: un'API che consente di creare oggetti con e senza stato tramite il modello di programmazione Actor virtuale. Questo modello può risultare utile in presenza di molte unità indipendenti di calcolo/stato. Poiché questo modello usa un modello di threading basato su turni, è consigliabile evitare codice che effettua chiamate ad altri attori o servizi, dal momento che un singolo attore non può elaborare altre richieste in ingresso fino a quando tutte le relative richieste in uscita non sono state completate.

Per altre informazioni, vedere [Scegliere un modello di programmazione per un servizio](service-fabric-choose-framework.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
Per altre informazioni su Service Fabric, vedere:

- [Panoramica di Service Fabric](service-fabric-overview.md)
- [Perché usare un approccio ai microservizi per la compilazione di applicazioni](service-fabric-overview-microservices.md)
- [Scenari applicativi](service-fabric-application-scenarios.md)

<!---HONumber=AcomDC_0518_2016-->