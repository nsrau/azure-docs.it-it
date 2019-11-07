---
title: Configurare un ambiente di staging nel cloud Spring di Azure | Microsoft Docs
description: Informazioni su come usare la distribuzione Blue-Green con il cloud Spring di Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607216"
---
# <a name="how-to-set-up-a-staging-environment"></a>Come configurare un ambiente di gestione temporanea

Questo articolo illustra come sfruttare una distribuzione di gestione temporanea usando il modello di distribuzione Blue-Green nel cloud Spring di Azure. Verrà inoltre illustrato come inserire la distribuzione di gestione temporanea in produzione senza modificare direttamente la distribuzione di produzione.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'applicazione PiggyMetrics sia già stata distribuita dall' [esercitazione sull'avvio di un'applicazione](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics comprende tre applicazioni: "gateway", "account-Service" e "auth-Service".  

Se è presente un'applicazione diversa da usare per questo esempio, è necessario apportare una semplice modifica in una parte pubblica dell'applicazione.  Questa modifica distingue la distribuzione di gestione temporanea dalla produzione.

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Con gli strumenti di Azure comuni preinstallati, incluse le versioni più recenti di Git, JDK, Maven e l'interfaccia della riga di comando di Azure. Se è stato effettuato l'accesso alla sottoscrizione di Azure, avviare il [Azure cloud Shell](https://shell.azure.com) da Shell.Azure.com.  Per altre informazioni sulle Azure Cloud Shell, [Leggi la documentazione](../cloud-shell/overview.md)

Per completare questo articolo:


## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring cloud per l'interfaccia della riga di comando di Azure usando il comando seguente

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Visualizza tutte le distribuzioni

Passare all'istanza del servizio nel portale di Azure e selezionare **gestione della distribuzione** per visualizzare tutte le distribuzioni. Per ulteriori informazioni, è possibile selezionare ogni distribuzione.

## <a name="create-a-staging-deployment"></a>Creare una distribuzione di staging

1. Nell'ambiente di sviluppo locale, apportare una piccola modifica all'applicazione gateway della metrica Piggy. Ad esempio, modificare il colore in `gateway/src/main/resources/static/css/launch.css`. Ciò consentirà di distinguere facilmente le due distribuzioni. Eseguire il comando seguente per compilare il pacchetto jar: 

    ```azurecli
    mvn clean package
    ```

1. Creare una nuova distribuzione con l'interfaccia della riga di comando di Azure, assegnando il nome della distribuzione di staging "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Al termine della distribuzione, accedere alla pagina del gateway dal **Dashboard dell'applicazione** e visualizzare tutte le istanze nella scheda **istanze app** a sinistra.
  
> [!NOTE]
> Lo stato di individuazione è "OUT_OF_SERVICE", quindi il traffico non verrà indirizzato a questa distribuzione prima del completamento della verifica.

## <a name="verify-the-staging-deployment"></a>Verificare la distribuzione di gestione temporanea

1. tornare alla pagina **Gestione distribuzione** e selezionare la nuova distribuzione. Lo stato della distribuzione dovrebbe essere **in esecuzione**. Il pulsante "assegna/Annulla assegnazione dominio" verrà disabilitato poiché si tratta di un ambiente di gestione temporanea.

1. Nella pagina **Overview** verrà visualizzato un **endpoint di test**. Copiarlo e incollarlo in una nuova pagina del browser. verrà visualizzata la pagina nuova metrica.

>[!TIP]
> * Verificare che l'endpoint di test termini con "/" per assicurarsi che il CSS venga caricato correttamente.  
> * Se il browser richiede l'immissione delle credenziali di accesso per visualizzare la pagina, usare la [decodifica URL](https://www.urldecoder.org/) per decodificare l'endpoint di test. La decodifica URL restituisce un URL nel formato "https://\<username >:\<password > @\<nome-cluster >. test. azureapps. io/gateway/Green".  Usare questo per accedere all'endpoint.

>[!NOTE]    
> Le impostazioni del server di configurazione si applicano all'ambiente di gestione temporanea e alla produzione. Ad esempio, se si imposta il percorso del contesto (`server.servlet.context-path`) per il gateway app nel server di configurazione come *somepath*, il percorso della distribuzione verde cambia: "https://\<username >:\<password > @\<nome-cluster >. test.azureapps.io/gateway/green/somepath/... "
 
 Se a questo punto si visita il gateway app pubblico, viene visualizzata la pagina precedente senza la nuova modifica.
    
## <a name="set-the-green-as-production-deployment"></a>Imposta il verde come distribuzione di produzione

1. Dopo aver verificato le modifiche nell'ambiente di gestione temporanea, è possibile eseguirne il push in produzione. Tornare alla **gestione della distribuzione** e selezionare la casella di controllo prima dell'applicazione "gateway".
2. Selezionare "imposta distribuzione".
3. Selezionare "verde" dal menu "distribuzione produzione" e selezionare **applica** .
4. Passare alla pagina **Panoramica** dell'applicazione gateway. Se è già stato assegnato un dominio per l'applicazione gateway, l'URL verrà visualizzato nella pagina **Panoramica** . Visitare l'URL per visualizzare la pagina delle metriche Piggy modificate.

>[!NOTE]
> Quando la distribuzione verde è impostata sull'ambiente di produzione, la distribuzione precedente diventa la distribuzione di gestione temporanea.

## <a name="modify-the-staging-deployment"></a>Modificare la distribuzione di gestione temporanea

Se non si è soddisfatti della modifica, è possibile modificare il codice dell'applicazione, compilare un nuovo pacchetto jar e caricarlo nella distribuzione verde usando l'interfaccia della riga di comando di Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Eliminare una distribuzione di staging

Per eliminare la distribuzione di gestione temporanea dalla porta di Azure, passare alla pagina distribuzione di staging e selezionare il pulsante **Elimina** .

In alternativa, eliminare la distribuzione di staging dall'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
