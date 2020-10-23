---
title: Avvio rapido - Configurare il server di configurazione di Azure Spring Cloud
description: Descrive come configurare il server di configurazione di Azure Spring Cloud per la distribuzione di app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2f6051277f1ddb89e67ce8013c78571a2a7314b7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089129"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Avvio rapido: Configurare il server di configurazione di Azure Spring Cloud

Azure Spring Cloud Config Server è un servizio di configurazione centralizzato per i sistemi distribuiti. Usa un livello di repository collegabile che attualmente supporta l'archiviazione locale, Git e Subversion. In questo argomento di avvio rapido viene configurato il server di configurazione per ottenere i dati da un repository Git.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Prerequisiti

* Completare l'argomento di avvio rapido precedente di questa serie: [Effettuare il provisioning del servizio Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedure del server di configurazione di Azure Spring Cloud

Configurare il server di configurazione con la posizione del repository Git per il progetto eseguendo il comando seguente. Sostituire `<service instance name>` con il nome del servizio creato in precedenza. Il valore predefinito del nome dell'istanza del servizio impostato nell'argomento di avvio rapido precedente non funziona con questo comando.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Questo comando indica al server di configurazione di trovare i dati di configurazione nella cartella [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) del repository di app di esempio. Poiché il nome dell'app che otterrà i dati di configurazione è `planet-weather-provider`, il file che verrà usato è [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud Config Server è un servizio di configurazione centralizzato per i sistemi distribuiti. Usa un livello di repository collegabile che attualmente supporta l'archiviazione locale, Git e Subversion.  Configurare il server di configurazione per distribuire app di microservizi in Azure Spring Cloud.

## <a name="prerequisites"></a>Prerequisiti

* [Installare JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* (Facoltativo) [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) e installare l'estensione Azure Spring Cloud con il comando `az extension add --name spring-cloud`
* (Facoltativo) [Installare Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ed [eseguire l'accesso](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

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
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono state create risorse di Azure per cui continueranno a essere addebitati i costi se rimangono nella sottoscrizione. Se non si intende continuare con la guida di avvio rapido successiva, vedere [Pulire le risorse](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). In caso contrario, passare alla guida di avvio rapido successiva:

> [!div class="nextstepaction"]
> [Compilare e distribuire app](spring-cloud-quickstart-deploy-apps.md)