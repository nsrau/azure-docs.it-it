---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 30ca4d330d9b16214396ac81e5ab5722ca0e7569
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89254296"
---
1. Nel [portale di Azure](https://portal.azure.com/) selezionare la risorsa Azure Stack Edge e quindi passare a **Panoramica**. Il dispositivo dovrebbe essere online.

2. Selezionare **+ Aggiungi account di archiviazione** sulla barra dei comandi del dispositivo. 

   ![Aggiungere un account di archiviazione](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. Nel riquadro **Aggiungi account di archiviazione Edge** specificare le impostazioni seguenti:

    a. Un nome univoco per l'account di archiviazione Edge disponibile nel dispositivo. I nomi degli account di archiviazione possono contenere solo numeri e lettere minuscole. I caratteri speciali non sono consentiti. Il nome dell'account di archiviazione deve essere univoco nel singolo dispositivo, non in tutti i dispositivi.

    b. Una descrizione facoltativa per le informazioni sui dati contenuti nell'account di archiviazione.  
    
    c. Per impostazione predefinita, l'account di archiviazione Edge è associato a un account di archiviazione di Azure nel cloud e viene eseguito il push automatico dei dati dell'account di archiviazione nel cloud. Specificare l'account di archiviazione di Azure a cui è associato l'account di archiviazione Edge.  

    d. Creare quindi un nuovo contenitore o selezionarlo da un contenitore esistente nell'account di archiviazione di Azure. Tutti i dati del dispositivo scritti nell'account di archiviazione Edge vengono caricati automaticamente nel contenitore di archiviazione selezionato nell'account di archiviazione di Azure associato.

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. Dopo aver specificato tutte le opzioni dell'account di archiviazione, selezionare **Aggiungi** per creare l'account di archiviazione Edge. Dopo la creazione dell'account di archiviazione Edge si riceverà una notifica. Il nuovo account di archiviazione Edge viene quindi visualizzato nell'elenco degli account di archiviazione nel portale di Azure. 

    
4. Se si seleziona questo nuovo account di archiviazione e si passa a **Chiavi di accesso**, è possibile trovare l'endpoint servizio BLOB e il nome dell'account di archiviazione corrispondente. Copiare queste informazioni perché questi valori, unitamente alle chiavi di accesso, consentiranno di connettersi all'account di archiviazione Edge.

    ![Aggiungere un account di archiviazione](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Per ottenere le chiavi di accesso, è necessario [connettersi alle API locali del dispositivo con Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 
