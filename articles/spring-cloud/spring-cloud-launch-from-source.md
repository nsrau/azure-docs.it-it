---
title: "Avvio rapido: Avviare l'applicazione Spring Cloud dal codice sorgente"
description: Questa guida di avvio rapido mostra come avviare l'applicazione Azure Spring Cloud direttamente dal codice sorgente
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: ca78a9d7eeeeccb71fea75f6ab429e76f2344bbe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895461"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Guida introduttiva: Avviare l'applicazione Spring Cloud dal codice sorgente

Azure Spring Cloud consente di eseguire facilmente applicazioni di microservizi basate su Spring Cloud in Azure.

Azure Spring Cloud consente di avviare un'applicazione direttamente dal codice sorgente Java o da un file JAR precompilato. Questo articolo illustra i passaggi necessari.

Seguendo questo argomento di avvio rapido, si apprenderà come:

> [!div class="checklist"]
> * Effettuare il provisioning di un'istanza del servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Assegnare un endpoint pubblico per l'applicazione

## <a name="prerequisites"></a>Prerequisiti

>[!Note]
> Azure Spring Cloud è attualmente disponibile come anteprima pubblica. Le offerte di anteprima pubblica consentono ai clienti di sperimentare le nuove funzionalità prima del rilascio della versione ufficiale.  I servizi e le funzionalità di anteprima pubblica non sono destinati all'uso in produzione.  Per altre informazioni sul supporto durante le anteprime, vedere le [domande frequenti](https://azure.microsoft.com/support/faq/) o inviare una [richiesta di supporto](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

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

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Effettuare il provisioning di un'istanza del servizio usando l'interfaccia della riga di comando di Azure

Accedere all'interfaccia della riga di comando di Azure e scegliere una sottoscrizione attiva. Assicurarsi di scegliere una sottoscrizione attiva autorizzata per Azure Spring Cloud

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Aprire una finestra dell'interfaccia della riga di comando di Azure ed eseguire i comandi seguenti per effettuare il provisioning di un'istanza di Azure Spring Cloud. Si noti che qui viene anche indicato ad Azure Spring Cloud di assegnare un dominio pubblico.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

La distribuzione dell'istanza del servizio richiede circa cinque minuti.

Impostare i nomi predefiniti del gruppo di risorse e del cluster usando i comandi seguenti:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Creare l'applicazione Spring Cloud

Il comando seguente crea un'applicazione Spring Cloud nella sottoscrizione.  In questo modo, viene creato un servizio Spring Cloud vuoto in cui è possibile caricare l'applicazione.

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
3. Selezionare **Assign Domain** (Assegna dominio) per assegnare un endpoint pubblico al gateway. Questa operazione può richiedere alcuni minuti. 
4. Immettere l'IP pubblico assegnato nel browser per visualizzare l'applicazione in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

> [!div class="checklist"]
> * Effettuare il provisioning di un'istanza del servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Modificare le variabili di ambiente per le applicazioni
> * Assegnare un IP pubblico per il gateway applicazione

> [!div class="nextstepaction"]
> [Preparare l'applicazione Azure Spring Cloud per la distribuzione](spring-cloud-tutorial-prepare-app-deployment.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
