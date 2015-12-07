<properties
   pageTitle="Visualizzazione del cluster con Service Fabric Explorer | Microsoft Azure"
   description="Service Fabric Explorer è uno strumento basato su interfaccia grafica per analizzare e gestire applicazioni cloud e nodi in un cluster di Infrastruttura di servizi Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# Visualizzazione del cluster con Service Fabric Explorer

Service Fabric Explorer è uno strumento basato sul web per analizzare e gestire applicazioni e nodi in un cluster di infrastruttura di servizi. Service Fabric Explorer è ospitato direttamente all'interno del cluster, pertanto è sempre disponibile, indipendentemente da dove il cluster è in esecuzione.

## Connessione a Service Fabric Explorer

Se si sono seguite le istruzioni per [preparare l'ambiente di sviluppo](service-fabric-get-started.md), è possibile avviare Service Fabric Explorer nel cluster locale, passando a http://localhost:19080/Explorer.

>[AZURE.NOTE]Se si utilizza Internet Explorer(IE) con Service Fabric Explorer per gestire un cluster remoto, è necessario configurare alcune impostazioni di Internet Explorer. Passare a **Strumenti -> Impostazioni visualizzazione compatibilità** e deselezionare **visualizzare siti intranet in visualizzazione compatibilità** affinché tutte le informazioni vengano caricate correttamente.

## Introduzione al layout di Service Fabric Explorer

È possibile spostarsi all’interno di Service Fabric Explorer utilizzando la struttura ad albero a sinistra. Nella radice dell'albero, il dashboard del cluster fornisce una panoramica del cluster, inclusi un riepilogo dell'applicazione e l'integrità del nodo.

![Dashboard del cluster di Service Fabric Explorer][sfx-cluster-dashboard]

### La mappa del cluster

I nodi del cluster di infrastruttura di servizi sono posizionati in una griglia a 2 dimensioni di domini di errore e domini di aggiornamento per garantire che le applicazioni siano disponibili anche in presenza di errori hardware e di aggiornamenti dell'applicazione. È possibile visualizzare la disposizione del cluster corrente utilizzando la mappa del Cluster.

![Mappa del cluster di Service Fabric Explorer][sfx-cluster-map]

### Visualizzazione applicazioni e servizi

Il cluster contiene due sotto-alberi: uno per le applicazioni e un altro per i nodi.

La visualizzazione delle applicazioni consente di spostarsi tra la gerarchia logica di Infrastruttura di servizi: applicazioni, servizi, partizioni e repliche.

Nell'esempio seguente, l'applicazione **MyApp** è costituita da due servizi, **MyStatefulService** e **WebService**. Poiché **MyStatefulService** è con stato, include una partizione con una replica primaria e due repliche secondarie. Al contrario, il WebSvcService è senza stato e contiene una singola istanza.

![Visualizzazione delle applicazioni di Service Fabric Explorer][sfx-application-tree]

A ogni livello della struttura ad albero, il riquadro principale mostra informazioni pertinenti all'elemento. Ad esempio, è possibile visualizzare lo stato di integrità e la versione di un determinato servizio.

![Riquadro essentials di Service Fabric Explorer][sfx-service-essentials]

### Visualizzazione dei nodi del cluster

La visualizzazione dei nodi mostra il layout fisico del cluster. Per un determinato nodo, è possibile controllare quali applicazioni hanno il codice distribuito in tale nodo e più nello specifico, quali le repliche vi sono attualmente in esecuzione.

## Esecuzione di azioni con Service Fabric Explorer

Service Fabric Explorer offre un modo rapido per richiamare le azioni su nodi, applicazioni e servizi all'interno del cluster.

Ad esempio, per eliminare un'istanza dell'applicazione, è sufficiente scegliere l'applicazione dall'albero a sinistra, quindi scegliere Azioni > Elimina applicazione.

![Eliminazione di un'applicazione in Service Fabric Explorer][sfx-delete-application]

Poiché molte azioni sono distruttive, verrà richiesto di confermare la finalità prima del completamento dell'azione.

>[AZURE.NOTE]Ogni azione che può essere eseguita con Service Fabric Explorer può essere eseguita anche tramite PowerShell o un'API REST, consentendo l’automazione.



## Connessione a un cluster di Infrastruttura di servizi remoto

Poiché Service Fabric Explorer è basato sul web e viene eseguito all'interno del cluster, è accessibile da qualsiasi browser, purché si conosca l'endpoint del cluster e si disponga di autorizzazioni sufficienti per accedervi.

### Individuare l'endpoint di Service Fabric Explorer per un Cluster remoto

Per raggiungere Service Fabric Explorer per un determinato cluster, è sufficiente inserire nel browser l'indirizzo seguente:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

L'URL completo è disponibile anche nel riquadro essentials del cluster del portale di Azure.

### Connessione a un cluster sicuro

È possibile controllare l'accesso al cluster di Infrastruttura di servizi richiedendo che i client presentino un certificato per connettersi ad esso.

Se si tenta di connettersi a Service Fabric Explorer in un cluster sicuro, il browser richiederà di presentare un certificato per ottenere l'accesso.

## Passaggi successivi

- [Panoramica di Testabilità](service-fabric-testability-overview.md).
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Distribuzione di un'applicazione di Infrastruttura di servizi mediante PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=AcomDC_1125_2015-->