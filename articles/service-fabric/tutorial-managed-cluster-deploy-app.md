---
title: Distribuire un'applicazione in un cluster gestito di Service Fabric tramite PowerShell (anteprima)
description: In questa esercitazione si effettuerà la connessione a un cluster gestito di Service Fabric e si distribuirà un'applicazione tramite PowerShell.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410228"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Esercitazione: Distribuire un'app in un cluster gestito di Service Fabric (anteprima)

In questa serie di esercitazioni verrà illustrato quanto segue:

> [!div class="checklist"]
> * [Come distribuire un cluster gestito di Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Come aumentare il numero di istanze di un cluster gestito di Service Fabric](tutorial-managed-cluster-scale.md)
> * [Come aggiungere e rimuovere nodi in un cluster gestito di Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * Come distribuire un'applicazione in un cluster gestito di Service Fabric

Questa parte della serie descrive come:

> [!div class="checklist"]
> * Connettersi al cluster gestito di Service Fabric
> * Caricare un'applicazione in un cluster
> * Creare un'istanza di un'applicazione in un cluster
> * Rimuovere un'applicazione da un cluster

## <a name="prerequisites"></a>Prerequisiti

* Un cluster gestito di Service Fabric (vedere [*Distribuire un cluster gestito*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Connettersi al cluster

Per connettersi al cluster, sarà necessaria l'identificazione personale del certificato del cluster. È possibile trovare questo valore nelle proprietà del cluster generate dalla distribuzione della risorsa oppure eseguendo una query sulle proprietà del cluster in una risorsa esistente.

Per eseguire query sulla risorsa cluster e trovare l'identificazione personale del certificato del cluster, è possibile usare il comando seguente.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Con l'identificazione personale del certificato del cluster, si è pronti a connettersi.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Caricare un pacchetto dell'applicazione

In questa esercitazione verrà usato l'esempio di [applicazione di voto di Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy). Per altri dettagli sulla distribuzione di applicazioni di Service Fabric tramite PowerShell, vedere [Distribuire e rimuovere applicazioni di Service Fabric](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> Nell'anteprima del cluster gestito di Service Fabric non sarà possibile pubblicare applicazioni direttamente da Visual Studio.

Sarà prima necessario [creare un pacchetto dell'applicazione per la distribuzione](service-fabric-package-apps.md). Per questa esercitazione, seguire i passaggi per la creazione del pacchetto di un'applicazione all'interno di Visual Studio. È importante prendere nota del percorso in cui è stato creato il pacchetto dell'applicazione, perché verrà usato di seguito.

Una volta creato il pacchetto dell'applicazione, è possibile caricarlo nel cluster. Aggiornare il valore `$path` per rappresentare il percorso in cui si trova il pacchetto dell'applicazione, quindi eseguire quanto segue:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Creare un'applicazione

È possibile creare un'istanza di un'applicazione da qualsiasi versione del tipo di applicazione registrata mediante il cmdlet New-ServiceFabricApplication. Il nome di ogni applicazione deve iniziare con lo schema "fabric:" e deve essere univoco per ogni istanza dell'applicazione. Vengono creati anche i servizi predefiniti specificati nel manifesto dell'applicazione del tipo di applicazione di destinazione.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Una volta completata l'operazione, le istanze dell'applicazione verranno eseguite in Service Fabric Explorer.

### <a name="remove-an-application"></a>Rimuovere un'applicazione

Quando un'istanza dell'applicazione non è più necessaria, è possibile rimuoverla definitivamente in base al nome con il cmdlet `Remove-ServiceFabricApplication`, che rimuove automaticamente anche tutti i servizi appartenenti all'applicazione, rimuovendo definitivamente tutto lo stato del servizio.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Passaggi successivi

In questo passaggio è stata distribuita un'app in un cluster gestito di Service Fabric. Per altre informazioni sui cluster gestiti di Service Fabric Mesh, vedere:

> [!div class="nextstepaction"]
> [Domande frequenti sui cluster gestiti di Service Fabric](faq-managed-cluster.md)
