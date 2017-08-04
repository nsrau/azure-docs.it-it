---
title: Gestire le applicazioni di Azure Service Fabric con l'interfaccia della riga di comando di Azure 2.0
description: Informazioni su come distribuire e rimuovere le applicazioni da un cluster Azure Service Fabric usando l'interfaccia della riga di comando di Azure 2.0.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5728339236e3819b301e428f9d7a8add08f02b3e
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-cli-20"></a>Gestire un'applicazione di Azure Service Fabric usando l'interfaccia della riga di comando di Azure 2.0

Informazioni su come creare ed eliminare applicazioni in esecuzione in un cluster Azure Service Fabric.

## <a name="prerequisites"></a>Prerequisiti

* Installare l'interfaccia della riga di comando di Azure 2.0. Selezionare il cluster Service Fabric. Per altre informazioni, vedere [Azure Service Fabric e interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md).

* È necessario avere un pacchetto dell'applicazione di Service Fabric pronto per essere distribuito. Per altre informazioni su come creare un'applicazione e inserirla in un pacchetto, vedere l'articolo sul [modello di applicazione di Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Panoramica

Per distribuire una nuova applicazione, completare questi passaggi:

1. Caricare un pacchetto dell'applicazione nell'archivio immagini di Service Fabric.
2. Eseguire il provisioning di un tipo di applicazione.
3. Specificare e creare un'applicazione.
4. Specificare e creare i servizi.

Per rimuovere un'applicazione esistente, completare questi passaggi:

1. Eliminare l'applicazione.
2. Annullare il provisioning del tipo di applicazione associato.
3. Eliminare il contenuto dell'archivio immagini.

## <a name="deploy-a-new-application"></a>Distribuire un'applicazione nuova

Per distribuire una nuova applicazione, completare le operazioni descritte di seguito.

### <a name="upload-a-new-application-package-to-the-image-store"></a>Caricare un nuovo pacchetto dell'applicazione nell'archivio immagini

Prima di creare un'applicazione, caricare il pacchetto dell'applicazione nell'archivio immagini di Service Fabric. 

Ad esempio, se il pacchetto dell'applicazione è nella directory `app_package_dir`, usare i comandi seguenti per caricare la directory:

```azurecli
az sf application upload --path ~/app_package_dir
```

Per i pacchetti di applicazione di grandi dimensioni è possibile specificare l'opzione `--show-progress` per visualizzare lo stato del caricamento.

### <a name="provision-the-application-type"></a>Eseguire il provisioning del tipo di applicazione

Al termine del caricamento eseguire il provisioning dell'applicazione. Per eseguire il provisioning dell'applicazione, usare il comando seguente:

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

Il valore per `application-type-build-path` è il nome della directory in cui è stato caricato il pacchetto dell'applicazione.

### <a name="create-an-application-from-an-application-type"></a>Creare un'applicazione da un tipo di applicazione

Dopo il provisioning dell'applicazione, usare il comando seguente per assegnare un nome e creare l'applicazione:

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` è il nome da usare per l'istanza di applicazione. È possibile ottenere parametri aggiuntivi dal manifesto dell'applicazione di cui è stato eseguito il provisioning.

Il nome dell'applicazione deve iniziare con il prefisso `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Creare i servizi per la nuova applicazione

Dopo avere creato un'applicazione, creare i servizi dall'applicazione. Nell'esempio seguente si crea dall'applicazione un nuovo servizio senza stato. I servizi che è possibile creare da un'applicazione sono definiti in un manifesto del servizio all'interno del pacchetto dell'applicazione di cui in precedenza è stato eseguito il provisioning.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verificare l'integrità e la distribuzione dell'applicazione

Per verificare se un'applicazione e un servizio sono stati distribuiti in modo corretto, controllarne la presenza nei relativi elenchi:

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Per verificare l'integrità del servizio, usare comandi simili per recuperare lo stato sia del servizio che dell'applicazione:

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

I servizi e le applicazioni integri devono avere il valore `HealthState` impostato su `Ok`.

## <a name="remove-an-existing-application"></a>Rimuovere un'applicazione esistente

Per rimuovere un'applicazione, completare le operazioni descritte di seguito.

### <a name="delete-the-application"></a>Eliminare l'applicazione

Per eliminare l'applicazione, usare il comando seguente:

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Annullare il provisioning del tipo di applicazione

Dopo aver eliminato l'applicazione, è possibile annullare il provisioning del tipo di applicazione se non è più necessario. Per annullare il provisioning del tipo di applicazione, usare il comando seguente:

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

Il nome del tipo e la versione del tipo devono corrispondere al nome e alla versione indicati nel manifesto dell'applicazione di cui in precedenza è stato eseguito il provisioning.

### <a name="delete-the-application-package"></a>Eliminare il pacchetto dell'applicazione

Dopo avere annullato il provisioning del tipo di applicazione, è possibile eliminare il pacchetto dell'applicazione dall'archivio immagini se non è più necessario. L'eliminazione di pacchetti di applicazioni consente di recuperare spazio su disco. 

Per eliminare il pacchetto dell'applicazione dall'archivio immagini, usare il comando seguente:

```azurecli
az sf application package-delete --content-path app_package_dir
```

`content-path` deve essere il nome della directory caricata al momento della creazione dell'applicazione.

## <a name="related-articles"></a>Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introduzione all'interfaccia della riga di comando XPlat per Service Fabric](service-fabric-azure-cli.md)

