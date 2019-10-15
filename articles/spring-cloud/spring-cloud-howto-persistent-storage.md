---
title: Come usare l'archiviazione persistente in Azure Spring Cloud | Microsoft Docs
description: Come usare l'archiviazione persistente in Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038450"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Come usare l'archiviazione persistente in Azure Spring Cloud

Azure Spring Cloud prevede due tipi di archiviazione per l'applicazione: persistente e temporanea.  Per impostazione predefinita, Azure Spring Cloud abilita l'archiviazione temporanea per ogni istanza dell'applicazione. L'archiviazione temporanea è limitata a 5 GB e il percorso di montaggio predefinito è `/tmp`.

> [!WARNING]
> Il riavvio di un'istanza dell'applicazione elimina definitivamente l'archiviazione temporanea associata.

L'archiviazione persistente è un contenitore di condivisioni file gestito da Azure e allocato in base all'ambito dell'applicazione. I dati archiviati nell'archiviazione persistente vengono condivisi tra tutte le istanze dell'applicazione. Un'istanza del servizio Azure Spring Cloud può includere un massimo di 10 applicazioni con il disco persistente abilitato e a ogni applicazione vengono assegnati 50 GB di archiviazione persistente. Il percorso di montaggio predefinito per l'archiviazione persistente è `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Abilitare l'archiviazione persistente tramite il portale di Azure

1. Nella pagina del servizio Azure Spring Cloud selezionare **Dashboard dell'applicazione**, quindi selezionare l'applicazione che richiede l'archiviazione persistente.
1. Nella scheda **Panoramica** individuare l'attributo **Persistent Storage** (Archiviazione persistente) e selezionare **Disabilitato**.
1. Fare clic su **Abilita** per abilitare l'archiviazione persistente, quindi fare clic su **OK** per applicare la modifica.

Se l'archiviazione persistente è abilitata, le relative dimensioni e il percorso sono indicati nell'attributo **Persistent Storage** (Archiviazione persistente) della pagina **Panoramica**.

> [!WARNING]
> Se si *disabilita* l'archiviazione persistente, l'archiviazione per tale applicazione verrà deallocata.  Tutti i dati inclusi nell'account di archiviazione andranno persi. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Abilitare l'archiviazione persistente tramite l'interfaccia della riga di comando di Azure

Creare un'app con il disco persistente abilitato:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Abilitare l'archiviazione persistente in un'app esistente:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Disabilitare l'archiviazione persistente in un'app esistente:

> [!WARNING]
> Se si disabilita l'archiviazione persistente, l'archiviazione per tale applicazione verrà deallocata e i dati archiviati al suo interno andranno persi definitivamente. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Passaggi successivi

Leggere informazioni sulle [quote di applicazioni e servizio](spring-cloud-quotas.md) o su come [ridimensionare manualmente l'applicazione](spring-cloud-tutorial-scale-manual.md).