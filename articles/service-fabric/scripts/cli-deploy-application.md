---
title: Esempio di distribuzione di script dell'interfaccia della riga di comando di Azure Service Fabric (sfctl)
description: Distribuire un'applicazione in un cluster di Azure Service Fabric usando l'interfaccia della riga di comando di Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 04/16/2018
ms.date: 04/29/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: f3ab32101a46c1044954f2efe88ac05ab81af24a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621999"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuire un'applicazione in un cluster di Service Fabric

Questo script di esempio copia un pacchetto dell'applicazione in un archivio immagini del cluster, registra il tipo di applicazione nel cluster e crea un'istanza di applicazione dal tipo di applicazione. Anche i servizi predefiniti vengono creati in questa fase.

Se necessario, installare l'[interfaccia della riga di comando di Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Script di esempio

```sh
#!/bin/bash

# Select cluster
sfctl cluster select \
    --endpoint http://svcfab1.chinanorth.cloudapp.chinacloudapi.cn:19080

# Upload the application files to the image store
# (note the last folder name, Debug in this example)
sfctl application upload \
    --path  C:\Code\svcfab-vs\svcfab-vs\pkg\Debug \
    --show-progress

# Register the application (manifest files) from the image store
# (Note the last folder from the previous command is used: Debug)
sfctl application provision \
    --application-type-build-path Debug \
    --timeout 500

# Create an instance of the registered application and 
# auto deploy any defined services
sfctl application create \
    --app-name fabric:/MyApp \
    --app-type MyAppType \
    --app-version 1.0.0

```

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Al termine, è possibile usare lo script [remove](cli-remove-application.md) per rimuovere l'applicazione. Lo script remove elimina l'istanza dell'applicazione, annulla la registrazione del tipo di applicazione ed elimina il pacchetto dell'applicazione dall'archivio immagini.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione sull'interfaccia della riga di comando di Service Fabric](../service-fabric-cli.md).

Altri esempi dell'interfaccia della riga di comando di Service Fabric per Azure Service Fabric sono disponibili negli [esempi dell'interfaccia della riga di comando di Service Fabric](../samples-cli.md).

<!--Update_Description: update meta properties, update link -->