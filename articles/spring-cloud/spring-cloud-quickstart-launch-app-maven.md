---
title: "Guida introduttiva: Avviare un'applicazione tramite Maven - Azure Spring Cloud"
description: Avviare un'applicazione di esempio tramite Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166509"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Guida introduttiva: Avviare un'app Azure Spring Cloud tramite il plug-in Maven

Il plug-in Maven di Azure Spring Cloud consente di creare e aggiornare facilmente le applicazioni del servizio Azure Spring Cloud. Con una configurazione predefinita, è possibile distribuire applicazioni nel servizio Azure Spring Cloud esistente. In questo articolo viene usata un'applicazione di esempio denominata PiggyMetrics per dimostrare questa funzionalità.

>[!Note]
> Prima di iniziare l'argomento di avvio rapido, assicurarsi che la propria sottoscrizione di Azure abbia accesso ad Azure Spring Cloud.  Essendo un servizio in anteprima, chiediamo ai clienti di contattarci in modo che possiamo aggiungere la loro sottoscrizione al nostro elenco di utenti autorizzati.  Se si desidera esplorare le funzionalità di Azure Spring Cloud, [compilare questo modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Include i comuni strumenti di Azure preinstallati, tra cui le ultime versioni di Git, JDK, Maven e dell'interfaccia della riga di comando di Azure. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

Per completare questa guida introduttiva:

1. [Installare Git](https://git-scm.com/)
2. [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi)
4. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Effettuare il provisioning di un'istanza del servizio nel portale di Azure

1. In un Web browser aprire il [portale di Azure](https://portal.azure.com) e accedere con il proprio account.

1. Cercare l'opzione **Azure Spring Cloud** e selezionarla per passare alla pagina di panoramica. Selezionare il pulsante **Crea** per iniziare.

1. Compilare il modulo, considerando le indicazioni seguenti:
    - Nome servizio: specificare il nome dell'istanza del servizio.  Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere in minuscolo, numeri e trattini.  Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.
    - Sottoscrizione: selezionare la sottoscrizione da fatturare per questa risorsa.  Assicurarsi che la sottoscrizione sia stata aggiunta al nostro elenco di utenti autorizzati per Azure Spring Cloud.
    - Gruppo di risorse: è consigliabile creare un nuovo gruppo di risorse per le nuove risorse.
    - Percorso: selezionare la località per l'istanza del servizio. Le località attualmente supportate sono Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.
    
Per distribuire il servizio sono necessari circa 5 minuti.  Una volta completata la distribuzione, verrà visualizzata la pagina **Panoramica** per l'istanza del servizio.

## <a name="set-up-your-configuration-server"></a>Configurare il server di configurazione

1. Passare alla pagina **Panoramica** e selezionare **Config Server**.
1. Nella sezione **Repository predefinito** impostare **URI** su "https://github.com/Azure-Samples/piggymetrics", **LABEL** su "config" e selezionare **Applica** per salvare le modifiche.

## <a name="clone-and-build-the-sample-application-repository"></a>Clonare e creare il repository dell'applicazione di esempio

1. Clonare il repository Git eseguendo il comando seguente.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Cambiare directory e creare il progetto eseguendo il comando seguente.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Generare e distribuire la configurazione di Azure Spring Cloud

1. Aggiungere quanto segue al file `pom.xml` o `settings.xml` per consentire l'interazione tra Maven e Azure Spring Cloud.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Generare una configurazione eseguendo il comando seguente.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Selezionare i moduli `gateway`, `auth-service` e `account-service`.

    1. Selezionare la sottoscrizione e il cluster del servizio Azure Spring Cloud.

    1. Nell'elenco di progetti immettere il numero che corrisponde a `gateway` per assegnargli l'accesso pubblico.
    
    1. Verificare la configurazione.

1. Distribuire le app usando il comando seguente:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. È possibile accedere a PiggyMetrics usando l'URL specificato nell'output del comando precedente.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata distribuita un'applicazione Spring Cloud da un repository Maven.  Per altre informazioni su Azure Spring Cloud, procedere con l'esercitazione sulla preparazione dell'app per la distribuzione.

> [!div class="nextstepaction"]
> [Preparare l'applicazione Azure Spring Cloud per la distribuzione](spring-cloud-tutorial-prepare-app-deployment.md)
