<properties
   pageTitle="Visualizzazione del cluster con Service Fabric Explorer | Microsoft Azure"
   description="Service Fabric Explorer è uno strumento basato sul Web per analizzare e gestire nodi e applicazioni cloud in un cluster di Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/22/2016"
   ms.author="seanmck"/>  

# Visualizzare il cluster con Service Fabric Explorer

Service Fabric Explorer è uno strumento basato sul web per analizzare e gestire applicazioni e nodi in un cluster di Service Fabric di Azure. Service Fabric Explorer è ospitato direttamente all'interno del cluster, pertanto è sempre disponibile indipendentemente da dove il cluster sia in esecuzione.

## Connettersi a Service Fabric Explorer

Se si sono seguite le istruzioni per [preparare l'ambiente di sviluppo](service-fabric-get-started.md), è possibile avviare Service Fabric Explorer nel cluster locale, passando a http://localhost:19080/Explorer.

>[AZURE.NOTE] Se si utilizza Internet Explorer con Service Fabric Explorer per gestire un cluster remoto, è necessario configurare alcune impostazioni di Internet Explorer. Passare a **Strumenti** > **Impostazioni Visualizzazione Compatibilità** e deselezionare **Visualizza siti Intranet in Visualizzazione Compatibilità**.

## Comprendere il layout di Service Fabric Explorer

È possibile spostarsi all'interno di Service Fabric Explorer seguendo la struttura ad albero a sinistra. Nella radice dell'albero, il dashboard del cluster fornisce una panoramica del cluster, inclusi un riepilogo dell'applicazione e l'integrità del nodo.

![Dashboard del cluster di Service Fabric Explorer][sfx-cluster-dashboard]

### Visualizzare il layout del cluster

I nodi in un cluster di Service Fabric sono posizionati in una griglia bidimensionale di domini di errore e domini di aggiornamento. Questa posizione garantisce la disponibilità delle applicazioni in caso di errori hardware e aggiornamenti dell'applicazione. È possibile visualizzare la disposizione del cluster corrente mediante la mappa del cluster.

![Mappa del cluster di Service Fabric Explorer][sfx-cluster-map]

### Visualizzare applicazioni e servizi

Il cluster contiene due sotto-alberi: uno per le applicazioni e un altro per i nodi.

È possibile usare la visualizzazione delle applicazioni per spostarsi nella gerarchia logica di Service Fabric: applicazioni, servizi, partizioni e repliche.

Nell'esempio seguente, l'applicazione **MyApp** è costituita da due servizi, **MyStatefulService** e **WebService**. Poiché **MyStatefulService** è con stato, include una partizione con una replica primaria e due repliche secondarie. Al contrario, il WebSvcService è senza stato e contiene una singola istanza.

![Visualizzazione delle applicazioni di Service Fabric Explorer][sfx-application-tree]

A ogni livello della struttura ad albero, il riquadro principale mostra informazioni pertinenti all'elemento. Ad esempio, è possibile visualizzare lo stato di integrità e la versione di un determinato servizio.

![Riquadro essentials di Service Fabric Explorer][sfx-service-essentials]

### Visualizzare i nodi del cluster

La visualizzazione dei nodi mostra il layout fisico del cluster. Per un determinato nodo, è possibile esaminare le applicazioni con il codice distribuito in quel nodo. In particolare, è possibile visualizzare le repliche attualmente in esecuzione.

## Azioni

Service Fabric Explorer offre un modo rapido per richiamare le azioni su nodi, applicazioni e servizi all'interno del cluster.

Ad esempio, per eliminare un'istanza dell'applicazione, è sufficiente scegliere l'applicazione dall'albero a sinistra, quindi scegliere **Azioni** > **Elimina applicazione**.

![Eliminazione di un'applicazione in Service Fabric Explorer][sfx-delete-application]  

>[AZURE.TIP] È possibile eseguire le stesse azioni facendo clic sui puntini di sospensione accanto a ogni elemento.

La tabella seguente elenca le azioni disponibili per ogni entità:

| **Entità** | **Azione** | **Descrizione** |
| ------ | ------ | ----------- |
| Tipo di applicazione | Annullare il provisioning del tipo | Rimuove il pacchetto dell'applicazione dall'archivio immagini del cluster. È necessario rimuovere prima tutte le applicazioni di quel tipo. |
| Applicazione | Eliminare l'applicazione | Eliminare l'applicazione, inclusi tutti i servizi correlati e il relativo stato, se presente. |
| Service | Eliminare il servizio | Eliminare il servizio e il relativo stato (se presente). |
| Nodo | Activate | Attivare il nodo. |
|| Disattivare (sospendere) | Sospendere il nodo nello stato corrente. I servizi continueranno a essere eseguiti, tuttavia Service Fabric non sposterà in modo proattivo alcun elemento a meno che non sia necessario per impedire un'interruzione o un caso di incoerenza di dati. Questa azione viene in genere usata per abilitare i servizi di debug in un nodo specifico in modo da garantire che non si spostino durante l'ispezione. |
|| Disattivare (riavviare) | Spostare tutti i servizi in memoria all'esterno di un nodo e chiudere i servizi permanenti in modo sicuro. Questa azione viene in genere usata quando i processi host o i computer devono essere riavviati. |
|| Disattivare (rimuovere i dati) | Chiudere in modo sicuro tutti i servizi in esecuzione sul nodo dopo la creazione di un numero sufficiente di repliche riserva. Questa azione viene in genere usata quando un nodo (o almeno lo spazio di archiviazione correlato) viene reso improduttivo in modo permanente. |
|| Rimuovere lo stato del nodo | Rimuovere le repliche di un nodo dal cluster. Questa azione viene in genere usata quando un nodo che ha già avuto esito negativo viene ritenuto non recuperabile. |

Poiché molte azioni sono distruttive, viene richiesto di confermare la finalità prima del completamento dell'azione.

>[AZURE.TIP] Ogni azione eseguibile con Service Fabric Explorer può essere eseguita anche tramite PowerShell o un'API REST per abilitare l'automazione.

È inoltre possibile usare Service Fabric Explorer per creare nuove istanze per un determinato tipo e versione di applicazione. Scegliere il tipo di applicazione nella visualizzazione albero, quindi fare clic sul collegamento **Create app instance** (Crea un'istanza dell'app).

![Creazione di un'istanza dell'applicazione in Service Fabric Explorer][sfx-create-app-instance]  

>[AZURE.NOTE] Non è attualmente possibile impostare parametri per le istanze dell'applicazione create mediante Service Fabric Explorer, per le quali vengono usati valori di parametro predefiniti.

## Connettersi a un cluster di Service Fabric remoto

Poiché Service Fabric Explorer è basato sul web e viene eseguito all'interno del cluster, è accessibile da qualsiasi browser, purché si conosca l'endpoint del cluster e si disponga di autorizzazioni sufficienti per accedervi.

### Scoprire l'endpoint di Service Fabric Explorer per un cluster remoto

Per raggiungere Service Fabric Explorer per un determinato cluster, è sufficiente inserire nel browser l'indirizzo seguente:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer  

L'URL completo è disponibile anche nel riquadro essentials del cluster del portale di Azure.

### Connettersi a un cluster sicuro

È possibile controllare l'accesso al cluster di Service Fabric con certificati oppure usando Azure Active Directory (AAD).

Se si prova a connettersi a Service Fabric Explorer in un cluster sicuro, a seconda del della configurazione del cluster, sarà necessario presentare un certificato client oppure eseguire l'accesso con AAD.

## Passaggi successivi

- [Panoramica di Testabilità](service-fabric-testability-overview.md)
- [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Distribuzione di un'applicazione di Infrastruttura di servizi mediante PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->  
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png

<!---HONumber=AcomDC_0824_2016-->