---
title: Avvio rapido - Configurare il server di configurazione di Azure Spring Cloud
description: Descrive come configurare il server di configurazione di Azure Spring Cloud per la distribuzione di app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951662"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Avvio rapido: Configurare il server di configurazione di Azure Spring Cloud

Azure Spring Cloud Config Server è un servizio di configurazione centralizzato per i sistemi distribuiti. Usa un livello di repository collegabile che attualmente supporta l'archiviazione locale, Git e Subversion.  Configurare il server di configurazione per distribuire app di microservizi in Azure Spring Cloud.

## <a name="prerequisites"></a>Prerequisiti

* [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* (Facoltativo) [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e installare l'estensione Azure Spring Cloud con il comando `az extension add --name spring-cloud`
* (Facoltativo) [Installare Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ed [eseguire l'accesso](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedure del server di configurazione di Azure Spring Cloud

#### <a name="portal"></a>[Portale](#tab/Azure-portal)

La procedura seguente configura il server di configurazione usando il portale di Azure per distribuire l'[app di esempio Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Passare alla pagina **Panoramica** e selezionare **Config Server**.

2. Nella sezione **Repository predefinito** impostare **URI** su "https://github.com/Azure-Samples/piggymetrics-config".

3. Fare clic su **Applica** per salvare le modifiche.

    ![Screenshot del portale ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

La procedura seguente usa l'interfaccia della riga di comando di Azure per configurare il server di configurazione per distribuire l'[app di esempio Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Configurare il server di configurazione con la posizione del repository Git per il progetto:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Compilare e distribuire app](spring-cloud-quickstart-deploy-apps.md)
