<properties
   pageTitle="Visualizzazione del cluster con Service Fabric Explorer"
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
   ms.date="05/20/2015"
   ms.author="jesseb"/>

# Visualizzazione del cluster con Service Fabric Explorer

Service Fabric Explorer è uno strumento grafico per analizzare e gestire applicazioni cloud e nodi in un cluster di infrastruttura dei servizi Microsoft Azure. Service Fabric Explorer può connettersi ai cluster di sviluppo locale e ai cluster Azure. Per informazioni sui cmdlet PowerShell di Infrastruttura di servizi, vedere i **Passaggi successivi**.

> [AZURE.NOTE]Creazione dei cluster di Infrastruttura di servizi in Azure non è ancora disponibile.

## Introduzione a Service Fabric Explorer

Verificare l'ambiente di sviluppo locale sia impostato in base alle istruzioni indicate in [Configurare l'ambiente di sviluppo di Infrastruttura di servizi](service-fabric-get-started.md).

Eseguire Service Fabric Explorer dal percorso di installazione locale (%Programmi%\\Microsoft SDKs\\Service Fabric\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe). Lo strumento si connetterà automaticamente a un cluster di sviluppo locale, se presente. Vengono visualizzate informazioni sul cluster, quali:

- Applicazioni in esecuzione nel cluster
- Informazioni sui nodi del cluster
- Eventi di stato da applicazioni e nodi
- Carico sulle applicazioni del cluster
- Monitoraggio di stato di aggiornamento dell'applicazione

![Rappresentazione visiva del cluster Infrastruttura di servizi e applicazioni distribuite][servicefabricexplorer]

Una delle visualizzazioni importante è la mappa del cluster, visibile nel dashboard per il cluster (ad esempio, facendo clic su **Onebox/Local cluster**). Nella mappa del cluster è elencato il set di domini di aggiornamento e domini di errore e i nodi mappati ai relativi domini. Per acquisire familiarità con i concetti relativi all'Infrastruttura di servizi, vedere la relativa [Panoramica tecnica](service-fabric-technical-overview.md).

![Nella mappa del cluster vengono indicati i domini di aggiornamento e di errore a cui appartiene ogni nodo.][clustermap]


## Visualizzazione applicazioni e servizi

Service Fabric Explorer consente di esplorare le applicazioni in esecuzione nel cluster. Espandere la **visualizzazione dell'applicazione** per visualizzare informazioni dettagliate sulle applicazioni, sui servizi, sulle partizioni e sulle repliche.

Nel diagramma seguente viene illustrato che l'applicazione denominata **"fabric:/Stateful1Application"** contiene un servizio senza stato denominato **"fabric:/Stateful1Application/MyFrontEnd"** e un servizio con stato denominato **"fabric:/Stateful1Application/Stateful1"**. Il servizio senza stato dispone di una partizione con una replica in esecuzione nel **Nodo 4**. Il servizio con stato presenta due partizioni, ognuna con 3 repliche, in esecuzione in diversi nodi differenti.

![Visualizzazione delle applicazioni in esecuzione nel cluster Infrastruttura di servizi][applicationview]

Facendo clic su un'applicazione, un servizio, una partizione o una replica vengono fornite informazioni dettagliate su tale entità. Nel diagramma seguente viene illustrato il dashboard di integrità della replica del servizio per una delle repliche primarie del servizio con stato. Ciò include il relativo ruolo, il nodo su cui è in esecuzione, l'indirizzo su cui è in ascolto, il percorso dei file su disco e gli eventi di stato.

![Informazioni dettagliate su una replica di Infrastruttura di servizi][replicadetails]


## Connessione a un cluster di Infrastruttura di servizi remoto

Per visualizzare un cluster di Infrastruttura di servizi remoto, fare clic su **Connect** per visualizzare la finestra di dialogo relativa alla **connessione al cluster di Infrastruttura di servizi**. Immettere l'**endpoint dell'Infrastruttura di servizi** relativo al cluster, quindi fare clic su **Connect**. L'endpoint dell'Infrastruttura di servizi in genere è il nome pubblico del servizio cluster in ascolto sulla porta 19000.

![Configurazione di una connessione al cluster Infrastruttura di servizi remoti][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

- [Panoramica di Testabilità](service-fabric-testability-overview.md).
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Distribuzione di un'applicazione di Infrastruttura di servizi mediante PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png
 

<!---HONumber=July15_HO2-->