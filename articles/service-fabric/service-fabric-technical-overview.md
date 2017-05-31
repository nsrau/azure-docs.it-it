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
ms.date: 2/17/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0753be48514bd2087b52fc85d27754c28dbcd58b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="service-fabric-terminology-overview"></a>Panoramica della terminologia di Service Fabric
Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Questa argomento illustra la terminologia usata da Service Fabric per agevolare la comprensione dei termini usati nella documentazione.

I concetti elencati in questa sezione vengono illustrati anche nei video seguenti di Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">Concetti fondamentali</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">Concetti di progettazione</a> e <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Concetti di esecuzione</a>.

## <a name="infrastructure-concepts"></a>Concetti relativi all'infrastruttura
**Cluster**: un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi.  I cluster possono supportare migliaia di macchine.

**Nodo**: un computer o una macchina virtuale che fa parte di un cluster viene chiamato nodo. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento. In ogni computer o macchina virtuale è disponibile un servizio di avvio automatico di Windows, `FabricHost.exe`, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: `Fabric.exe` e `FabricGateway.exe`. Questi due eseguibili costituiscono il nodo. Negli scenari di test è possibile ospitare più nodi in un singolo PC o una singola macchina virtuale eseguendo più istanze di `Fabric.exe` e `FabricGateway.exe`.

## <a name="application-concepts"></a>Concetti relativi alle applicazioni
**Tipo di applicazione**: il nome o la versione assegnata a una raccolta di tipi di servizio. Definita in un file `ApplicationManifest.xml` incorporato in una directory del pacchetto dell'applicazione che viene quindi copiato nell'archivio immagini del cluster di Service Fabric. È quindi possibile creare un'applicazione denominata da questo tipo di applicazione all'interno del cluster.

Per altre informazioni, leggere l'articolo [Modellare un'applicazione](service-fabric-application-model.md) .

**Pacchetto dell'applicazione**: una directory del disco contenente il file `ApplicationManifest.xml` del tipo di applicazione. Fa riferimento ai pacchetti del servizio per ogni servizio che costituisce il tipo di applicazione. I file nella directory del pacchetto dell'applicazione vengono copiati nell'archivio immagini del cluster di Service Fabric. Ad esempio, un pacchetto dell'applicazione per il tipo di applicazione posta elettronica può contenere un pacchetto del servizio di accodamento, un pacchetto del servizio front-end e un pacchetto del servizio di database.

**Applicazione denominata**: al termine della copia di un pacchetto dell'applicazione nell'archivio immagini, è possibile creare un'istanza dell'applicazione all'interno del cluster specificando il tipo di applicazione del pacchetto dell'applicazione, usando nome e versione corrispondenti. A ogni istanza del tipo di applicazione viene assegnato un nome URI simile al seguente: `"fabric:/MyNamedApp"`. All'interno di un cluster è possibile creare più applicazioni denominate da un singolo tipo di applicazione. È anche possibile creare applicazioni denominate da tipi di applicazione diversi. L'amministrazione e il controllo delle versioni sono gestiti in modo indipendente per ogni applicazione.      

**Tipo di servizio**: il nome o la versione assegnata ai pacchetti di codice, ai pacchetti di dati e ai pacchetti di configurazione del servizio. È definito in un file `ServiceManifest.xml`, incorporato in una directory del pacchetto del servizio a cui a sua volta fa riferimento un file `ApplicationManifest.xml` del pacchetto dell'applicazione. All'interno del cluster, dopo aver creato un'applicazione denominata, è possibile creare un servizio denominato da uno dei tipi di servizio del tipo di applicazione. Il file `ServiceManifest.xml` del tipo di servizio descrive il servizio.

Per altre informazioni, leggere l'articolo [Modellare un'applicazione](service-fabric-application-model.md) .

Sono disponibili due tipi di servizi:

* **Senza stato:** usare un servizio senza stato quando lo stato permanente del servizio è archiviato in un servizio di archiviazione esterno, ad esempio Archiviazione di Azure, database SQL di Azure o Azure Cosmos DB. Usare un servizio senza stato nei casi in cui il servizio non prevede alcun tipo di archivio permanente. Ad esempio, un servizio di calcolo in cui i valori sono passati al servizio, viene eseguito un calcolo usando tali valori e viene restituito un risultato.
* **Con stato:** usare un servizio con stato quando si vuole che Service Fabric gestisca lo stato del servizio tramite i modelli di programmazione Reliable Collections o Reliable Actors. Per la scalabilità specificare il numero di partizioni su cui distribuire lo stato durante la creazione di un servizio denominato. Specificare inoltre quante volte replicare lo stato tra i nodi, per l'affidabilità. Ogni servizio denominato ha un'unica replica primaria e più repliche secondarie. Per modificare lo stato del servizio denominato, scrivere nella replica primaria. Service Fabric replica quindi questo stato in tutte le repliche secondarie, mantenendo lo stato sincronizzato. Quando si verifica un errore nella replica primaria, Service Fabric lo rileva automaticamente e alza di livello una delle repliche secondarie rendendola così la replica primaria. Crea quindi una nuova replica secondaria.  

**Pacchetto del servizio**: una directory del disco contenente il file `ServiceManifest.xml` del tipo di servizio. Questo file fa riferimento al codice, ai dati statici e ai pacchetti di configurazione per il tipo di servizio. Il file `ApplicationManifest.xml` del tipo di applicazione fa riferimento ai file nella directory del pacchetto del servizio. Ad esempio, un pacchetto del servizio può fare riferimento al codice, ai dati statici e ai pacchetti di configurazione che costituiscono un servizio di database.

**Servizio denominato**: dopo aver creato un'applicazione denominata, è possibile creare un'istanza di uno dei relativi tipi di servizio all'interno del cluster specificando il tipo di servizio, usando nome e versione corrispondenti. A ogni istanza del tipo di servizio viene assegnato un nome URI con un ambito definito dall'URI della relativa applicazione denominata. Se ad esempio si crea un servizio denominato "MyDatabase" all'interno di un'applicazione denominata "MyNamedApp", l'URI corrispondente sarà simile al seguente: `"fabric:/MyNamedApp/MyDatabase"`. All'interno di un'applicazione denominata è possibile creare vari servizi denominati. Ogni servizio denominato può avere uno schema di partizione e numeri di istanze/repliche specifici.

**Pacchetto di codice**: directory del disco contenente i file eseguibili del tipo di servizio, in genere file EXE/DLL. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di codice. Al momento della creazione di un servizio denominato, il pacchetto di codice viene copiato in uno o più nodi selezionati per l'esecuzione del servizio. Viene quindi avviata l'esecuzione. Esistono due tipi di eseguibili di pacchetto di codice:

* **Eseguibili guest**: eseguibili che vengono eseguiti così come sono nel sistema operativo (Windows o Linux) host. Significa che questi eseguibili non indirizzano o non fanno riferimento ad alcun file del runtime di Service Fabric e che di conseguenza non usano alcun modello di programmazione di Service Fabric. Questi file eseguibili possono usare alcune funzionalità di Service Fabric, ad esempio il servizio denominato per l'individuazione di endpoint. I file guest eseguibili non possono indicare le metriche di caricamento specifiche per ogni istanza del servizio.
* **Eseguibili host del servizio**: eseguibili che usano i modelli di programmazione di Service Fabric tramite il collegamento ai file di runtime di Service Fabric, abilitando le funzionalità di Service Fabric. Un'istanza di servizio denominato può ad esempio registrare gli endpoint con il servizio Naming di Service Fabric e anche segnalare le metriche di carico.      

**Pacchetto dati**: una directory del disco contenente file di dati di sola lettura statici, in genere file di foto, audio e video, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto dati. Al momento della creazione di un servizio denominato, il pacchetto dati viene copiato in uno o più nodi selezionati per l'esecuzione del servizio.  Il codice avvia l'esecuzione e può quindi accedere ai file di dati.

**Pacchetto di configurazione**: una directory del disco contenente file di configurazione di sola lettura statici, in genere file di testo, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di configurazione. Al momento della creazione di un servizio denominato, i file nel pacchetto di configurazione vengono copiati in uno o più nodi selezionati per l'esecuzione del servizio. Il codice avvia l'esecuzione e può quindi accedere ai file di configurazione.

**Contenitori**: per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in immagini contenitore. I contenitori sono una tecnologia di virtualizzazione che virtualizza il sistema operativo sottostante rispetto alle applicazioni. Un'applicazione, il relativo runtime e le relative dipendenze e librerie di sistema vengono eseguiti all'interno di un contenitore con accesso privato completo alla vista isolata specifica del contenitore dei costrutti del sistema operativo. Service Fabric supporta contenitori Docker in Linux e contenitori Windows Server.  Per altre informazioni, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md).

**Schema di partizione**: quando si crea un servizio denominato, è necessario specificare uno schema di partizione. I servizi con grandi quantità di stato suddividono i dati tra partizioni che li distribuiscono nei vari nodi del cluster. In questo modo è possibile ridimensionare lo stato del servizio denominato. All'interno di una partizione, per i servizi denominati senza stato sono presenti istanze mentre per i servizi denominati con stato sono presenti repliche. In genere i servizi denominati senza stato avranno sempre una sola partizione, dal momento che non hanno uno stato interno. Le istanze della partizione garantiscono la disponibilità. Se un'istanza presenta un errore, le altre continuano a funzionare normalmente e Service Fabric creerà una nuova istanza. I servizi denominati con stato gestiscono il proprio stato all'interno delle repliche e ogni partizione contiene un set di repliche dedicato con tutti gli stati sincronizzati. Se una replica presenta un errore, Service Fabric ne crea una nuova da quelle esistenti.

Per altre informazioni, vedere [Partizionare i servizi Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Servizi di sistema
In ogni cluster vengono creati alcuni servizi di sistema che forniscono le funzionalità della piattaforma di Service Fabric.

**Servizio Naming**: ogni cluster di Service Fabric ha un servizio Naming che risolve i nomi del servizio in una posizione nel cluster. Consente agli utenti di gestire i nomi e le proprietà del servizio in modo analogo al servizio DNS (Domain Name Service) Internet per il cluster. Usando il servizio Naming, i client possono comunicare in modo sicuro con qualsiasi nodo del cluster per risolvere il nome di un servizio e il suo percorso.  I client ottengono l'indirizzo IP del computer effettivo e la porta su cui è in esecuzione. È possibile sviluppare servizi e client in grado di risolvere il percorso di rete corrente, anche se le applicazioni vengono spostate all'interno del cluster, ad esempio a causa di errori, bilanciamento delle risorse o ridimensionamento del cluster.

Per altre informazioni sulle API di comunicazione del client e del servizio che interagiscono con il servizio Naming, vedere l'articolo [Comunicare con i servizi](service-fabric-connect-and-communicate-with-services.md) .

**Servizio Image Store**: ogni cluster di Service Fabric ha un servizio Image Store in cui vengono conservati i pacchetti dell'applicazione distribuiti e con controllo delle versioni. Copiare il contenuto di un pacchetto dell'applicazione nell'archivio immagini e quindi registrare il tipo dell'applicazione all'interno di quel pacchetto dell'applicazione. Una volta eseguito il provisioning del tipo di applicazione, è possibile creare applicazioni denominate. È possibile annullare la registrazione di un tipo di applicazione dal servizio Image Store solo dopo aver eliminato tutte le relative applicazioni denominate.

Per altre informazioni sul servizio Image Store leggere [Informazioni sull'impostazione ImageStoreConnectionString](service-fabric-image-store-connection-string.md).

Per altre informazioni sulla distribuzione di applicazioni nel servizio Image Store, vedere [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md) .

## <a name="built-in-programming-models"></a>Modelli di programmazione predefiniti
Sono disponibili modelli di programmazione di .NET Framework per creare servizi di Service Fabric:

**Reliable Services**: API che consente di creare servizi con e senza stato. I servizi con stato archiviano il proprio stato in Reliable Collections, ad esempio un dizionario o una coda. È anche possibile collegare un'ampia gamma di stack di comunicazione, ad esempio API Web e Windows Communication Foundation (WCF).

**Reliable Actors**: API che consente di creare oggetti con e senza stato tramite il modello di programmazione Actor virtuale. Questo modello può risultare utile in presenza di molte unità indipendenti di calcolo/stato. Poiché questo modello usa un modello di threading basato su turni, è consigliabile evitare codice che effettua chiamate ad altri attori o servizi, dal momento che un singolo attore non può elaborare altre richieste in ingresso fino a quando tutte le relative richieste in uscita non sono state completate.

Per altre informazioni, vedere [Scegliere un modello di programmazione per un servizio](service-fabric-choose-framework.md) .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Service Fabric, vedere:

* [Panoramica di Service Fabric](service-fabric-overview.md)
* [Perché usare un approccio ai microservizi per la compilazione di applicazioni](service-fabric-overview-microservices.md)
* [Scenari applicativi](service-fabric-application-scenarios.md)


