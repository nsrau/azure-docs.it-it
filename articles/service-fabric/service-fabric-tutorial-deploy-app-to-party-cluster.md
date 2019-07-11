---
title: Distribuire un'app Service Fabric in un cluster in Azure | Microsoft Docs
description: Informazioni su come distribuire un'applicazione in un cluster da Visual Studio.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/14/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: c08633a45030e63366a1c17f512fa81e6556085e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620230"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Esercitazione: Distribuire un'applicazione Service Fabric in un cluster in Azure

Questa è la seconda di una serie di esercitazioni. Illustra come distribuire un'applicazione Azure Service Fabric in un nuovo cluster in Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster.
> * Distribuire un'applicazione in un cluster remoto con Visual Studio.

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md).
> * Distribuire l'applicazione in un cluster remoto.
> * [Aggiungere un endpoint HTTPS a un servizio front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Configurare l'integrazione continua e la distribuzione continua con Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Installare Visual Studio 2019](https://www.visualstudio.com/) e i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
* [Installare Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio

Se non si è creata l'applicazione di voto di esempio nella [prima parte di questa serie di esercitazioni](service-fabric-tutorial-create-dotnet-app.md), è possibile scaricarla. In una finestra di comando eseguire il codice seguente per clonare il repository dell'applicazione di esempio nel computer locale.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Aprire l'applicazione in Visual Studio, eseguirla come amministratore e compilare l'applicazione.

## <a name="create-a-cluster"></a>Creare un cluster

Ora che l'applicazione è pronta, creare un cluster di Service Fabric e quindi distribuire l'applicazione al cluster. Un [cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi.

In questa esercitazione si crea un nuovo cluster di test a tre nodi nell'IDE di Visual Studio e quindi pubblicare l'applicazione a tale cluster. Per informazioni sulla creazione un cluster di produzione, vedere l'esercitazione [Creare e gestire un cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md). È anche possibile distribuire l'applicazione in un cluster esistente precedentemente creato dal [portale di Azure](https://portal.azure.com), usando [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) oppure gli script dell'[interfaccia della riga di comando di Azure](./scripts/cli-create-cluster.md) o da un [modello di Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> L'applicazione di voto, così come molte altre applicazioni, usa il proxy inverso di Service Fabric per comunicare tra i servizi. Nei cluster creati da Visual Studio, il proxy inverso è abilitato per impostazione predefinita. Se si distribuisce in un cluster esistente, per il corretto funzionamento dell'applicazione di voto è necessario [abilitare il proxy inverso nel cluster](service-fabric-reverseproxy-setup.md).


### <a name="find-the-votingweb-service-endpoint"></a>Trovare l'endpoint del servizio VotingWeb

Il servizio web front-end dell'applicazione di voto è in ascolto su una porta specifica (8080 se è stata eseguita la procedura nella [parte 1 di questa serie di esercitazioni](service-fabric-tutorial-create-dotnet-app.md)). Quando l'applicazione viene distribuita in un cluster in Azure, sia il cluster che l'applicazione vengono eseguiti dietro un servizio di bilanciamento del carico di Azure. Per aprire la porta dell'applicazione è necessaria una regola del servizio di bilanciamento del carico di Azure. La regola invia il traffico in ingresso al servizio Web attraverso il bilanciamento del carico. La porta è indicata nel file **VotingWeb/PackageRoot/ServiceManifest.xml** nell'elemento **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Prendere nota dell'endpoint di servizio, che sarà necessario in un passaggio successivo.  Se si esegue la distribuzione in un cluster esistente, aprire questa porta creando una regola di bilanciamento del carico e un probe nel servizio di bilanciamento del carico di Azure con uno [script di PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) oppure tramite il servizio di bilanciamento del carico per il cluster nel [portale di Azure](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Creare un cluster di test in Azure
In Esplora soluzioni fare clic con il pulsante destro del mouse su **Voting** e scegliere **Pubblica**.

In **Endpoint connessione** selezionare **Crea un nuovo cluster**.  Se si esegue la distribuzione in un cluster esistente, selezionare l'endpoint del cluster nell'elenco.  Viene visualizzata la finestra di dialogo Crea cluster Service Fabric.

Nella scheda **Cluster** immettere il **Nome cluster** (ad esempio, "mytestcluster"), selezionare la propria sottoscrizione, selezionare un'area per il cluster (ad esempio, Stati Uniti centro-meridionali), immettere il numero di nodi del cluster (si consigliano tre nodi per un cluster di test) e infine immettere un gruppo di risorse (ad esempio, "mytestclustergroup"). Fare clic su **Avanti**.

![Creare un cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Nella scheda **Certificato** immettere la password e il percorso di output del certificato del cluster. Viene creato un certificato autofirmato come file PFX, che viene salvato nel percorso di output specificato.  Il certificato viene usato per la sicurezza da nodo a nodo e da client a nodo.  Non usare un certificato autofirmato per i cluster di produzione.  Questo certificato viene usato da Visual Studio per eseguire l'autenticazione con il cluster e distribuire applicazioni. Selezionare **Importa certificato** per installare il certificato PFX nell'archivio certificati dell'utente corrente.  Fare clic su **Avanti**.

![Creare un cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Nella scheda **Dettagli macchina virtuale** immettere il **Nome utente** e la **Password** per l'account amministratore del cluster.  Selezionare l'**Immagine di macchina virtuale** per i nodi del cluster e le **Dimensioni delle macchine virtuali** per ogni nodo del cluster.  Fare clic sulla scheda **Avanzate**.

![Creare un cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

In **Porte** immettere l'endpoint del servizio VotingWeb del passaggio precedente (ad esempio, 8080).  Alla creazione del cluster, queste porte applicazione vengono aperte nel servizio di bilanciamento del carico di Azure per inoltrare il traffico al cluster.  Fare clic su **Crea** per creare il cluster in alcuni minuti.

![Creare un cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Pubblicare l'applicazione nel cluster

Quando il nuovo cluster è pronto, è possibile distribuire l'applicazione di voto direttamente da Visual Studio.

In Esplora soluzioni fare clic con il pulsante destro del mouse su **Voting** e scegliere **Pubblica**. Viene visualizzata la finestra di dialogo **Pubblica**.

In **Endpoint connessione** selezionare l'endpoint del cluster creato al passaggio precedente.  Ad esempio, "mytestcluster.southcentral.cloudapp.azure.com:19000". Se si seleziona **Parametri di connessione avanzati**, le informazioni del certificato verranno compilate automaticamente.  
![Pubblicare un'applicazione di Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Selezionare **Pubblica**.

Al termine della distribuzione dell'applicazione, aprire un browser e immettere l'indirizzo del cluster seguito da **:8080**. In alternativa immettere un'altra porta, se ne è stata configurata una. Un esempio è `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Viene visualizzata l'applicazione in esecuzione nel cluster in Azure. Nella pagina Web dell'applicazione di voto provare ad aggiungere ed eliminare opzioni di voto e a votare una o più di queste opzioni.

![Esempio di voto di Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Passaggi successivi
In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un cluster.
> * Distribuire un'applicazione in un cluster remoto con Visual Studio.

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Abilitare HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
