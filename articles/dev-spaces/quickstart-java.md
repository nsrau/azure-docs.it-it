---
title: Sviluppare con Java in Kubernetes usando Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Sviluppo rapido Kubernetes con contenitori, microservizi e Java in Azure
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Java, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: c1c039ba8696baff11abed3930998983647f4356
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59425747"
---
# <a name="quickstart-develop-with-java-on-kubernetes-using-azure-dev-spaces"></a>Avvio rapido: Sviluppare con Java in Kubernetes usando Azure Dev Spaces

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare codice in modo iterativo nei contenitori con Visual Studio Code e la riga di comando.
- Eseguire il debug del codice nello spazio di Dev Spaces da Visual Studio Code.


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free).
- [Visual Studio Code installato](https://code.visualstudio.com/download).
- Le estensioni [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e [Java Debugger for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) per Visual Studio Code installate.
- [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Maven installato e configurato](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

È necessario creare un cluster del servizio Azure Kubernetes in un'[area supportata](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). I comandi seguenti creano un gruppo di risorse denominato *MyResourceGroup* e un cluster del servizio Azure Kubernetes denominato *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-count 1 --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Abilitare Azure Dev Spaces nel cluster del servizio Azure Kubernetes

Usare il comando `use-dev-spaces` per abilitare Dev Spaces nel cluster del servizio Azure Kubernetes e seguire i prompt. Il comando seguente abilita Dev Spaces nel cluster *MyAKS* all'interno del gruppo *MyResourceGroup* e crea uno spazio *predefinito*.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Ottenere il codice dell'applicazione di esempio

In questo articolo verrà usata l'[applicazione di esempio Azure Dev Spaces](https://github.com/Azure/dev-spaces) per dimostrare l'uso di Azure Dev Spaces.

Clonare l'applicazione da GitHub e passare alla directory *dev-spaces/samples/java/getting-started/webfrontend*:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/java/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Preparare l'applicazione

Generare le risorse grafico Docker e Helm per l'esecuzione dell'applicazione in Kubernetes usando il comando `azds prep`:

```cmd
azds prep --public
```

Per la corretta generazione delle risorse grafico Docker e Helm, è necessario eseguire il comando `prep` dalla directory *dev-spaces/samples/java/getting-started/webfrontend*.

## <a name="build-and-run-code-in-kubernetes"></a>Compilare ed eseguire codice in Kubernetes

Compilare ed eseguire il codice nel servizio Azure Kubernetes usando il comando `azds up`:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...3s
Installing Helm chart...8s
Waiting for container image build...28s
Building container image...
Step 1/8 : FROM maven:3.5-jdk-8-slim
Step 2/8 : EXPOSE 8080
Step 3/8 : WORKDIR /usr/src/app
Step 4/8 : COPY pom.xml ./
Step 5/8 : RUN /usr/local/bin/mvn-entrypoint.sh     mvn package -Dmaven.test.skip=true -Dcheckstyle.skip=true -Dmaven.javadoc.skip=true --fail-never
Step 6/8 : COPY . .
Step 7/8 : RUN mvn package -Dmaven.test.skip=true -Dcheckstyle.skip=true -Dmaven.javadoc.skip=true
Step 8/8 : ENTRYPOINT ["java","-jar","target/webfrontend-0.1.0.jar"]
Built container image in 37s
Waiting for container...57s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Per vedere il servizio in esecuzione, aprire l'URL pubblico, visualizzato nell'output del comando `azds up`. In questo esempio l'URL pubblico è *http://webfrontend.1234567890abcdef1234.eus.azds.io/*.

Se si arresta il comando `azds up` premendo *CTRL+C*, il servizio continuerà a essere eseguito nel servizio Azure Kubernetes e l'URL pubblico rimarrà disponibile.

## <a name="update-code"></a>Aggiornare il codice

Per distribuire una versione aggiornata del servizio, è possibile aggiornare qualsiasi file del progetto ed eseguire di nuovo il comando `azds up`. Ad esempio: 

1. Se `azds up` è ancora in esecuzione, premere *CTRL+C*.
1. Aggiornare la [riga 16 di `src/main/java/com/ms/sample/webfrontend/Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L16) in:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Salvare le modifiche.
1. Eseguire di nuovo il comando `azds up`:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Passare al servizio in esecuzione e osservare le modifiche.
1. Premere *CTRL+C* per arrestare il comando `azds up`.

## <a name="enable-visual-studio-code-to-debug-in-kubernetes"></a>Abilitare Visual Studio Code per il debug in Kubernetes

Aprire Visual Studio Code, fare clic su *File*, quindi su *Apri*, passare alla directory *dev-spaces/samples/java/getting-started/webfrontend* e fare clic su *Apri*.

In Visual Studio Code è ora aperto il progetto *webfrontend*, ossia lo stesso servizio eseguito usando il comando `azds up`. Per eseguire il debug di questo servizio nel servizio Azure Kubernetes con Visual Studio Code, invece che direttamente con `azds up`, è necessario preparare questo progetto in modo tale da usare Visual Studio Code per comunicare con il proprio spazio di Dev Spaces.

Per aprire il riquadro comandi in Visual Studio Code, fare clic su *Visualizza* e quindi su *Riquadro comandi*. Iniziare a digitare `Azure Dev Spaces` e fare clic su `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Preparare i file di configurazione per Azure Dev Spaces](./media/common/command-palette.png)

Quando il codice di Visual Studio Code chiede anche di configurare le immagini di base e la porta esposta, scegliere `Azul Zulu OpenJDK for Azure (Free LTS)` per l'immagine di base e `8080` per la porta esposta.

![Selezionare l'immagine di base](media/get-started-java/select-base-image.png)

![Selezionare la porta esposta](media/get-started-java/select-exposed-port.png)

Questo comando prepara il progetto per l'esecuzione in Azure Dev Spaces direttamente da Visual Studio Code. Genera inoltre una directory *.vscode* con la configurazione di debug alla radice del progetto.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Compilare ed eseguire il codice in Kubernetes da Visual Studio

Fare clic sull'icona *Debug* a sinistra e quindi su *Launch Java Program (AZDS)* (Avvia programma Java AZDS) nella parte superiore.

![Avviare il programma Java](media/get-started-java/debug-configuration.png)

Questo comando compila ed esegue il servizio in Azure Dev Spaces in modalità debug. La finestra del *terminale* nella parte inferiore visualizza l'output della compilazione e gli URL per il servizio che esegue Azure Dev Spaces. La *Console di debug* visualizza l'output del log.

> [!Note]
> Se nel *riquadro comandi* non vengono visualizzati comandi di Azure Dev Spaces, assicurarsi di aver installato l'[estensione Visual Studio Code per Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verificare inoltre di aver aperto la directory *dev-spaces/samples/java/getting-started/webfrontend* in Visual Studio Code.

Fare clic su *Debug* e quindi su *Arresta debug* per arrestare il debugger.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Impostazione e uso di punti di interruzione per il debug

Avviare il servizio in modalità debug usando *Launch Java Program (AZDS)* (Avvia programma Java AZDS).

Tornare nella visualizzazione *Explorer* facendo clic su *Visualizza* e quindi su *Explorer*. Aprire `src/main/java/com/ms/sample/webfrontend/Application.java` e fare clic in un punto della riga 16 per posizionarvi il cursore. Per impostare un punto di interruzione premere *F9* oppure fare clic su *Debug* e quindi su *Attiva/Disattiva punto di interruzione*.

Aprire il servizio in un browser e notare che non vengono visualizzati messaggi. Tornare in Visual Studio Code e notare che la riga 16 è evidenziata. Il punto di interruzione impostato ha sospeso il servizio in corrispondenza della riga 16. Per riprendere il servizio, premere *F5* oppure fare clic su *Debug* e quindi su *Continua*. Tornare nel browser e notare che ora il messaggio è visualizzato.

Durante l'esecuzione del servizio in Kubernetes con un debugger collegato, si ha accesso completo alle informazioni di debug, ad esempio su stack di chiamate, variabili locali ed eccezioni.

Rimuovere il punto di interruzione posizionando il cursore sulla riga 16 di `src/main/java/com/ms/sample/webfrontend/Application.java` e premendo *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aggiornare il codice da Visual Studio Code

Mentre il servizio è in esecuzione in modalità debug, aggiornare la riga 16 di `src/main/java/com/ms/sample/webfrontend/Application.java`. Ad esempio: 
```java
return "Hello from webfrontend in Azure while debugging!";
```

Salvare il file. Fare clic su *Debug*, quindi su *Riavvia debug* oppure premere il pulsante *Riavvia debug* sulla barra degli strumenti *Debug*.

![Aggiornare il debug](media/get-started-java/debug-action-refresh.png)

Aprire il servizio in un browser e notare che è visualizzato il messaggio aggiornato.

Invece di ricompilare e ridistribuire una nuova immagine del contenitore ogni volta che vengono apportate modifiche al codice, Azure Dev Spaces ricompila in modo incrementale il codice nel contenitore esistente in modo da velocizzare il ciclo di modifica/debug.

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Uso di più contenitori e sviluppo in team](multi-service-java.md)
