---
title: Gestire le applicazioni Service Fabric con l'interfaccia della riga di comando di Azure 2.0
description: L'articolo descrive il processo di distribuzione e rimozione di applicazioni da un cluster di Service Fabric mediante l'interfaccia della riga di comando di Azure 2.0
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>Gestire le applicazioni Service Fabric con l'interfaccia della riga di comando di Azure 2.0

Seguire questa documentazione per creare ed eliminare applicazioni in esecuzione in un cluster di Service Fabric.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di installare l'interfaccia della riga di comando di Azure 2.0 e selezionare il cluster di Service Fabric. Altre informazioni sono disponibili nell'[introduzione alla documentazione dell'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md).

È necessario avere anche un pacchetto dell'applicazione di Service Fabric pronto per essere distribuito. Per altre informazioni su come creare un'applicazione e generarne un pacchetto, vedere la [documentazione sul modello di applicazione](service-fabric-application-model.md).

## <a name="overview"></a>Panoramica

La distribuzione di una nuova applicazione consiste in quattro passaggi:

1. Caricare un pacchetto dell'applicazione nell'archivio immagini di Service Fabric
1. Eseguire il provisioning di un tipo di applicazione
1. Specificare e creare un'applicazione
1. Specificare e creare i servizi

La rimozione di un'applicazione esistente richiede tre passaggi:

1. Eliminare applicazione
1. Annullare il provisioning del tipo di applicazione associato
1. Eliminare il contenuto dell'archivio immagini

## <a name="deploy-a-new-application"></a>Distribuire un'applicazione nuova

Per distribuire una nuova applicazione, seguire questa procedura:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Caricare un nuovo pacchetto dell'applicazione nell'archivio immagini

Prima di creare un'applicazione, il pacchetto dell'applicazione deve essere caricato nell'archivio immagini di Service Fabric.
Si supponga quindi che il pacchetto dell'applicazione sia presente nella directory `app_package_dir`. Usare i comandi seguenti per caricare la directory:

```azurecli
az sf application upload --path ~/app_package_dir
```

Per i pacchetti di applicazione di grandi dimensioni è possibile specificare l'opzione `--show-progress` per visualizzare lo stato del caricamento.

### <a name="provision-application-type"></a>Eseguire il provisioning di un tipo di applicazione

Dopo avere completato il caricamento, è necessario eseguire il provisioning dell'applicazione. Per eseguire il provisioning dell'applicazione, usare il comando seguente:

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

`application-type-build-path` è identico al nome della directory contenente il pacchetto dell'applicazione che è stato caricato in precedenza.

### <a name="create-application-from-application-type"></a>Creare l'applicazione dal tipo di applicazione

Dopo il provisioning dell'applicazione, è possibile assegnare un nome e creare l'applicazione usando il comando seguente:

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

Qui `app-name` è il nome che si desidera assegnare all'istanza dell'applicazione. Gli altri parametri sono disponibili nel manifesto dell'applicazione a cui è stato eseguito il provisioning in precedenza.

Il nome dell'applicazione deve iniziare con il prefisso `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Creare i servizi per la nuova applicazione

Dopo avere creato un'applicazione, è possibile creare servizi dall'applicazione. In questo esempio si crea dall'applicazione un nuovo servizio senza stato. I servizi che è possibile creare da un'applicazione sono definiti in un manifesto del servizio all'interno del pacchetto dell'applicazione di cui precedentemente è stato eseguito il provisioning.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>Verificare l'integrità e la creazione dell'applicazione

Per verificare la corretta distribuzione dell'applicazione e del servizio, è possibile controllarne la presenza nell'apposito elenco usando i comandi seguenti:

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Per verificare l'integrità del servizio, usare comandi simili per recuperare l'integrità sia del servizio che dell'applicazione:

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

I servizi e le applicazioni integri devono avere il valore `HealthState` impostato su `Ok`.

## <a name="remove-an-existing-application"></a>Rimuovere un'applicazione esistente

Per rimuovere un'applicazione, seguire questa procedura.

### <a name="delete-the-application"></a>Eliminare l'applicazione

Eliminare l'applicazione eseguendo comando seguente:

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Annullare il provisioning del tipo di applicazione

Dopo che l'applicazione è stata eliminata, è possibile annullare il provisioning del tipo di applicazione se non è più necessario. Per annullare il provisioning del tipo di applicazione, usare il comando seguente:

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

In questo caso, il nome del tipo e la versione del tipo devono corrispondere al nome e alla versione indicati nel manifesto dell'applicazione di cui è stato precedentemente eseguito il provisioning.

### <a name="delete-application-package"></a>Eliminare il pacchetto dell'applicazione

Dopo che è stato annullato il provisioning del tipo di applicazione, il pacchetto dell'applicazione può essere eliminato dall'archivio immagini se non è più necessario. L'eliminazione di pacchetti di applicazioni consente di recuperare spazio su disco. Per eliminare il pacchetto dell'applicazione dall'archivio immagini, usare il comando seguente:

```azurecli
az sf application package-delete --content-path app_package_dir
```

In questo caso, `content-path` deve essere lo stesso nome della directory che è stata caricata inizialmente durante la creazione dell'applicazione.

## <a name="related-articles"></a>Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introduzione all'interfaccia della riga di comando di XPlat per Service Fabric](service-fabric-azure-cli.md)

