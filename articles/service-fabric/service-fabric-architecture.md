<properties
   pageTitle="Architettura di Service Fabric"
   description="Service Fabric è una piattaforma di sistemi distribuiti che consente di creare applicazioni scalabili, affidabili e facilmente gestibili per il cloud. Questo articolo illustra l'architettura di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="rsinha"/>

# Architettura di Service Fabric

Come qualsiasi piattaforma, Service Fabric si basa su molti sottosistemi. Questi sottosistemi stratificati a livelli consentono agli sviluppatori di applicazioni di scrivere applicazioni di Service Fabric con le caratteristiche seguenti:

* Disponibilità elevata 
* Scalabilità 
* Facilità di gestione 
* Facilità di test

L'immagine seguente illustra l'architettura e i sottosistemi principali di Service Fabric.

![](media/service-fabric-architecture/service-fabric-architecture.png)

In un sistema distribuito è fondamentale poter comunicare in modo sicuro tra nodi. Alla base di questi livelli si trova il sottosistema di trasporto, che consente di comunicare in modo sicuro tra i diversi nodi. Al di sopra del sottosistema di trasporto si trova il sottosistema di federazione, che raggruppa i diversi nodi in una singola entità denominata cluster, in modo che il sistema possa rilevare gli errori, eseguire l'algoritmo di elezione e garantire la coerenza del routing. Il sottosistema di affidabilità, posizionato al di sopra del sottosistema di federazione, è responsabile dell'elevata affidabilità dei servizi di Service Fabric mediante meccanismi come la replica, la gestione delle risorse e la gestione dei failover. Al di sopra del sottosistema di federazione si trova anche il sottosistema di hosting, che gestisce il ciclo di vita di un'applicazione in un singolo nodo. Il sottosistema di gestione del cluster gestisce il ciclo di vita di applicazioni e servizi per più computer Il sottosistema di testabilità consente allo sviluppatore di applicazioni di testare i servizi mediante errori simulati prima di distribuire applicazioni e servizi in ambienti di produzione. Service Fabric consente anche di risolvere le posizioni dei servizi mediante il sottosistema di comunicazione. Il modello di programmazione applicativo esposto allo sviluppatore è posizionato al di sopra di questi sottosistemi.

## Sottosistema di trasporto
Il sottosistema di trasporto implementa un canale di comunicazione tramite diagrammi point-to-point. Questo canale viene usato per le comunicazioni all'interno del cluster di Service Fabric e tra il cluster e i client. Supporta il modello di comunicazione Request-Reply unidirezionale, che fornisce la base per l'implementazione di broadcast e multicast nei livelli superiori. Il sottosistema di trasporto protegge le comunicazioni mediante certificati X509 o la sicurezza di Windows. Questo sottosistema viene usato internamente da Service Fabric e non è direttamente accessibile agli sviluppatori per la programmazione di un'applicazione.

## Sottosistema di federazione
Per controllare un set di nodi in un sistema distribuito, è necessaria una vista uniforme del sistema. Il sottosistema di federazione usa le primitive di comunicazione fornite dal sottosistema di trasporto e unisce i diversi nodi in un singolo sistema unificato che è in grado di controllare. Fornisce ai sistemi distribuiti le primitive necessarie per gli altri sottosistemi, ovvero il rilevamento degli errori, l'algoritmo di elezione e la coerenza del routing, Il sottosistema di federazione si basa su tabelle hash distribuite con uno spazio di token a 128 bit e crea una topologia ad anello sui nodi, in cui a ogni nodo dell'anello viene allocato un subset dello spazio di token per la proprietà. Per il rilevamento degli errori, il livello usa un meccanismo di lease basato su heartbeat e arbitraggio. Il sottosistema di federazione garantisce inoltre tramite intricati protocolli di join e partenza che esista un solo proprietario di token per volta. In questo modo è possibile offrire garanzie di algoritmi di elezione e routing coerenti.

## Sottosistema di affidabilità
Il sottosistema di affidabilità offre il meccanismo che consente l'elevata disponibilità dello stato di un servizio di Service Fabric tramite l'uso del _replicatore_, di _Failover Manager_ e di _Resource Balancer_.

* Il replicatore verifica che i cambiamenti di stati nella replica di servizi primaria vengano replicati automaticamente nelle repliche secondarie, mantenendo la coerenza tra le repliche primaria e secondarie in un set di repliche di servizi. Il replicatore è responsabile della gestione del quorum tra le repliche nel set di repliche. Interagisce con l'unità di failover per ottenere l'elenco di operazioni da replicare e l'agente di riconfigurazione (RA) gli fornisce la configurazione del set di repliche, che indica le repliche in cui devono essere replicate le operazioni. Service Fabric fornisce un replicatore predefinito denominato Fabric Replicator, che può essere usato dagli sviluppatori di servizi in modo da garantire l'elevata disponibilità e l'affidabilità dello stato dei servizi.
* Failover Manager garantisce che con l'aggiunta o la rimozione di nodi dal cluster, il carico venga automaticamente ridistribuito tra i nodi disponibili. Se si verifica un errore in un nodo del cluster, il cluster riconfigurerà automaticamente le repliche di servizi in modo che non si verifichi alcuna perdita di disponibilità.
* Resource Balancer posiziona le repliche di servizi nel dominio di errore nel cluster e verifica che tutte le unità di failover siano operative. Resource Balancer applica anche il bilanciamento delle risorse dei servizi nel pool condiviso sottostante di nodi del cluster per garantire una distribuzione del carico uniforme e ottimale.

## Sottosistema di gestione
Il sottosistema di gestione offre una gestione del ciclo di vita dell'applicazione e dei servizi end-to-end. I cmdlet di PowerShell e le API amministrative consentono di effettuare il provisioning, la distribuzione, l'applicazione di patch, l'aggiornamento e il deprovisioning di applicazioni senza perdita di disponibilità. A tale scopo, il sottosistema di gestione usa i servizi seguenti:

* Cluster Manager - Questo è il servizio primario che interagisce con Failover Manager dai componenti di affidabilità per posizionare le applicazioni nei diversi nodi in base ai vincoli di posizionamento dei servizi. Resource Balancer nei componenti di failover garantisce che i vincoli non vengano mai violati. Cluster Manager gestisce il ciclo di vita delle applicazioni dal provisioning al deprovisioning. Si integra con Health Manager, descritto nel passaggio successivo, per garantire che durante gli aggiornamenti la disponibilità delle applicazioni non vada perduta dal punto di vista dell'integrità semantica.
* Health Manager - Questo servizio consente il monitoraggio dell'integrità di applicazioni e servizi e delle entità del cluster. Le entità del cluster (ad esempio nodi, partizioni di servizi e repliche) possono segnalare informazioni sull'integrità, che vengono quindi aggregate nel servizio centralizzato Health Store. Queste informazioni sull'integrità forniscono uno snapshot dell'integrità globale in un determinato momento per i servizi e i nodi distribuiti in più nodi del cluster, consentendo così di intraprendere le azioni correttive necessarie. API di query sull'integrità consentono di eseguire query sugli eventi di integrità segnalati al sottosistema di integrità. Queste API restituiscono dati sull'integrità non elaborati archiviati in Health Store oppure restituiscono i dati sull'integrità interpretati e aggregati per una specifica entità del cluster.
* Image Store - Questo servizio consente l'archiviazione e la distribuzione di file binari delle applicazioni. Offre un semplice archivio di file distribuito in cui vengono caricate o da cui vengono scaricate le applicazioni.


## Sottosistema di hosting
Cluster Manager indica al sottosistema di hosting (in esecuzione in ogni nodo) i servizi che devono essere gestiti per un determinato nodo. Il sottosistema di hosting gestisce quindi il ciclo di vita dell'applicazione in tale nodo. Interagisce con i componenti di affidabilità e integrità per garantire che le repliche siano posizionate correttamente e siano integre.

## Sottosistema di comunicazione
Questo sottosistema offre messaggistica affidabile nel cluster e rilevamento dei servizi mediante il servizio Naming. Il servizio Naming risolve i nomi dei servizi in una posizione nel cluster e consente agli utenti di gestire i nomi e le proprietà dei servizi. Usando il servizio Naming, i client possono comunicare in modo sicuro con i nodi del cluster per risolvere il nome di un servizio e recuperare i metadati di un servizio. Usando una semplice API client Naming, gli utenti di Service Fabric possono sviluppare servizi e client in grado di risolvere il percorso di rete corrente nonostante il dinamismo dei nodi o le dimensioni del cluster.

## Sottosistema di testabilità
Il sottosistema di testabilità è costituito da strumenti progettati specificamente per il test di servizi basati su Service Fabric. Gli strumenti consentono allo sviluppatore di causare, in modo semplice, errori significativi ed eseguire scenari di test per verificare e convalidare i numerosi stati diversi e le transizioni sperimentate da un servizio nel corso della durata, il tutto in modo controllato e sicuro. Questo sottosistema fornisce anche un meccanismo per test a esecuzione prolungata in grado di eseguire l'iterazione di diversi possibili errori senza perdere la disponibilità fornendo agli utenti un ambiente di testing in produzione.
 

<!---HONumber=July15_HO4-->