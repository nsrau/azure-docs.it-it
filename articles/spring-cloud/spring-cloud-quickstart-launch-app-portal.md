---
title: "Avvio rapido: Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure"
description: In questa guida di avvio rapido verrà distribuita un'applicazione in Azure Spring Cloud usando il portale di Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: e4ea76a888ba51b3560139e9efc3df512c4fbadf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120943"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Avvio rapido: Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure

Questo argomento di avvio rapido illustra come distribuire un'applicazione Spring Cloud esistente in Azure. Azure Spring Cloud consente di eseguire facilmente applicazioni di microservizi basate su Spring Cloud in Azure. 

Il codice dell'applicazione di esempio usato in questa esercitazione è reperibile nel [repository degli esempi di GitHub](https://github.com/Azure-Samples/PiggyMetrics). Al termine, l'applicazione di esempio sarà accessibile online e pronta per essere gestita tramite il portale di Azure.

Seguendo questo argomento di avvio rapido, si apprenderà come:

> [!div class="checklist"]
> * Effettuare il provisioning di un'istanza del servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Assegnare un endpoint pubblico per l'applicazione

## <a name="prerequisites"></a>Prerequisiti

>[!Note]
> Azure Spring Cloud è attualmente disponibile come anteprima pubblica. Le offerte di anteprima pubblica consentono ai clienti di sperimentare le nuove funzionalità prima del rilascio della versione ufficiale.  I servizi e le funzionalità di anteprima pubblica non sono destinati all'uso in produzione.  Per altre informazioni sul supporto durante le anteprime, vedere le [domande frequenti](https://azure.microsoft.com/support/faq/) o inviare una [richiesta di supporto](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Include i comuni strumenti di Azure preinstallati, tra cui le ultime versioni di Git, JDK, Maven e dell'interfaccia della riga di comando di Azure. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

Per completare questa guida introduttiva:

1. [Installare Git](https://git-scm.com/)
2. [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi)
4. [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Effettuare il provisioning di un'istanza del servizio nel portale di Azure

1. In una nuova scheda accedere al [portale di Azure](https://ms.portal.azure.com/). 

2. Nella casella di ricerca in alto cercare **Azure Spring Cloud**.

3. Selezionare **Azure Spring Cloud** nei risultati.

 ![Icona di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Nella pagina di Azure Spring Cloud fare clic su **+ Aggiungi**.

 ![Icona di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Compilare il modulo nella pagina di **creazione** di Azure Spring Cloud.  Considerare le linee guida seguenti:
    - **Sottoscrizione** selezionare la sottoscrizione da fatturare per questa risorsa.  Assicurarsi che la sottoscrizione sia stata aggiunta al nostro elenco di utenti autorizzati per Azure Spring Cloud.
    - **Gruppo di risorse**: è consigliabile creare un nuovo gruppo di risorse per le nuove risorse.
    - **Dettagli servizio/Nome**: specificare il nome dell'istanza del servizio.  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.
    - **Località**: selezionare la località per l'istanza del servizio. Le località attualmente supportate sono Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.

    ![Avvio del portale di Azure Spring Cloud](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Fare clic sulla scheda **Impostazioni di diagnostica** per aprire la finestra di dialogo seguente.

7. È possibile impostare **Abilita log** su *sì* o su *no* in base alle esigenze.

    ![Abilitare i log](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Fare clic sulla scheda **Traccia**.

9. È possibile impostare **Abilita traccia** su *sì* o su *no* in base alle esigenze.  Se si imposta **Abilita traccia** su sì, selezionare anche un'istanza esistente di Application Insights o crearne una nuova. Se non si specifica l'istanza di **Application Insights**, si verificherà un errore di convalida.


    ![Traccia](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Fare clic su **Rivedi e crea**.

11. Verificare le specifiche, quindi fare clic su **rea**.

Per distribuire il servizio sono necessari circa 5 minuti.  Una volta completata la distribuzione, verrà visualizzata la pagina **Panoramica** per l'istanza del servizio.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Configurare il server di configurazione

1. Passare alla pagina **Panoramica** e selezionare **Config Server**.

2. Nella sezione **Repository predefinito** impostare **URI** su "https://github.com/Azure-Samples/piggymetrics-config".

3. Fare clic su **Applica** per salvare le modifiche.

    ![Screenshot del portale ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Creare e distribuire applicazioni di microservizi

1. Aprire un'istanza di [Azure Cloud Shell](https://shell.azure.com) e clonare il repository dell'app di esempio nel computer locale.  Qui viene prima di tutto creata una directory temporanea denominata `source-code` prima di clonare l'app.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Compilare il pacchetto clonato.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Assegnare i nomi al gruppo di risorse e al servizio. Assicurarsi di sostituire i segnaposto con il nome del gruppo di risorse e il nome del servizio di cui è stato effettuato il provisioning in precedenza in questa esercitazione.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Creare l'applicazione `gateway` e distribuire il file JAR.  Per la procedura seguente è necessaria l'estensione Spring Cloud. Se non è stata installata con i prerequisiti, eseguire il comando seguente:

    ```azurecli
    az extension add --name spring-cloud
    ```

    Usando l'estensione Spring Cloud, creare l'app:

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Seguendo lo stesso modello, creare le applicazioni `account-service` e `auth-service` e distribuire i rispettivi file JAR.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Per completare la distribuzione delle applicazioni, sono necessari alcuni minuti. Per verificare se sono state distribuite, passare al pannello **App** nel portale di Azure. Dovrebbe essere visualizzata una riga per ognuna delle tre applicazioni.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Assegnare un endpoint pubblico al gateway

1. Nel menu a sinistra aprire la scheda **App**.

2. Selezionare l'applicazione `gateway` per visualizzare la pagina **Panoramica**.

3. Selezionare **Assign Endpoint** (Assegna endpoint) per assegnare un endpoint pubblico al gateway. L'operazione potrebbe richiedere alcuni minuti.

    ![Screenshot del portale ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Immettere l'IP pubblico assegnato (etichettato come **URL**) nel browser per visualizzare l'applicazione in esecuzione.

    ![Screenshot del portale ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

> [!div class="checklist"]
> * Effettuare il provisioning di un'istanza del servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Assegnare un endpoint pubblico per il gateway applicazione

> [!div class="nextstepaction"]
> [Preparare l'applicazione Azure Spring Cloud per la distribuzione](spring-cloud-tutorial-prepare-app-deployment.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
