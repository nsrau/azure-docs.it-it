---
title: Usare Azure PowerShell per creare una coda del bus di servizio
description: Questo argomento di avvio rapido illustra come creare uno spazio dei nomi del bus di servizio e una coda nello spazio dei nomi con Azure PowerShell.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 085eedf3a3ce09689a5a7b7d4c69d1aade42ffb3
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185443"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Usare Azure PowerShell per creare uno spazio dei nomi del bus di servizio e una coda
Questo argomento di avvio rapido illustra come creare uno spazio dei nomi del bus di servizio e una coda con Azure PowerShell. Spiega anche come ottenere le credenziali di autorizzazione che un'applicazione client può utilizzare per inviare/ricevere messaggi da e verso la coda. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido, assicurarsi di avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][] prima di iniziare. 

In questa guida di avvio rapido si usa Azure Cloud Shell, che è possibile avviare dopo l'accesso al portale di Azure. Per informazioni dettagliate su Azure Cloud Shell, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md). È anche possibile [installare](/powershell/azure/install-Az-ps) e usare Azure PowerShell nel computer. 


## <a name="provision-resources"></a>Effettuare il provisioning delle risorse
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Avviare Azure Cloud Shell selezionando l'icona indicata nell'immagine seguente: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Avviare Cloud Shell":::
3. Nella finestra Cloud Shell in basso passare da **Bash** a **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Passare alla modalità PowerShell":::    
4. Usare il comando seguente per creare un gruppo di risorse di Azure. Se si vuole, aggiornare il nome del gruppo di risorse e la località. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Usare il comando seguente per creare uno spazio dei nomi per la messaggistica del bus di servizio. In questo esempio `ContosoRG` è il gruppo di risorse creato nel passaggio precedente. `ContosoSBusNS` è il nome dello spazio dei nomi del bus di servizio creato in tale gruppo di risorse. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Eseguire il comando seguente per creare una coda nello spazio dei nomi creato nel passaggio precedente. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Ottenere la stringa di connessione primaria per lo spazio dei nomi. Questa stringa di connessione verrà usata per connettersi alla coda e inviare e ricevere messaggi. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

