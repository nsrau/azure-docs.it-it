---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673346"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Recuperare la stringa di connessione di Archiviazione di Azure

In precedenza è stato creato un account di archiviazione di Azure per l'uso da parte dell'app per le funzioni. La stringa di connessione per questo account è archiviata in modo sicuro nelle impostazioni dell'app in Azure. Scaricando l'impostazione nel file *local.settings.json*, è possibile usare tale connessione per scrivere in una coda di archiviazione nello stesso account quando si esegue la funzione localmente. 

1. Dalla radice del progetto eseguire il comando seguente, sostituendo `<app_name>` con il nome dell'app per le funzioni dall'argomento di avvio rapido precedente. Questo comando sovrascriverà tutti i valori esistenti nel file.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Aprire *local.settings.json* e copiare il valore denominato `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Il nome `AzureWebJobsStorage` e la stringa di connessione si usano in altre sezioni di questo articolo.

> [!IMPORTANT]
> Poiché *local.settings.json* contiene i segreti scaricati da Azure, escludere sempre questo file dal controllo del codice sorgente. Il file con estensione *gitignore* creato con un progetto di funzioni locale esclude il file per impostazione predefinita.