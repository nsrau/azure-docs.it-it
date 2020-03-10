---
title: 'Avvio rapido: Creare ed eseguire il push delle immagini del contenitore Java in Registro Azure Container usando Maven e Jib'
description: Creare un'app Java in contenitori ed eseguirne il push in Registro Azure Container usando il plug-in Jib di Maven.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 62d63b24baab204cb029565b109ea2de768e1d80
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165087"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Guida introduttiva: Creare ed eseguire il push di immagini del contenitore Java in Registro Azure Container

Questo argomento di avvio rapido illustra come creare un'app Java in contenitori ed eseguirne il push in Registro Azure Container con il plug-in Jib di Maven. Maven e Jib sono esempi dell'uso degli strumenti di sviluppo per interagire con un registro contenitori di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure; se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account Azure gratuito](https://azure.microsoft.com/pricing/free-trial).
* [Interfaccia della riga di comando di Azure](/cli/azure/overview).
* Java Development Kit (JDK) supportato. Per altre informazioni sulle versioni di JDK utilizzabili per lo sviluppo in Azure, vedere <https://aka.ms/azure-jdks>.
* Strumento di compilazione [Maven](http://maven.apache.org) di Apache (versione 3 o successiva).
* Un client [Git](https://git-scm.com).
* Un client [Docker](https://www.docker.com).
* L'[helper per le credenziali Docker di Registro Azure Container](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Creare l'app Web introduttiva di Spring Boot in Docker

La procedura seguente illustra come creare un'applicazione Web di Spring Boot e come testarla in locale.

1. Al prompt dei comandi usare il comando seguente per clonare il progetto di esempio [Spring Boot on Docker Getting Started](https://github.com/spring-guides/gs-spring-boot-docker).

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Passare alla directory del progetto completato.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Usare Maven per compilare ed eseguire l'app di esempio.

   ```bash
   mvn package spring-boot:run
   ```

1. Testare l'app Web passando a `http://localhost:8080` oppure con il comando `curl` seguente:

   ```bash
   curl http://localhost:8080
   ```

Dovrebbe essere visualizzato il messaggio **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Creare un'istanza di Registro Azure Container usando l'interfaccia della riga di comando di Azure

Successivamente, si creerà un gruppo di risorse di Azure e la propria istanza di Registro Azure Container seguendo questa procedura:

1. Accedere al proprio account di Azure usando il comando seguente:

   ```azurecli
   az login
   ```

1. Specificare la sottoscrizione di Azure da usare:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Creare un gruppo di risorse per le risorse di Azure usate in questa esercitazione. Nel comando seguente assicurarsi di sostituire i segnaposto con il nome e l'area della risorsa, ad esempio `eastus`.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Creare un'istanza privata di Registro Azure Container nel gruppo di risorse con il comando seguente. Assicurarsi di sostituire i segnaposto con i valori effettivi. L'esercitazione effettua il push dell'app di esempio come immagine Docker in questo registro nei passaggi successivi.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Eseguire il push dell'app nel registro contenitori tramite Jib

Infine, si aggiornerà la configurazione del progetto e si userà il prompt dei comandi per creare e distribuire l'immagine.

1. Accedere alla propria istanza di Registro Azure Container dall'interfaccia della riga di comando di Azure con il comando seguente. Assicurarsi di sostituire il segnaposto con il nome del registro.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Il comando `az configure` imposta il nome predefinito del registro da usare con i comandi `az acr`.

1. Passare alla directory del progetto completato per l'applicazione Spring Boot (ad esempio, "*C:\SpringBoot\gs-spring-boot-docker\complete*" o " */users/robert/SpringBoot/gs-spring-boot-docker/complete*") e aprire il file *pom.xml* con un editor di testo.

1. Aggiornare la raccolta `<properties>` nel file *pom.xml* con il codice XML seguente. Sostituire il segnaposto con il nome del registro e aggiornare il valore di `<jib-maven-plugin.version>` con la versione più recente di [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Aggiornare la raccolta `<plugins>` nel file *pom.xml* in modo che l'elemento `<plugin>` contenga una voce per `jib-maven-plugin`, come illustrato nell'esempio seguente. Si noti che si sta usando un'immagine di base di Microsoft Container Registry, `mcr.microsoft.com/java/jdk:8-zulu-alpine`, che contiene una versione di JDK supportata ufficialmente per Azure. Per altre immagini di base di MCR con versioni di JDK supportate ufficialmente, vedere [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) e [Java SE JDK e Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Passare alla directory del progetto completato per l'applicazione Spring Boot ed eseguire questo comando per creare l'immagine ed eseguire il push dell'immagine nel registro:

   ```bash
   mvn compile jib:build
   ```

> [!NOTE]
>
> Per motivi di sicurezza, le credenziali create da `az acr login` sono valide solo per un'ora. Se si riceve un errore *401: accesso non autorizzato*, è possibile eseguire nuovamente il comando `az acr login -n <your registry name>` per ripetere l'autenticazione.

## <a name="verify-your-container-image"></a>Verificare l'immagine del contenitore

Congratulazioni! È ora disponibile un'app Java in contenitori basata su una versione di JDK supportata da Azure e di cui è stato eseguito il push in Registro Azure Container. È possibile testare l'immagine distribuendola in Servizio app di Azure oppure eseguendone il pull in locale con il comando (sostituendo il segnaposto):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>Passaggi successivi

Per altre versioni delle immagini di base Java supportate da Microsoft ufficialmente, vedere:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK e Maven](https://hub.docker.com/_/microsoft-java-maven)

Per altre informazioni su Spring e Azure, passare al centro di documentazione di Spring in Azure.

> [!div class="nextstepaction"]
> [Spring in Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere le seguenti risorse:

* [Azure per sviluppatori Java](/azure/java)
* [Uso di Azure DevOps e Java](/azure/devops/java)
* [Spring Boot on Docker Getting Started](https://spring.io/guides/gs/spring-boot-docker) (Introduzione a Spring Boot in Docker)
* [Spring Initializr](https://start.spring.io)
* [Distribuire un'applicazione Spring Boot nel servizio app di Azure](/azure/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Uso di un'immagine Docker personalizzata per App Web di Azure in Linux](/azure/app-service-web/app-service-linux-using-custom-docker-image)
