---
title: Configurare un ambiente di staging in Azure Spring Cloud | Microsoft Docs
description: Informazioni su come usare la distribuzione blu/verde con Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: cd1573a3d896f3d2d5cb53130d8fac86be43beb6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152083"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurare un ambiente di staging nel cloud Spring di Azure

Questo articolo illustra come configurare una distribuzione di gestione temporanea usando il modello di distribuzione Blue-Green nel cloud Spring di Azure. Viene inoltre illustrato come inserire la distribuzione di gestione temporanea in produzione senza modificare direttamente la distribuzione di produzione.

## <a name="prerequisites"></a>prerequisiti

Questo articolo presuppone che l'applicazione PiggyMetrics sia già stata distribuita dall' [esercitazione sull'avvio di un'applicazione Azure Spring cloud](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics comprende tre applicazioni: "gateway", "account-Service" e "auth-Service".  

Se si vuole usare un'applicazione diversa per questo esempio, è necessario apportare una semplice modifica in una parte pubblica dell'applicazione.  Questa modifica differenzia la distribuzione di staging dalla produzione.

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire le istruzioni riportate in questo articolo.  Con gli strumenti di Azure preinstallati e comuni, incluse le versioni più recenti di Git, JDK, Maven e l'interfaccia della riga di comando di Azure. Se è stato effettuato l'accesso alla sottoscrizione di Azure, avviare il [Azure cloud Shell](https://shell.azure.com).  Per altre informazioni, vedere [Panoramica di Azure cloud Shell](../cloud-shell/overview.md).

Per configurare un ambiente di staging nel cloud Spring di Azure, seguire le istruzioni riportate nelle sezioni successive.

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Visualizzare tutte le distribuzioni

Passare all'istanza del servizio nell'portale di Azure e selezionare **gestione della distribuzione** per visualizzare tutte le distribuzioni. Per visualizzare altri dettagli, è possibile selezionare ogni distribuzione.

## <a name="create-a-staging-deployment"></a>Creare una distribuzione di staging

1. Nell'ambiente di sviluppo locale, apportare una piccola modifica all'applicazione gateway PiggyMetrics. Ad esempio, modificare il colore nel file *gateway/src/main/Resources/static/CSS/Launch. CSS* . Questa operazione consente di distinguere facilmente le due distribuzioni. Per compilare il pacchetto jar, eseguire il comando seguente: 

    ```azurecli
    mvn clean package
    ```

1. Nell'interfaccia della riga di comando di Azure creare una nuova distribuzione e assegnarle il nome della distribuzione di staging "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Al termine della distribuzione, accedere alla pagina del gateway dal **Dashboard dell'applicazione**e visualizzare tutte le istanze nella scheda **istanze app** a sinistra.
  
> [!NOTE]
> Lo stato di individuazione è *OUT_OF_SERVICE* in modo che il traffico non venga indirizzato a questa distribuzione prima del completamento della verifica.

## <a name="verify-the-staging-deployment"></a>Verificare la distribuzione di staging

1. Tornare alla pagina **Gestione distribuzione** e selezionare la nuova distribuzione. Lo stato della distribuzione dovrebbe essere *In esecuzione*. Il pulsante **assegna/Annulla assegnazione dominio** dovrebbe essere visualizzato in grigio, perché l'ambiente è un ambiente di gestione temporanea.

1. Nel riquadro **Panoramica** verrà visualizzato un **endpoint di test**. Copiarlo e incollarlo in una nuova finestra del browser e visualizzare la nuova pagina PiggyMetrics.

>[!TIP]
> * Verificare che l'endpoint di test termini con una barra (/) per assicurarsi che il file CSS sia caricato correttamente.  
> * Se il browser richiede di immettere le credenziali di accesso per visualizzare la pagina, usare [Decodifica URL](https://www.urldecoder.org/) per decodificare l'endpoint di test. La decodifica dell'URL restituisce un URL nel formato "https://\<nomeutente>:\<password>@\<nome-cluster>.test.azureapps.io/gateway/green".  Usare questo modulo per accedere all'endpoint.

>[!NOTE]    
> Le impostazioni del server di configurazione si applicano sia all'ambiente di gestione temporanea che alla produzione. Se ad esempio si imposta il percorso del contesto (`server.servlet.context-path`) per il gateway app nel server di configurazione come *somepath*, il percorso della distribuzione verde cambierà in "https://\<username >:\<password > @\<nome-cluster >. test. azureapps. io/gateway/verde/somepath/...".
 
 Se a questo punto si visita il gateway app pubblico, viene visualizzata la pagina precedente senza la nuova modifica.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Impostare la distribuzione verde come ambiente di produzione

1. Una volta verificata la modifica nell'ambiente di gestione temporanea, è possibile eseguirne il push in produzione. Tornare alla **gestione della distribuzione**e selezionare la casella di controllo applicazione **gateway** .

2. Selezionare **imposta distribuzione**.
3. Nell'elenco **distribuzione produzione** selezionare **verde**, quindi selezionare **applica**.
4. Passare alla pagina **Panoramica** dell'applicazione gateway. Se è già stato assegnato un dominio per l'applicazione gateway, l'URL verrà visualizzato nel riquadro **Panoramica** . Per visualizzare la pagina PiggyMetrics modificata, selezionare l'URL e passare al sito.

>[!NOTE]
> Dopo aver impostato la distribuzione verde come ambiente di produzione, la distribuzione precedente diventa la distribuzione di gestione temporanea.

## <a name="modify-the-staging-deployment"></a>Modificare la distribuzione di staging

Se non si è soddisfatti della modifica, è possibile modificare il codice dell'applicazione, compilare un nuovo pacchetto jar e caricarlo nella distribuzione verde usando l'interfaccia della riga di comando di Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminare la distribuzione di gestione temporanea

Per eliminare la distribuzione di staging dalla porta di Azure, passare alla pagina distribuzione di staging e quindi selezionare il pulsante **Elimina** .

In alternativa, eliminare la distribuzione di staging dall'interfaccia della riga di comando di Azure eseguendo il comando seguente:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
