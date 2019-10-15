---
title: Come avviare, arrestare ed eliminare un'applicazione Azure Spring Cloud | Microsoft Docs
description: Come avviare, arrestare ed eliminare un'applicazione Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038440"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Come avviare, arrestare ed eliminare un'applicazione Azure Spring Cloud

Questa guida illustra come cambiare lo stato di un'applicazione in Azure Spring Cloud usando il portale di Azure o l'interfaccia della riga di comando di Azure.

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Dopo aver distribuito un'applicazione, è possibile usare i comandi **Avvia**, **Arresta** ed **Elimina** nel portale di Azure.

1. Passare all'istanza del servizio Azure Spring Cloud nel portale di Azure.
1. Selezionare la scheda **Dashboard dell'applicazione**.
1. Selezionare l'applicazione di cui cambiare lo stato.
2. Nella pagina **Panoramica** di questa applicazione trovare i pulsanti **Avvia/Arresta**, **Riavvia** ed **Elimina**.

## <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

> [!NOTE]
> È possibile usare parametri facoltativi e configurare le impostazioni predefinite con l'interfaccia della riga di comando di Azure. Per informazioni, vedere la [documentazione di riferimento](spring-cloud-cli-reference.md).

* Per avviare l'applicazione:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Per arrestare l'applicazione:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Per riavviare l'applicazione:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Per eliminare l'applicazione:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
