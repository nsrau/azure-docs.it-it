---
title: 'Tabella di Azure : Azure Marketplace'
description: Configurare la gestione dei clienti potenziali per le tabelle di Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a36c411b9ababc42adb51d82a316df4252c01e24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252015"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Configurare la gestione dei lead usando una tabella di AzureConfigure lead management using an Azure Table

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel Centro per i partner per la ricezione dei lead di Azure Marketplace e AppSource, è possibile usare una tabella di Azure per gestire questi lead. È quindi possibile scegliere di esportare i dati e importarlo nel sistema CRM. Le istruzioni contenute in questo articolo illustrano il processo di creazione di un account di Archiviazione di Azure e una tabella di Azure con tale account. Inoltre, è possibile creare un nuovo flusso usando Microsoft Flow per inviare una notifica tramite posta elettronica quando l'offerta riceve un lead.

## <a name="configure-azure-table"></a>Configurare la tabella di AzureConfigure Azure Table

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).
2. Dopo aver attivo l'account Azure, accedere al portale di [Azure.](https://portal.azure.com)
3. Nel portale di Azure creare un account di archiviazione usando la procedura seguente.  
    1. Nella barra dei menu a sinistra, **selezionare Crea una risorsa.**  Il riquadro **Nuovo** (blade) verrà visualizzato a destra.
    2. Selezionare Archiviazione nel riquadro **Nuovo.Select Storage** in the **New** pane.  A destra viene visualizzato un elenco **In primo piano.**
    3. Selezionare **Account di archiviazione** per iniziare la creazione dell'account.  Seguire le istruzioni nell'articolo [Creare un account di archiviazione.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

        ![Procedura di creazione di un account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Per altre informazioni sugli account di archiviazione, selezionare [Esercitazione sulle guide rapide](https://docs.microsoft.com/azure/storage/).  Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

4. Attendere il provisioning dell'account di archiviazione, un processo che richiede in genere alcuni minuti.  Accedere quindi all'account di archiviazione dalla **home** page del portale di Azure selezionando Visualizza tutte le risorse o Tutte le risorse nella barra dei menu di spostamento sinistra del portale di Azure.Then access your storage account from the Home page of the Azure portal by selecting **See all your resources** or by selecting All **resources** from the left navigation menu bar of the Azure portal.

    ![Accedere all'account di archiviazione di AzureAccess your Azure storage account](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. Nel riquadro dell'account di archiviazione selezionare Chiavi di **accesso** e copiare il valore *Stringa* di connessione per la chiave. Salvare questo valore perché questo è il valore Stringa di *connessione dell'account* di archiviazione che sarà necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace. 

    Un esempio di puntura di connessione è:An example of a connection sting is:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. Nel riquadro dell'account di archiviazione selezionare **Tabelle** e **selezionare Tabella** per creare una tabella. Immettere un nome per la tabella e selezionare **OK**. Salvare questo valore perché sarà necessario se si desidera configurare un flusso MS per ricevere notifiche tramite posta elettronica quando vengono ricevuti i lead.

    ![Tabelle di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    È possibile usare lo [strumento di esplorazione dell'archiviazione di Azure](https://archive.codeplex.com/?p=azurestorageexplorer) o qualsiasi altro strumento per visualizzare i dati nella tabella di archiviazione. È anche possibile esportare i dati nella tabella di Azure.You can also export the data in the Azure Table. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Facoltativo) Usare Microsoft Flow con una tabella di AzureUse Microsoft Flow with an Azure Table  

È possibile usare [Microsoft Flow](https://docs.microsoft.com/flow/) per automatizzare le notifiche ogni volta che un cliente potenziale viene aggiunto alla tabella di Azure. Se non hai un account, puoi [iscriverti per un account gratuito.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Esempio di notifica di cliente potenziale

Usare questo esempio come guida per creare un semplice flusso per l’invio automatico di una notifica di posta elettronica quando un nuovo cliente potenziale viene aggiunto a una tabella di Azure. In questo esempio viene impostata una ricorrenza per l'invio di informazioni sui lead ogni ora se lo spazio di archiviazione tabelle viene aggiornato.

1. Accedere all'account Microsoft Flow.
2. Selezionare **Flussi personali** nella barra di spostamento a sinistra.
3. Selezionare **+ Nuovo** nella barra di spostamento in alto.  
4. Nell'elenco a discesa, selezionare **- Pianificato - da vuoto**

   ![I miei flussi - programmati - dal vuoto](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.    Nella finestra *Crea flusso pianificato* in Ripeti *ogni* selezionare "1" per intervallo e "ora" per frequenza. Inoltre, assegnare un nome al flusso. Selezionare **Create** (Crea).

>[!Note]
>Anche se in questo esempio viene utilizzato un intervallo di 1 ora, è possibile selezionare l'intervallo e la frequenza più adatti alle esigenze aziendali.

![Creare un flusso pianificato.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Selezionare **+ nuovo passaggio**.
7. Nella finestra *Scegliere un'azione* cercare "ottieni l'ora passata" e quindi selezionare **Ottieni ora passata** in Azioni.

   ![Scegliere un'azione.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Nella finestra **Recupera ora precedente**, impostare l’**intervallo** su 1. Dall’elenco a discesa **Unità di tempo**, selezionare **Ora**.

    >[!Important]
    >Assicurarsi che questo intervallo e unità di tempo corrispondano all'intervallo e alla frequenza configurati per la ricorrenza nel passaggio 5.

    ![Configurare l’intervallo per recuperare l’ora precedente](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>È possibile controllare il flusso in qualsiasi momento per verificare che ogni passaggio sia configurato correttamente. Per controllare il flusso, selezionare **Verifica flusso** dalla barra dei menu Flusso.

Nel set successivo di passaggi, ci si connetterà alla tabella di Azure e si imposterà la logica di elaborazione per gestire i nuovi lead.

9. Dopo il passaggio Superato, selezionare **Nuovo passaggio**, quindi cercare "Ottieni entità" nella finestra *Scegli un'azione.*
10. In **Azioni**selezionare **Ottieni entità (Archiviazione tabelle di Azure).**
11.    Nella finestra Archiviazione tabelle di Azure fornire informazioni per i campi seguenti e selezionare Crea:In the **Azure Table Storage** window, provide information for the following fields and select **Create**:
* *Nome connessione:* specificare un nome significativo per la connessione che si sta stabilendo tra questo flusso e la tabella di Azure.Connection Name - provide a meaningful name for the connection you are establishing between this flow and the Azure Table.
* *Nome account di archiviazione:* specificare il nome dell'account di archiviazione per la tabella di Azure.Storage Account Name - provide the name of the storage account for your Azure table. È possibile trovare questo nella pagina **Chiavi** di accesso dell'account di archiviazione.
* *Chiave di archiviazione condivisa:* fornire il valore chiave per l'account di archiviazione per la tabella di Azure.Shared Storage Key - provide the key value for your store account for your Azure table. È possibile trovare questo nella pagina **Chiavi** di accesso dell'account di archiviazione.
    ![Archiviazione tabelle di Azure.Azure Table storage.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

Dopo aver fatto clic su Crea verrà visualizzata una finestra *Ottieni entità.* Qui selezionare **Mostra opzioni avanzate** e fornire informazioni per i seguenti campi:
* *Tabella:* selezionare il nome dell'archiviazione tabelle di Azure (dal passaggio 6 delle istruzioni su come configurare una tabella di Azure). La schermata successiva mostra il messaggio di richiesta quando la tabella "marketplaceleads" è selezionata per questo esempio.
    ![Tabella di Azure ottenere le entità.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

* *Query filtro:* selezionare questo campo e `Timestamp gt datetime'@{body('Get_past_time')}'` ![incollare questa funzione nel campo: Azure Table get entities - Filter Query (Query filtro).](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Dopo aver completato la configurazione della connessione alla tabella di Azure, selezionare **Nuovo passaggio** per aggiungere una condizione per eseguire l'analisi della tabella di Azure alla ricerca di nuovi lead. 

13. Nella finestra **Scegliere un'azione** selezionare **Azioni**, quindi selezionare il **controllo Condizione**.

    ![Tabella di Azure: scegliere un'azione.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Nella finestra **Condizione**, selezionare il campo **Scegliere un valore**, quindi selezionare **Espressione** nella finestra popup.

15. Incollare `length(body('Get_entities')?['value'])` nel campo ***fx***. Selezionare **OK** per aggiungere questa funzione. 

16. Per completare la configurazione della condizione:
    1. Selezionare "è maggiore di" dall'elenco a discesa.
    2. Immettere 0 come valore

        ![Azure Table - Condition.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

Nei passaggi successivi verrà impostata l'azione da eseguire in base al risultato della condizione.

* Se la condizione si risolve in **Se no**, non eseguire alcuna operazione.
* Se la condizione restituisce **Se sì**, attivare un'azione che si connetta all'account di Office 365 per inviare un messaggio di posta elettronica. 

17. Selezionare **Aggiungi un'azione** in **Se sì**.

    ![Tabella di Azure - Condizione, se sì.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)**.

    ![Tabella di Azure - Condizione, se sì, inviare posta elettronica.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Se si desidera utilizzare una ricerca di un provider di posta elettronica diverso e selezionare Invia una notifica tramite posta elettronica (Posta) come azione. Le istruzioni illustrano come configurare con Office 365 Outlook, ma le istruzioni sono simili per un provider di posta elettronica diverso.

19. Nella finestra di **Office 365 Outlook,** fornire informazioni per i campi seguenti:

    1. **A**: immettere un indirizzo di posta elettronica per tutti gli utenti che riceveranno la notifica.
    1. **Oggetto:** fornire un oggetto per l'e-mail. Ad esempio: Nuovi lead!
    1. **Corpo:** aggiungere il testo che si desidera includere in ogni `body('Get_entities')?['value']`messaggio di posta elettronica (facoltativo) e quindi incollare nel corpo .

    >[!Note]
    >È possibile inserire punti dati statici o dinamici aggiuntivi al corpo del messaggio di posta elettronica.

    ![Tabella di Azure - Condizione, in caso affermativo, finestra di Office 365 di Outlook.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Selezionare **Salva** per salvare il flusso. Microsoft Flow eseguirà automaticamente la verifica del flusso per cercare eventuali errori. Se non sono presenti errori, il flusso inizia a essere eseguito dopo il salvataggio.

Nella schermata successiva è mostrato un esempio di come dovrebbe apparire il flusso finale.

![Esempio del flusso finale.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gestire il flusso

Gestire il flusso dopo l'esecuzione è facile. Il controllo del flusso è completo. Ad esempio, è possibile arrestarlo, modificarlo, visualizzare una cronologia di esecuzione e ottenere analisi. Nella schermata successiva sono mostrate le opzioni disponibili per gestire un flusso. 

 ![Gestione di un flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Il flusso rimane in esecuzione fino a quando non viene arrestato manualmente con **Disattiva flusso**.

Se non si ricevono notifiche tramite posta elettronica dei lead, significa che i nuovi lead non sono stati aggiunti alla tabella di Azure.If you're not getting any lead email notifications, it means that new leads haven't been added to the Azure table. Se si verificano errori di flusso, si otterrà un messaggio di posta elettronica come l'esempio nella schermata successiva.

 ![Notifica di posta elettronica sugli errori del flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurare l'offerta per l'invio dei lead alla tabella di AzureConfigure your offer to send leads to the Azure Table

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, attenersi alla seguente procedura:

1. Accedi alla pagina **Configurazione offerta** per la tua offerta.
2. Selezionare **Connetti** nella sezione Gestione lead.
3. Nella finestra popup Dettagli connessione selezionare **Tabella di Azure** come Destinazione **lead**e incollare la stringa di connessione dell'account di archiviazione di Azure creato seguendo i passaggi precedenti nel campo Stringa di **connessione dell'account di archiviazione.**
4. **Email di contatto:** fornisci email alle persone della tua azienda che devono ricevere notifiche e-mail quando viene ricevuto un nuovo lead. È possibile fornire più messaggi di posta elettronica separandoli con un punto e virgola.
5. Selezionare **Ok**.

Per assicurarsi di aver eseguito correttamente la connessione a una destinazione lead, fare clic sul pulsante di convalida. In caso di esito positivo, si avrà un lead di test nella destinazione del lead.

>[!Note]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati lead, Microsoft invia lead alla tabella di Azure.When leads are generated, Microsoft sends leads to the Azure Table. Se è stato configurato un flusso, verrà inviato anche un messaggio di posta elettronica all'indirizzo di posta elettronica configurato.

![Lead management (Gestione di clienti potenziali)](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gestione lead - dettagli di connessione](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gestione lead - Account di archiviazione dei dettagli di connessione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

