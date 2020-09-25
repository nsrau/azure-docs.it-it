---
title: Procedura - Avviare l'applicazione Spring Cloud dal codice sorgente
description: Questa guida di avvio rapido mostra come avviare l'applicazione Azure Spring Cloud direttamente dal codice sorgente
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a95ec76c63a35c29b061c2fddff67a28e0f8f553
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883658"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Procedura - Avviare l'applicazione Spring Cloud dal codice sorgente

**Questo articolo si applica a:** ✔️ Java

Azure Spring Cloud consente di eseguire applicazioni di microservizi basate su Spring Cloud in Azure.

È possibile avviare le applicazioni direttamente dal codice sorgente Java o da un file JAR predefinito. Questo articolo illustra le procedure di distribuzione.

Questa guida di avvio rapido illustra come:

> [!div class="checklist"]
> * Effettuare il provisioning di un'istanza del servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Assegnare un endpoint pubblico per l'applicazione

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare che la sottoscrizione di Azure abbia le dipendenze necessarie:

1. [Installare Git](https://git-scm.com/)
2. [Installare JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi)
4. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Include i comuni strumenti di Azure preinstallati, tra cui le ultime versioni di Git, JDK, Maven e dell'interfaccia della riga di comando di Azure. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure con il comando seguente

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Effettuare il provisioning di un'istanza del servizio usando l'interfaccia della riga di comando di Azure

Accedere all'interfaccia della riga di comando di Azure e scegliere una sottoscrizione attiva. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Creare un gruppo di risorse in cui includere il servizio Azure Spring Cloud. Per altre informazioni, vedere [Gruppi di risorse di Azure](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Eseguire i comandi seguenti per effettuare il provisioning di un'istanza di Azure Spring Cloud. Preparare un nome per il servizio Azure Spring Cloud. Il nome deve essere composto da 4-32 caratteri e può contenere solo lettere minuscole, numeri e trattini. Il primo carattere del nome del servizio deve essere una lettera e l'ultimo deve essere una lettera o un numero.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

La distribuzione dell'istanza del servizio richiede circa cinque minuti.

Impostare i nomi predefiniti del gruppo di risorse e dell'istanza di Azure Spring Cloud usando i comandi seguenti:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Creare l'applicazione Azure Spring Cloud

Il comando seguente crea un'applicazione Azure Spring Cloud nella sottoscrizione.  In questo modo viene creato un servizio vuoto in cui è possibile caricare l'applicazione.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Distribuire l'applicazione Spring Cloud

È possibile distribuire l'applicazione da un file JAR precompilato o da un repository Gradle o Maven.  Le istruzioni relative a ogni caso sono riportate di seguito.

### <a name="deploy-a-built-jar"></a>Distribuire un file JAR compilato

Per eseguire la distribuzione da un file JAR compilato nel computer locale, assicurarsi che la compilazione produca un file di tipo [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Per distribuire il fat-JAR in una distribuzione attiva

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Per distribuire il fat-JAR in una distribuzione specifica

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Distribuire dal codice sorgente

Azure Spring Cloud usa [kpack](https://github.com/pivotal/kpack) per compilare il progetto.  È possibile usare l'interfaccia della riga di comando di Azure per caricare il codice sorgente, compilare il progetto usando kpack e distribuirlo all'applicazione di destinazione.

> [!WARNING]
> Il progetto deve produrre un solo file JAR con una voce `main-class` nel file `MANIFEST.MF` in `target` (per le distribuzioni Maven) o `build/libs` (per le distribuzioni Gradle).  Più file JAR con voci `main-class` determineranno l'esito negativo della distribuzione.

Per i progetti Maven/Gradle con un singolo modulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Per i progetti Maven/Gradle con più moduli, ripetere per ogni modulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Visualizzare i log di distribuzione

Esaminare i log di compilazione kpack usando il comando seguente:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> I log kpack visualizzeranno solo la distribuzione più recente se questa è stata compilata dal codice sorgente usando kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Assegnare un endpoint pubblico al gateway

1. Aprire la pagina **Dashboard dell'applicazione**.
2. Selezionare l'applicazione `gateway` per visualizzare la pagina **Dettagli applicazione**.
3. Selezionare **Assign endpoint** (Assegna endpoint) per assegnare un endpoint pubblico al gateway. L'operazione potrebbe richiedere alcuni minuti. 
4. Immettere l'IP pubblico assegnato nel browser per visualizzare l'applicazione in esecuzione.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Effettuare il provisioning di un'istanza del servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Modificare le variabili di ambiente per le applicazioni
> * Assegnare un IP pubblico per il gateway applicazione

> [!div class="nextstepaction"]
> [Log, metriche e traccia di Spring Cloud](spring-cloud-quickstart-logs-metrics-tracing.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
