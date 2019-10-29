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
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554566"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Guida introduttiva: Avviare un'app Azure Spring Cloud tramite il plug-in Maven

Con il plug-in Maven di Azure Spring Cloud è possibile creare e aggiornare facilmente le applicazioni del servizio Azure Spring Cloud. Con una configurazione predefinita, è possibile distribuire applicazioni nel servizio Azure Spring Cloud esistente. In questo articolo viene usata un'applicazione di esempio denominata PiggyMetrics per dimostrare questa funzionalità.

>[!Note]
> Prima di iniziare questa guida di avvio rapido, assicurarsi che la propria sottoscrizione di Azure abbia accesso ad Azure Spring Cloud. Essendo un servizio in anteprima, invitiamo i clienti a contattarci in modo che possiamo aggiungere la loro sottoscrizione al nostro elenco di utenti autorizzati. Per esplorare le funzionalità di Azure Spring Cloud, compilare e inviare il [modulo di interesse per Azure Spring Cloud (anteprima privata)](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire i comandi riportati in questo articolo. Include i comuni strumenti di Azure preinstallati, tra cui le ultime versioni di Git, Java Development Kit (JDK), Maven e dell'interfaccia della riga di comando di Azure. Se è stato effettuato l'accesso alla propria sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com). Per altre informazioni, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md).

Per completare questa guida introduttiva:

1. [Installare Git](https://git-scm.com/).
2. [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi).
4. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Iscriversi per ottenere una sottoscrizione gratuita di Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Effettuare il provisioning di un'istanza del servizio nel portale di Azure

1. In un Web browser aprire il [portale di Azure](https://portal.azure.com) e accedere con il proprio account.

1. Cercare e selezionare **Azure Spring Cloud**. 
1. Nella pagina Panoramica selezionare **Crea** e quindi eseguire le operazioni seguenti:  

    a. Nella casella **Nome servizio** specificare il nome dell'istanza del servizio. Il nome deve avere una lunghezza compresa tra 4 e 32 caratteri e può contenere solo lettere minuscole, numeri e trattini. Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.  

    b. Nel menu a discesa **Sottoscrizione** selezionare la sottoscrizione da fatturare per questa risorsa. Assicurarsi che la sottoscrizione sia stata aggiunta all'elenco di utenti autorizzati per Azure Spring Cloud.  

    c. Nella casella **Gruppo di risorse** creare un nuovo gruppo di risorse. La creazione di gruppi di risorse per le nuove risorse è una procedura consigliata.  

    d. Nell'elenco a discesa **Località** selezionare la località per l'istanza del servizio. Le località attualmente supportate sono Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.
    
Per distribuire il servizio sono necessari circa 5 minuti. Dopo la distribuzione, verrà visualizzata la pagina **Panoramica** dell'istanza del servizio.

## <a name="set-up-your-configuration-server"></a>Configurare il server di configurazione

1. Nella pagina **Panoramica** selezionare **Config Server** (Server di configurazione).
1. Nella sezione **Repository predefinito** impostare **URI** su **https://github.com/Azure-Samples/piggymetrics** , **Etichetta** su **config** e quindi selezionare **Applica** per salvare le modifiche.

## <a name="clone-and-build-the-sample-application-repository"></a>Clonare e creare il repository dell'applicazione di esempio

1. Clonare il repository Git eseguendo il comando seguente:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Cambiare directory e compilare il progetto eseguendo il comando seguente:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Generare e distribuire la configurazione di Azure Spring Cloud

1. Per consentire il funzionamento di Maven con Azure Spring Cloud, aggiungere il codice seguente al file *pom.xml* o *settings.xml*.

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

1. Generare una configurazione eseguendo il comando seguente:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Selezionare i moduli `gateway`, `auth-service` e `account-service`.

    b. Selezionare la sottoscrizione e il cluster del servizio Azure Spring Cloud.

    c. Nell'elenco dei progetti disponibili immettere il numero che corrisponde a `gateway` per assegnargli l'accesso pubblico.
    
    d. Verificare la configurazione.

1. Distribuire le app usando il comando seguente:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. È possibile accedere a PiggyMetrics usando l'URL specificato nell'output del comando precedente.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata distribuita un'applicazione Spring Cloud da un repository Maven. Per altre informazioni su Azure Spring Cloud, procedere con l'esercitazione sulla preparazione dell'app per la distribuzione.

> [!div class="nextstepaction"]
> [Preparare l'applicazione Azure Spring Cloud per la distribuzione](spring-cloud-tutorial-prepare-app-deployment.md)
