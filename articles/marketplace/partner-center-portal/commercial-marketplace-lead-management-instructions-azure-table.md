---
title: Configurare Microsoft Commercial Marketplace Lead Management con una tabella di Azure
description: Informazioni su come usare una tabella di Azure per gestire lead da Microsoft AppSource e Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 9814b03e348fc807c04364afbf027369f917670a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131135"
---
# <a name="configure-lead-management-by-using-an-azure-table"></a>Configurare la gestione dei lead con una tabella di Azure

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel centro per i partner per ricevere Microsoft AppSource e i lead di Azure Marketplace, è possibile usare una tabella di Azure per gestire questi lead. È quindi possibile scegliere di esportare i dati e importarli nel sistema CRM. Le istruzioni riportate in questo articolo illustrano il processo di creazione di un account di archiviazione di Azure e di una tabella di Azure con tale account. Inoltre, è possibile creare un nuovo flusso usando Power Automate per inviare una notifica tramite posta elettronica quando l'offerta riceve un lead.

## <a name="configure-an-azure-table"></a>Configurare una tabella di Azure

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).
1. Quando l'account Azure è attivo, accedere al [portale di Azure](https://portal.azure.com).
1. Nella portale di Azure creare un account di archiviazione usando la procedura seguente:

    1. Selezionare **+ Crea una risorsa** nella barra dei menu a sinistra. Il **nuovo** riquadro viene visualizzato a destra.
    1. Selezionare **archiviazione** nel **nuovo** riquadro. A destra viene visualizzato un elenco in **primo piano** .
    1. Selezionare **account di archiviazione** per avviare la creazione dell'account. Seguire le istruzioni riportate in [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Procedura di creazione di un account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Per altre informazioni sugli account di archiviazione, vedere [esercitazione introduttiva](https://docs.microsoft.com/azure/storage/). Per altre informazioni sui prezzi di archiviazione, vedere [prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/).

1. Attendere fino a quando non viene eseguito il provisioning dell'account di archiviazione. Questo processo richiede in genere alcuni minuti. Accedere quindi all'account di archiviazione dalla **Home** page del portale di Azure selezionando **Visualizza tutte le risorse**. È anche possibile selezionare **tutte le risorse** dalla barra dei menu a sinistra del portale di Azure.

    ![Accedere all'account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. Nel riquadro account di archiviazione selezionare **chiavi di accesso** e copiare il valore della **stringa di connessione** per la chiave. Salvare questo valore perché si tratta del valore della **stringa di connessione dell'account di archiviazione** che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta di Azure Marketplace. 

    Di seguito è riportato un esempio di una stringa di connessione.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. Nel riquadro account di archiviazione selezionare **tabelle**e selezionare **+ tabella** per creare una tabella. Immettere un nome per la tabella e fare clic su **OK**. Salvare questo valore perché sarà necessario se si vuole configurare un flusso per ricevere notifiche tramite posta elettronica quando vengono ricevuti i lead.

    ![Tabelle di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    È possibile usare [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) o qualsiasi altro strumento per visualizzare i dati nella tabella di archiviazione. È anche possibile esportare i dati nella tabella di Azure. 

## <a name="optional-use-power-automate-with-an-azure-table"></a>Opzionale Usare Power automatizzate con una tabella di Azure 

È possibile usare [Power automatici](https://docs.microsoft.com/flow/) per automatizzare le notifiche ogni volta che un lead viene aggiunto a una tabella di Azure. Se non si dispone di un account, è possibile [iscriversi per ottenere un account gratuito](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Esempio di notifica di cliente potenziale

Usare questo esempio come guida per creare un semplice flusso per l’invio automatico di una notifica di posta elettronica quando un nuovo cliente potenziale viene aggiunto a una tabella di Azure. In questo esempio viene impostata una ricorrenza per l'invio di informazioni sui lead ogni ora se l'archivio tabelle viene aggiornato.

1. Accedere all'account Power automatizzate.
1. Sulla barra a sinistra selezionare **flussi personali**.
1. Nella barra superiore selezionare **+ nuovo**. 
1. Nell'elenco a discesa selezionare **+ scheduled--from blank**.

   ![Flussi personali + pianificato--da zero](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Nella finestra **Compila un flusso pianificato** , per **Ripeti ogni**, selezionare **1** per l'intervallo e l' **ora** per la frequenza. Inoltre, assegnare un nome al flusso se lo si desidera. Selezionare **Crea**.

   >[!NOTE]
   >Sebbene in questo esempio venga usato un intervallo di un'ora, è possibile selezionare l'intervallo e la frequenza più adatti alle esigenze aziendali.

   ![Creazione di un flusso pianificato](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Selezionare **+ nuovo passaggio**.
1. Nella finestra **scegliere un'azione** cercare **Ottieni ora precedente**. Quindi in **azioni**selezionare **Ottieni ora passata**.

   ![Scegliere un'azione](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Nella finestra **Ottieni ora precedente** impostare **intervallo** su **1**. Dall'elenco a discesa **unità di tempo** selezionare **ora**.

    >[!IMPORTANT]
    >Verificare che l'intervallo e l'unità di tempo inviati nel passaggio 8 corrispondano all'intervallo e alla frequenza configurati per la ricorrenza nel passaggio 5.

    ![Imposta intervallo di tempo di ottenimento precedente](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >È possibile controllare il flusso in qualsiasi momento per verificare che ogni passaggio sia configurato correttamente. Per controllare il flusso, selezionare **controllo del flusso** dalla barra dei menu di **Flow** .

   Nel prossimo set di passaggi verrà stabilita la connessione alla tabella di Azure e si configurerà la logica di elaborazione per gestire i nuovi lead.

1. Dopo il passaggio 8, selezionare **+ nuovo passaggio**. Cercare quindi **Ottieni entità** nella finestra **Scegli un'azione** .
1. In **azioni**selezionare **Ottieni entità (archiviazione tabelle di Azure)**.
1. Nella finestra **archiviazione tabelle di Azure** specificare le informazioni per le caselle seguenti e selezionare **Crea**:

    * **Nome connessione**: fornire un nome significativo per la connessione che si sta stabilendo tra questo flusso e la tabella di Azure.
    * **Nome dell'account di archiviazione**: specificare il nome dell'account di archiviazione per la tabella di Azure. È possibile trovare questo nome nella pagina **chiavi di accesso** dell'account di archiviazione.
    * **Chiave di archiviazione condivisa**: specificare il valore della chiave per l'account di archiviazione per la tabella di Azure. È possibile trovare questo valore nella pagina **chiavi di accesso** dell'account di archiviazione.

      ![Finestra di archiviazione tabelle di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Dopo aver selezionato **Crea**, viene visualizzata la finestra **Ottieni entità** . Qui, selezionare **Mostra opzioni avanzate**e specificare le informazioni per le caselle seguenti:

   * **Tabella**: selezionare il nome dell'archiviazione tabelle di Azure (dal passaggio 6 delle istruzioni nella sezione "configurare una tabella di Azure"). La figura seguente mostra la richiesta di selezione della tabella "marketplaceleads" per questo esempio.

     ![Ottenere la finestra entità](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Filtro query**: selezionare questa casella e incollare la funzione nella casella:`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Ottenere entità, casella query filtro](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Ora che è stata completata la configurazione della connessione alla tabella di Azure, selezionare **nuovo passaggio** per aggiungere una condizione per l'analisi della tabella di Azure per i nuovi lead.

1. Nella finestra **scegliere un'azione** selezionare **azioni**. Quindi selezionare **controllo condizione**.

    ![Finestra Scegliere un'azione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Nella finestra **condizione** selezionare **scegliere un valore**. Quindi selezionare **espressione** nella finestra popup.

1. Incollare `length(body('Get_entities')?['value'])` nella casella **FX** . Selezionare **OK** per aggiungere questa funzione. 

1. Per completare la configurazione della condizione:
    1. Select **è maggiore di** nell'elenco a discesa.
    2. Immettere **0** come valore.

        ![Finestra condizione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   Nei passaggi successivi si configura l'azione da eseguire in base al risultato della condizione:

   * Se la condizione si risolve in in **caso**contrario, non eseguire alcuna operazione.
   * Se la condizione restituisce **Se sì**, attivare un'azione che si connetta all'account di Office 365 per inviare un messaggio di posta elettronica. 

1. Selezionare **Aggiungi un'azione** in **se sì**.

    ![Finestra condizione, se sì, aggiungere un'azione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)**.

    ![Finestra condizione, se sì, invia un messaggio di posta elettronica](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Per usare un provider di posta elettronica diverso, cercare e selezionare **Invia una notifica di posta elettronica (mail)** come azione. Le istruzioni illustrano come configurare usando Office 365 Outlook, ma le istruzioni sono simili per un provider di posta elettronica diverso.

1. Nella finestra Office 365 Outlook specificare le informazioni per le caselle seguenti:

    1. **A**: immettere un indirizzo di posta elettronica per tutti gli utenti che riceveranno la notifica.
    1. **Oggetto**: specificare un oggetto per il messaggio di posta elettronica. Ecco un esempio di **nuovi lead.**
    1. **Corpo**: aggiungere il testo che si desidera includere in ogni messaggio di posta elettronica (facoltativo), quindi incollare `body('Get_entities')?['value']`.

    >[!NOTE]
    >È possibile inserire punti dati statici o dinamici aggiuntivi al corpo del messaggio di posta elettronica.

    ![Finestra di condizione, se sì, finestra di Outlook per Office 365](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Selezionare **Salva** per salvare il flusso. Power automatizzate esegue automaticamente il test del flusso per individuare eventuali errori. Se non sono presenti errori, l'esecuzione del flusso viene avviata dopo il salvataggio.

La figura seguente illustra un esempio di come dovrebbe apparire il flusso finale.

![Esempio di flusso finale](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gestire il flusso

La gestione del flusso dopo l'esecuzione è facile. Il controllo del flusso è completo. Ad esempio, è possibile arrestarlo, modificarlo, visualizzare una cronologia di esecuzione e ottenere analisi. La figura seguente mostra le opzioni disponibili per la gestione di un flusso.

 ![Gestire un flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Il flusso rimane in esecuzione fino a quando non viene arrestato manualmente con **Disattiva flusso**.

Se non si ricevono notifiche di posta elettronica di lead, significa che non sono stati aggiunti nuovi lead alla tabella di Azure. Se si verificano errori di flusso, si riceverà un messaggio di posta elettronica simile a questo esempio.

 ![Notifica di posta elettronica sugli errori del flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurare l'offerta per l'invio di lead alla tabella di Azure

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura.

1. Passare alla pagina di **installazione dell'offerta** per l'offerta.
1. Selezionare **Connetti** nella sezione **gestione dei lead** .
1. Nella finestra popup **Dettagli connessione** selezionare **tabella di Azure** per la **destinazione principale**. Incollare la stringa di connessione dall'account di archiviazione di Azure creato seguendo i passaggi precedenti nella casella **stringa di connessione dell'account di archiviazione** .
1. **Email di contatto**: fornire i messaggi di posta elettronica per gli utenti della società che devono ricevere notifiche tramite posta elettronica quando viene ricevuto un nuovo lead. È possibile specificare più messaggi di posta elettronica separandoli con punti e virgola.
1. Selezionare **OK**.

Per assicurarsi di avere effettuato la connessione a una destinazione principale, selezionare il pulsante **convalida** . In caso di esito positivo, sarà presente un lead di test nella destinazione principale.

>[!NOTE]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati i lead, Microsoft invia lead alla tabella di Azure. Se è stato configurato un flusso, verrà inviato anche un messaggio di posta elettronica all'indirizzo di posta elettronica configurato.

![Lead management (Gestione di clienti potenziali)](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gestione dei lead, dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gestione lead, account di archiviazione dettagli connessione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

