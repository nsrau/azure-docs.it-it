---
title: Distribuire un'applicazione di Azure Service Fabric in un cluster | Microsoft Docs
description: Informazioni su come distribuire un'applicazione in un cluster da Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 4716cacf840dcf7a372923e29f758dbdc82fbf51
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208880"
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Esercitazione: Distribuire un'applicazione in un cluster di Service Fabric in Azure
Questa esercitazione è la seconda parte di una serie e illustra come distribuire un'applicazione di Azure Service Fabric in un nuovo cluster in Azure direttamente da Visual Studio.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster da Visual Studio
> * Distribuire un'applicazione in un cluster remoto usando Visual Studio


In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md)
> * Distribuire l'applicazione in un cluster remoto
> * [Aggiungere un endpoint HTTPS a un servizio front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurare l'integrazione continua e la distribuzione continua usando Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
- [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio
Se non si è creata l'applicazione di voto di esempio nella [prima parte di questa serie di esercitazioni](service-fabric-tutorial-create-dotnet-app.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric
Ora che l'applicazione è pronta, è possibile distribuirla in un cluster direttamente da Visual Studio. Un [cluster di Service Fabric](/service-fabric/service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi.

Per la distribuzione all'interno di Visual Studio sono disponibili due opzioni:
- Creare un cluster in Azure da Visual Studio. Questa opzione consente di creare un cluster sicuro direttamente da Visual Studio con le configurazioni preferite. Questo tipo di cluster è la soluzione ideale per scenari di test, in cui è possibile creare il cluster e quindi eseguirvi direttamente la pubblicazione all'interno di Visual Studio.
- Eseguire la pubblicazione in un cluster esistente nella sottoscrizione.  È possibile creare cluster di Service Fabric tramite il [portale di Azure](https://portal.azure.com), usando [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) oppure script dell'[interfaccia della riga di comando di Azure](./scripts/cli-create-cluster.md) o da un [modello di Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Questa esercitazione consente di creare un cluster da Visual Studio. Se è già disponibile un cluster distribuito, è possibile copiare e incollare l'endpoint di connessione o sceglierlo dalla sottoscrizione.
> [!NOTE]
> Molti servizi usano il proxy inverso per comunicare tra loro. Nei cluster creati da Visual Studio e nei party cluster, il proxy inverso è abilitato per impostazione predefinita.  Se si usa un cluster esistente, è necessario [abilitare il proxy inverso nel cluster](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="find-the-votingweb-service-endpoint"></a>Trovare l'endpoint del servizio VotingWeb
Prima di tutto, trovare l'endpoint del servizio Web front-end.  Il servizio Web front-end è in ascolto su una porta specifica.  Quando l'applicazione viene distribuita in un cluster in Azure, sia il cluster che l'applicazione vengono eseguiti dietro un servizio di bilanciamento del carico di Azure.  La porta dell'applicazione deve essere aperta nel servizio di bilanciamento del carico di Azure in modo che il traffico in entrata possa raggiungere il servizio Web.  La porta (ad esempio, 8080) è indicata nel file *VotingWeb/PackageRoot/ServiceManifest.xml* nell'elemento **Endpoint**:

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Nel passaggio successivo specificare questa porta nella scheda **Avanzate** della finestra di dialogo **Crea cluster**.  Se si distribuisce l'applicazione in un cluster esistente, è possibile aprire questa porta nel servizio di bilanciamento carico di Azure usando uno [script di PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) o il [portale di Azure](https://portal.azure.com).

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Creare un cluster in Azure tramite Visual Studio
Fare clic con il pulsante destro del mouse sul progetto di applicazione in Esplora soluzioni e scegliere **Pubblica**.

Accedere con l'account Azure per poter avere accesso alle sottoscrizioni. Se si usa un party cluster, questo passaggio è facoltativo.

Selezionare l'elenco a discesa per **Endpoint connessione** e quindi l'opzione **<Create New Cluster...>**.
    
![Finestra di dialogo Pubblica](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
Nella finestra di dialogo **Crea cluster** modificare le impostazioni seguenti:

1. Specificare il nome del cluster nel campo **Nome del cluster**, nonché la sottoscrizione e la località da usare.
2. Facoltativamente, è possibile modificare il numero di nodi. Per impostazione predefinita vengono usati tre nodi, il numero minimo necessario per testare gli scenari di Service Fabric.
3. Selezionare la scheda **Certificato**. In questa scheda digitare una password che verrà usata per proteggere il certificato del cluster. Questo certificato consente di rendere sicuro il cluster. È anche possibile modificare il percorso in cui si vuole salvare il certificato. Visual Studio può anche importare automaticamente il certificato, perché questo passaggio è obbligatorio per la pubblicazione dell'applicazione nel cluster.
4. Selezionare la scheda **Dettagli macchina virtuale**. Specificare la password che si vuole usare per le macchine virtuali (VM) che costituiscono il cluster. Il nome utente e la password possono essere usati per la connessione remota alle VM. Si deve anche selezionare una dimensione di macchina virtuale ed è possibile modificare l'immagine di VM, se necessario.
5. Nella scheda **Avanzate** è possibile modificare l'elenco delle porte da aprire nel servizio di bilanciamento del carico creato con il cluster.  Aggiungere l'endpoint del servizio VotingWeb individuato in un passaggio precedente. È anche possibile aggiungere una chiave di Application Insights esistente a cui indirizzare i file di log dell'applicazione.
6. Al termine della modifica delle impostazioni, selezionare il pulsante **Crea**. La creazione del cluster richiede alcuni minuti. Il completamento dell'operazione verrà indicato nella finestra di output.

![Finestra di dialogo per la creazione del cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>Distribuire l'applicazione di esempio
Quando il cluster che si vuole usare è pronto, fare clic con il pulsante destro del mouse sul progetto di applicazione e scegliere **Pubblica**.

Al termine della pubblicazione, dovrebbe essere possibile inviare una richiesta all'applicazione tramite un browser.

Aprire il browser preferito, digitare l'indirizzo del cluster (l'endpoint di connessione senza le informazioni sulla porta, ad esempio win1kw5649s.westus.cloudapp.azure.com).

Deve apparire lo stesso risultato visualizzato quando si esegue l'applicazione in locale.

![Risposta API dal cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un cluster da Visual Studio
> * Distribuire un'applicazione in un cluster remoto usando Visual Studio

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Abilitare HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
