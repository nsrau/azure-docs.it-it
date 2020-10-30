---
title: Gestione dei clienti potenziali con Archiviazione BLOB di Azure - Marketplace commerciale di Microsoft
description: Informazioni su come usare BLOB di Azure per configurare i clienti potenziali per Microsoft AppSource e Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/01/2020
ms.openlocfilehash: cd0b708ac3a1b16804430584dfcb01b3d2a4fae2
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042385"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Usare Archiviazione BLOB di Azure per gestire i clienti potenziali del marketplace commerciale

>[!Caution]
>Il supporto di Archiviazione BLOB di Azure per il marketplace commerciale è stato deprecato e il servizio non è più disponibile tra le opzioni di elaborazione dei clienti potenziali nell'offerta. Se si dispone di un'offerta del marketplace commerciale la cui gestione dei clienti potenziali è configurata su BLOB di Azure, non si riceveranno più i clienti potenziali. Aggiornare la configurazione della gestione dei clienti potenziali a una delle altre opzioni di gestione dei clienti potenziali. Informazioni sulle altre opzioni disponibili nella [pagina di destinazione della gestione dei clienti potenziali](./commercial-marketplace-get-customer-leads.md).

 Se il sistema Customer Relationship Management (CRM) non è supportato in modo esplicito nel Centro per i partner per la ricezione dei clienti potenziali di Microsoft AppSource e Azure Marketplace, è possibile usare Archiviazione BLOB di Azure. È quindi possibile scegliere di esportare i dati e importarli nel sistema CRM. Le istruzioni in questo articolo illustrano il processo di creazione di un account di Archiviazione di Azure e di un BLOB in tale account. È anche possibile creare un nuovo flusso usando Power Automate per inviare una notifica tramite posta elettronica quando l'offerta riceve un cliente potenziale.

>[!NOTE]
>Il connettore di Power Automate usato in queste istruzioni richiede una sottoscrizione a pagamento di Power Automate. Prima di seguire le istruzioni riportate in questo articolo, assicurarsi di tenere conto di questo aspetto.

## <a name="configure-azure-blob-storage"></a>Configurare Archiviazione BLOB di Azure

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).

2. Quando l'account di Azure è attivo, accedere al [portale di Azure](https://portal.azure.com).

3. Nel portale di Azure creare un account di archiviazione seguendo questa procedura.  
    1. Selezionare **+Crea una risorsa** nella barra del menu a sinistra.  Il riquadro (pannello) **Nuovo** verrà visualizzato a destra.
    2. Selezionare **Archiviazione** nel riquadro **Nuovo** .  Verrà visualizzato l'elenco **In primo piano** a destra.
    3. Selezionare **Account di archiviazione** per iniziare la creazione dell'account.  Seguire le istruzioni riportate nell'articolo [Creare un account di archiviazione](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).

    ![Procedura di creazione di un account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Per altre informazioni sugli account di archiviazione, vedere questa [Guida introduttiva](../../storage/blobs/storage-quickstart-blobs-portal.md).  Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

4. Attendere fino a quando non viene eseguito il provisioning dell'account di archiviazione, procedura che in genere richiede alcuni minuti.  Accedere quindi all'account di archiviazione dalla pagina **Home** del portale di Azure selezionando **Visualizzare tutte le risorse** oppure **Tutte le risorse** dalla barra del menu di spostamento a sinistra del portale di Azure.

    ![Accedere all'account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Nel riquadro dell'account di archiviazione selezionare **Chiavi di accesso** e copiare il valore di *Stringa di connessione* per la chiave. Salvare questo valore perché si tratta del valore di *Stringa di connessione all'account di archiviazione* che sarà necessario specificare nel portale di pubblicazione per ricevere i clienti potenziali per l'offerta nel marketplace.

     Un esempio di stringa di connessione è:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Screenshot che mostra la pagina "chiavi di accesso" con la casella di testo "stringa di connessione" evidenziata.](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Dalla pagina dell'account di archiviazione selezionare **BLOB** .

   ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Nella pagina BLOB selezionare il pulsante **+ Contenitore** .

8. Digitare un **nome** per il nuovo contenitore. Il nome del contenitore deve essere composto da lettere minuscole, deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e trattino (-). Per altre informazioni sui nomi di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

    Salvare questo valore perché si tratta del valore di *Nome contenitore* che è necessario specificare nel portale di pubblicazione per ricevere i clienti potenziali per l'offerta nel marketplace.

9. Impostare il livello di accesso pubblico al contenitore come **Privato (nessun accesso anonimo)** .

10. Fare clic su **OK** per creare il contenitore.

    ![Nuovo contenitore](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Configurare l'offerta per inviare i clienti potenziali ad Archiviazione BLOB di Azure

Quando si è pronti a configurare le informazioni di gestione dei clienti potenziali per l'offerta nel portale di pubblicazione, seguire questa procedura:

1. Andare alla pagina **Configurazione dell'offerta** relativa all'offerta.
2. Nella sezione **Customer leads** (Clienti potenziali) selezionare **Connetti** .

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Clienti potenziali":::

3. Nella finestra popup Dettagli connessione selezionare **BLOB di Azure** come Destinazione del lead.

    ![Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Immettere il **nome del contenitore** e la **stringa di connessione dell'account di archiviazione** ottenuti seguendo queste istruzioni.

    * Esempio di nome del contenitore: `marketplaceleadcontainer`
    * Esempio di stringa di connessione dell'account di archiviazione: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selezionare **Salva** .

    > [!NOTE]
    > È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i clienti potenziali per l'offerta.


