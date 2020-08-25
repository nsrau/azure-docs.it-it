---
title: Guida introduttiva - Usare l'interfaccia della riga di comando di Azure per creare una coda del bus di servizio | Microsoft Docs
description: Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare uno spazio dei nomi del bus di servizio e quindi una coda in tale spazio dei nomi.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 201ea38c6feabbda2576d8480a9983f00d62d175
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191258"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Usare l'interfaccia della riga di comando di Azure per creare uno spazio dei nomi del bus di servizio e una coda
Questo argomento di avvio rapido illustra come creare uno spazio dei nomi del bus di servizio e una coda con l'interfaccia della riga di comando di Azure. Spiega anche come ottenere le credenziali di autorizzazione che un'applicazione client può utilizzare per inviare/ricevere messaggi da e verso la coda. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Prerequisiti
Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][free account] prima di iniziare.

In questa guida di avvio rapido si usa Azure Cloud Shell, che è possibile avviare dopo l'accesso al portale di Azure. Per informazioni dettagliate su Azure Cloud Shell, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md). È anche possibile [installare](/cli/azure/install-azure-cli) e usare Azure PowerShell nel computer. 

## <a name="provision-resources"></a>Effettuare il provisioning delle risorse
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Avviare Azure Cloud Shell selezionando l'icona indicata nell'immagine seguente. Se Cloud Shell è in modalità **PowerShell**, passare alla modalità **Bash**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Avviare Cloud Shell":::
3. Usare il comando seguente per creare un gruppo di risorse di Azure. Se si vuole, aggiornare il nome del gruppo di risorse e la località. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Usare il comando seguente per creare uno spazio dei nomi per la messaggistica del bus di servizio.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Eseguire il comando seguente per creare una coda nello spazio dei nomi creato nel passaggio precedente. In questo esempio `ContosoRG` è il gruppo di risorse creato nel passaggio precedente. `ContosoSBusNS` è il nome dello spazio dei nomi del bus di servizio creato in tale gruppo di risorse. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Usare il comando seguente per ottenere la stringa di connessione primaria per lo spazio dei nomi. Questa stringa di connessione verrà usata per connettersi alla coda e inviare e ricevere messaggi. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Prendere nota della stringa di connessione e del nome della coda. Serviranno per inviare e ricevere messaggi. 


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato uno spazio dei nomi del bus di servizio e una coda nello spazio dei nomi. Per informazioni su come inviare/ricevere messaggi da e verso la coda, vedere uno degli argomenti di avvio rapido nella sezione **Inviare e ricevere messaggi**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

