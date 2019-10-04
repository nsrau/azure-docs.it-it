---
title: Tabella di Azure | Azure Marketplace
description: Configurare la gestione dei clienti potenziali per le tabelle di Azure.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902637"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Configurare la gestione dei lead con una tabella di Azure

Se il sistema CRM (Customer Relationship Management) non è supportato in modo esplicito nel centro per i partner per la ricezione di Azure Marketplace e AppSource lead, è possibile usare una tabella di Azure per gestire i lead. È quindi possibile scegliere di esportare i dati e importarli nel sistema CRM. Le istruzioni riportate in questo articolo illustrano il processo di creazione di un account di archiviazione di Azure e di una tabella di Azure con tale account. Inoltre, è possibile creare un nuovo flusso usando Microsoft Flow per inviare una notifica tramite posta elettronica quando l'offerta riceve un lead.

## <a name="configure-azure-table"></a>Configurare la tabella di Azure

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).
2. Quando l'account Azure è attivo, accedere al [portale di Azure](https://portal.azure.com).
3. Nella portale di Azure creare un account di archiviazione usando la procedura riportata di seguito.  
    1. Selezionare **+ Crea una risorsa** nella barra dei menu a sinistra.  Il **nuovo** riquadro (pannello) verrà visualizzato a destra.
    2. Selezionare **archiviazione** nel **nuovo** riquadro.  A destra viene visualizzato un elenco in **primo piano** .
    3. Selezionare **account di archiviazione** per avviare la creazione dell'account.  Seguire le istruzioni riportate nell'articolo [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Procedura di creazione di un account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Per altre informazioni sugli account di archiviazione, selezionare [esercitazione introduttiva](https://docs.microsoft.com/azure/storage/).  Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

4. Attendere fino a quando non viene eseguito il provisioning dell'account di archiviazione, un processo che in genere richiede alcuni minuti.  Accedere quindi all'account di archiviazione dalla **Home** page del portale di Azure selezionando **Visualizza tutte le risorse** oppure selezionando **tutte le risorse** dalla barra dei menu di spostamento a sinistra del portale di Azure.

    ![Accedere all'account di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. Nel riquadro account di archiviazione selezionare **chiavi di accesso** e copiare il valore della *stringa di connessione* per la chiave. Salvare questo valore poiché si tratta del valore della *stringa di connessione dell'account di archiviazione* che sarà necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace. 

    Un esempio di una connessione Sting è:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Chiave di archiviazione di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. Nel riquadro account di archiviazione selezionare **tabelle** e selezionare **+ tabella** per creare una tabella. Immettere un nome per la tabella e fare clic su **OK**. Salvare questo valore perché sarà necessario se si vuole configurare un flusso MS per ricevere notifiche tramite posta elettronica quando vengono ricevuti i lead.

    ![Tabelle di Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    È possibile usare lo [strumento di esplorazione dell'archiviazione di Azure](https://archive.codeplex.com/?p=azurestorageexplorer) o qualsiasi altro strumento per visualizzare i dati nella tabella di archiviazione. È anche possibile esportare i dati nella tabella di Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Opzionale Usare Microsoft Flow con una tabella di Azure  

È possibile usare [Microsoft Flow](https://docs.microsoft.com/flow/) per automatizzare le notifiche ogni volta che un cliente potenziale viene aggiunto alla tabella di Azure. Se non si dispone di un account, è possibile [iscriversi per ottenere un account gratuito](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Esempio di notifica di cliente potenziale

Usare questo esempio come guida per creare un semplice flusso per l’invio automatico di una notifica di posta elettronica quando un nuovo cliente potenziale viene aggiunto a una tabella di Azure. In questo esempio viene impostata una ricorrenza per l'invio di informazioni sui lead ogni ora se l'archivio tabelle viene aggiornato.

1. Accedere all'account Microsoft Flow.
2. Selezionare **Flussi personali** nella barra di spostamento a sinistra.
3. Selezionare **+ Nuovo** nella barra di spostamento in alto.  
4. Nell'elenco a discesa selezionare **+ pianificato-da zero**

   ![Flussi personali * * + pianificato-da vuoto * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  Nella finestra *Compila un flusso pianificato* in *Ripeti ogni* Selezionare "1" per intervallo e "ora" per frequenza. Inoltre, assegnare un nome al flusso se lo si desidera. Selezionare **Create**.

    >[!Note]
    >Sebbene in questo esempio venga usato un intervallo di 1 ora, è possibile selezionare l'intervallo e la frequenza che meglio si adattano alle proprie esigenze aziendali.

    ![Compilare un flusso pianificato.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Selezionare **+ nuovo passaggio**.
7. Nella finestra *scegliere un'azione* cercare "Get paste Time" e quindi selezionare **Get paste Time** in Actions.

   ![Scegliere un'azione.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. Nella finestra **Recupera ora precedente**, impostare l’**intervallo** su 1. Dall’elenco a discesa **Unità di tempo**, selezionare **Ora**.

    >[!Important]
    >Verificare che l'intervallo e l'unità di tempo corrispondano all'intervallo e alla frequenza configurati per la ricorrenza nel passaggio 5.

    ![Configurare l’intervallo per recuperare l’ora precedente](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>È possibile controllare il flusso in qualsiasi momento per verificare che ogni passaggio sia configurato correttamente. Per controllare il flusso, selezionare **Verifica flusso** dalla barra dei menu Flusso.

Nel successivo set di passaggi, ci si collegherà alla propria tabella Azure e si configurerà la logica di elaborazione per gestire i nuovi clienti potenziali.

9. Dopo il passaggio precedente, selezionare **+ nuovo passaggio**, quindi cercare "Ottieni entità" nella finestra *Scegli un'azione* .
10. In **azioni**selezionare **Ottieni entità (archiviazione tabelle di Azure)** .
11. Nella finestra **archiviazione tabelle di Azure** specificare le informazioni per i campi seguenti e selezionare **Crea**:

    * *Nome connessione* : fornire un nome significativo per la connessione che si sta stabilendo tra questo flusso e la tabella di Azure.
    * *Nome dell'account di archiviazione* : specificare il nome dell'account di archiviazione per la tabella di Azure. È possibile trovarlo nella pagina **chiavi di accesso** dell'account di archiviazione.
    * *Chiave di archiviazione condivisa* : specificare il valore della chiave per l'account di archiviazione per la tabella di Azure. È possibile trovarlo nella pagina **chiavi di accesso** dell'account di archiviazione.

        ![Archiviazione tabelle di Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Dopo aver fatto clic su Crea, viene visualizzata una finestra *Ottieni entità* . Qui selezionare **Mostra opzioni avanzate** e fornire informazioni per i campi seguenti:

       * *Tabella* : selezionare il nome dell'archiviazione tabelle di Azure (dal passaggio 6 di istruzioni su come configurare una tabella di Azure). L'acquisizione della schermata successiva mostra la richiesta di selezione della tabella "marketplaceleads" per questo esempio.

            ![Tabella di Azure ottenere le entità.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Filtra query* : selezionare questo campo e incollare questa funzione nel campo:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Tabella di Azure ottenere entità: filtrare Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Ora che è stata completata la configurazione della connessione alla tabella di Azure, selezionare **nuovo passaggio** per aggiungere una condizione per l'analisi della tabella di Azure per i nuovi lead. 

13. Nella finestra **scegliere un'azione** selezionare **azioni**, quindi selezionare il **controllo condizione**.

    ![Tabella di Azure: scegliere un'azione.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. Nella finestra **Condizione**, selezionare il campo **Scegliere un valore**, quindi selezionare **Espressione** nella finestra popup.

15. Incollare `length(body('Get_entities')?['value'])` nel campo ***fx***. Selezionare **OK** per aggiungere questa funzione. 

16. Per completare la configurazione della condizione:
    1. Selezionare “è maggiore di” dall'elenco a discesa.
    2. Immettere 0 come valore

        ![Tabella-Condition di Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

Nei passaggi successivi verrà configurata l'azione da eseguire in base al risultato della condizione.

* Se la condizione restituisce **Se no**, non eseguire alcuna operazione.
* Se la condizione restituisce **Se sì**, attivare un'azione che si connetta all'account di Office 365 per inviare un messaggio di posta elettronica. 

17. Selezionare **Aggiungi un'azione** in **se sì**.

    ![Tabella-Condition di Azure, * * se sì * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Selezionare **Invia un messaggio di posta elettronica (Office 365 Outlook)** .

    ![Condizione di tabella di Azure, * * se sì * *, inviare un messaggio di posta elettronica.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Se si vuole usare una ricerca diversa del provider di posta elettronica per e selezionare Invia una notifica di posta elettronica (mail) come azione. Le istruzioni illustrano come configurare usando Office 365 Outlook, ma le istruzioni sono simili per un provider di posta elettronica diverso.

19. Nella finestra **Office 365 Outlook** specificare le informazioni per i campi seguenti:

    1. **A**: immettere un indirizzo di posta elettronica per tutti gli utenti che riceveranno la notifica.
    1. **Oggetto**: inserire un oggetto per il messaggio di posta elettronica. Ad esempio:  Nuovi clienti potenziali!
    1. **Corpo** : aggiungere il testo che si desidera includere in ogni messaggio di posta elettronica (facoltativo) e quindi incollare il `body('Get_entities')?['value']`corpo.

    >[!Note]
    >È possibile inserire punti dati statici o dinamici aggiuntivi al corpo del messaggio di posta elettronica.

    ![Tabella di Azure-condizione, * * se sì * *, finestra di Outlook per Office 365.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Selezionare **Salva** per salvare il flusso. Microsoft Flow eseguirà automaticamente la verifica del flusso per cercare eventuali errori. Se non sono presenti errori, il flusso verrà avviato dopo il salvataggio.

Nella schermata successiva è mostrato un esempio di come dovrebbe apparire il flusso finale.

![Esempio del flusso finale.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gestire il flusso

Gestire il flusso dopo la relativa esecuzione è semplice. Il controllo del flusso è completo. Ad esempio, è possibile arrestarlo, modificarlo, visualizzare una cronologia di esecuzione e ottenere analisi. Nella schermata successiva sono mostrate le opzioni disponibili per gestire un flusso. 

 ![Gestione di un flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Il flusso rimane in esecuzione fino a quando non viene arrestato manualmente con **Disattiva flusso**.

Se non si ricevono notifiche di posta elettronica sui clienti potenziali, non sono stati aggiunti nuovi clienti potenziali alla tabella di Azure. Se si verificano errori di flusso, si otterrà un messaggio di posta elettronica simile all'esempio nella prossima schermata.

 ![Notifica di posta elettronica sugli errori del flusso](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurare l'offerta per l'invio di lead alla tabella di Azure

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, attenersi alla procedura seguente:

1. Passare alla pagina di **configurazione dell'offerta** per l'offerta.
2. Selezionare **Connetti** nella sezione gestione dei lead.
3. Nella finestra popup Dettagli connessione selezionare **tabella di Azure** per la **destinazione lead**e incollare la stringa di connessione dall'account di archiviazione di Azure creato seguendo i passaggi precedenti nella **stringa di connessione dell'account di archiviazione** . campo.
4. Selezionare **Salva**. 

>[!Note]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

Quando vengono generati i lead, Microsoft invia lead alla tabella di Azure. Se è stato configurato un flusso, verrà inviato anche un messaggio di posta elettronica all'indirizzo di posta elettronica configurato.

![Lead management (Gestione di clienti potenziali)](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Gestione dei lead-dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Gestione lead-account di archiviazione dettagli connessione](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

