---
title: Configurare un ambiente di staging in Azure Spring Cloud | Microsoft Docs
description: Informazioni su come usare la distribuzione blu/verde con Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038300"
---
# <a name="how-to-set-up-a-staging-environment"></a>Come configurare un ambiente di staging

Questo articolo illustra come sfruttare un ambiente di staging usando il modello di distribuzione blu/verde in Azure Spring Cloud. Dimostra anche come portare la distribuzione di staging in produzione senza cambiare direttamente la distribuzione di produzione.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già stata distribuita l'applicazione PiggyMetrics come descritto nell'[esercitazione sull'avvio di un'applicazione](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics comprende tre applicazioni: "gateway", "account-service" e "auth-service".  

Se si vuole usare un'applicazione diversa per questo esempio, è necessario apportare una semplice modifica nella relativa parte pubblica.  Questa modifica differenzia la distribuzione di staging dalla produzione.

>[!NOTE]
> Prima di iniziare l'argomento di avvio rapido, assicurarsi che la propria sottoscrizione di Azure abbia accesso ad Azure Spring Cloud.  Essendo un servizio in anteprima, chiediamo ai clienti di contattarci in modo che possiamo aggiungere la loro sottoscrizione al nostro elenco di utenti autorizzati.  Se si vogliono esplorare le funzionalità di Azure Spring Cloud, contattarci tramite posta elettronica: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Include i comuni strumenti di Azure preinstallati, tra cui le ultime versioni di Git, JDK, Maven e dell'interfaccia della riga di comando di Azure. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

Per completare questo articolo:

1. [Installare Git](https://git-scm.com/)
1. [Installare JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi)
1. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>Visualizzare tutte le distribuzioni

Passare all'istanza del servizio nel portale di Azure e selezionare **Gestione della distribuzione** per visualizzare tutte le distribuzioni. È possibile selezionare ogni distribuzione per visualizzare altri dettagli.

## <a name="create-a-staging-deployment"></a>Creare una distribuzione di staging

1. Nell'ambiente di sviluppo locale apportare una piccola modifica all'applicazione gateway di Piggy Metric. Ad esempio, cambiare il colore in `gateway/src/main/resources/static/css/launch.css`. In questo modo sarà possibile differenziare facilmente le due distribuzioni. Eseguire il comando seguente per creare il pacchetto jar: 

    ```azurecli
    mvn clean package
    ```

1. Creare una nuova distribuzione con l'interfaccia della riga di comando di Azure, assegnando alla distribuzione di staging il nome "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Una volta completata la distribuzione, accedere alla pagina del gateway da **Dashboard dell'applicazione** e vedere tutte le istanze nella scheda **App Instances** (Istanze di app) a sinistra.
  
> [!NOTE]
> Lo stato di individuazione è "OUT_OF_SERVICE", quindi tutto il traffico verrà instradato in questa distribuzione prima che venga completata la verifica.

## <a name="verify-the-staging-deployment"></a>Verificare la distribuzione di staging

1.Tornare nella pagina **Gestione della distribuzione** e selezionare la nuova distribuzione. Lo stato della distribuzione dovrebbe essere **In esecuzione**. Il pulsante "Assegna/Annulla assegnazione dominio" sarà disabilitato perché si tratta di un ambiente di staging.

1. Nella pagina **Panoramica** dovrebbe essere presente un **endpoint di test**. Copiarlo e incollarlo in una nuova pagina del browser. Dovrebbe essere visualizzata la nuova pagina di Piggy Metrics.

>[!TIP]
> * Verificare se l'endpoint di testo termina con "/" per assicurarsi che il file CSS venga caricato correttamente.  
> * Se il browser richiede di immettere le credenziali di accesso per visualizzare la pagina, usare [Decodifica URL](https://www.urldecoder.org/) per decodificare l'endpoint di test. La decodifica dell'URL restituisce un URL nel formato "https://\<nomeutente>:\<password>@\<nome-cluster>.test.azureapps.io/gateway/green".  Usarlo per accedere all'endpoint.

>[!NOTE]    
> Le impostazioni del server di configurazione si applicano all'ambiente di staging oltre che a quello di produzione. Se ad esempio si imposta il percorso del contesto (`server.servlet.context-path`) per il gateway dell'app nel server di configurazione su *somepath*, il percorso della distribuzione verde cambia in: "https://\<nomeutente>:\<password>@\<nome-cluster>.test.azureapps.io/gateway/green/somepath/..."
 
 Se si visita il gateway dell'app pubblico a questo punto, si dovrebbe vedere la pagina precedente senza la nuova modifica.
    
## <a name="set-the-green-as-production-deployment"></a>Impostare la distribuzione verde come distribuzione di produzione

1. Avendo verificato la modifica nell'ambiente di staging, è possibile eseguirne il push in produzione. Tornare in **Gestione della distribuzione** e selezionare la casella di controllo prima dell'applicazione "gateway".
2. Selezionare "Imposta distribuzione".
3. Scegliere "Verde" dal menu "DISTRIBUZIONE DI PRODUZIONE" e quindi selezionare **Applica**
4. Passare alla pagina **Panoramica** dell'applicazione gateway. Se è già stato assegnato un dominio per l'applicazione gateway, l'URL verrà visualizzato nella pagina **Panoramica**. Visitare l'URL per vedere la pagina di Piggy Metrics modificata.

>[!NOTE]
> Dopo aver impostato la distribuzione verde sull'ambiente di produzione, la distribuzione precedente diventa quella di staging.

## <a name="modify-the-staging-deployment"></a>Modificare la distribuzione di staging

Se non si è soddisfatti della modifica, è possibile modificare il codice dell'applicazione, creare un nuovo pacchetto jar e caricarlo nella distribuzione verde tramite l'interfaccia della riga di comando di Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Eliminare una distribuzione di staging

Per eliminare la distribuzione di staging dal portale di Azure, passare alla relativa pagina e selezionare il pulsante **Elimina**.

In alternativa, eliminare la distribuzione di staging dall'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
