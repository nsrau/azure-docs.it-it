---
title: Configurare un ambiente di staging in Azure Spring Cloud | Microsoft Docs
description: Informazioni su come usare la distribuzione blu/verde con Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471031"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurare un ambiente di gestione temporanea in Azure Spring CloudSet up a staging environment in Azure Spring Cloud

Questo articolo illustra come configurare una distribuzione di gestione temporanea usando il modello di distribuzione blu-verde in Azure Spring Cloud.This article discusses how to set up a staging deployment by using the blue-green deployment pattern in Azure Spring Cloud. La distribuzione di tipo blu-verde è un modello di recapito continuo Azure DevOps che si basa sul mantenimento di una versione esistente (blu) attiva mentre viene distribuita una versione nuova (verde). Questo articolo illustra come inserire la distribuzione della gestione temporanea nell'ambiente senza modificare direttamente la distribuzione di produzione.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'applicazione PiggyMetrics sia già stata distribuita [dall'esercitazione sull'avvio di un'applicazione Azure Spring Cloud.](spring-cloud-quickstart-launch-app-portal.md) PiggyMetrics è costituito da tre applicazioni: "gateway", "account-service" e "auth-service".  

Se si desidera utilizzare un'applicazione diversa per questo esempio, è necessario apportare una semplice modifica in una parte pubblica dell'applicazione.  Questa modifica differenzia la distribuzione di staging dalla produzione.

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che è possibile usare per eseguire le istruzioni in questo articolo.  Include strumenti di Azure comuni preinstallati, incluse le versioni più recenti di Git, JDK, Maven e l'interfaccia della riga di comando di Azure.It has common, preinstalled Azure tools, including the latest versions of Git, JDK, Maven, and the Azure CLI. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell.](https://shell.azure.com)  Per altre informazioni, vedere [Panoramica di Azure Cloud Shell.To](../cloud-shell/overview.md)learn more, see Overview of Azure Cloud Shell .

Per configurare un ambiente di gestione temporanea in Azure Spring Cloud, seguire le istruzioni nelle sezioni successive.

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Visualizzare tutte le distribuzioni

Passare all'istanza del servizio nel portale di Azure e selezionare **Gestione distribuzione** per visualizzare tutte le distribuzioni. Per visualizzare ulteriori dettagli, è possibile selezionare ogni distribuzione.

## <a name="create-a-staging-deployment"></a>Creare una distribuzione di staging

1. Nell'ambiente di sviluppo locale apportare una piccola modifica all'applicazione gateway PiggyMetrics.In your local development environment, make a small modification to the PiggyMetrics gateway application. Ad esempio, modificare il colore nel file *gateway/src/main/resources/static/css/launch.css.* In questo modo è possibile differenziare facilmente le due distribuzioni. Per compilare il pacchetto jar, eseguire il comando seguente: 

    ```console
    mvn clean package
    ```

1. Nell'interfaccia della riga di comando di Azure creare una nuova distribuzione e assegnarle il nome di distribuzione di gestione temporanea "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Al termine della distribuzione, accedere alla pagina del gateway dal **dashboard dell'applicazione**e visualizzare tutte le istanze nella scheda **Istanze dell'app** a sinistra.
  
> [!NOTE]
> Lo stato di individuazione è *OUT_OF_SERVICE* in modo che il traffico non venga instradato a questa distribuzione prima del completamento della verifica.

## <a name="verify-the-staging-deployment"></a>Verificare la distribuzione di staging

1. Tornare alla pagina **Gestione distribuzione** e selezionare la nuova distribuzione. Lo stato della distribuzione dovrebbe essere *In esecuzione*. Il pulsante **Assegna/Annulla assegnazione dominio** dovrebbe essere visualizzato in grigio, perché l'ambiente è un ambiente di gestione temporanea.

1. Nel riquadro **Panoramica** dovrebbe essere visualizzato un endpoint di **test.** Copiarlo e incollarlo in una nuova finestra del browser e verrà visualizzata la nuova pagina PiggyMetrics.

>[!TIP]
> * Verificare che l'endpoint di test termini con una barra (/) per assicurarsi che il file CSS venga caricato correttamente.  
> * Se il browser richiede di immettere le credenziali di accesso per visualizzare la pagina, usare [Decodifica URL](https://www.urldecoder.org/) per decodificare l'endpoint di test. La decodifica dell'URL restituisce un URL nel formato "https://\<nomeutente>:\<password>@\<nome-cluster>.test.azureapps.io/gateway/green".  Utilizzare questo modulo per accedere all'endpoint.

>[!NOTE]    
> Le impostazioni del server di configurazione si applicano sia all'ambiente di gestione temporanea che alla produzione. Ad esempio, se si imposta`server.servlet.context-path`il percorso di contesto ( ) per il gateway dell'app nel\<server\<di configurazione come *somepath*, il percorso della distribuzione verde viene modificato in "https://\<nome utente>: password> nome-cluster>.test.azureapps.io/gateway/green/somepath/...".
 
 Se visiti il gateway dell'app pubblico a questo punto, dovresti vedere la pagina precedente senza la nuova modifica.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Impostare la distribuzione verde come ambiente di produzioneSet the green deployment as the production environment

1. Dopo aver verificato la modifica nell'ambiente di gestione temporanea, è possibile eseguirne il push nell'ambiente di produzione. Tornare a **Gestione distribuzione**e selezionare la casella di controllo dell'applicazione **gateway.**

2. Selezionare **Imposta distribuzione**.
3. Nell'elenco **Distribuzione di produzione** selezionare **Verde**e quindi **Applica**.
4. Passare alla pagina **Panoramica** dell'applicazione gateway. Se è già stato assegnato un dominio per l'applicazione gateway, l'URL verrà visualizzato nel riquadro **Panoramica.** Per visualizzare la pagina PiggyMetrics modificata, selezionare l'URL e passare al sito.

>[!NOTE]
> Dopo aver impostato la distribuzione verde come ambiente di produzione, la distribuzione precedente diventa la distribuzione di gestione temporanea.

## <a name="modify-the-staging-deployment"></a>Modificare la distribuzione di staging

Se non si è soddisfatti della modifica, è possibile modificare il codice dell'applicazione, compilare un nuovo pacchetto jar e caricarlo nella distribuzione verde usando l'interfaccia della riga di comando di Azure.If you're not satisfied with your change, you can modify your application code, build a new jar package, and upload it to your green deployment by using the Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminare la distribuzione di gestione temporaneaDelete the staging deployment

Per eliminare la distribuzione di gestione temporanea dalla porta di Azure, passare alla pagina di distribuzione di gestione temporanea e quindi selezionare il pulsante **Elimina.To** delete your staging deployment from the Azure port, go to your staging deployment page, and then select the Delete button.

In alternativa, eliminare la distribuzione di gestione temporanea dall'interfaccia della riga di comando di Azure eseguendo il comando seguente:Alternatively, delete your staging deployment from the Azure CLI by running the following command:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
