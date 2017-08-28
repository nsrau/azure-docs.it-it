---
title: Creare un cluster di Service Fabric in Azure | Microsoft Docs
description: Informazioni su come creare un cluster di Service Fabric per Windows o Linux in Azure usando PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 80c5a2a43302e1cc8ec3b4298eb393a1861252d3
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Creare un cluster sicuro in Azure con PowerShell
Questa esercitazione illustra come creare un cluster di Service Fabric (Windows o Linux) in esecuzione in Azure. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un cluster sicuro di Service Fabric in Azure tramite PowerShell
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Service Fabric SDK e il modulo PowerShell](service-fabric-get-started.md)
- Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

La procedura seguente consente di creare un cluster di Service Fabric a nodo singolo, con una sola macchina virtuale, protetto da un certificato autofirmato creato insieme al cluster e posizionato in un insieme di credenziali delle chiavi. I cluster a nodo singolo non possono essere ridimensionati per più di una macchina virtuale e i cluster di anteprima non possono essere aggiornati a versioni più recenti.

Per calcolare i costi sostenuti per l'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).
Per altre informazioni sulla creazione di cluster di Service Fabric, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="create-the-cluster-using-azure-powershell"></a>Creare il cluster con Azure PowerShell
1. Scaricare una copia locale del modello di Azure Resource Manager e del file del parametri dal repository GitHub [Azure Resource Manager template for Service Fabric](https://aka.ms/securepreviewonelineclustertemplate) (Modello di Azure Resource Manager per Service Fabric).  *azuredeploy.json* è il modello di Azure Resource Manager che definisce un cluster di Service Fabric. *azuredeploy.parameters.json* è il file di parametri per la personalizzazione della distribuzione del cluster.

2. Personalizzare i parametri seguenti nel file di parametri *azuredeploy.parameters.json*:

   | Parametro       | Descrizione | Valore consigliato |
   | --------------- | ----------- | --------------- |
   | clusterLocation | Area di Azure per la distribuzione del cluster. | *ad esempio westeurope, eastasia, eastus* |
   | clusterName     | Nome del cluster che si desidera creare. | *ad esempio bobs-sfpreviewcluster* |
   | adminUserName   | Account dell'amministratore locale nelle macchine virtuali del cluster. | *Qualsiasi nome utente di Windows Server valido* |
   | adminPassword   | Password dell'amministratore locale nelle macchine virtuali del cluster. | *Qualsiasi password di Windows Server valido* |
   | clusterCodeVersion | La versione di Service Fabric da eseguire. 255.255.X.255 sono versioni di anteprima. | **255.255.5718.255** |
   | vmInstanceCount | Il numero di macchine virtuali del cluster, può essere 1 o variare da 3 a 99. | **1** | *Per un cluster di anteprima, specificare solo una macchina virtuale* |

3. Aprire una console di PowerShell, accedere ad Azure e selezionare la sottoscrizione in cui si vuole distribuire il cluster:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. Creare e crittografare una password per il certificato che Service Fabric deve usare.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. Creare il cluster e il relativo certificato eseguendo il comando seguente:

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Il parametro `-CertificateSubjectName` deve essere allineato al parametro clusterName specificato nel file dei parametri, così come il dominio associato all'area di Azure scelta, ad esempio: `clustername.eastus.cloudapp.azure.com`.

Al termine della configurazione, vengono generate informazioni relative al cluster creato in Azure. La configurazione copia anche il certificato del cluster nella directory CertificateOutputFolder nel percorso specificato per questo parametro. È necessario questo certificato per accedere a Service Fabric Explorer e visualizzare l'integrità del cluster.

Prendere nota dell'URL per il cluster, che potrebbe essere simile al seguente: *https://mycluster.westeurope.cloudapp.azure.com:19080*

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>Modificare il certificato e accedere a Service Fabric Explorer ##

1. Fare doppio clic sul certificato per aprire l'Importazione guidata certificati.

2. Usare le impostazioni predefinite, ma assicurarsi di spuntare la casella di testo **Mark this key as exportable.** (Contrassegnare questa chiave come esportabile.) nel passaggio **Protezione della chiave privata**. Visual Studio deve esportare il certificato durante la configurazione del Registro contenitori di Azure per l'autenticazione del cluster di Service Fabric in seguito in questa esercitazione.

3. È ora possibile aprire Service Fabric Explorer in un browser. A tale scopo, passare all'URL **ManagementEndpoint** per il cluster tramite un web browser e selezionare il certificato salvato nel computer.

>[!NOTE]
>Quando si apre Service Fabric Explorer, viene visualizzato un errore di certificato, poiché si usa un certificato autofirmato. In Edge, è necessario fare clic su *Dettagli* e quindi sul collegamento *Continua per la pagina Web*. In Chrome, è necessario fare clic su *Advanced* (Impostazioni avanzate)e quindi sul collegamento *Procedi*.

>[!NOTE]
>Se la creazione del cluster non riesce, è sempre possibile rieseguire il comando che aggiorna le risorse già distribuite. Se un certificato è stato creato come parte della distribuzione non riuscita, ne viene generato uno nuovo. Per risolvere i problemi di creazione del cluster, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="connect-to-the-secure-cluster"></a>Connettersi al cluster sicuro
Connettersi al cluster usando il modulo di PowerShell Service Fabric installato con Service Fabric SDK.  Installare prima di tutto il certificato nell'archivio personale (My) dell'utente corrente nel computer in uso.  Eseguire il seguente comando PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

È ora possibile connettersi al cluster sicuro.

Il modulo di PowerShell **Service Fabric** include molti cmdlet per la gestione di cluster, applicazioni e servizi di Service Fabric.  Usare il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) per connettersi al cluster sicuro. L'identificazione personale del certificato e i dettagli dell'endpoint della connessione sono disponibili nell'output di un passaggio precedente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verificare di essere connessi e che il cluster sia integro usando il cmdlet [Get ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Pulire le risorse

Un cluster è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster.  È possibile trovare l'ID della sottoscrizione accedendo al [portale di Azure](http://portal.azure.com). Eliminare il gruppo di risorse e tutte le risorse del cluster con il [cmdlet Remove-AzureRmResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un cluster di Service Fabric in Azure
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

Procedere con l'esercitazione seguente per scoprire come distribuire un'applicazione esistente.
> [!div class="nextstepaction"]
> [Distribuire un'app .NET tramite Docker Compose](service-fabric-host-app-in-a-container.md)

