---
title: "Avvio rapido: Avviare l'applicazione Spring cloud dal codice sorgente"
description: Informazioni su come avviare l'applicazione Azure Spring cloud direttamente dal codice sorgente
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 445cac1494828362d54a8c15e68d27f01b165841
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170539"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>Avviare l'applicazione Spring cloud dal codice sorgente

Azure Spring cloud consente di avviare l'applicazione direttamente dal codice sorgente Java o da un file JAR predefinito. Questo articolo illustra i passaggi necessari.

## <a name="prerequisites"></a>Prerequisiti

>[!Note]
> Prima di iniziare questa Guida introduttiva, assicurarsi che la sottoscrizione di Azure abbia accesso al cloud Spring di Azure.  Come servizio in anteprima, ti chiediamo di contattare Microsoft per poter aggiungere la tua sottoscrizione al nostro elenco di consentiti.  Per esplorare le funzionalità di Azure Spring cloud, @no__t 0fill questo modulo @ no__t-1.

Prima di iniziare, verificare che la sottoscrizione di Azure abbia le dipendenze richieste:

1. [Installare Git](https://git-scm.com/)
2. [Installare JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Installare Maven 3,0 o versione successiva](https://maven.apache.org/download.cgi)
4. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Iscriversi per una sottoscrizione di Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Con gli strumenti di Azure comuni preinstallati, incluse le versioni più recenti di Git, JDK, Maven e l'interfaccia della riga di comando di Azure. Se è stato effettuato l'accesso alla sottoscrizione di Azure, avviare il [Azure cloud Shell](https://shell.azure.com) da Shell.Azure.com.  Per altre informazioni sulle Azure Cloud Shell, [Leggi la documentazione](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring cloud per l'interfaccia della riga di comando di Azure con il comando seguente

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Eseguire il provisioning di un'istanza del servizio usando l'interfaccia della

Accedere all'interfaccia della riga di comando di Azure e scegliere la sottoscrizione attiva. Assicurarsi di scegliere la sottoscrizione attiva che è consentita per Azure Spring cloud

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Aprire una finestra dell'interfaccia della riga di comando di Azure ed eseguire i comandi seguenti per effettuare il provisioning di un'istanza del cloud Spring Si noti che viene anche indicato ad Azure Spring cloud di assegnare un dominio pubblico qui.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

La distribuzione dell'istanza del servizio può richiedere circa cinque minuti.

Impostare il nome del gruppo di risorse e il nome del cluster predefiniti usando i comandi seguenti:

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
> Il progetto deve produrre un solo file JAR con una voce `main-class` nel `MANIFEST.MF` in `target` (per le distribuzioni Maven o `build/libs` (per le distribuzioni Gradle).  Più file con estensione JAR con voci `main-class` provocheranno un errore di distribuzione.

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

1. Aprire la pagina **Dashboard dell'applicazione** .
2. Selezionare l'applicazione `gateway` per visualizzare la pagina dei **Dettagli dell'applicazione** .
3. Selezionare **assegna dominio** per assegnare un endpoint pubblico al gateway. Questa operazione può essere di pochi minuti. 
4. Immettere l'indirizzo IP pubblico assegnato nel browser per visualizzare l'applicazione in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

> [!div class="checklist"]
> * Provisioning di un'istanza di servizio
> * Impostare un server di configurazione per un'istanza
> * Creare un'applicazione di microservizi in locale
> * Distribuire ogni microservizio
> * Modificare le variabili di ambiente per le applicazioni
> * Assegnare un indirizzo IP pubblico per il gateway applicazione

> [!div class="nextstepaction"]
> [Preparare l'applicazione Azure Spring cloud per la distribuzione](spring-cloud-tutorial-prepare-app-deployment.md)
