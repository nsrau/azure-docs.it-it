---
title: Avviare, arrestare ed eliminare l'applicazione Azure Spring cloud | Microsoft Docs
description: Come avviare, arrestare ed eliminare un'applicazione Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: ed3db5cfbdeb501e564a8da0020821c550a54787
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461642"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Avviare, arrestare ed eliminare l'applicazione Azure Spring cloud

Questa guida illustra come modificare lo stato di un'applicazione nel cloud Spring di Azure usando l'interfaccia della riga di portale di Azure o l'interfaccia della riga di comando di Azure.

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Dopo aver distribuito un'applicazione, è possibile avviarla, arrestarla ed eliminarla usando il portale di Azure.

1. Passare all'istanza del servizio Azure Spring Cloud nel portale di Azure.
1. Selezionare la scheda **Dashboard dell'applicazione**.
1. Selezionare l'applicazione di cui cambiare lo stato.
1. Nella pagina **Panoramica** relativa a tale applicazione selezionare **Start/Stop**, **Restart**o **Delete**.

## <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

> [!NOTE]
> È possibile usare parametri facoltativi e configurare le impostazioni predefinite con l'interfaccia della riga di comando di Azure. Per altre informazioni sull'interfaccia della riga di comando di Azure, leggere la [documentazione di riferimento](spring-cloud-cli-reference.md).  

Installare prima di tutto l'estensione Azure Spring cloud per l'interfaccia della riga di comando di Azure come indicato di seguito:

```azurecli
az extension add --name spring-cloud
```

Selezionare quindi una delle operazioni dell'interfaccia della riga di comando di Azure seguenti:

* Per avviare l'applicazione:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Per arrestare l'applicazione:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Per riavviare l'applicazione:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Per eliminare l'applicazione:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
