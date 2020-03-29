---
title: Avviare, arrestare ed eliminare l'applicazione Azure Spring Cloud Documenti Microsoft
description: Come avviare, arrestare ed eliminare un'applicazione Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276830"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Avviare, arrestare ed eliminare l'applicazione Azure Spring CloudStart, stop, and delete your Azure Spring Cloud application

Questa guida illustra come modificare lo stato di un'applicazione in Azure Spring Cloud usando il portale di Azure o l'interfaccia della riga di comando di Azure.This guide explains how to change an application's state in Azure Spring Cloud by using either the Azure portal or the Azure CLI.

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Dopo aver distribuito un'applicazione, è possibile avviarla, arrestarla ed eliminarla tramite il portale di Azure.After you deploy an application, you can start, stop, and delete it by using the Azure portal.

1. Passare all'istanza del servizio Azure Spring Cloud nel portale di Azure.
1. Selezionare la scheda **Dashboard dell'applicazione**.
1. Selezionare l'applicazione di cui cambiare lo stato.
1. Nella pagina **Panoramica** dell'applicazione selezionare **Avvia/Interrompi,** **Riavvia**o **Elimina**.

## <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

> [!NOTE]
> È possibile usare parametri facoltativi e configurare le impostazioni predefinite con l'interfaccia della riga di comando di Azure. Per altre informazioni sull'interfaccia della riga di comando di Azure, leggere [la documentazione di riferimento.](spring-cloud-cli-reference.md)  

Installare innanzitutto l'estensione Cloud di Primavera di Azure per l'interfaccia della riga di comando di Azure come segue:First, install the Azure Spring Cloud extension for the Azure CLI as follows:

```azurecli
az extension add --name spring-cloud
```

Selezionare quindi una delle operazioni dell'interfaccia della riga di comando di Azure:Next, select any of these Azure CLI operations:

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
