---
title: Gestione dei lead con l'archiviazione tabelle di Azure - Marketplace commerciale Microsoft
description: Informazioni su come usare l'archiviazione tabelle di Azure per configurare lead per Microsoft AppSource e Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 485da8549175af8813a9d0c3052d1e77f336e619
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120841"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Usare l'archiviazione tabelle di Azure per gestire i lead del marketplace commerciale

Se il sistema CRM (Customer Relationship Management, Gestione rapporti con clienti) non è supportato in modo esplicito nel Centro per i partner per la ricezione dei lead di Microsoft AppSource e Azure Marketplace, è possibile usare l'archiviazione tabelle di Azure per gestire i lead. È quindi possibile scegliere di esportare i dati e importarli nel sistema CRM. Questo articolo illustra come creare un account di archiviazione di Azure e una tabella in tale account. È anche possibile creare un nuovo flusso usando Power Automate per inviare una notifica tramite posta elettronica quando l'offerta riceve un lead.

## <a name="configure-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).
1. Quando l'account di Azure è attivo, accedere al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure creare un account di archiviazione seguendo questa procedura:

    1. Selezionare **Crea una risorsa** nella barra del menu a sinistra. Sulla destra viene visualizzato il riquadro **Nuovo**.
    1. Selezionare **Archiviazione** nel riquadro **Nuovo**. A destra viene visualizzato un elenco **In primo piano**.
    1. Selezionare **Account di archiviazione** per iniziare la creazione dell'account. Seguire le istruzioni in [Crea un account di archiviazione](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).

        ![Procedura di creazione di un account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Per altre informazioni sugli account di archiviazione, vedere [Documentazione di Archiviazione di Azure](../../storage/index.yml). Per altre informazioni sui prezzi delle risorse di archiviazione, vedere [Panoramica dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

1. Attendere fino a quando non viene eseguito il provisioning dell'account di archiviazione. Questo processo in genere richiede qualche minuto. 

## <a name="create-a-table-in-your-storage-account"></a>Creare una tabella nell'account di archiviazione

1. Dalla **pagina iniziale** del portale di Azure, selezionare **Visualizzare tutte le risorse** per accedere all'account di archiviazione. È anche possibile selezionare **Tutte le risorse** nel menu a sinistra del portale di Azure.

    ![Accedere all'account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. Nel riquadro dell'account di archiviazione selezionare **Chiavi di accesso** e copiare il valore di **Stringa di connessione** per la chiave. Salvare questo valore perché si tratta del valore di **Stringa di connessione a account di archiviazione** che sarà necessario specificare nel portale di pubblicazione per ricevere i lead per l'offerta di Azure Marketplace. 

    Di seguito è riportato un esempio di stringa di connessione.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. Nel riquadro dell'account di archiviazione selezionare **Tabelle** e **Tabella** per creare una tabella. Immettere un nome per la tabella e selezionare **OK**. Salvare questo valore perché sarà necessario se si vuole configurare un flusso per ricevere notifiche di posta elettronica quando vengono ricevuti i lead.

    ![Tabelle di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    È possibile usare [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) o qualsiasi altro strumento per visualizzare i dati nella tabella di archiviazione. È anche possibile esportare i dati nella tabella di Azure. 

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(Facoltativo) Usare Power Automate per ottenere le notifiche dei lead

È possibile usare [Power Automate](https://docs.microsoft.com/flow/) per automatizzare le notifiche ogni volta che un lead viene aggiunto alla tabella di Archiviazione di Azure. Se non si ha un account, è possibile [iscriversi per creare un account gratuito](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Esempio di notifica di cliente potenziale

In questo esempio viene creato un flusso per l’invio automatico di una notifica di posta elettronica quando un nuovo lead viene aggiunto all'archiviazione tabelle di Azure. Viene anche configurata una ricorrenza per inviare le informazioni sui lead ogni ora, se viene aggiornata l'archiviazione tabelle.

1. Accedere al proprio account Power Automate.
1. Nella barra a sinistra selezionare **Flussi personali**.
1. Nella barra in alto selezionare **Nuovo**. 
1. Nell'elenco a discesa selezionare **+ Scheduled--from blank** (Pianificato--Da zero).

   ![Flussi personali + Pianificato--Da zero](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Nella finestra **Crea un flusso pianificato** per **Ripeti ogni** selezionare **1** per l'intervallo e **Ora** per la frequenza. Se si vuole, è anche possibile assegnare un nome al flusso. Selezionare **Create** (Crea).

   >[!NOTE]
   >Sebbene in questo esempio venga usato un intervallo di un'ora, è possibile selezionare l'intervallo e la frequenza che meglio si adattano alle proprie esigenze aziendali.

   ![Creare un flusso pianificato](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Selezionare **+ nuovo passaggio**.
1. Nella finestra **Scegliere un'azione** cercare **Recupera ora precedente** e quindi in **Azioni** selezionare **Recupera ora precedente**.

   ![Scegliere un'azione](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Nella finestra **Recupera ora precedente** impostare il valore di **Intervallo** su **1**. Dall’elenco a discesa **Unità di tempo**, selezionare **Ora**.

    >[!IMPORTANT]
    >Assicurarsi che l’intervallo e l’unità di tempo del passaggio 8 corrispondano all'intervallo e alla frequenza configurati per la ricorrenza nel passaggio 5.

    ![Configurare l’intervallo per recuperare l’ora precedente](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >È possibile controllare il flusso in qualsiasi momento per verificare che ogni passaggio sia configurato correttamente. Per controllare il flusso, selezionare **Verifica flusso** dalla barra del menu **Flusso**.

   Nel set di passaggi successivo ci si collega alla propria tabella e si configura la logica di elaborazione per gestire i nuovi lead.

1. Selezionare **+ nuovo passaggio**. In seguito cercare **Recupera entità** nella finestra **Scegliere un'azione**.
1. In **Azioni** selezionare **Recupera entità (Archiviazione tabelle di Azure)** .
1. Nella finestra **Archiviazione tabelle di Azure** specificare le informazioni per le caselle seguenti e selezionare **Crea**:

    * **Nome connessione**: specificare un nome significativo per la connessione che si sta stabilendo tra questo flusso e la tabella.
    * **Nome dell'account di archiviazione**: specificare il nome dell'account di archiviazione per la tabella. È possibile trovare questo nome nella pagina **Chiavi di accesso** dell'account di archiviazione.
    * **Chiave di archiviazione condivisa**: specificare il valore della chiave per l'account di archiviazione per la tabella. È possibile trovare questo valore nella pagina **Chiavi di accesso** dell'account di archiviazione.

      ![Finestra Archiviazione tabelle di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Dopo aver selezionato **Crea**, viene visualizzata la finestra **Recupera entità**. Da qui selezionare **Mostra le opzioni avanzate** e specificare informazioni per le caselle seguenti:

   * **Tabella**: selezionare il nome della tabella da [Crea una tabella](#create-a-table-in-your-storage-account). La figura seguente mostra la richiesta quando la `marketplaceleads` tabella è selezionata per questo esempio.

     ![Finestra Recupera entità](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Query filtro**: selezionare questa casella e incollarvi la funzione seguente: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Recupera entità, casella Query filtro](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Ora che è stata completata la configurazione della connessione alla tabella di Azure, selezionare **Nuovo passaggio** per aggiungere una condizione per analizzare la tabella di Azure per i nuovi lead.

1. Nella finestra **Scegliere un'azione** selezionare **Azioni** e quindi selezionare **Condition Control** (Controllo condizione).

    ![Finestra Scegliere un'azione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Nella finestra **Condizione** selezionare **Scegliere un valore**. In seguito selezionare **Espressione** nella finestra popup.

1. Incollare `length(body('Get_entities')?['value'])` nella casella **fx**. Selezionare **OK** per aggiungere questa funzione. 

1. Per completare la configurazione della condizione:
    1. Selezionare **è maggiore di** dall'elenco a discesa.
    2. Immettere **0** come valore.

        ![Finestra Condizione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   Nei passaggi successivi viene configurata l'azione da eseguire in base al risultato della condizione:

   * Se la condizione restituisce **Se no**, non eseguire alcuna operazione.
   * Se la condizione restituisce **Se sì**, attivare un'azione che si connetta all'account di Office 365 per inviare un messaggio di posta elettronica. 

1. Selezionare **Aggiungi un'azione** in **Se sì**.

    ![Finestra Condizione, Se sì, Aggiungi un'azione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)** .

    ![Finestra Condizione, Se sì, Invia un messaggio di posta elettronica](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Per usare un provider di posta elettronica diverso, cercare e selezionare **Invia una notifica di posta elettronica (posta)** come azione. Le istruzioni illustrano come eseguire la configurazione usando Office 365 Outlook, ma per un provider di posta elettronica diverso sono comunque molto simili.

1. Nella finestra Office 365 Outlook specificare le informazioni per le caselle seguenti:

    1. **A**: immettere un indirizzo di posta elettronica per tutti gli utenti che riceveranno la notifica.
    1. **Soggetto**: specificare l'oggetto del messaggio di posta elettronica. Un esempio è **Nuovi lead**.
    1. **Corpo**: aggiungere il testo da includere in ogni messaggio di posta elettronica (facoltativo) e incollarlo in `body('Get_entities')?['value']`.

    >[!NOTE]
    >È possibile inserire punti dati statici o dinamici aggiuntivi al corpo del messaggio di posta elettronica.

    ![Finestra Condizione, Se sì, finestra di Office 365 Outlook](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Selezionare **Salva** per salvare il flusso. Power Automate esegue automaticamente il test del flusso per individuare eventuali errori. Se non sono presenti errori, il flusso verrà avviato dopo il salvataggio.

La figura seguente illustra un esempio di come dovrebbe apparire il flusso finale.

![Esempio di flusso finale](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gestire il flusso

Gestire il flusso dopo che è stato eseguito è semplice. Il controllo del flusso è completo. Ad esempio, è possibile arrestarlo, modificarlo, visualizzare una cronologia di esecuzione e ottenere analisi. La figura seguente illustra le opzioni disponibili per gestire un flusso.

 ![Gestire un flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Il flusso rimane in esecuzione fino a quando non viene arrestato manualmente con **Disattiva flusso**.

Se non si ricevono notifiche di posta elettronica sui lead, significa che non sono stati aggiunti nuovi lead alla tabella di Azure. Se si verificano errori di flusso, si riceve un messaggio di posta elettronica simile all'esempio seguente.

 ![Notifica di posta elettronica sugli errori del flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurare l'offerta per inviare lead alla tabella di Azure

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura.

1. Passare alla pagina **Configurazione dell'offerta** per l'offerta.

1. Nella sezione **Customer leads** (Lead clienti) selezionare **Connect** (Connetti).

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Lead clienti":::

1. Nella finestra popup **Dettagli connessione** selezionare **Tabella di Azure** per **Destinazione del lead**. 
     ![Gestione dei lead, Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Incollare la stringa di connessione dall'account di archiviazione di Azure creato seguendo i passaggi precedenti nella casella **Stringa di connessione dell'account di archiviazione**.
     ![Gestione dei lead, Dettagli connessione, account di archiviazione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **Indirizzo di posta elettronica del contatto**: immettere gli indirizzi degli utenti della società che devono ricevere notifiche di posta elettronica quando viene ricevuto un nuovo lead. È possibile specificare più indirizzi di posta elettronica separandoli con un punto e virgola.

1. Selezionare **OK**.

Per assicurarsi di avere eseguito la connessione a una destinazione del lead, selezionare il pulsante **Convalida**. In caso di esito positivo, sarà presente un lead di test nella destinazione del lead.

>[!NOTE]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati lead, Microsoft li invia alla tabella di Azure. Se è stato configurato un flusso, verrà inviato un messaggio di posta elettronica anche all'indirizzo configurato.
