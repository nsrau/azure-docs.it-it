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
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885710"
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

* [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* (Facoltativo) [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) e installare l'estensione Azure Spring Cloud con il comando `az extension add --name spring-cloud`
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
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare con l'argomento di avvio rapido successivo di questa serie, ignorare questo passaggio.

In questi argomenti di avvio rapido sono state create risorse di Azure per cui continueranno a essere addebitati i costi se rimangono nella sottoscrizione. Se non si prevede di continuare con l'argomento di avvio rapido successivo e si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse usando il portale o eseguendo questo comando in Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

In un argomento di avvio rapido precedente è stato anche impostato il nome del gruppo di risorse predefinito. Se non si prevede di continuare con l'argomento di avvio rapido successivo, cancellare tale impostazione predefinita eseguendo il comando dell'interfaccia della riga di comando seguente:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare e distribuire app](spring-cloud-quickstart-deploy-apps.md)
