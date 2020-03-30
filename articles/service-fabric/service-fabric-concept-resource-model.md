---
title: Modello di risorse dell'applicazione Azure Service FabricAzure Service Fabric application resource model
description: Questo articolo offre una panoramica della gestione di un'applicazione Azure Service Fabric tramite Azure Resource Manager.This article provides an overview of managing an Azure Service Fabric application by using Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 69c10b0e9d3b7c29122c8432ab1e4bc06d3a3120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481117"
---
# <a name="service-fabric-application-resource-model"></a>Modello di risorse dell'applicazione Service FabricService Fabric application resource model

Sono disponibili più opzioni per la distribuzione di applicazioni di Azure Service Fabric nel cluster di Service Fabric.You have multiple options for deploying Azure Service Fabric applications on your Service Fabric cluster. È consigliabile usare Azure Resource Manager.We recommend using Azure Resource Manager. Se si usa Resource Manager, è possibile descrivere applicazioni e servizi in JSON e quindi distribuirli nello stesso modello di Resource Manager del cluster. A differenza dell'uso di PowerShell o dell'interfaccia della riga di comando di Azure per distribuire e gestire le applicazioni, se si usa Gestione risorse, non è necessario attendere che il cluster sia pronto. la registrazione, il provisioning e la distribuzione delle applicazioni possono essere eseguite in un unico passaggio. L'uso di Resource Manager è il modo migliore per gestire il ciclo di vita dell'applicazione nel cluster. Per ulteriori informazioni, vedere [Procedure consigliate: infrastruttura come codice](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

La gestione delle applicazioni come risorse in Resource Manager consente di ottenere miglioramenti in queste aree:Managing your applications as resources in Resource Manager can help you gain improvements in these areas:

* Audit trail: Resource Manager controlla ogni operazione e mantiene un log attività dettagliato. Un log attività consente di tenere traccia delle modifiche apportate alle applicazioni e al cluster.
* Controllo degli accessi in base al ruolo: è possibile gestire l'accesso ai cluster e alle applicazioni distribuite nel cluster usando lo stesso modello di Resource Manager.Role-based access control: You can manage access to clusters and to applications deployed on the cluster by using the same Resource Manager template.
* Efficienza di gestione: l'uso di Resource Manager offre un'unica posizione (il portale di Azure) per la gestione delle distribuzioni di cluster e applicazioni critiche.

In questo documento imparerai a:

> [!div class="checklist"]
>
> * Distribuire le risorse dell'applicazione tramite Resource Manager.Deploy application resources by using Resource Manager.
> * Aggiornare le risorse dell'applicazione tramite Resource Manager.Upgrade application resources by using Resource Manager.
> * Eliminare le risorse dell'applicazione.

## <a name="deploy-application-resources"></a>Distribuire le risorse dell'applicazioneDeploy application resources

I passaggi generali da eseguire per distribuire un'applicazione e i relativi servizi tramite il modello di risorse dell'applicazione Resource Manager sono:The high-level steps you take to deploy an application and its services by using the Resource Manager application resource model are:
1. Creare il pacchetto del codice dell'applicazione.
1. Caricare il pacchetto.
1. Fare riferimento al percorso del pacchetto in un modello di Resource Manager come risorsa dell'applicazione. 

Per ulteriori informazioni, visualizzare [Pacchetto di un'applicazione](service-fabric-package-apps.md#create-an-sfpkg).

Quindi, creare un modello di Resource Manager, aggiornare il file di parametri con i dettagli dell'applicazione e distribuire il modello nel cluster di Service Fabric. [Esplorare gli esempi](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per distribuire un'applicazione da un modello di Resource Manager, è necessario disporre di un account di archiviazione. L'account di archiviazione viene usato per gestire temporaneamente l'immagine dell'applicazione. 

È possibile riutilizzare un account di archiviazione esistente oppure creare un nuovo account di archiviazione per la gestione temporanea delle applicazioni. Se si usa un account di archiviazione esistente, è possibile ignorare questo passaggio. 

![Creare un account di archiviazione][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configurare l'account di archiviazioneConfigure your storage account

Dopo aver creato l'account di archiviazione, si crea un contenitore BLOB in cui le applicazioni possono essere gestite temporaneamente. Nel portale di Azure passare all'account di archiviazione di Azure in cui si vogliono archiviare le applicazioni. Selezionare **BLOB** > **Aggiungi contenitore**. 

Le risorse del cluster possono essere protette impostando il livello di accesso pubblico su **private**. È possibile concedere l'accesso in diversi modi:You can grant access in multiple ways:

* Autorizzare l'accesso a BLOB e code tramite [Azure Active Directory.](../storage/common/storage-auth-aad-app.md)
* Concedere l'accesso ai dati BLOB e coda di Azure usando il controllo degli accessi in base al ruolo nel portale di [Azure.](../storage/common/storage-auth-aad-rbac-portal.md)
* Delegare l'accesso utilizzando una firma di [accesso condiviso.](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)

L'esempio nella schermata seguente usa l'accesso in lettura anonimo per i BLOB.

![Crea BLOB][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Scorrere l'applicazione nell'account di archiviazioneStage the application in your storage account

Prima di poter distribuire un'applicazione, è necessario eseguire temporaneal'applicazione nell'archiviazione BLOB. In questa esercitazione viene creato manualmente il pacchetto dell'applicazione. Tieni presente che questo passaggio può essere automatizzato. Per ulteriori informazioni, vedere Creare un pacchetto di [un'applicazione](service-fabric-package-apps.md#create-an-sfpkg). 

In questa esercitazione viene usata [l'applicazione di esempio Voting](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **Votazione** e quindi scegliere **Pacchetto**.

   ![Applicazione pacchetto][PackageApplication]  
1. Passare alla directory *.* Comprimere il contenuto in un file denominato *Voting.zip*. Il file *ApplicationManifest.xml* deve trovarsi nella radice del file zip.

   ![Applicazione zip][ZipApplication]  
1. Rinominare il file per modificare l'estensione da .zip a *.sfpkg*.

1. Nel portale di Azure, nel contenitore di app per **l'account** di archiviazione, selezionare **Carica**e quindi caricare **Voting.sfpkg**. 

   ![Carica pacchetto dell'app][UploadAppPkg]

A questo punto, l'applicazione è ora in fasi ed è possibile creare il modello resource Manager per distribuire l'applicazione.

### <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager

L'applicazione di esempio contiene modelli di [Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) che è possibile usare per distribuire l'applicazione. I nomi dei file modello sono *UserApp.json* e *UserApp.Parameters.json*.

> [!NOTE]
> Il file *UserApp.Parameters.json* deve essere aggiornato con il nome del cluster.
>
>

| Parametro              | Descrizione                                 | Esempio                                                      | Commenti                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Il nome del cluster in cui si esegue la distribuzione | sf-cluster123                                                |                                                              |
| application            | Nome dell'applicazione                 | Voto                                                       |
| applicationTypeName (NomeTipoApplicazione)    | Il nome del tipo dell'applicazione           | VotingTipo                                                   | Deve corrispondere a ApplicationManifest.xml                 |
| applicationTypeVersion (gestionedei) | La versione del tipo di applicazione         | 1.0.0                                                        | Deve corrispondere a ApplicationManifest.xml                 |
| serviceName            | Il nome del servizio         | Votazione -VotingWeb                                             | Il formato NomeApplicazione/NomeServizio            |
| nomeservizio        | Il nome del tipo del servizio                | VotingWeb                                                    | Deve corrispondere a ServiceManifest.xml                 |
| appPackageUrl (informazioni in questo gruppo)          | URL di archiviazione BLOB dell'applicazione     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | URL del pacchetto dell'applicazione nell'archiviazione BLOB (la procedura per impostare l'URL è descritta più avanti nell'articolo): |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Distribuire l'applicazione

Eseguire il cmdlet **New-AzResourceGroupDeployment** per distribuire l'applicazione al gruppo di risorse che contiene il cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Aggiornare l'applicazione Service Fabric tramite Resource Manager

È possibile aggiornare un'applicazione già distribuita in un cluster di Service Fabric per uno dei motivi seguenti:You might upgrade an application that's already deployed to a Service Fabric cluster for one of these reasons:

* Un nuovo servizio viene aggiunto all'applicazione. Una definizione di servizio deve essere aggiunta ai file *service-manifest.xml* e *application-manifest.xml* quando un servizio viene aggiunto all'applicazione. Per riflettere una nuova versione di un'applicazione, è inoltre necessario modificare la versione del tipo di applicazione da 1.0.0 a 1.0.1 in [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Una nuova versione di un servizio esistente viene aggiunta all'applicazione. Gli esempi includono le modifiche al codice dell'applicazione e gli aggiornamenti alla versione e al nome del tipo di app. Per questo aggiornamento, aggiornare UserApp.Parameters.json in questo modo:For this upgrade, update UserApp.Parameters.json like this:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Eliminare le risorse dell'applicazioneDelete application resources

Per eliminare un'applicazione distribuita tramite il modello di risorse dell'applicazione in Resource Manager:To delete an application that was deployed by using the application resource model in Resource Manager:

1. Utilizzare il cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) per ottenere l'ID risorsa per l'applicazione:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Utilizzare il cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) per eliminare le risorse dell'applicazione:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Passaggi successivi

Ottenere informazioni sul modello di risorse dell'applicazione:Get information about the application resource model:

* [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md)
* [Manifesti delle applicazioni e dei servizi di Service Fabric](service-fabric-application-and-service-manifests.md)
* [Procedure consigliate: infrastruttura come codiceBest practices: Infrastructure as code](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Gestire applicazioni e servizi come risorse di AzureManage applications and services as Azure resources](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
