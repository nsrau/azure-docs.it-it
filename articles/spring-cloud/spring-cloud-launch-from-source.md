---
title: "Guida introduttiva: avviare l'applicazione Spring cloud dal codice sorgente"
description: Informazioni su come avviare l'applicazione Azure Spring cloud direttamente dal codice sorgente
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 573baa242c06868326568a82bc358e136f1ece2c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177963"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>Avviare l'applicazione Spring cloud dal codice sorgente

Azure Spring Cloud consente di eseguire facilmente applicazioni di microservizi basate su Spring Cloud in Azure.

Azure Spring cloud consente di avviare l'applicazione direttamente dal codice sorgente Java o da un file JAR predefinito. Questo articolo illustra i passaggi necessari.

Seguendo questo argomento di avvio rapido, si apprenderà come:

> [!div class="checklist"]
> * Effettuare il provisioning di un'istanza del servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Assegnare un endpoint pubblico per l'applicazione

## <a name="prerequisites"></a>Prerequisiti

>[!Note]
> Prima di iniziare l'argomento di avvio rapido, assicurarsi che la propria sottoscrizione di Azure abbia accesso ad Azure Spring Cloud.  Essendo un servizio in anteprima, chiediamo ai clienti di contattarci in modo che possiamo aggiungere la loro sottoscrizione al nostro elenco di utenti autorizzati.  Se si desidera esplorare le funzionalità di Azure Spring Cloud, [compilare questo modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).  Mentre Azure Spring cloud è in versione di anteprima, Microsoft offre supporto limitato senza un contratto di servizio.  Per ulteriori informazioni sul supporto tecnico durante le anteprime, consultare le [domande frequenti sul supporto](https://azure.microsoft.com/support/faq/).

Prima di iniziare, verificare che la sottoscrizione di Azure abbia le dipendenze richieste:

1. [Installare Git](https://git-scm.com/)
2. [Installare JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi)
4. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Include i comuni strumenti di Azure preinstallati, tra cui le ultime versioni di Git, JDK, Maven e dell'interfaccia della riga di comando di Azure. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring cloud per l'interfaccia della riga di comando di Azure con il comando seguente

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Eseguire il provisioning di un'istanza del servizio usando l'interfaccia della

Accedere all'interfaccia della riga di comando di Azure e scegliere una sottoscrizione attiva. Assicurarsi di scegliere una sottoscrizione attiva autorizzata per Azure Spring Cloud

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Aprire una finestra dell'interfaccia della riga di comando di Azure ed eseguire i comandi seguenti per effettuare il provisioning di un'istanza di Azure Spring Cloud. Si noti che viene anche indicato ad Azure Spring cloud di assegnare un dominio pubblico qui.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

La distribuzione dell'istanza del servizio può richiedere circa cinque minuti.

Impostare i nomi predefiniti del gruppo di risorse e del cluster usando i comandi seguenti:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Creare l'applicazione Spring cloud

Il comando seguente consente di creare un'applicazione Spring cloud nella sottoscrizione.  In questo modo viene creato un servizio cloud Spring vuoto a cui è possibile caricare l'applicazione.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Distribuire un'applicazione Spring cloud

È possibile distribuire l'applicazione da un file JAR predefinito o da un repository Gradle o Maven.  Trovare le istruzioni per ogni caso.

### <a name="deploy-a-built-jar"></a>Distribuire un file JAR compilato

Per eseguire la distribuzione da un file JAR compilato nel computer locale, assicurarsi che la compilazione produca un file [jar Fat](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Per distribuire Fat-JAR in una distribuzione attiva

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Per distribuire Fat-JAR in una distribuzione specifica

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Distribuisci dal codice sorgente

Azure Spring cloud USA [kpack](https://github.com/pivotal/kpack) per compilare il progetto.  È possibile usare l'interfaccia della riga di comando di Azure per caricare il codice sorgente, compilare il progetto usando kpack e distribuirlo nell'applicazione di destinazione.

> [!WARNING]
> Il progetto deve produrre un solo file JAR con una voce `main-class` nel `MANIFEST.MF` `target` (per le distribuzioni Maven o `build/libs` (per le distribuzioni Gradle).  La distribuzione avrà esito negativo in più file con estensione JAR con `main-class` voci.

Per i progetti single Module Maven/gradle:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Per i progetti Maven/Gradle con più moduli, ripetere per ogni modulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Mostra log di distribuzione

Esaminare i log di compilazione kpack usando il comando seguente:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> I registri kpack visualizzeranno solo la distribuzione più recente se la distribuzione è stata creata dall'origine usando kpack.

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
> * Assegnare un indirizzo IP pubblico per il gateway applicazione

> [!div class="nextstepaction"]
> [Preparare l'applicazione Azure Spring Cloud per la distribuzione](spring-cloud-tutorial-prepare-app-deployment.md)
