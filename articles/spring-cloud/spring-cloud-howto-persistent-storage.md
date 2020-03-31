---
title: Come usare l'archiviazione persistente in Azure Spring Cloud | Microsoft Docs
description: Come usare l'archiviazione persistente in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278526"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Usare l'archiviazione persistente in Azure Spring Cloud

Azure Spring Cloud offre due tipi di archiviazione per l'applicazione: persistente e temporanea.

Per impostazione predefinita, Azure Spring Cloud offre archiviazione temporanea per ogni istanza dell'applicazione. L'archiviazione temporanea è limitata a 5 GB per istanza con il percorso di montaggio predefinito /tmp.

> [!WARNING]
> Se si riavvia un'istanza dell'applicazione, l'archiviazione temporanea associata viene eliminata definitivamente.

L'archiviazione permanente è un contenitore di condivisione file gestito da Azure e allocato per applicazione. I dati archiviati in un archivio permanente vengono condivisi da tutte le istanze di un'applicazione. Un'istanza di Azure Spring Cloud può avere un massimo di 10 applicazioni con l'archiviazione permanente abilitata. A ogni applicazione vengono allocati 50 GB di spazio di archiviazione permanente. Il percorso di montaggio predefinito per l'archiviazione permanente è /persistent.

> [!WARNING]
> Se si disabilita l'archiviazione permanente di un'applicazione, tutta l'archiviazione viene deallocata e tutti i dati archiviati vengono persi.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Usare il portale di Azure per abilitare l'archiviazione permanenteUse the Azure portal to enable persistent storage

1. Nella **home** page del portale di Azure selezionare **Tutte le risorse.**

    >![Individuare l'icona Tutte le risorse](media/portal-all-resources.jpg)

1. Selezionare la risorsa Cloud di Primavera di Azure che richiede un'archiviazione permanente. In questo esempio, l'applicazione selezionata viene chiamata **upspring**.

    > ![Selezionare l'applicazione.](media/select-service.jpg)

1. Sotto l'intestazione **Impostazioni** selezionare **App**.

1. I servizi di Azure Spring Cloud vengono visualizzati in una tabella.  Selezionare il servizio a cui si desidera aggiungere l'archiviazione permanente. In questo esempio viene selezionato il servizio **gateway.**

    > ![Selezionare il servizio](media/select-gateway.jpg)

1. Nella pagina di configurazione del servizio, selezionare **Configurazione**

1. Selezionare la scheda **Archiviazione permanente** e selezionare **Abilita**.

    > ![Abilitare l'archiviazione permanenteEnable persistent storage](media/enable-persistent-storage.jpg)

Dopo aver abilitato l'archiviazione permanente, le dimensioni e il percorso vengono visualizzati nella pagina di configurazione.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Usare l'interfaccia della riga di comando di Azure per modificare l'archiviazione permanenteUse the Azure CLI to modify persistent storage

Se necessario, installare l'estensione Spring Cloud per l'interfaccia della riga di comando di Azure:If necessary, install the Spring Cloud extension for the Azure CLI:

```azurecli
az extension add --name spring-cloud
```
Altre operazioni:

* Per creare un'app con l'archiviazione permanente abilitata:

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
    > Se si disabilita l'archiviazione permanente di un'applicazione, tutta l'archiviazione viene deallocata e tutti i dati archiviati vengono persi in modo permanente.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle quote di [applicazioni e servizi](spring-cloud-quotas.md).
* Informazioni su come [ridimensionare manualmente l'applicazione.](spring-cloud-tutorial-scale-manual.md)
