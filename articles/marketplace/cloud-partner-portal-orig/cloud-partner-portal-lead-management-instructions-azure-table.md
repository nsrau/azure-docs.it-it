---
title: Archiviazione tabelle di Azure | Azure Marketplace
description: Configurare la gestione dei lead nell'archiviazione tabelle di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a53ed93813215655c4a165faa0bce36d9249e8e6
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227894"
---
# <a name="lead-management-instructions-for-table-storage"></a>Istruzioni relative alla gestione dei lead per l'archiviazione tabelle

Questo articolo descrive come configurare l'archiviazione tabelle di Azure per gestire i lead di vendita. L'archiviazione tabelle consente di archiviare e modificare le informazioni dei clienti.

## <a name="configure-table-storage"></a>Configurare Archiviazione tabelle

1. Se non si ha un account Azure, [creare un account di valutazione gratuito](https://azure.microsoft.com/pricing/free-trial/).
1. Quando l'account è attivo, accedere al [portale di Azure](https://portal.azure.com).
1. Nella portale di Azure eseguire le operazioni seguenti:  
    1. Selezionare **+ Crea una risorsa** nel riquadro sul lato sinistro. Viene visualizzato il **nuovo** riquadro.
    1. Nel riquadro **nuovo** selezionare **archiviazione**. Un elenco in **primo piano** si aprirà sul lato destro.
    1. Selezionare **Account di archiviazione**. Seguire quindi le istruzioni riportate in [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Creare un account di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Per altre informazioni sugli account di archiviazione, vedere [Esercitazioni introduttive](https://docs.microsoft.com/azure/storage/). Per informazioni sui prezzi, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

1. Attendere che venga eseguito il provisioning dell'account di archiviazione, che in genere richiede alcuni minuti. Quindi, accedere all'account dal home page della portale di Azure: Selezionare **Visualizza tutte le risorse** o **tutte le risorse** nel riquadro di spostamento.

    ![Accedere all'account di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Nel riquadro dell'account di archiviazione copiare la stringa di connessione dell'account di archiviazione per la chiave. Incollarlo nel campo **stringa di connessione** per l'account di archiviazione nell'portale cloud partner.

    Stringa di connessione di esempio:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Chiave di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

È possibile utilizzare [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) o uno strumento simile per visualizzare i dati nell'archiviazione tabelle. È anche possibile esportare dati da esso.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Usare Microsoft Flow con l'archiviazione tabelle (*facoltativo*)

È possibile usare [Microsoft Flow](https://docs.microsoft.com/flow/) per inviare automaticamente notifiche quando viene aggiunto un lead all'archiviazione tabelle. Se non si dispone di un account Microsoft Flow, [iscriversi per ottenere un account gratuito](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Esempio di notifica di cliente potenziale

Questo esempio illustra come creare un flusso di base. Il flusso invia automaticamente una notifica di posta elettronica ogni ora quando vengono aggiunti nuovi lead all'archiviazione tabelle.

1. Accedere all'account Microsoft Flow.
1. Nel riquadro di spostamento sul lato sinistro selezionare **flussi personali**.
1. Nella barra di spostamento superiore selezionare **+ nuovo**.  
1. Nell'elenco a discesa selezionare **+ Crea da zero**.
1. In **Crea un flusso da zero**selezionare **Crea da zero**.

   ![Creare un nuovo flusso da zero](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Nella pagina di ricerca dei trigger e dei connettori, selezionare **Trigger**.
1. In **Trigger**, selezionare **Ricorrenza**.
1. Nella finestra ricorrenza Mantieni l'impostazione predefinita **1** per **intervallo**. Dall'elenco a discesa **Frequency (frequenza** ) selezionare **hour (ora**).

   >[!NOTE] 
   >Questo esempio usa un intervallo di un'ora. È tuttavia possibile selezionare un intervallo e una frequenza più adatti alle proprie esigenze aziendali.

   ![Impostare una frequenza di 1 ora per la ricorrenza](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Selezionare **+ nuovo passaggio**.
1. Cercare **Ottieni ora precedente**, quindi selezionare **Ottieni ora precedente** in **scegliere un'azione**.

    ![Trovare e selezionare l'azione "Ottieni ora precedente"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Nella finestra **Get paste Time** impostare l' **intervallo** su **1**.  Dall'elenco a discesa **unità di tempo** selezionare **ora**.
    >[!IMPORTANT] 
    >Verificare **che l'intervallo** e l' **unità di tempo** corrispondano all'intervallo e alla frequenza configurati per la ricorrenza (passaggio 8).

    ![Impostare l'intervallo di tempo di Get paste](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >È possibile controllare il flusso in qualsiasi momento per verificare che ogni passaggio sia configurato correttamente: Selezionare **controllo di flusso** dalla barra dei menu di Microsoft Flow.

Nel prossimo set di passaggi, ci si connette alla tabella di archiviazione e si configura la logica di elaborazione per gestire i nuovi lead.

1. Dopo il passaggio **precedente** , selezionare **+ nuovo passaggio**, quindi cercare **Ottieni entità**.
1. In **Azioni**, selezionare **Recupera entità**, quindi selezionare **Mostra opzioni avanzate**.
1. Nella finestra **Ottieni entità** compilare i campi seguenti:

   - **Table**: nome dell'archivio tabelle. La figura seguente mostra "MarketPlaceLeads" immesso:

     ![Scegliere un valore personalizzato come nome della tabella di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Query di filtro**: Quando si seleziona questo campo, l'icona **Ottieni ora precedente** viene visualizzata in una finestra popup. Selezionare **ora precedente** per usare questo valore come timestamp per filtrare la query. In alternativa, è possibile incollare la funzione seguente nel campo:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Configurare la funzione di query del filtro](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Selezionare **nuovo passaggio** per aggiungere una condizione per l'analisi dell'archiviazione tabelle per i nuovi lead.

   ![Usare "nuovo passaggio" per aggiungere una condizione per l'analisi dell'archiviazione tabelle](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Nella finestra **scegliere un'azione** selezionare **Azioni**, quindi selezionare **controllo condizione**.

     ![Aggiungere un controllo Condition](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Nella finestra **condizione** selezionare **Scegli un valore**, quindi selezionare **espressione** nella finestra popup.
1. Incollare `length(body('Get_entities')?['value'])` il campo ***FX*** . Selezionare **OK** per aggiungere questa funzione. 



     ![Aggiungere una funzione alla condizione](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Configurare l'azione da eseguire in base al risultato della condizione.

    1. Select **è maggiore di** nell'elenco a discesa.
   1. Immettere **0** come valore.

     ![Configurare un'azione in base ai risultati della condizione](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Se la condizione si risolve in "If no", non eseguire alcuna operazione.

    Se la condizione si risolve in "Se sì," attiva un'azione che connette l'account Office 365 per inviare un messaggio di posta elettronica:
   1. Selezionare **Aggiungi un'azione**.
   1. Selezionare **Inviare un messaggio di posta elettronica**.
   1. Nella finestra **Invia un messaggio di posta elettronica** immettere le informazioni nei campi seguenti:

      - **A**: un indirizzo di posta elettronica per tutti gli utenti che riceveranno la notifica.
      - **Subject**: oggetto per il messaggio di posta elettronica. Ad esempio:  *Nuovi lead!*
      - **Body**: il testo che si vuole includere in ogni messaggio di posta elettronica (facoltativo). Incollare `body('Get_entities')?['value']` anche come funzione per inserire informazioni sui lead.

        >[!NOTE] 
        >È possibile inserire punti dati statici o dinamici aggiuntivi nel corpo del messaggio di posta elettronica.

      ![Configurare la posta elettronica per notifiche sui clienti potenziali](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Selezionare **Salva** per salvare il flusso. Microsoft Flow la verificherà automaticamente per individuare eventuali errori. Se non sono presenti errori, il flusso verrà avviato dopo il salvataggio.

    La figura seguente illustra un esempio di come dovrebbe apparire il flusso finale.

    [![Sequenza di flusso finale](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    *Selezionare l'immagine per ingrandirla.*

### <a name="manage-your-flow"></a>Gestire il flusso

È facile gestire il flusso dopo l'esecuzione. Il controllo del flusso è completo. Ad esempio, è possibile arrestarlo, modificarlo, visualizzare una cronologia di esecuzione e ottenere analisi. La figura seguente mostra le opzioni di gestione del flusso.

 ![Opzioni di gestione dei flussi](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Il flusso rimane in esecuzione fino a quando non si seleziona **Disattiva il flusso**.

Se non vengono ricevute notifiche tramite posta elettronica del lead, non sono stati aggiunti nuovi lead all'archiviazione tabelle.
Se si verifica un errore di flusso, si riceverà un messaggio di posta elettronica simile all'esempio seguente:

 ![Notifica di posta elettronica sugli errori del flusso](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Passaggi successivi

[Configurare lead relativi ai clienti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
