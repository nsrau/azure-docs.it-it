---
title: Avvio rapido - Effettuare il provisioning del servizio Azure Spring Cloud
description: Descrive la creazione di un'istanza del servizio Azure Spring Cloud per la distribuzione di app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951673"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Avvio rapido: Effettuare il provisioning del servizio Azure Spring Cloud

È possibile creare un'istanza di Azure Spring Cloud usando il portale di Azure o l'interfaccia della riga di comando di Azure.  I due metodi sono illustrati nelle procedure seguenti.
## <a name="prerequisites"></a>Prerequisiti

* [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* (Facoltativo) [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e installare l'estensione Azure Spring Cloud con il comando `az extension add --name spring-cloud`
* (Facoltativo) [Installare Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ed [eseguire l'accesso](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Effettuare il provisioning di un'istanza di Azure Spring Cloud

#### <a name="portal"></a>[Portale](#tab/Azure-portal)

Nella procedura seguente viene creata un'istanza di Azure Spring Cloud usando il portale di Azure.

1. In una nuova scheda accedere al [portale di Azure](https://ms.portal.azure.com/). 

2. Nella casella di ricerca in alto cercare **Azure Spring Cloud**.

3. Selezionare **Azure Spring Cloud** nei risultati.

    ![Icona di avvio di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Nella pagina di Azure Spring Cloud fare clic su **+ Aggiungi**.

    ![Icona Aggiungi di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Compilare il modulo nella pagina di **creazione** di Azure Spring Cloud.  Considerare le linee guida seguenti:
    - **Sottoscrizione** selezionare la sottoscrizione da fatturare per questa risorsa.
    - **Gruppo di risorse**: è consigliabile creare un nuovo gruppo di risorse per le nuove risorse. Si noti che verrà usato in passaggi successivi come **\<resource group name\>** .
    - **Dettagli servizio/Nome**: specificare il **\<service instance name\>** .  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.
    - **Località**: selezionare la località per l'istanza del servizio.

    ![Avvio del portale di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Fare clic su **Rivedi e crea**.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

La procedura seguente usa l'estensione dell'interfaccia della riga di comando di Azure per effettuare il provisioning di un'istanza di Azure Spring Cloud.

1. Accedere all'interfaccia della riga di comando di Azure e scegliere una sottoscrizione attiva. Assicurarsi di scegliere una sottoscrizione attiva autorizzata per Azure Spring Cloud

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

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Configurare un server di configurazione](spring-cloud-quickstart-setup-config-server.md)


