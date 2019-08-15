---
title: Modello di risorsa dell'applicazione Service Fabric di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della gestione di un'applicazione Service Fabric di Azure con Azure Resource Manager
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: atsenthi
ms.openlocfilehash: 7795612d8aa4974bc640571d49ad1520e2a0f94c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963834"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>Che cos'è il modello di risorsa Service Fabric applicazione?
Si consiglia di distribuire Service Fabric applicazioni nel cluster Service Fabric tramite Azure Resource Manager. Questo metodo rende possibile descrivere le applicazioni e i servizi in JSON e distribuirli nello stesso modello di Gestione risorse del cluster. Invece di distribuire e gestire le applicazioni tramite PowerShell o l'interfaccia della riga di comando di Azure, non è necessario attendere che il cluster sia pronto. Il processo di registrazione, provisioning e distribuzione delle applicazioni può essere eseguito in un unico passaggio. Questa è la procedura consigliata per gestire il ciclo di vita dell'applicazione nel cluster. Per altre informazioni, vedere [procedure consigliate](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources).

Se possibile, gestire le applicazioni come risorse di Resource Manager in modo da usufruire dei vantaggi seguenti.
* Audit Trail: Gestione risorse controlla ogni operazione e mantiene un *log attività* dettagliato che consente di tracciare le modifiche apportate a queste applicazioni e al cluster.
* Controllo degli accessi in base al ruolo: La gestione dell'accesso ai cluster e alle applicazioni distribuite nel cluster può essere eseguita tramite lo stesso modello di Gestione risorse.
* Azure Resource Manager (tramite la portale di Azure) diventa uno sportello unico per gestire il cluster e le distribuzioni di applicazioni critiche.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Service Fabric ciclo di vita dell'applicazione con Azure Resource Manager 
In questo documento si apprenderà come:

> [!div class="checklist"]
> * Distribuire le risorse dell'applicazione usando Azure Resource Manager 
> * Aggiornare le risorse dell'applicazione usando Azure Resource Manager
> * Elimina risorse dell'applicazione

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Distribuire le risorse dell'applicazione usando Azure Resource Manager  
Per distribuire un'applicazione e i relativi servizi usando il modello di risorsa dell'applicazione Azure Resource Manager, è necessario creare il pacchetto del codice dell'applicazione, caricare il pacchetto e quindi fare riferimento al percorso del pacchetto in un modello di Azure Resource Manager come applicazione risorse. Per altre informazioni, vedere creare [un pacchetto di un'applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).
          
Quindi, creare un modello di Azure Resource Manager, aggiornare il file dei parametri con i dettagli dell'applicazione e distribuirlo nel cluster di Service Fabric. Vedere gli esempi qui

### <a name="create-a-storage-account"></a>Creare un account di archiviazione 
Per la distribuzione di un'applicazione da un modello di Gestione risorse è necessario un account di archiviazione per organizzare l'immagine dell'applicazione. È possibile riutilizzare un account di archiviazione esistente o creare un nuovo account di archiviazione per organizzare le applicazioni. Se si vuole usare un account di archiviazione esistente, è possibile ignorare questo passaggio. 

![Creare un account di archiviazione][CreateStorageAccount]

### <a name="configure-storage-account"></a>Configura account di archiviazione 
Una volta creato l'account di archiviazione, è necessario creare un contenitore BLOB in cui le applicazioni possono essere preparate per la gestione temporanea. Nella portale di Azure passare all'account di archiviazione in cui si vogliono archiviare le applicazioni. Selezionare il pannello **BLOB** , quindi fare clic sul pulsante **Aggiungi contenitore** . Aggiungere un nuovo contenitore con il livello di accesso pubblico BLOB.
   
![Crea BLOB][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Organizzare l'applicazione in un account di archiviazione
Prima di poter distribuire l'applicazione, è necessario eseguire la gestione temporanea nell'archivio BLOB. In questa esercitazione verrà creato manualmente il pacchetto dell'applicazione, tuttavia questo passaggio può essere automatizzato.  Per altre informazioni, vedere creare [un pacchetto di un'applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). Nei passaggi seguenti verrà utilizzata l' [applicazione di voto di esempio](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) .

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di voto e selezionare pacchetto.   
![Applicazione pacchetto][PackageApplication]  
2. Aprire la directory **.\Service-Fabric-DotNet-quickstart\Voting\pkg\Debug** che è stata creata e comprimere il contenuto in un file denominato **voter. zip** in modo che ApplicationManifest. XML si trovi alla radice del file zip.  
![Applicazione zip][ZipApplication]  
3. Rinominare l'estensione del file da zip a **. sfpkg**.
4. Nel portale di Azure, nel contenitore **app** dell'account di archiviazione, fare clic su **carica** e carica **Voto. sfpkg**.  
![Carica pacchetto dell'app][UploadAppPkg]

L'applicazione è ora preparata per la gestione temporanea. A questo punto è possibile creare il modello di Azure Resource Manager per distribuire l'applicazione.      
   
### <a name="create-the-azure-resource-manager-template"></a>Creare il modello di Azure Resource Manager
L'applicazione di esempio contiene [modelli di Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) che possono essere usati per distribuire l'applicazione. I file modello sono denominati **UserApp. JSON** e **UserApp. Parameters. JSON**. 

> [!NOTE] 
> Il file **UserApp. Parameters. JSON** deve essere aggiornato con il nome del cluster.
>
>

| Parametro              | DESCRIZIONE                                 | Esempio                                                      | Commenti                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Nome del cluster in cui si sta eseguendo la distribuzione | SF-cluster123                                                |                                                              |
| Applicazione            | Nome dell'applicazione                 | Voto                                                       |
| applicationTypeName    | Nome del tipo di applicazione           | VotingType                                                   | Deve corrispondere a ciò che è presente in ApplicationManifest. XML                 |
| applicationTypeVersion | Versione del tipo di applicazione         | 1.0.0                                                        | Deve corrispondere a ciò che è presente in ApplicationManifest. XML                 |
| serviceName            | Nome del servizio         | Voto ~ VotingWeb                                             | Deve essere nel formato ApplicationName ~ ServiceType            |
| serviceTypeName        | Nome del tipo del servizio                | VotingWeb                                                    | Deve corrispondere a ciò che si trova in ServiceManifest. XML                 |
| appPackageUrl          | URL di archiviazione BLOB dell'applicazione     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | L'URL del pacchetto dell'applicazione nell'archivio BLOB (la procedura per impostarlo è descritto di seguito) |
       
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
Per distribuire l'applicazione, eseguire New-AzResourceGroupDeployment per eseguire la distribuzione nel gruppo di risorse che contiene il cluster.
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Aggiornare Service Fabric applicazione utilizzando Azure Resource Manager
Le applicazioni già distribuite in un cluster Service Fabric verranno aggiornate per i motivi seguenti:

1. Un nuovo servizio viene aggiunto all'applicazione. È necessario aggiungere una definizione del servizio al file Service-manifest. XML e application-manifest. XML. Per riflettere la nuova versione dell'applicazione, è necessario aggiornare la versione del tipo di applicazione da 1.0.0 a 1.0.1 [UserApp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json).

    ```
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
2. All'applicazione viene aggiunta una nuova versione di un servizio esistente. Questo implica modifiche del codice dell'applicazione e aggiornamenti alla versione e al nome del tipo di app.

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Elimina risorse dell'applicazione
Le applicazioni distribuite con il modello di risorsa dell'applicazione in Azure Resource Manager possono essere eliminate dal cluster usando i passaggi seguenti

1) Ottenere l'ID risorsa per l'applicazione usando il comando [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)  

    #### <a name="get-resource-id-for-application"></a>Ottenere l'ID risorsa per l'applicazione
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) Eliminare le risorse dell'applicazione usando [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)  

    #### <a name="delete-application-resource-using-its-resource-id"></a>Elimina la risorsa applicazione usando il relativo ID risorsa
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sul modello di risorsa dell'applicazione:

* [Modellare un'applicazione in Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric manifesti dell'applicazione e del servizio](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png