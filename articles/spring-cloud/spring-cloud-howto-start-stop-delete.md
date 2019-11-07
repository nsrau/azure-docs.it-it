---
title: Come avviare, arrestare ed eliminare l'applicazione Azure Spring cloud | Microsoft Docs
description: Come avviare, arrestare ed eliminare l'applicazione Azure Spring cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607731"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Come avviare, arrestare ed eliminare l'applicazione Azure Spring cloud

Questa guida illustra come modificare lo stato di un'applicazione nel cloud Spring di Azure usando il portale di Azure o l'interfaccia della riga di comando.

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Una volta distribuita un'applicazione, è possibile **avviarla**, **arrestarla**ed **eliminarla** usando il portale di Azure.

1. Passare all'istanza del servizio cloud di Azure Spring nel portale di Azure.
1. Selezionare la scheda **Dashboard dell'applicazione** .
1. Selezionare l'applicazione di cui si desidera modificare lo stato.
2. Nella pagina **Panoramica** dell'applicazione trovare i pulsanti per avviare, **arrestare**, **riavviare**ed **eliminare** l'applicazione.

## <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

> [!NOTE]
> È possibile usare i parametri facoltativi e configurare le impostazioni predefinite con l'interfaccia della riga di comando di Azure. Per ulteriori informazioni, leggere la [documentazione di riferimento](spring-cloud-cli-reference.md).  

Installare l'estensione cloud Spring per l'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name spring-cloud
```

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
