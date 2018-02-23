---
title: Informazioni sulla terminologia di Azure Service Fabric | Documentazione Microsoft
description: Panoramica della terminologia di Service Fabric. Illustra i concetti chiave relativi alla terminologia e i termini usati nel resto della documentazione.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/18/2017
ms.author: ryanwi
ms.openlocfilehash: dc7e536ce40bf95e1950e1e44844cd8fe26ea1a1
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="service-fabric-terminology-overview"></a>Panoramica della terminologia di Service Fabric
Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Questo articolo illustra in modo dettagliato la terminologia usata da Service Fabric, per agevolare la comprensione dei termini usati nella documentazione.

I concetti elencati in questa sezione vengono illustrati anche nei video di Microsoft Virtual Academy relativi a: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">Concetti fondamentali</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">Concetti di progettazione</a> e <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Concetti di esecuzione</a>.

## <a name="infrastructure-concepts"></a>Concetti relativi all'infrastruttura
**Cluster**: un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi.  I cluster possono supportare migliaia di macchine.

**Nodo**: un computer o una macchina virtuale che fa parte di un cluster viene detto *nodo*. A ogni nodo viene assegnato un nome (stringa). I nodi hanno alcune caratteristiche, ad esempio le proprietà di selezione host. Ogni computer o macchina virtuale ha un servizio Windows di avvio automatico, `FabricHost.exe`, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: `Fabric.exe` e `FabricGateway.exe`. Questi due eseguibili costituiscono il nodo. Negli scenari di test è possibile ospitare più nodi in un singolo PC o una singola macchina virtuale eseguendo più istanze di `Fabric.exe` e `FabricGateway.exe`.

## <a name="application-concepts"></a>Concetti relativi alle applicazioni
**Tipo di applicazione**: il nome e la versione assegnati a una raccolta di tipi di servizio. Viene definito in un file `ApplicationManifest.xml` e incorporato in una directory del pacchetto dell'applicazione. La directory viene quindi copiata nell'archivio immagini del cluster di Service Fabric. È quindi possibile creare un'applicazione denominata da questo tipo di applicazione all'interno del cluster.

Per altre informazioni, leggere l'articolo [Modellare un'applicazione](service-fabric-application-model.md).

**Pacchetto dell'applicazione**: directory del disco contenente il file `ApplicationManifest.xml` del tipo di applicazione. Fa riferimento ai pacchetti del servizio per ogni servizio che costituisce il tipo di applicazione. I file nella directory del pacchetto dell'applicazione vengono copiati nell'archivio immagini del cluster di Service Fabric. Un pacchetto dell'applicazione per il tipo di applicazione posta elettronica può ad esempio contenere riferimenti a un pacchetto del servizio di accodamento, un pacchetto del servizio front-end e un pacchetto del servizio di database.

**Applicazione denominata**: dopo aver copiato un pacchetto dell'applicazione nell'archivio immagini, è possibile creare un'istanza dell'applicazione all'interno del cluster. Si crea un'istanza quando si specifica il tipo di applicazione del pacchetto dell'applicazione, usando il relativo nome o versione. A ogni istanza del tipo di applicazione viene assegnato un URI (Uniform Resource Identifier) simile al seguente: `"fabric:/MyNamedApp"`. All'interno di un cluster è possibile creare più applicazioni denominate da un singolo tipo di applicazione. È anche possibile creare applicazioni denominate da tipi di applicazione diversi. L'amministrazione e il controllo delle versioni sono gestiti in modo indipendente per ogni applicazione.      

**Tipo di servizio**: il nome e la versione assegnati ai pacchetti di codice, ai pacchetti di dati e ai pacchetti di configurazione di un servizio. Il tipo di servizio è definito nel file `ServiceManifest.xml` e incorporato in una directory del pacchetto del servizio. Il file `ApplicationManifest.xml` del pacchetto dell'applicazione fa quindi riferimento alla directory del pacchetto del servizio. All'interno del cluster, dopo aver creato un'applicazione denominata, è possibile creare un servizio denominato da uno dei tipi di servizio del tipo di applicazione. Il file `ServiceManifest.xml` del tipo di servizio descrive il servizio.

Per altre informazioni, leggere l'articolo [Modellare un'applicazione](service-fabric-application-model.md).

Sono disponibili due tipi di servizi:

* **Senza stato**: usare un servizio senza stato quando lo stato permanente del servizio è archiviato in un servizio di archiviazione esterno, ad esempio Archiviazione di Azure, database SQL di Azure o Azure Cosmos DB. Usare un servizio senza stato nei casi in cui il servizio non prevede alcun tipo di archivio permanente. Ad esempio, per un servizio di calcolo in cui i valori vengono passati al servizio, viene eseguito un calcolo che usa tali valori e viene restituito un risultato.
* **Con stato**: usare un servizio con stato quando si vuole che Service Fabric gestisca lo stato del servizio tramite i modelli di programmazione Reliable Collections o Reliable Actors. Quando si crea un servizio denominato, specificare il numero di partizioni su cui distribuire lo stato per ottenere scalabilità. Specificare anche quante volte replicare lo stato tra i nodi, per ottenere affidabilità. Ogni servizio denominato ha un'unica replica primaria e più repliche secondarie. Lo stato del servizio denominato si modifica quando si scrive nella replica primaria. Service Fabric replica quindi questo stato in tutte le repliche secondarie, per mantenere lo stato sincronizzato. Quando si verifica un errore nella replica primaria, Service Fabric lo rileva automaticamente e alza di livello una delle repliche secondarie rendendola così la replica primaria. Crea quindi una nuova replica secondaria.  

Le **repliche o istanze** fanno riferimento al codice (e allo stato per i servizi con stato) di un servizio distribuito e in esecuzione. Vedere [Repliche e istanze](service-fabric-concepts-replica-lifecycle.md).

**Riconfigurazione** fa riferimento al processo di qualsiasi modifica nel set di repliche di un servizio. Vedere [Riconfigurazione](service-fabric-concepts-reconfiguration.md).

**Pacchetto del servizio**: directory del disco contenente il file `ServiceManifest.xml` del tipo di servizio. Questo file fa riferimento al codice, ai dati statici e ai pacchetti di configurazione per il tipo di servizio. Il file `ApplicationManifest.xml` del tipo di applicazione fa riferimento ai file nella directory del pacchetto del servizio. Un pacchetto del servizio può ad esempio fare riferimento al codice, ai dati statici e ai pacchetti di configurazione che costituiscono un servizio di database.

**Servizio denominato**: dopo aver creato un'applicazione denominata, è possibile creare un'istanza di uno dei relativi tipi di servizio all'interno del cluster. Specificare il tipo di servizio usando i relativi nome e versione. A ogni istanza del tipo di servizio viene assegnato un nome URI con un ambito definito dall'URI della relativa applicazione denominata. Se ad esempio si crea un servizio denominato "MyDatabase" all'interno di un'applicazione denominata "MyNamedApp", l'URI corrispondente sarà simile al seguente: `"fabric:/MyNamedApp/MyDatabase"`. All'interno di un'applicazione denominata è possibile creare vari servizi denominati. Ogni servizio denominato può avere uno schema di partizione e numeri di istanze o repliche specifici.

**Pacchetto di codice**: directory del disco contenente i file eseguibili del tipo di servizio, in genere file EXE/DLL. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di codice. Quando si crea un servizio denominato, il pacchetto di codice viene copiato nel nodo o nei nodi selezionati per l'esecuzione del servizio denominato. Viene quindi avviata l'esecuzione del codice. Esistono due tipi di eseguibili di pacchetto di codice:

* **Eseguibili guest**: eseguibili che vengono eseguiti così come sono nel sistema operativo (Windows o Linux) host. Questi eseguibili non rimandano né fanno riferimento ad alcun file del runtime di Service Fabric e quindi non usano i modelli di programmazione di Service Fabric. Questi file eseguibili non sono in grado di usare alcune funzionalità di Service Fabric, ad esempio il servizio Naming per l'individuazione di endpoint. I file guest eseguibili non possono segnalare le metriche di caricamento specifiche per ogni istanza del servizio.
* **Eseguibili host del servizio**: eseguibili che usano i modelli di programmazione di Service Fabric tramite il collegamento ai file di runtime di Service Fabric, per abilitare le funzionalità di Service Fabric. Un'istanza di servizio denominato può ad esempio registrare gli endpoint con il servizio Naming di Service Fabric e anche segnalare le metriche di carico.      

**Pacchetto dati**: directory del disco contenente file di dati di sola lettura statici, in genere file di foto, audio e video, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto dati. Quando si crea un servizio denominato, il pacchetto di dati viene copiato nel nodo o nei nodi selezionati per l'esecuzione del servizio denominato. Il codice avvia l'esecuzione e può quindi accedere ai file di dati.

**Pacchetto di configurazione**: directory del disco contenente file di configurazione di sola lettura statici, in genere file di testo, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di configurazione. Quando si crea un servizio denominato, i file nel pacchetto di configurazione vengono copiati in uno o più nodi selezionati per l'esecuzione del servizio denominato. Viene quindi avviata l'esecuzione del codice, che può accedere ai file di configurazione.

**Contenitori**: per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in immagini contenitore. I contenitori sono una tecnologia di virtualizzazione che virtualizza il sistema operativo sottostante rispetto alle applicazioni. Un'applicazione e i relativi runtime, dipendenze e librerie di sistema vengono eseguiti in un contenitore. Il contenitore ha accesso privato completo alla vista isolata dei costrutti del sistema operativo specifica del contenitore stesso. Service Fabric supporta contenitori Docker in Linux e contenitori Windows Server. Per altre informazioni, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md).

**Schema di partizione**: quando si crea un servizio denominato, si specifica uno schema di partizione. I servizi con una notevole quantità di stato suddividono i dati tra partizioni, distribuendo così lo stato tra i nodi del cluster. Suddividendo i dati tra partizioni, è possibile ridimensionare lo stato del servizio denominato. All'interno di una partizione, i servizi denominati senza stato hanno istanze, mentre i servizi denominati con stato hanno repliche. In genere, i servizi denominati senza stato hanno una sola partizione, perché non hanno uno stato interno. Le istanze della partizione garantiscono la disponibilità. In caso di errore di un'istanza, le altre istanze continuano a funzionare normalmente e Service Fabric crea una nuova istanza. I servizi denominati con stato gestiscono il proprio stato all'interno delle repliche e ogni partizione ha il proprio set di repliche, in modo che lo stato venga mantenuto sincronizzato. Se una replica presenta un errore, Service Fabric ne crea una nuova da quelle esistenti.

Per altre informazioni, vedere [Partizionare i servizi Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Servizi di sistema
In ogni cluster vengono creati alcuni servizi di sistema che forniscono le funzionalità della piattaforma di Service Fabric.

**Servizio Naming**: ogni cluster di Service Fabric ha un servizio Naming che risolve i nomi dei servizi in una posizione nel cluster. L'utente gestisce i nomi e le proprietà dei servizi in modo analogo al servizio DNS (Domain Name Service) Internet per il cluster. I client comunicano in modo sicuro con qualsiasi nodo del cluster usando il servizio Naming per risolvere il nome di un servizio e il suo percorso. Le applicazioni si spostano nel cluster. Ciò può ad esempio essere dovuto a errori, bilanciamento delle risorse o ridimensionamento del cluster. È possibile sviluppare servizi e client in grado di risolvere il percorso di rete corrente. I client ottengono l'indirizzo IP del computer effettivo e la porta su cui è in esecuzione.

Per altre informazioni sulle API di comunicazione di client e servizi che funzionano con il servizio Naming, vedere l'articolo [Comunicare con i servizi](service-fabric-connect-and-communicate-with-services.md).

**Servizio archivio immagini**: ogni cluster di Service Fabric ha un servizio archivio immagini in cui vengono conservati i pacchetti dell'applicazione distribuiti e con controllo delle versioni. Copiare il contenuto di un pacchetto dell'applicazione nell'archivio immagini e quindi registrare il tipo dell'applicazione all'interno di quel pacchetto dell'applicazione. Una volta effettuato il provisioning del tipo di applicazione, è possibile creare applicazioni denominate. È possibile annullare la registrazione di un tipo di applicazione dal servizio Image Store solo dopo aver eliminato tutte le relative applicazioni denominate.

Per altre informazioni sul servizio Image Store leggere [Informazioni sull'impostazione ImageStoreConnectionString](service-fabric-image-store-connection-string.md).

Per altre informazioni sulla distribuzione di applicazioni nel servizio archivio immagini, vedere [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md).

**Servizio Gestione failover**: ogni cluster di Service Fabric ha un servizio Gestione failover responsabile delle azioni seguenti:
   - Eseguire funzioni correlate a disponibilità elevata e coerenza dei servizi.
   - Orchestrare gli aggiornamenti di applicazioni e cluster.
   - Interagire con altri componenti del sistema.

## <a name="built-in-programming-models"></a>Modelli di programmazione predefiniti
Sono disponibili modelli di programmazione di .NET Framework per creare servizi di Service Fabric:

**Reliable Services**: API che consente di creare servizi con e senza stato. I servizi con stato archiviano il proprio stato in Reliable Collections, ad esempio un dizionario o una coda. È anche possibile collegare vari stack di comunicazione, ad esempio API Web e Windows Communication Foundation (WCF).

**Reliable Actors**: API che consente di creare oggetti con e senza stato tramite il modello di programmazione Actor virtuale. Questo modello è utile in presenza di molte unità indipendenti di calcolo o stato. Questo modello usa un modello di threading basato su turni, quindi è consigliabile evitare codice che effettua chiamate ad altri attori o servizi, perché un singolo attore non può elaborare altre richieste in ingresso fino a quando tutte le relative richieste in uscita non sono state completate.

Per altre informazioni, vedere [Scegliere un modello di programmazione per un servizio](service-fabric-choose-framework.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Service Fabric, vedere:

* [Panoramica di Service Fabric](service-fabric-overview.md)
* [Perché usare un approccio ai microservizi per la compilazione di applicazioni](service-fabric-overview-microservices.md)
* [Scenari applicativi](service-fabric-application-scenarios.md)


