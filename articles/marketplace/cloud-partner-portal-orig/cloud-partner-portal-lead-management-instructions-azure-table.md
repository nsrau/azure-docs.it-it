---
title: Tabelle di Azure | Azure Marketplace
description: Configurare la gestione dei clienti potenziali per le tabelle di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a1bcab9816627b453ba8b20b7bcd9402c2dfd151
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240581"
---
# <a name="lead-management-instructions-for-azure-table"></a>Istruzioni di gestione dei lead per tabelle di Azure

Questo articolo descrive come configurare le tabelle di Azure per l'archiviazione dei clienti potenziali. La tabella di Azure consente di archiviare e personalizzare le informazioni dei clienti.


## <a name="how-to-configure-azure-table"></a>Come configurare tabelle di Azure

1. Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).
2. Una volta attivo l'account di Azure, accedere al [portale di Azure](https://portal.azure.com).
3. Nel portale di Azure, creare un account di archiviazione usando la procedura seguente.  
    1. Selezionare **+ crea una risorsa** nella barra dei menu a sinistra.  Il **New** riquadro (pannello) verrà visualizzato a destra.
    2. Selezionare **memorizzazione** nel **New** riquadro.  Oggetto **in primo piano** elenco viene visualizzato a destra.
    3. Selezionare **Account di archiviazione** per avviare la creazione dell'account.  Seguire le istruzioni nell'articolo [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Procedura di creazione di un account di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Per altre informazioni sugli account di archiviazione, selezionare [esercitazione introduttiva](https://docs.microsoft.com/azure/storage/).  Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

4. Attendere finché non viene eseguito il provisioning dell'account di archiviazione, un processo che in genere richiede alcuni minuti.  Quindi accedere all'account di archiviazione dal **Home** pagina del portale di Azure, selezionando **visualizzare tutte le risorse** oppure selezionando **tutte le risorse** dal riquadro di spostamento a sinistra sulla barra dei menu del portale di Azure.

    ![Accedere all'account di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

5. Dal pannello account di archiviazione, copiare la stringa di connessione di account di archiviazione per la chiave e incollarla il **stringa di connessione di Account di archiviazione** campo nel portale Cloud Partner. Un esempio di una stringa di connessione è:

```sql
DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
```

  ![Chiave di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

È possibile usare [Esplora archivi Azure](https://azurestorageexplorer.codeplex.com/) o altro strumento simile per visualizzare i dati nella tabella di archiviazione. È anche possibile esportare i dati dalle tabelle di Azure.


## <a name="use-microsoft-flow-with-an-azure-table-optional"></a>Usare Microsoft Flow con una tabella di Azure (*facoltativi*) 

È possibile usare [Microsoft Flow](https://docs.microsoft.com/flow/) per automatizzare le notifiche ogni volta che un cliente potenziale viene aggiunto alla tabella di Azure. Se non hai un account, è possibile [iscriversi a un account gratuito](https://flow.microsoft.com/).


### <a name="lead-notification-example"></a>Esempio di notifica di cliente potenziale

Usare questo esempio come guida per creare un flusso di base che invia automaticamente una notifica di posta elettronica quando un nuovo cliente potenziale viene aggiunto a una tabella di Azure. In questo esempio viene impostata una ricorrenza per inviare le informazioni sul cliente potenziale ogni ora, se viene aggiornata l'archiviazione tabelle.

1. Accedere all'account Microsoft Flow.
2. Selezionare **Flussi personali** nella barra di spostamento a sinistra.
3. Selezionare **+ Nuovo** nella barra di spostamento in alto.  
4. Nell'elenco a discesa, selezionare **+ Crea da zero**
5. In Crea un flusso da zero, selezionare **Crea da zero**.

   ![Creare un nuovo flusso da zero](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Nella pagina di ricerca dei trigger e dei connettori, selezionare **Trigger**.
7. In **Trigger**, selezionare **Ricorrenza**.
8. Nella finestra **Ricorrenza**, mantenere l'impostazione predefinita 1 per **Intervallo**. Dall’elenco a discesa **Frequenza**, selezionare **Ora**.

   >[!NOTE] 
   >Sebbene in questo esempio venga usato un intervallo di 1 ora, è possibile selezionare l'intervallo e la frequenza che meglio si adattano alle proprie esigenze aziendali.

   ![Impostare la frequenza di 1 ora per la ricorrenza](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Selezionare **+ nuovo passaggio**.
10. Cercare "Recupera ora precedente", quindi selezionare **Recupera ora precedente** in Azioni. 

    ![Trovare e selezionare l’azione Recupera ora precedente](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. Nella finestra **Recupera ora precedente**, impostare l’**intervallo** su 1.  Dall’elenco a discesa **Unità di tempo**, selezionare **Ora**.
    >[!IMPORTANT] 
    >Assicurarsi che l’intervallo e l’unità di tempo corrispondano all'intervallo e alla frequenza configurati per la ricorrenza.

    ![Configurare l’intervallo per recuperare l’ora precedente](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >È possibile controllare il flusso in qualsiasi momento per verificare che ogni passaggio sia configurato correttamente. Per controllare il flusso, selezionare **Verifica flusso** dalla barra dei menu Flusso.

Nel successivo set di passaggi, ci si collegherà alla propria tabella Azure e si configurerà la logica di elaborazione per gestire i nuovi clienti potenziali.

1. Dopo l'operazione Recupera ora precedente, selezionare **+ Nuovo passaggio**, quindi cercare "Recupera entità".
2. In **Azioni**, selezionare **Recupera entità**, quindi selezionare **Mostra opzioni avanzate**.
3. Nella finestra **Recupera entità**, inserire le informazioni nei seguenti campi:

   - **Tabella**: immettere il nome dell'archiviazione tabelle di Azure. Nella schermata successiva è mostrato il prompt dei comandi quando si immette "MarketPlaceLeads" in questo esempio. 

     ![Scegliere un valore personalizzato come nome della tabella di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Query di filtro** – fare clic su questo campo e il **Recupera ora precedente** icona viene visualizzata in una finestra popup. Selezionare **Ora precedente** per usarla come timestamp per filtrare la query. In alternativa, è possibile incollare la funzione seguente nel campo: CreatedTime `gt datetime'@{body('Get_past_time')}'` 

     ![Impostare la funzione di filtraggio delle query](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Selezionare **Nuovo passaggio** per aggiungere una condizione di analisi della tabella di Azure per i nuovi clienti potenziali.

   ![Usare Nuovo passaggio per aggiungere una condizione di analisi della tabella di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. Nella finestra **Scegliere un’azione**, selezionare **Azioni**, quindi selezionare **Condizione**.

     ![Aggiungere una condizione](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. Nella finestra **Condizione**, selezionare il campo **Scegliere un valore**, quindi selezionare **Espressione** nella finestra popup.
7. Incollare `length(body('Get_entities')?['value'])` nel campo ***fx***. Selezionare **OK** per aggiungere questa funzione. Per completare la configurazione della condizione:

   - Selezionare “è maggiore di” dall'elenco a discesa.
   - Immettere 0 come valore 

     ![Aggiungere una funzione alla condizione](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Configurare l'azione da eseguire in base al risultato della condizione.

     ![Impostare un’azione in base ai risultati della condizione](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Se la condizione restituisce **Se no**, non eseguire alcuna operazione. 
10. Se la condizione restituisce **Se sì**, attivare un'azione che si connetta all'account di Office 365 per inviare un messaggio di posta elettronica. Selezionare **Aggiungi un'azione**.
11. Selezionare **Inviare un messaggio di posta elettronica**. 
12. Nella finestra **Inviare un messaggio di posta elettronica**, inserire le informazioni nei seguenti campi:

    - **A**: immettere un indirizzo di posta elettronica per tutti gli utenti che riceveranno la notifica.
    - **Oggetto**: inserire un oggetto per il messaggio di posta elettronica. Ad esempio: Nuovi clienti potenziali!
    - **Corpo**:   Aggiungere il testo da includere in ogni messaggio di posta elettronica (facoltativo) e incollarlo nel corpo `body('Get_entities')?['value']` come una funzione per inserire le informazioni sul cliente potenziale.

      >[!NOTE] 
      >È possibile inserire punti dati statici o dinamici aggiuntivi al corpo del messaggio di posta elettronica.

      ![Configurare la posta elettronica per notifiche sui clienti potenziali](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Selezionare **Salva** per salvare il flusso. Microsoft Flow eseguirà automaticamente la verifica del flusso per cercare eventuali errori. Se non sono presenti errori, il flusso verrà avviato dopo il salvataggio.

Nella schermata successiva è mostrato un esempio di come dovrebbe apparire il flusso finale.

[![Sequenza flusso finale](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

(*Fare clic sull'immagine per ingrandirla.* )


### <a name="manage-your-flow"></a>Gestire i flussi

Gestire il flusso dopo la relativa esecuzione è semplice.  Il controllo del flusso è completo. Ad esempio, è possibile arrestarlo, modificarlo, visualizzare una cronologia di esecuzione e ottenere analisi. Nella schermata successiva sono mostrate le opzioni disponibili per gestire un flusso. 

 ![Gestione di un flusso](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Il flusso rimane in esecuzione fino a quando non viene arrestato manualmente con **Disattiva flusso**.

Se non si ricevono notifiche di posta elettronica sui clienti potenziali, non sono stati aggiunti nuovi clienti potenziali alla tabella di Azure. Se si verificano errori di flusso, si otterrà un messaggio di posta elettronica simile all'esempio nella prossima schermata.

 ![Notifica di posta elettronica sugli errori del flusso](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)


## <a name="next-steps"></a>Passaggi successivi

[Configurare lead relativi ai clienti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
