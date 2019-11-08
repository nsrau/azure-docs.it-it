---
title: Tabella di Azure in un programma per Marketplace commerciale | Azure Marketplace
description: Configurare la gestione dei lead per il BLOB di Azure
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: 5da4e0ab315b3f66a477b816f6fc5d27de7aa339
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812377"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Istruzioni per la gestione dei lead per BLOB di Azure

>[!Caution]
>L'opzione BLOB di Azure per elaborare i lead dall'offerta del Marketplace è stata deprecata. Se è attualmente disponibile un'offerta pubblicata con la configurazione della gestione dei lead per il BLOB di Azure, non si ricevono più clienti potenziali. Aggiornare la configurazione della gestione dei lead a una delle altre opzioni di gestione dei lead. Informazioni sulle altre opzioni nella pagina di [destinazione della gestione dei lead](./commercial-marketplace-get-customer-leads.md). "

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel centro per i partner per la ricezione di Azure Marketplace e AppSource lead, è possibile usare un BLOB di Azure per gestire i lead. È quindi possibile scegliere di esportare i dati e importarli nel sistema CRM. Le istruzioni riportate in questo articolo illustrano il processo di creazione di un account di archiviazione di Azure e di un BLOB di Azure con tale account. Inoltre, è possibile creare un nuovo flusso usando Microsoft Flow per inviare una notifica tramite posta elettronica quando l'offerta riceve un lead.


## <a name="how-to-configure-azure-blob"></a>Come configurare un BLOB di Azure

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).
1. Quando l'account Azure è attivo, accedere al [portale di Azure](https://portal.azure.com).
1. Nella portale di Azure creare un account di archiviazione usando la procedura riportata di seguito.  
    1. Selezionare **+ Crea una risorsa** nella barra dei menu a sinistra.  Il **nuovo** riquadro (pannello) verrà visualizzato a destra.
    2. Selezionare **archiviazione** nel **nuovo** riquadro.  A destra viene visualizzato un elenco in **primo piano** .
    3. Selezionare l' **account di archiviazione** per iniziare la creazione dell'account.  Seguire le istruzioni riportate nell'articolo [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Procedura di creazione di un account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Per altre informazioni sugli account di archiviazione, selezionare [esercitazione introduttiva](https://docs.microsoft.com/azure/storage/).  Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

4. Attendere fino a quando non viene eseguito il provisioning dell'account di archiviazione, un processo che in genere richiede alcuni minuti.  Accedere quindi all'account di archiviazione dalla **Home** page del portale di Azure selezionando **Visualizza tutte le risorse** oppure selezionando **tutte le risorse** dalla barra dei menu di spostamento a sinistra del portale di Azure.

    ![Accedere all'account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Nel riquadro account di archiviazione selezionare **chiavi di accesso** e copiare il valore della *stringa di connessione* per la chiave. Salvare questo valore poiché si tratta del valore della *stringa di connessione dell'account di archiviazione* che sarà necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace.

     Un esempio di una connessione Sting è:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Nella pagina dell'account di archiviazione selezionare **BLOB**.

   ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Una volta nella pagina BLOB, selezionare il pulsante **+ contenitore** .

8. Digitare un **nome** per il nuovo contenitore. Il nome del contenitore deve essere composto da lettere minuscole, deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e trattino (-). Per altre informazioni sui nomi di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

    Salvare questo valore poiché si tratta del valore del *nome del contenitore* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace.

9. Impostare il livello di accesso pubblico sul contenitore come **privato (nessun accesso anonimo)** .

10. Fare clic su **OK** per creare il contenitore.

    ![Nuovo contenitore](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Configurare l'offerta per inviare lead al BLOB di Azure

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura:

1. Passare alla pagina di **configurazione dell'offerta** per l'offerta.
2. Selezionare **Connetti** nella sezione gestione dei lead.

    ![Connetti offerta](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Nella finestra popup Dettagli connessione selezionare **BLOB di Azure** per la destinazione principale.

    ![Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Fornire il **nome del contenitore** e la **stringa di connessione dell'account di archiviazione** ottenuta seguendo queste istruzioni.

    * Esempio di nome contenitore: `marketplaceleadcontainer`
    * Esempio di stringa di connessione all'account di archiviazione: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selezionare **Salva**.

    > [!NOTE]
    > È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.


