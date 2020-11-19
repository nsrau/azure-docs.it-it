---
title: Modello di risorsa dell'applicazione Service Fabric di Azure
description: Questo articolo fornisce una panoramica della gestione di un'applicazione Service Fabric di Azure usando Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 886b7d6b40bebf6234064b0627017db1d8cfe31f
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888828"
---
# <a name="service-fabric-application-resource-model"></a>Modello di risorsa dell'applicazione Service Fabric

Sono disponibili più opzioni per la distribuzione di applicazioni Azure Service Fabric nel cluster Service Fabric. Si consiglia di usare Azure Resource Manager. Se si usa Gestione risorse, è possibile descrivere le applicazioni e i servizi in JSON e quindi distribuirli nello stesso modello di Gestione risorse del cluster. A differenza dell'uso di PowerShell o dell'interfaccia della riga di comando di Azure per distribuire e gestire le applicazioni, se si usa Gestione risorse, non è necessario attendere che il cluster sia pronto; la registrazione, il provisioning e la distribuzione dell'applicazione possono essere tutti effettuati in un unico passaggio. L'uso di Gestione risorse rappresenta il modo migliore per gestire il ciclo di vita dell'applicazione nel cluster. Per altre informazioni, vedere [procedure consigliate: infrastruttura come codice](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

La gestione delle applicazioni come risorse in Gestione risorse consente di ottenere miglioramenti in queste aree:

* Audit Trail: Gestione risorse controlla ogni operazione e mantiene un log attività dettagliato. Un log attività consente di tenere traccia delle modifiche apportate alle applicazioni e al cluster.
* Controllo degli accessi in base al ruolo: è possibile gestire l'accesso ai cluster e alle applicazioni distribuite nel cluster usando lo stesso modello di Gestione risorse.
* Efficienza di gestione: l'uso di Gestione risorse offre un'unica posizione (la portale di Azure) per la gestione del cluster e delle distribuzioni di applicazioni critiche.

In questo documento si apprenderà come:

> [!div class="checklist"]
>
> * Distribuire le risorse dell'applicazione usando Gestione risorse.
> * Aggiornare le risorse dell'applicazione usando Gestione risorse.
> * Elimina le risorse dell'applicazione.

## <a name="deploy-application-resources"></a>Distribuire le risorse dell'applicazione

I passaggi di alto livello necessari per distribuire un'applicazione e i relativi servizi usando il modello di risorse dell'applicazione Gestione risorse sono:
1. Creare il pacchetto del codice dell'applicazione.
1. Caricare il pacchetto.
1. Fare riferimento al percorso del pacchetto in un modello di Gestione risorse come risorsa dell'applicazione. 

Per altre informazioni, vedere creare [un pacchetto di un'applicazione](service-fabric-package-apps.md#create-an-sfpkg).

Si crea quindi un modello di Gestione risorse, si aggiorna il file dei parametri con i dettagli dell'applicazione e si distribuisce il modello nel cluster Service Fabric. [Esplorare gli esempi](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per distribuire un'applicazione da un modello di Gestione risorse, è necessario disporre di un account di archiviazione. L'account di archiviazione viene usato per organizzare l'immagine dell'applicazione. 

È possibile riutilizzare un account di archiviazione esistente oppure è possibile creare un nuovo account di archiviazione per la gestione temporanea delle applicazioni. Se si usa un account di archiviazione esistente, è possibile ignorare questo passaggio. 

![Creare un account di archiviazione][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configurare l'account di archiviazione

Dopo aver creato l'account di archiviazione, si crea un contenitore BLOB in cui le applicazioni possono essere preparate per la gestione temporanea. Nella portale di Azure passare all'account di archiviazione di Azure in cui si desidera archiviare le applicazioni. Selezionare **BLOB**  >  **Aggiungi contenitore**. 

Le risorse del cluster possono essere protette impostando il livello di accesso pubblico su **privato**. È possibile concedere l'accesso in diversi modi:

* Autorizzare l'accesso a BLOB e code usando [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Concedere l'accesso ai dati di Accodamento e BLOB di Azure usando [RBAC di Azure nel portale di Azure](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegare l'accesso usando una [firma di accesso condiviso](/rest/api/storageservices/delegate-access-with-shared-access-signature).

L'esempio nella schermata seguente usa l'accesso in lettura anonimo per i BLOB.

![Crea BLOB][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Organizzare l'applicazione nell'account di archiviazione

Prima di poter distribuire un'applicazione, è necessario organizzare l'applicazione nell'archivio BLOB. In questa esercitazione viene creato manualmente il pacchetto dell'applicazione. Tenere presente che questo passaggio può essere automatizzato. Per altre informazioni, vedere creare [un pacchetto di un'applicazione](service-fabric-package-apps.md#create-an-sfpkg). 

In questa esercitazione viene usata l' [applicazione di voto di esempio](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **votante** , quindi selezionare **pacchetto**.

   ![Applicazione pacchetto][PackageApplication]  
1. Passare alla directory *.\Service-Fabric-DotNet-quickstart\Voting\pkg\Debug* Zippare il contenuto in un file denominato *Voting.zip*. Il file di *ApplicationManifest.xml* deve trovarsi nella radice del file zip.

   ![Applicazione zip][ZipApplication]  
1. Rinominare il file per modificare l'estensione da zip a *. sfpkg*.

1. Nel portale di Azure, nel contenitore **app** per l'account di archiviazione, selezionare **carica** e quindi carica **Voto. sfpkg**. 

   ![Carica pacchetto dell'app][UploadAppPkg]

A questo punto, l'applicazione è in fase di gestione temporanea ed è possibile creare il modello di Gestione risorse per distribuire l'applicazione.

### <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager

Nell'applicazione di esempio sono contenuti [Azure Resource Manager modelli](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) che è possibile utilizzare per distribuire l'applicazione. I nomi dei file di modello vengono *UserApp.jsin* e *UserApp.Parameters.js*.

> [!NOTE]
> Il *UserApp.Parameters.jsnel* file deve essere aggiornato con il nome del cluster.
>
>


| Parametro              | Descrizione                                 | Esempio                                                      | Commenti                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Nome del cluster in cui si sta eseguendo la distribuzione | SF-cluster123                                                |                                                              |
| application            | Nome dell'applicazione                 | Voto                                                       |
| applicationTypeName    | Nome del tipo di applicazione           | VotingType                                                   | Deve corrispondere ApplicationManifest.xml                 |
| applicationTypeVersion | Versione del tipo di applicazione         | 1.0.0                                                        | Deve corrispondere ApplicationManifest.xml                 |
| serviceName            | Nome del servizio         | Voto ~ VotingWeb                                             | Deve essere nel formato ApplicationName ~ ServiceType            |
| serviceTypeName        | Nome del tipo del servizio                | VotingWeb                                                    | Deve corrispondere ServiceManifest.xml                 |
| appPackageUrl          | URL di archiviazione BLOB dell'applicazione     | https: \/ /servicefabricapps.blob.Core.Windows.NET/Apps/voting.sfpkg | L'URL del pacchetto dell'applicazione nell'archivio BLOB (la procedura per impostare l'URL è descritto più avanti in questo articolo) |

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

Eseguire il cmdlet **New-AzResourceGroupDeployment** per distribuire l'applicazione nel gruppo di risorse che contiene il cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Aggiornare l'applicazione Service Fabric tramite Gestione risorse


> [!IMPORTANT]
> Tutti i servizi distribuiti tramite la definizione JSON ARM devono essere rimossi dalla sezione DefaultServices del file di ApplicationManifest.xml corrispondente.


È possibile aggiornare un'applicazione che è già stata distribuita in un cluster Service Fabric per uno dei seguenti motivi:

* Un nuovo servizio viene aggiunto all'applicazione. È necessario aggiungere una definizione del servizio a *service-manifest.xml* e *application-manifest.xml* file quando un servizio viene aggiunto all'applicazione. Per riflettere una nuova versione di un'applicazione, è necessario modificare anche la versione del tipo di applicazione da 1.0.0 a 1.0.1 in [UserApp.Parameters.js](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)in:

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

* All'applicazione viene aggiunta una nuova versione di un servizio esistente. Gli esempi includono modifiche al codice dell'applicazione e aggiornamenti alla versione e al nome del tipo di app. Per questo aggiornamento, aggiornare UserApp.Parameters.jsin modo analogo al seguente:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Elimina risorse dell'applicazione

Per eliminare un'applicazione distribuita usando il modello di risorsa dell'applicazione in Gestione risorse:

1. Usare il cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-2.5.0) per ottenere l'ID risorsa per l'applicazione:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Usare il cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) per eliminare le risorse dell'applicazione:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Passaggi successivi

Ottenere informazioni sul modello di risorsa dell'applicazione:

* [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md)
* [Manifesti delle applicazioni e dei servizi di Service Fabric](service-fabric-application-and-service-manifests.md)
* [Procedure consigliate: infrastruttura come codice](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Gestire le applicazioni e i servizi come risorse di Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
