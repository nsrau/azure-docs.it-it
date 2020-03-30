---
title: Archiviazione tabelle di Azure - Azure Marketplace
description: Configurare la gestione dei lead nell'archiviazione tabelle di Azure.Configure lead management in Azure Table storage.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280355"
---
# <a name="lead-management-instructions-for-table-storage"></a>Istruzioni per la gestione dei lead per l'archiviazione tabelle

Questo articolo descrive come configurare archiviazione tabelle di Azure per gestire i lead di vendita. L'archiviazione tabelle consente di archiviare e modificare le informazioni sui clienti.

## <a name="configure-table-storage"></a>Configurare Archiviazione tabelle

1. Se non si dispone di un account Azure, creare un account di [prova gratuito.](https://azure.microsoft.com/pricing/free-trial/)
1. Dopo aver attivo l'account, accedere al portale di [Azure.](https://portal.azure.com)
1. Nel portale di Azure seguire questi passaggi:In the Azure portal, follow these steps:  
    1. Nel riquadro a sinistra, selezionare **Crea una risorsa.** Si aprirà il riquadro **Nuovo.**
    1. Nel riquadro **Nuovo** selezionare **Archiviazione**. Un elenco **In primo piano** si aprirà sul lato destro.
    1. Selezionare **Account di archiviazione**. Seguire quindi le istruzioni in [Creare un account di archiviazione.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    ![Creare un account di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Per altre informazioni sugli account di archiviazione, vedere [Esercitazioni sulle guide introduttive.](https://docs.microsoft.com/azure/storage/) Per informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure.For](https://azure.microsoft.com/pricing/details/storage/)pricing information, see Azure storage pricing .

1. Attendere il provisioning dell'account di archiviazione, operazione che richiede in genere alcuni minuti. Quindi, accedere all'account dalla home page del portale di Azure: selezionare **Visualizza tutte le risorse** o Tutte le **risorse** nel riquadro di spostamento.

    ![Accedere all'account di archiviazione di AzureAccess your Azure storage account](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Nel riquadro dell'account di archiviazione copiare la stringa di connessione dell'account di archiviazione per la chiave. Incollarlo nel campo **Stringa di connessione** per l'account di archiviazione nel portale Cloud Partner.Paste it in the Connection String field for the storage account in the Cloud Partner Portal.

    Stringa di connessione di esempio:Example connection string:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Chiave di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

È possibile usare [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) o uno strumento simile per visualizzare i dati nell'archivio tabelle. È inoltre possibile esportare i dati da esso.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Usare Microsoft Flow con archiviazione tabelle *(facoltativo)*

È possibile usare [Microsoft Flow](https://docs.microsoft.com/flow/) per inviare automaticamente notifiche quando viene aggiunto un lead all'archiviazione tabelle. Se non si dispone di un account Microsoft Flow, [iscriversi per ottenere un account gratuito.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Esempio di notifica di cliente potenziale

In questo esempio viene illustrato come creare un flusso di base. Il flusso invia automaticamente una notifica tramite posta elettronica ogni ora quando vengono aggiunti nuovi lead all'archiviazione tabelle.

1. Accedere all'account Microsoft Flow.
1. Nel riquadro di spostamento a sinistra selezionare **Flussi personali**.
1. Nella barra di spostamento superiore, selezionare **Nuovo**.  
1. Nell'elenco a discesa, selezionare **Crea da vuoto.**
1. In **Crea un flusso da vuoto**selezionare Crea da **vuoto**.

   ![Creare un nuovo flusso da zero](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Nella pagina di ricerca dei trigger e dei connettori, selezionare **Trigger**.
1. In **Trigger**, selezionare **Ricorrenza**.
1. Nella finestra **Ricorrenza** mantenere l'impostazione predefinita **1** per **Intervallo**. Nell'elenco a discesa **Frequenza** selezionare **Ora**.

   >[!NOTE] 
   >In questo esempio viene utilizzato un intervallo di un'ora. È tuttavia possibile selezionare un intervallo e una frequenza che meglio si adattano alle esigenze aziendali.

   ![Impostare una frequenza di 1 ora per la ricorrenza](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Selezionare **Nuovo passaggio**.
1. Cercare **Ottieni ora passata**e quindi selezionare **Ottieni ora passata** in **Scegliere un'azione**.

    ![Trovare e selezionare l'azione "Ottieni ora passata"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Nell'intervallo **Ottieni ora passata** impostare **Intervallo** su **1**.  Nell'elenco a discesa **Unità di tempo** selezionare **Ora**.
    >[!IMPORTANT] 
    >Assicurarsi che **intervallo** e **unità di tempo** corrispondano all'intervallo e alla frequenza configurati per la ricorrenza (passaggio 8).

    ![Impostare l'intervallo di tempo superato](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >È possibile controllare il flusso in qualsiasi momento per verificare che ogni passaggio sia configurato correttamente: selezionare **Controllo flusso** dalla barra dei menu Flusso.

Nel set di passaggi successivo, connettersi alla tabella di archiviazione e impostare la logica di elaborazione per gestire i nuovi lead.

1. Dopo il passaggio **Ottieni ora precedente,** selezionare **Nuovo passaggio**e quindi cercare **Get entities**.
1. In **Azioni**, selezionare **Recupera entità**, quindi selezionare **Mostra opzioni avanzate**.
1. Nella finestra **Prendi entità** compilare i seguenti campi:

   - **Tabella**: il nome dell'archiviazione della tabella. L'immagine seguente mostra "MarketPlaceLeads" inserito:

     ![Scegliere un valore personalizzato come nome della tabella di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filtra query**: Quando si seleziona questo campo, l'icona **Ottieni ora passata** viene visualizzata in una finestra popup. Selezionare **Ora passata** per utilizzare questo valore come timestamp per filtrare la query. In alternativa, è possibile incollare la seguente funzione nel campo:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Impostare la funzione di query del filtro](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Selezionare **Nuovo passaggio** per aggiungere una condizione per eseguire la scansione dell'archiviazione tabelle alla ricerca di nuovi lead.

   ![Usare "Nuovo passaggio" per aggiungere una condizione per l'archiviazione della tabella di scansioneUse "New step" to add a condition to scan table storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Nella finestra **Scegliere un'azione** selezionare **Azioni**, quindi **Controllo condizione**.

     ![Aggiungere un controllo condizioneAdd a condition control](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Nella finestra **Condizione** selezionare **Scegli un valore**e quindi **Espressione** nella finestra popup.
1. Incollare `length(body('Get_entities')?['value'])` nel campo ***fx.*** Selezionare **OK** per aggiungere questa funzione. 



     ![Aggiungere una funzione alla condizione](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Configurare l'azione da eseguire in base al risultato della condizione.

    1. Select **è maggiore di** dall'elenco a discesa.
   1. Immettere **0** come valore.

     ![Impostare un'azione in base ai risultati della condizione](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Se la condizione si risolve in "Se no", non eseguire alcuna operazione.

    Se la condizione viene risolta in "Se sì", attivare un'azione che connette l'account di Office 365 per inviare un messaggio di posta elettronica:
   1. Selezionare **Aggiungi un'azione**.
   1. Selezionare **Inviare un messaggio di posta elettronica**.
   1. Nella finestra **Invia un messaggio di posta elettronica,** immettere le informazioni nei seguenti campi:

      - **A**: un indirizzo e-mail per tutti coloro che otterranno la notifica.
      - **Oggetto**: oggetto dell'e-mail. Ad esempio: *Nuovi lead!*
      - **Corpo**: il testo che si desidera includere in ogni e-mail (opzionale). Incollare `body('Get_entities')?['value']` anche come funzione per inserire le informazioni sul lead.

        >[!NOTE] 
        >È possibile inserire ulteriori punti dati statici o dinamici nel corpo del messaggio di posta elettronica.

      ![Configurare la posta elettronica per notifiche sui clienti potenziali](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Selezionare **Salva** per salvare il flusso. Microsoft Flow lo testerà automaticamente per gli errori. Se non sono presenti errori, il flusso inizia a essere eseguito dopo il salvataggio.

    L'immagine seguente mostra un esempio di come dovrebbe apparire il flusso finale.

    [![Sequenza del flusso finale](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Selezionare l'immagine per ingrandirla.)*

### <a name="manage-your-flow"></a>Gestire il flusso

È facile gestire il flusso dopo l'esecuzione. Il controllo del flusso è completo. Ad esempio, è possibile arrestarlo, modificarlo, visualizzare una cronologia di esecuzione e ottenere analisi. L'immagine seguente mostra le opzioni di gestione del flusso.

 ![Opzioni di gestione del flusso](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Il flusso continua a funzionare fino a quando non si seleziona **Disattiva flusso**.

Se non si ricevono notifiche tramite posta elettronica lead, non sono stati aggiunti nuovi lead all'archiviazione tabelle.
Se si verifica un errore di flusso, verrà visualizzato un messaggio di posta elettronica simile all'esempio seguente:You'll get an email like the following example if a flow failure occurs:

 ![Notifica di posta elettronica sugli errori del flusso](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Passaggi successivi

[Configurare i lead dei clienti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
