---
title: Come usare l'archiviazione persistente in Azure Spring Cloud | Microsoft Docs
description: Come usare l'archiviazione persistente in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76278526"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Usare l'archiviazione persistente in Azure Spring Cloud

Azure Spring cloud fornisce due tipi di archiviazione per l'applicazione: permanente e temporanea.

Per impostazione predefinita, Azure Spring cloud fornisce l'archiviazione temporanea per ogni istanza dell'applicazione. L'archiviazione temporanea è limitata a 5 GB per istanza con il percorso di montaggio predefinito/TMP.

> [!WARNING]
> Se si riavvia un'istanza dell'applicazione, l'archiviazione temporanea associata viene eliminata definitivamente.

L'archiviazione persistente è un contenitore di condivisione file gestito da Azure e allocato per ogni applicazione. I dati archiviati nell'archivio permanente sono condivisi da tutte le istanze di un'applicazione. Un'istanza del cloud Spring di Azure può avere un massimo di 10 applicazioni con archiviazione permanente abilitata. A ogni applicazione sono allocati 50 GB di spazio di archiviazione permanente. Il percorso di montaggio predefinito per l'archiviazione persistente è/persistent.

> [!WARNING]
> Se si disabilita l'archiviazione persistente di un'applicazione, tutte le archiviazioni vengono deallocate e tutti i dati archiviati vengono persi.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Usare il portale di Azure per abilitare l'archiviazione permanente

1. Dalla **Home** page del portale di Azure selezionare **tutte le risorse**.

    >![Individuare l'icona tutte le risorse](media/portal-all-resources.jpg)

1. Selezionare la risorsa cloud di Azure Spring che necessita di archiviazione permanente. In questo esempio, l'applicazione selezionata viene chiamata **Spring**.

    > ![Selezionare l'applicazione.](media/select-service.jpg)

1. Sotto l'intestazione **Impostazioni** selezionare **app**.

1. I servizi cloud Spring di Azure vengono visualizzati in una tabella.  Selezionare il servizio a cui si desidera aggiungere l'archiviazione permanente. In questo esempio è selezionato il servizio **gateway** .

    > ![Selezionare il servizio](media/select-gateway.jpg)

1. Dalla pagina Configurazione del servizio selezionare **configurazione** .

1. Selezionare la scheda **archiviazione permanente** e selezionare **Abilita**.

    > ![Abilita archiviazione permanente](media/enable-persistent-storage.jpg)

Una volta abilitata l'archiviazione persistente, le dimensioni e il percorso vengono visualizzati nella pagina di configurazione.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Usare l'interfaccia della riga di comando di Azure per modificare l'archiviazione permanente

Se necessario, installare l'estensione Spring cloud per l'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name spring-cloud
```
Altre operazioni:

* Per creare un'app con archiviazione permanente abilitata:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Per abilitare l'archiviazione permanente per un'app esistente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Per disabilitare l'archiviazione permanente in un'app esistente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Se si disabilita l'archiviazione persistente di un'applicazione, tutte le archiviazioni vengono deallocate e tutti i dati archiviati vengono persi definitivamente.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [quote di applicazioni e servizi](spring-cloud-quotas.md).
* Informazioni su come [ridimensionare manualmente l'applicazione](spring-cloud-tutorial-scale-manual.md).
