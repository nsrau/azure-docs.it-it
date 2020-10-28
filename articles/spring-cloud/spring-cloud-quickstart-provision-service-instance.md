---
title: Avvio rapido - Effettuare il provisioning del servizio Azure Spring Cloud
description: Descrive la creazione di un'istanza del servizio Azure Spring Cloud per la distribuzione di app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 22abdb64a2349045923e148db57f683b4be87636
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742709"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Avvio rapido: Effettuare il provisioning del servizio Azure Spring Cloud

::: zone pivot="programming-language-csharp"
In questo argomento di avvio rapido si usa l'interfaccia della riga di comando di Azure per effettuare il provisioning di un'istanza del servizio Azure Spring Cloud.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Il servizio Azure Spring Cloud supporta .NET Core 3.1 e versioni successive.
* [Interfaccia della riga di comando di Azure versione 2.0.67 o successiva](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.0.67 o successiva:

```azurecli
az --version
```

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Accedere ad Azure

1. Accedere all'interfaccia della riga di comando di Azure.

    ```azurecli
    az login
    ```

1. Se sono disponibili più sottoscrizioni, scegliere quella che si vuole usare per questo argomento di avvio rapido.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Effettuare il provisioning di un'istanza di Azure Spring Cloud

1. Creare un [gruppo di risorse](../azure-resource-manager/management/overview.md) in cui includere il servizio Azure Spring Cloud. Il gruppo di risorse può includere caratteri alfanumerici, di sottolineatura e Unicode, parentesi, trattino e punto (tranne alla fine).

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. Effettuare il provisioning di un'istanza del servizio Azure Spring Cloud. Il nome dell'istanza del servizio deve essere univoco, composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini. Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Il completamento di questo comando potrebbe richiedere alcuni minuti.

1. Impostare il nome del gruppo di risorse predefinito e il nome dell'istanza del servizio per non dover specificare più volte questi valori nei comandi successivi.

   ```azurecli
   az configure --defaults group=<resource group name>
   ```

   ```azurecli
   az configure --defaults spring-cloud=<service instance name>
   ```
::: zone-end

::: zone pivot="programming-language-java"
È possibile creare un'istanza di Azure Spring Cloud usando il portale di Azure o l'interfaccia della riga di comando di Azure.  I due metodi sono illustrati nelle procedure seguenti.
## <a name="prerequisites"></a>Prerequisiti

* [Installare JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* (Facoltativo) [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) e installare l'estensione Azure Spring Cloud con il comando `az extension add --name spring-cloud`
* (Facoltativo) [Installare Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ed [eseguire l'accesso](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Effettuare il provisioning di un'istanza di Azure Spring Cloud

#### <a name="portal"></a>[Portale](#tab/Azure-portal)

Nella procedura seguente viene creata un'istanza di Azure Spring Cloud usando il portale di Azure.

1. In una nuova scheda accedere al [portale di Azure](https://ms.portal.azure.com/). 

2. Nella casella di ricerca in alto cercare **Azure Spring Cloud** .

3. Selezionare **Azure Spring Cloud** nei risultati.

    ![Icona di avvio di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Nella pagina di Azure Spring Cloud fare clic su **+ Aggiungi** .

    ![Icona Aggiungi di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Compilare il modulo nella pagina di **creazione** di Azure Spring Cloud.  Considerare le linee guida seguenti:
    - **Sottoscrizione** selezionare la sottoscrizione da fatturare per questa risorsa.
    - **Gruppo di risorse** : è consigliabile creare un nuovo gruppo di risorse per le nuove risorse. Si noti che verrà usato in passaggi successivi come **\<resource group name\>** .
    - **Dettagli servizio/Nome** : specificare il **\<service instance name\>** .  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.
    - **Località** : selezionare la località per l'istanza del servizio.

    ![Avvio del portale di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Fare clic su **Rivedi e crea** .

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

La procedura seguente usa l'estensione dell'interfaccia della riga di comando di Azure per effettuare il provisioning di un'istanza di Azure Spring Cloud.

1. Accedere all'interfaccia della riga di comando di Azure e scegliere una sottoscrizione attiva.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Preparare un nome per il servizio Azure Spring Cloud.  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.

1. Creare un gruppo di risorse in cui includere il servizio Azure Spring Cloud.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/management/overview.md).

1. Aprire una finestra dell'interfaccia della riga di comando di Azure ed eseguire i comandi seguenti per effettuare il provisioning di un'istanza di Azure Spring Cloud.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    La distribuzione dell'istanza del servizio richiede circa cinque minuti.
---
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono state create risorse di Azure per cui continueranno a essere addebitati i costi se rimangono nella sottoscrizione. Se non si intende continuare con la guida di avvio rapido successiva, vedere [Pulire le risorse](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). In caso contrario, passare alla guida di avvio rapido successiva:

> [!div class="nextstepaction"]
> [Configurare un server di configurazione](spring-cloud-quickstart-setup-config-server.md)
