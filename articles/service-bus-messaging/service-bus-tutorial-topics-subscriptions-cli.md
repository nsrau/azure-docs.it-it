---
title: Usare l'interfaccia della riga di comando di Azure per creare argomenti e sottoscrizioni del bus di servizio
description: Questa guida di avvio rapido illustra come creare un argomento del bus di servizio e le sottoscrizioni a tale argomento usando l'interfaccia della riga di comando di Azure.
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069697"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Usare l'interfaccia della riga di comando di Azure per creare un argomento del bus di servizio e le sottoscrizioni all'argomento
In questa guida di avvio rapido si usa l'interfaccia della riga di comando di Azure per creare un argomento del bus di servizio e quindi le sottoscrizioni a tale argomento. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Cosa sono gli argomenti e le sottoscrizioni del bus di servizio?
Gli argomenti e le sottoscrizioni del bus di servizio supportano un modello di comunicazione con messaggistica di *pubblicazione-sottoscrizione* . Quando si usano gli argomenti e le sottoscrizioni, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro, ma scambiano messaggi tramite un argomento, che agisce da intermediario.

![Concetti relativi agli argomenti](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Diversamente dalle code del bus di servizio, in cui ogni messaggio viene elaborato da un unico consumer, gli argomenti e le sottoscrizioni offrono una forma di comunicazione "uno a molti" tramite un modello di pubblicazione-sottoscrizione. È possibile registrare più sottoscrizioni a un argomento. Quando un messaggio viene inviato a un argomento, viene reso disponibile affinché ogni sottoscrizione possa gestirlo o elaborarlo in modo indipendente. La sottoscrizione a un argomento è simile a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. È possibile registrare regole di filtro per un argomento in base alla sottoscrizione in modo da poter filtrare/limitare i messaggi a un argomento ricevuti dalle diverse sottoscrizioni degli argomenti.

Gli argomenti e le sottoscrizioni del bus di servizio consentono scalabilità per elaborare grandi quantità di messaggi tra un numero elevato di utenti e applicazioni.

## <a name="prerequisites"></a>Prerequisiti
Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][free account] prima di iniziare.

In questa guida di avvio rapido si usa Azure Cloud Shell, che è possibile avviare dopo l'accesso al portale di Azure. Per informazioni dettagliate su Azure Cloud Shell, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md). È anche possibile [installare](/cli/azure/install-azure-cli) e usare Azure PowerShell nel computer. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Creare un argomento del bus di servizio e le sottoscrizioni
Ogni [sottoscrizione a un argomento](service-bus-messaging-overview.md#topics) può ricevere una copia di ogni messaggio. Gli argomenti sono completamente compatibili a livello di protocollo e di semantica con le code del bus di servizio. Gli argomenti del bus di servizio supportano un'ampia gamma di regole di selezione con condizioni di filtro, con azioni facoltative per impostare o modificare le proprietà dei messaggi. Ogni volta che una regola corrisponde, genera un messaggio. Per altre informazioni su regole, filtri e azioni, seguire questo [collegamento](topic-filters.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Avviare Azure Cloud Shell selezionando l'icona indicata nell'immagine seguente. Se Cloud Shell è in modalità **PowerShell**, passare alla modalità **Bash**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Avviare Cloud Shell":::
3. Usare il comando seguente per creare un gruppo di risorse di Azure. Se si vuole, aggiornare il nome del gruppo di risorse e la località. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Usare il comando seguente per creare uno spazio dei nomi per la messaggistica del bus di servizio. Aggiornare il nome dello spazio dei nomi in modo che sia univoco. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Usare il comando seguente per creare un argomento nello spazio dei nomi. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Creare la prima sottoscrizione all'argomento.
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Creare la seconda sottoscrizione all'argomento.
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Creare la terza sottoscrizione all'argomento.
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Creare un filtro per la prima sottoscrizione usando un filtro con proprietà personalizzate (`StoreId` è `Store1`, `Store2` o `Store3`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Creare un filtro per la seconda sottoscrizione usando un filtro con proprietà personalizzate (`StoreId = Store4`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Creare un filtro per la terza sottoscrizione usando un filtro con proprietà personalizzate (`StoreId` non è `Store1`, `Store2`, `Store3` o `Store4`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Usare il comando seguente per ottenere la stringa di connessione primaria per lo spazio dei nomi. Questa stringa di connessione verrà usata per connettersi alla coda e inviare e ricevere messaggi. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Prendere nota della stringa di connessione e del nome dell'argomento. Si useranno per inviare e ricevere messaggi. 
    

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come inviare messaggi a un argomento e ricevere tali messaggi tramite una sottoscrizione, vedere l'articolo seguente: selezionare il linguaggio di programmazione nel sommario. 

> [!div class="nextstepaction"]
> [Pubblicare e sottoscrivere messaggi](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
