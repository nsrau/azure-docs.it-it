---
title: Distribuire un'applicazione .NET in un contenitore in Azure Service Fabric | Microsoft Docs
description: Viene illustrato come creare un pacchetto di un'applicazione .NET in Visual Studio in un contenitore Docker. La nuova app "contenitore" viene quindi distribuita in un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.openlocfilehash: cd1c3b063132ae549bfbf1e059667c5056c91046
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Distribuire un'applicazione .NET in un contenitore Windows in Azure Service Fabric

Questa esercitazione illustra come distribuire un'applicazione ASP.NET esistente in un contenitore Windows in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto Docker in Visual Studio
> * Distribuire un'applicazione esistente in un contenitore
> * Configurare l'integrazione continua con Visual Studio e VSTS

## <a name="prerequisites"></a>prerequisiti

1. Installare [Docker CE per Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) in modo da poter eseguire i contenitori in Windows 10.
2. Acquisire familiarità con le informazioni riportate nella [Guida introduttiva ai contenitori di Windows 10][link-container-quickstart].
3. Scaricare l'applicazione di esempio [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Installare [Azure PowerShell][link-azure-powershell-install]
5. Installare l'[estensione Strumenti di recapito continuo per Visual Studio 2017][link-visualstudio-cd-extension]
6. Creare una [sottoscrizione di Azure][link-azure-subscription] e un [account di Visual Studio Team Services][link-vsts-account]. 
7. [Creare un cluster in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Creare un cluster in Azure
Le applicazioni Service Fabric sono eseguite in un cluster, un set di macchine virtuali o fisiche connesse mediante una rete. [Installare un cluster di Service Fabric in esecuzione in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) prima di creare e distribuire l'applicazione. Quando si crea il cluster, scegliere uno SKU che supporta i contenitori in esecuzione (ad esempio Windows Server 2016 Datacenter con contenitori).

## <a name="containerize-the-application"></a>Distribuire l'applicazione in un contenitore

Un cluster di Service Fabric è ora in esecuzione in Azure, perciò è possibile creare e distribuire un'applicazione in un contenitore. Per avviare l'esecuzione dell'applicazione in un contenitore, è necessario aggiungere il **supporto per Docker** al progetto in Visual Studio. Quando si aggiunge il **supporto per Docker** all'applicazione, si verifica quanto segue. Per prima cosa, un _Dockerfile_ viene aggiunto al progetto. Questo nuovo file descrive il modo in cui l'immagine del contenitore deve essere compilata. In secondo luogo, alla soluzione viene aggiunto un nuovo progetto _docker-compose_. Il nuovo progetto contiene alcuni file docker-compose. I file docker-compose possono essere usati per descrivere le modalità di esecuzione del contenitore.

Per altre informazioni, vedere [Visual Studio Container Tools][link-visualstudio-container-tools].

>[!NOTE]
>Se questa è la prima volta che si eseguono immagini del contenitori Windows sul computer in uso, è necessario che Docker CE ottenga le immagini di base per i contenitori. Le immagini usate in questa esercitazione sono pari a 14 GB. Procedere ed eseguire il comando seguente per ottenere le immagini di base:
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Aggiungere il supporto di Docker

Aprire il file [FabrikamFiber.CallCenter.sln][link-fabrikam-github] in Visual Studio.

Fare clic con il pulsante destro del mouse sul progetto **FabrikamFiber.Web** > **Aggiungi** > **Supporto Docker**.

### <a name="add-support-for-sql"></a>Aggiungere il supporto per SQL

Questa applicazione usa SQL come provider di dati, motivo per cui per eseguire l'applicazione è necessaria un'istanza di SQL Server. Fare riferimento a un'immagine del contenitore SQL Server nel file docker-compose.override.yml.

In Visual Studio aprire **Esplora soluzioni**, trovare **docker-compose** e aprire il file **docker-compose.override.yml**.

Passare al nodo `services:` e aggiungere un nodo chiamato `db:` che definisce la voce di SQL Server per il contenitore.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Per il debug locale è possibile usare un'istanza di SQL Server a scelta, a condizione che sia raggiungibile dall'host. **localdb**, tuttavia, non supporta la comunicazione `container -> host`.

>[!WARNING]
>L'esecuzione di SQL Server in un contenitore non consente di rendere persistenti i dati. Quando si arresta il contenitore, i dati vengono cancellati. Non usare questa configurazione per la produzione.

Passare al nodo `fabrikamfiber.web:` e aggiungere un nodo figlio di nome `depends_on:`. Il questo modo si assicura che il servizio `db` (il contenitore SQL Server) venga avviato prima dell'applicazione Web (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Aggiornare il file di configurazione Web

Tornare al progetto **FabrikamFiber.Web** e aggiornare la stringa di connessione nel file **web.config** in modo che punti all'istanza di SQL Server nel contenitore.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Se si desidera usare un'istanza di SQL Server diversa durante la creazione di una compilazione di rilascio dell'applicazione Web, aggiungere un'altra stringa di connessione al file web.release.config.

### <a name="test-your-container"></a>Testare il contenitore

Premere **F5** per eseguire l'applicazione ed eseguirne il debug nel contenitore.

Edge apre la pagina di avvio definita dell'applicazione usando l'indirizzo IP del contenitore nella rete NAT interna (in genere 172.x.x.x). Per altre informazioni sul debug delle applicazioni nei contenitori mediante Visual Studio 2017, vedere [questo articolo][link-debug-container].

![esempio di fabrikam in un contenitore][image-web-preview]

Il contenitore è ora pronto per la compilazione e l'inserimento come pacchetto in un'applicazione di Service Fabric. Dopo aver creato l'immagine del contenitore sul computer, è possibile eseguirne il push in qualsiasi registro contenitori e quindi estrarla in qualsiasi host per l'esecuzione.

## <a name="get-the-application-ready-for-the-cloud"></a>Preparazione l'applicazione per il cloud

Per preparare l'applicazione per l'esecuzione in Service Fabric di Azure, è necessario completare due passaggi:

1. Esporre la porta in cui si vuole poter raggiungere l'applicazione Web nel cluster di Service Fabric.
2. Predisporre un database SQL di produzione pronto per l'applicazione.

### <a name="expose-the-port-for-the-app"></a>Esporre la porta per l'app
Il cluster di Service Fabric configurato presenta la porta *80* aperta per impostazione predefinita in Azure Load Balancer, che consente di bilanciare il traffico in ingresso nel cluster. È possibile esporre il contenitore su questa porta tramite il file docker-compose.ym.

In Visual Studio aprire **Esplora soluzioni**, trovare **docker-compose** e aprire il file **docker-compose.yml**.

Modificare il nodo `fabrikamfiber.web:` e aggiungere un nodo figlio denominato `ports:`.

Aggiungere una voce di stringa `- "80:80"`. Il file docker-compose.yml dovrebbe essere simile al seguente:

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Usare un database SQL di produzione
Durante l'esecuzione nell'ambiente di produzione, è necessario mantenere i dati persistenti nel database. Al momento non c'è modo di garantire la persistenza dei dati in un contenitore, pertanto non è possibile archiviare i dati di produzione in SQL Server in un contenitore.

È consigliabile usare un database SQL di Azure. Per configurare ed eseguire un SQL Server gestito in Azure, consultare l'articolo [Documentazione sul database SQL di Azure][link-azure-sql].

>[!NOTE]
>Ricordarsi di modificare le stringhe di connessione a SQL Server nel file **web.release.config**all'interno del progetto **FabrikamFiber.Web**.
>
>Questa applicazione non funziona correttamente se i database SQL non sono raggiungibili. È possibile proseguire e distribuire l'applicazione senza un server SQL.

## <a name="deploy-with-visual-studio-team-services"></a>Distribuire con Visual Studio Team Services

Per configurare la distribuzione con Visual Studio Team Services, è necessario installare l'[estensione Strumenti di recapito continuo per Visual Studio 2017][link-visualstudio-cd-extension]. Questa estensione facilita la distribuzione in Azure mediante la configurazione di Visual Studio Team Services e consente di distribuire l'app nel cluster di Service Fabric.

Per iniziare, il codice deve essere ospitato nel controllo del codice sorgente. Nella restante parte della sezione si presuppone l'uso di **git**.

### <a name="set-up-a-vsts-repo"></a>Configurare un repository VSTS
Nell'angolo inferiore destro di Visual Studio fare clic su **Aggiungi al controllo del codice sorgente** > **Git** (o su qualsiasi opzione desiderata).

![fare clic sul pulsante del controllo del codice sorgente][image-source-control]

Nel riquadro _Team Explorer_ premere **Pubblica repository GIT**.

Selezionare il nome dell'archivio VSTS e premere **Repository**.

![pubblicazione dell'archivio in VSTS][image-publish-repo]

Ora che il codice è sincronizzato con l'archivio del codice sorgente VSTS, è possibile configurare la distribuzione continua e il recapito continuo.

### <a name="setup-continuous-delivery"></a>Configurare il recapito continuo

In _Esplora soluzioni_ fare clic con il pulsante destro del mouse sulla **soluzione** > **Configura recapito continuo**.

Selezionare la sottoscrizione di Azure.

Impostare **Tipo host** su **Cluster di Service Fabric**.

Impostare **Target Host** (Host di destinazione) sul cluster di Service Fabric creato nella sezione precedente.

Scegliere un **Registro contenitori** in cui pubblicare il contenitore.

>[!TIP]
>Usare il pulsante **Modifica** per creare un registro contenitori.

Premere **OK**.

![impostazione dell'integrazione continua di Service Fabric][image-setup-ci]
   
   Dopo aver completato la configurazione, il contenitore viene distribuito in Service Fabric. Ogni volta che si esegue il push degli aggiornamenti nel repository viene eseguita una nuova compilazione e una nuova versione.
   
   >[!NOTE]
   >La compilazione delle immagini di contenitore richiede circa 15 minuti.
   >La prima distribuzione nel cluster di Service Fabric comporta il download delle immagini del contenitore di base di Windows Server Core. Il completamento del download richiede altri 5-10 minuti.

Passare all'applicazione Fabrikam Call Center usando l'URL del cluster: ad esempio *http://mycluster.westeurope.cloudapp.azure.com*

Dopo aver inserito la soluzione Fabrikam Call in un pacchetto e averla distribuita, è possibile aprire il [portale di Azure ][link-azure-portal] e visualizzare l'applicazione in esecuzione in Service Fabric. Per verificare l'applicazione, aprire un Web browser e passare all'URL del cluster di Service Fabric.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un progetto Docker in Visual Studio
> * Distribuire un'applicazione esistente in un contenitore
> * Configurare l'integrazione continua con Visual Studio e VSTS

La parte successiva dell'esercitazione comprende informazioni su come configurare il [monitoraggio del contenitore](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
