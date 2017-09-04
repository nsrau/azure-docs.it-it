---
title: Esempio di distribuzione di script dell'interfaccia della riga di comando di Azure Service Fabric
description: Distribuire un'applicazione in un cluster di Azure Service Fabric usando l'interfaccia della riga di comando di Azure Service Fabric
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 74d54b314e81dc260e800e32d6c3edd5215c82b4
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuire un'applicazione in un cluster di Service Fabric

Questo script di esempio copia un pacchetto dell'applicazione in un archivio immagini del cluster, registra il tipo di applicazione nel cluster e crea un'istanza di applicazione dal tipo di applicazione. Anche i servizi predefiniti vengono creati in questa fase.

Se necessario, installare l'[interfaccia della riga di comando di Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Script di esempio

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Distribuire un'applicazione in un cluster")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Al termine, è possibile usare lo script [remove](cli-remove-application.md) per rimuovere l'applicazione. Lo script remove elimina l'istanza dell'applicazione, annulla la registrazione del tipo di applicazione ed elimina il pacchetto dell'applicazione dall'archivio immagini.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione sull'interfaccia della riga di comando di Service Fabric](../service-fabric-cli.md).

Altri esempi dell'interfaccia della riga di comando di Service Fabric per Azure Service Fabric sono disponibili negli [esempi dell'interfaccia della riga di comando di Service Fabric](../samples-cli.md).

