---
title: Tabella di Azure nel programma del marketplace commerciale Azure Marketplace
description: Configurare la gestione dei lead per il BLOB di AzureConfigure lead management for Azure Blob
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285249"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Lead management instructions for Azure Blob

>[!Caution]
>L'opzione BLOB di Azure per elaborare i lead dall'offerta del marketplace è deprecata. Se al momento è stata pubblicata un'offerta con la configurazione di gestione dei lead per il BLOB di Azure, non si ricevono più i clienti. Aggiornare la configurazione di gestione dei lead a una qualsiasi delle altre opzioni di gestione dei lead. Scopri di più sulle altre opzioni nella pagina di destinazione della [gestione dei lead."](./commercial-marketplace-get-customer-leads.md)

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel Centro per i partner per la ricezione dei lead di Azure Marketplace e AppSource, è possibile usare un BLOB di Azure per gestire questi lead. È quindi possibile scegliere di esportare i dati e importarlo nel sistema CRM. Le istruzioni in questo articolo illustrano il processo di creazione di un account di Archiviazione di Azure e un BLOB di Azure con tale account. Inoltre, è possibile creare un nuovo flusso usando Microsoft Flow per inviare una notifica tramite posta elettronica quando l'offerta riceve un lead.


## <a name="how-to-configure-azure-blob"></a>Come configurare il BLOB di AzureHow to configure Azure Blob

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).
1. Dopo aver attivo l'account Azure, accedere al portale di [Azure.](https://portal.azure.com)
1. Nel portale di Azure creare un account di archiviazione usando la procedura seguente.  
    1. Nella barra dei menu a sinistra, **selezionare Crea una risorsa.**  Il riquadro **Nuovo** (blade) verrà visualizzato a destra.
    2. Selezionare Archiviazione nel riquadro **Nuovo.Select Storage** in the **New** pane.  A destra viene visualizzato un elenco **In primo piano.**
    3. Selezionare **l'account di archiviazione** per avviare la creazione dell'account.  Seguire le istruzioni nell'articolo [Creare un account di archiviazione.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    ![Procedura di creazione di un account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Per altre informazioni sugli account di archiviazione, selezionare [Esercitazione sulle guide rapide](https://docs.microsoft.com/azure/storage/).  Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

4. Attendere il provisioning dell'account di archiviazione, un processo che richiede in genere alcuni minuti.  Accedere quindi all'account di archiviazione dalla **home** page del portale di Azure selezionando Visualizza tutte le risorse o Tutte le risorse dalla barra di menu di spostamento sinistra del portale di Azure.Then access your storage account from the Home page of the Azure portal by selecting **See all your resources** or by selecting All **resources** from the left navigation menubar of the Azure portal.

    ![Accedere all'account di archiviazione di AzureAccess your Azure storage account](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Nel riquadro dell'account di archiviazione selezionare Chiavi di **accesso** e copiare il valore *Stringa* di connessione per la chiave. Salvare questo valore perché questo è il valore Stringa di *connessione dell'account* di archiviazione che sarà necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace.

     Un esempio di puntura di connessione è:An example of a connection sting is:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Nella pagina dell'account di archiviazione selezionare **BLOB**.

   ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Una volta nella pagina dei BLOB, selezionare il pulsante **Contenitore.**

8. Digitare un **nome** per il nuovo contenitore. Il nome del contenitore deve essere composto da lettere minuscole, deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e trattino (-). Per altre informazioni sui nomi di contenitori e BLOB, vedere Denominazione e riferimento a [contenitori, BLOB e metadati.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)

    Salvare questo valore perché questo è il valore *Di nome contenitore* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace.

9. Impostare il livello di accesso pubblico al contenitore su **Privato (nessun accesso anonimo).**

10. Fare clic su **OK** per creare il contenitore.

    ![Nuovo contenitore](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Configurare l'offerta per l'invio dei lead al BLOB di AzureConfigure your offer to send leads to the Azure Blob

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, attenersi alla seguente procedura:

1. Accedi alla pagina **Configurazione offerta** per la tua offerta.
2. Selezionare **Connetti** nella sezione Gestione lead.

    ![Offerta Connect](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Nella finestra popup Dettagli connessione selezionare BLOB di **Azure** per Destinazione lead.

    ![Dettaglio connessione](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Specificare il **nome del contenitore** e la stringa di connessione dell'account di **archiviazione** ottenuti seguendo queste istruzioni.

    * Esempio di nome del contenitore:Container name example:`marketplaceleadcontainer`
    * Esempio di stringa `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![di connessione dell'account di archiviazione: Dettagli connessioneStorage Account Connection string example: Connection Detail](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selezionare **Salva**.

    > [!NOTE]
    > È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.


