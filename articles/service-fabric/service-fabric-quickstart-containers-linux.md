---
title: Creare un'applicazione contenitore di Azure Service Fabric in Linux | Microsoft Docs
description: Creare la prima applicazione contenitore Linux in Azure Service Fabric.  Compilare un'immagine Docker con l'applicazione, eseguire il push dell'immagine in un registro contenitori e compilare e distribuire un'applicazione contenitore di Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: d07d5d59632791a52bcb3a2f54bebe194cc76a54
ms.openlocfilehash: 44eaaae123490934bc62b4ea30968656900d48fc
ms.contentlocale: it-it
ms.lasthandoff: 10/04/2017

---

# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Distribuire un'applicazione contenitore Linux di Azure Service Fabric in Azure
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili. 

Questa guida introduttiva illustra come distribuire contenitori Linux in un cluster di Service Fabric. Al termine, si avrà un'applicazione di voto costituita da un front-end Web Python e un back-end Redis in esecuzione in un cluster di Service Fabric. 

![quickstartpic][quickstartpic]

In questa guida introduttiva si apprende come:
> [!div class="checklist"]
> * Distribuire contenitori Linux in Service Fabric
> * Eseguire il ridimensionamento e il failover di contenitori in Service Fabric

## <a name="prerequisite"></a>Prerequisito
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/en-us/free/) prima di iniziare.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, assicurarsi di eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Per trovare la versione, eseguire az --version. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Ottenere il pacchetto dell'applicazione
Per distribuire contenitori in Service Fabric, è necessario un set di file manifesto che descrivono i singoli contenitori e l'applicazione (definizione di applicazione).

In Cloud Shell usare git per clonare una copia della definizione di applicazione.

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

cd service-fabric-dotnet-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>Distribuire i contenitori in un cluster di Service Fabric in Azure
Per distribuire l'applicazione in un cluster in Azure, usare il proprio cluster o un party cluster.

I party cluster sono cluster di Service Fabric gratuiti disponibili per un periodo di tempo limitato, ospitati in Azure. Sono gestiti dal team di Service Fabric e consentono a chiunque di distribuirvi applicazioni e imparare a usare la piattaforma. Per ottenere l'accesso a un party cluster, [seguire le istruzioni](http://aka.ms/tryservicefabric). 

Per informazioni sulla creazione di un cluster, vedere [Creare il primo cluster di Service Fabric di Azure](service-fabric-get-started-azure-cluster.md).

> [!Note]
> Il servizio front-end Web è configurato per l'ascolto del traffico in ingresso sulla porta 80. Assicurarsi che tale porta sia aperta nel cluster. Se si usa un party cluster, questa porta è aperta.
>

### <a name="deploy-the-application-manifests"></a>Distribuire i manifesti dell'applicazione 
Installare l'[interfaccia della riga di comando di Service Fabric (sfctl)](service-fabric-cli.md) nell'ambiente dell'interfaccia della riga di comando

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```
Connettersi al cluster di Service Fabric in Azure con l'interfaccia della riga di comando di Azure. L'endpoint è l'endpoint di gestione del cluster, ad esempio `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

Usare lo script di installazione messo a disposizione per copiare la definizione dell'applicazione di voto nel cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

```azurecli-interactive
./install.sh
```

Aprire un browser e passare a Service Fabric Explorer all'indirizzo http://\<my-azure-service-fabric-cluster-url>:19080/Explorer, ad esempio `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Espandere il nodo Applicazioni, in cui sarà ora presente una voce per il tipo dell'applicazione di voto e l'istanza creata.

![Service Fabric Explorer][sfx]

Connettersi al contenitore in esecuzione.  Aprire un Web browser puntando all'URL del cluster, ad esempio `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Nel browser verrà visualizzata l'applicazione di voto.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Effettuare il failover di un contenitore in un cluster
Service Fabric garantisce lo spostamento automatico delle istanze di contenitore in altri nodi del cluster in caso di errore. È anche possibile svuotare manualmente un nodo per i contenitori e passare quindi normalmente ad altri nodi del cluster. È possibile ridimensionare i servizi in più modi. In questo esempio si usa Service Fabric Explorer.

Per effettuare il failover del contenitore front-end, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Fare clic sul nodo **fabric:/Voting/azurevotefront** nella visualizzazione albero ed espandere il nodo della partizione (rappresentato da un GUID). Si noti il nome del nodo nella visualizzazione albero, che indica i nodi in cui il contenitore è attualmente in esecuzione, ad esempio `_nodetype_4`.
3. Espandere il nodo **Nodi** nella visualizzazione albero. Fare clic sui puntini di sospensione accanto al nodo che esegue il contenitore.
4. Scegliere **Riavvia** per riavviare il nodo e confermare l'azione di riavvio. Il riavvio causa il failover del contenitore in un altro nodo del cluster.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster
I servizi di Service Fabric possono essere facilmente ridimensionati in un cluster per supportarne il carico. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster.

Per scalare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/Voting/azurevotefront** nella visualizzazione albero e scegliere **Scale Service** (Ridimensiona servizio).

    ![containersquickstartscale][containersquickstartscale]

  Ora è possibile scegliere di modificare il numero di istanze del servizio front-end Web.

3. Impostare il numero su **2** e fare clic su **Scale Service** (Ridimensiona servizio).
4. Fare clic sul nodo **fabric:/Voting/azurevotefront** nella visualizzazione albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Saranno ora presenti due istanze del servizio. Nella visualizzazione albero si può verificare in quali nodi vengono eseguite le istanze.

Con questa semplice attività di gestione abbiamo raddoppiato le risorse disponibili per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="clean-up"></a>Eseguire la pulizia
Usare lo script di disinstallazione incluso nel modello per eliminare l'istanza dell'applicazione dal cluster e annullare la registrazione del tipo di applicazione. La pulizia dell'istanza con questo comando richiede tempo ed è consigliabile non eseguire il comando "install.sh" immediatamente dopo questo script. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come:
> [!div class="checklist"]
> * Distribuire un'applicazione contenitore Linux in Azure
> * Effettuare il failover di un contenitore in un cluster di Service Fabric
> * Ridimensionare un contenitore in un cluster di Service Fabric

* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
* Vedere gli [esempi di codice per i contenitori di Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) in GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png

