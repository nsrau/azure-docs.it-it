---
title: Gestire le applicazioni di Azure Service Fabric usando l'interfaccia della riga di comando di Azure Service Fabric
description: Informazioni su come distribuire e rimuovere le applicazioni da un cluster Azure Service Fabric usando l'interfaccia della riga di comando di Azure Service Fabric.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 08/22/2017
ms.author: edwardsa
ms.openlocfilehash: 6c8501e8c863e1240a277bf8064118a2b113a81b
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>Gestire un'applicazione di Azure Service Fabric usando l'interfaccia della riga di comando di Azure Service Fabric

Informazioni su come creare ed eliminare applicazioni in esecuzione in un cluster Azure Service Fabric.

## <a name="prerequisites"></a>prerequisiti

* Installare l'interfaccia della riga di comando di Service Fabric. Selezionare il cluster Service Fabric. Per altre informazioni, vedere [Introduzione all'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md).

* È necessario avere un pacchetto dell'applicazione di Service Fabric pronto per essere distribuito. Per altre informazioni su come creare un'applicazione e inserirla in un pacchetto, vedere l'articolo sul [modello di applicazione di Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Panoramica

Per distribuire una nuova applicazione, completare questi passaggi:

1. Caricare un pacchetto dell'applicazione nell'archivio immagini di Service Fabric.
2. Eseguire il provisioning di un tipo di applicazione.
3. Eliminare il contenuto dell'archivio immagini.
4. Specificare e creare un'applicazione.
5. Specificare e creare i servizi.

Per rimuovere un'applicazione esistente, completare questi passaggi:

1. Eliminare l'applicazione.
2. Annullare il provisioning del tipo di applicazione associato.

## <a name="deploy-a-new-application"></a>Distribuire un'applicazione nuova

Per distribuire una nuova applicazione, completare le attività seguenti:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Caricare un nuovo pacchetto dell'applicazione nell'archivio immagini

Prima di creare un'applicazione, caricare il pacchetto dell'applicazione nell'archivio immagini di Service Fabric.

Ad esempio, se il pacchetto dell'applicazione è nella directory `app_package_dir`, usare i comandi seguenti per caricare la directory:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Per i pacchetti di applicazione di grandi dimensioni è possibile specificare l'opzione `--show-progress` per visualizzare lo stato del caricamento.

### <a name="provision-the-application-type"></a>Eseguire il provisioning del tipo di applicazione

Al termine del caricamento eseguire il provisioning dell'applicazione. Per eseguire il provisioning dell'applicazione, usare il comando seguente:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Il valore per `application-type-build-path` è il nome della directory in cui è stato caricato il pacchetto dell'applicazione.

### <a name="delete-the-application-package"></a>Eliminare il pacchetto dell'applicazione

Al termine della registrazione dell'applicazione, è consigliabile rimuovere il pacchetto dell'applicazione.  L'eliminazione dei pacchetti di applicazioni dall'archivio immagini consente di liberare risorse di sistema.  Conservando pacchetti inutilizzati, viene occupato spazio di archiviazione su disco e si verificano problemi di prestazioni delle applicazioni. 

Per eliminare il pacchetto dell'applicazione dall'archivio immagini, usare il comando seguente:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` deve essere il nome della directory caricata al momento della creazione dell'applicazione.

### <a name="create-an-application-from-an-application-type"></a>Creare un'applicazione da un tipo di applicazione

Dopo il provisioning dell'applicazione, usare il comando seguente per assegnare un nome e creare l'applicazione:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` è il nome da usare per l'istanza di applicazione. È possibile ottenere parametri aggiuntivi dal manifesto dell'applicazione di cui è stato eseguito il provisioning.

Il nome dell'applicazione deve iniziare con il prefisso `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Creare i servizi per la nuova applicazione

Dopo avere creato un'applicazione, creare i servizi dall'applicazione. Nell'esempio seguente si crea dall'applicazione un nuovo servizio senza stato. I servizi che è possibile creare da un'applicazione sono definiti in un manifesto del servizio all'interno del pacchetto dell'applicazione di cui in precedenza è stato eseguito il provisioning.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verificare l'integrità e la distribuzione dell'applicazione

Per verificare l'integrità completa, usare i seguenti comandi di integrità:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Per verificare l'integrità del servizio, usare comandi simili per recuperare lo stato sia del servizio che dell'applicazione:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

I servizi e le applicazioni integri devono avere il valore `HealthState` impostato su `Ok`.

## <a name="remove-an-existing-application"></a>Rimuovere un'applicazione esistente

Per rimuovere un'applicazione, completare le attività seguenti:

### <a name="delete-the-application"></a>Eliminare l'applicazione

Per eliminare l'applicazione, usare il comando seguente:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Annullare il provisioning del tipo di applicazione

Dopo aver eliminato l'applicazione, è possibile annullare il provisioning del tipo di applicazione se non è più necessario. Per annullare il provisioning del tipo di applicazione, usare il comando seguente:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Il nome del tipo e la versione del tipo devono corrispondere al nome e alla versione indicati nel manifesto dell'applicazione di cui in precedenza è stato eseguito il provisioning.

## <a name="upgrade-application"></a>Aggiornare l'applicazione

Dopo avere creato l'applicazione, è possibile ripetere lo stesso set di passaggi per eseguire il provisioning di una seconda versione dell'applicazione. Con un aggiornamento dell'applicazione Service Fabric è possibile passare a eseguire la seconda versione dell'applicazione. Per altre informazioni, vedere la documentazione su [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md).

Per eseguire un aggiornamento, eseguire prima il provisioning della versione successiva dell'applicazione usando gli stessi comandi, come indicato in precedenza:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Si consiglia quindi di eseguire un aggiornamento automatico monitorato, avviare l'aggiornamento usando il comando seguente:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Gli aggiornamenti eseguono l'override dei parametri esistenti con il set specificato. I parametri dell'applicazione devono essere passati come argomenti al comando di aggiornamento, se necessario. I parametri dell'applicazione devono essere codificati come oggetto JSON.

Per recuperare tutti i parametri specificati in precedenza, è possibile usare il comando `sfctl application info`.

Quando un aggiornamento dell'applicazione è in corso, è possibile recuperare lo stato tramite il comando `sfctl application upgrade-status`.

Infine, se un aggiornamento è in corso e deve essere annullato, è possibile usare `sfctl application upgrade-rollback` per eseguire il rollback dell'aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

* [Azure Service Fabric command line](service-fabric-cli.md) (Riga di comando di Service Fabric)
* [Introduzione a Service Fabric in Linux](service-fabric-get-started-linux.md)
* [Service Fabric application upgrade](service-fabric-application-upgrade.md) (Aggiornamento di un'applicazione Service Fabric)
