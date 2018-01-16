---
title: Connettersi a Dynamics 365 (online) dalle app per la logica di Azure | Microsoft Docs
description: "Creare flussi di lavoro di app per la logica che gestiscono le entità di Dynamics 365 (online) tramite l'API inclusa nel connettore di Dynamics 365"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2018
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Connettersi a Dynamics 365 da flussi di lavoro di app per la logica

Con il servizio App per la logica è possibile connettersi a Dynamics 365 (online) e creare flussi aziendali utili che restituiscono record, aggiornano elementi o restituiscono un elenco di record. Con il connettore Dynamics 365, è possibile:

* Creare un flusso aziendale in base ai dati ottenuti da Dynamics 365 (online).
* Usare azioni che ottengono una risposta e quindi rendono disponibile l'output per altre azioni. Ad esempio, quando viene aggiornato un elemento in Dynamics 365 (online), è possibile inviare un messaggio di posta elettronica con Office 365.

Questo argomento illustra come creare un'app per la logica che crea un'attività in Dynamics 365 ogni volta che viene creato un nuovo lead in Dynamics 365.

## <a name="prerequisites"></a>prerequisiti
* Un account Azure.
* Un account Dynamics 365 (online).

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Creare un'attività quando viene creato un nuovo lead in Dynamics 365

1.  [Accedere ad Azure](https://portal.azure.com).

2.  Nella casella di ricerca di Azure digitare `Logic apps` e premere INVIO.

      ![Trovare App per la logica](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  In **App per la logica** fare clic su **Aggiungi**.

      ![Aggiungere un'app per la logica](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Per creare l'app per la logica, completare i campi **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Percorso** e quindi fare clic su **Crea**.

5.  Selezionare la nuova app per la logica. Quando viene visualizzata la notifica **La distribuzione è riuscita** fare clic su **Aggiorna**.

6.  In **Strumenti di sviluppo** fare clic su **Progettazione app per la logica**. Nell'elenco dei modelli fare clic su **App per la logica vuota**.

7.  Nella casella di ricerca digitare `Dynamics 365`. Nell'elenco dei trigger di Dynamics 365 selezionare **Dynamics 365 - Quando un record viene creato**.

8.  Se viene richiesto di accedere a Dynamics 365, eseguire l'operazione.

9.  Nei dettagli del trigger immettere le informazioni seguenti:

  * **Nome organizzazione**. Selezionare l'istanza di Dynamics 365 di cui l'app per la logica dovrà essere in ascolto.

  * **Nome entità**. Selezionare l'entità che si vuole ascoltare. Questo evento funge da trigger per avviare l'app per la logica. 
  In questa procedura guidata si seleziona **Lead**.

  * **Specificare la frequenza in base a cui verificare gli elementi**. Questi valori impostano la frequenza con cui l'app per la logica verificherà la presenza di aggiornamenti correlati al trigger. L'impostazione predefinita prevede la verifica della presenza di aggiornamenti ogni tre minuti.

    * **Frequenza**. Selezionare secondi, minuti, ore o giorni.

    * **Intervallo**. Immettere il numero di secondi, minuti, ore o giorni che deve trascorrere prima della verifica successiva.

      ![Dettagli del trigger dell'app per la logica](./media/connectors-create-api-crmonline/trigger-details.png)

10. Fare clic su **Nuovo passaggio** e quindi su **Aggiungi un'azione**.

11. Nella casella di ricerca digitare `Dynamics 365`. Nell'elenco di azioni selezionare **Dynamics 365 - Crea un nuovo record**.

12. Immettere le seguenti informazioni:

    * **Nome organizzazione**. Selezionare l'istanza di Dynamics 365 in cui si vuole che il flusso crei il record. 
    Si noti che questa istanza non deve essere necessariamente la stessa da cui viene attivato l'evento.

    * **Nome entità**. Selezionare l'entità in cui si vuole che venga creato un record quando viene attivato l'evento. 
    In questa procedura guidata si seleziona **Attività**.

13. Fare clic nella casella **Oggetto** visualizzata. Nell'elenco del contenuto dinamico visualizzato è possibile selezionare uno di questi campi:

    * **Cognome**. La selezione di questo campo inserisce il cognome del lead nel campo Oggetto dell'attività, quando viene creato il record dell'attività.
    * **Argomento**. La selezione di questo campo inserisce il campo dell'argomento per il lead nel campo Oggetto dell'attività, quando viene creato il record dell'attività. 
    Fare clic su **Argomento** per aggiungere questo campo alla casella **Oggetto**.

      ![Dettagli per la creazione di un nuovo record nell'app per la logica](./media/connectors-create-api-crmonline/create-record-details.png)

14. Fare clic su **Salva** nella barra degli strumenti di Progettazione app per la logica.

    ![Salva nella barra degli strumenti di Progettazione app per la logica](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Per avviare l'app per la logica, fare clic su **Esegui**.

    ![Salva nella barra degli strumenti di Progettazione app per la logica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Creare ora un record lead in Dynamics 365 for Sales e verificare il funzionamento del flusso.

## <a name="set-advanced-options-for-a-logic-app-step"></a>Impostare le opzioni avanzate per un passaggio dell'app per la logica

Per specificare come filtrare i dati in un passaggio dell'app per la logica, fare clic su **Visualizza opzioni avanzate** in tale passaggio, quindi aggiungere un filtro o eseguire un ordinamento tramite una query.

Ad esempio, è possibile usare una query di filtro per recuperare solo gli account attivi ed eseguire l'ordinamento in base al nome dell'account. A tale scopo, immettere la query di filtro OData `statuscode eq 1` e selezionare **Nome account** nel riquadro di contenuto dinamico. Per altre informazioni, vedere [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) e [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Opzioni avanzate delle app per la logica](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Procedure consigliate per l'uso delle opzioni avanzate

Quando si aggiunge un valore a un campo, il tipo di campo deve corrispondere, indipendentemente dal fatto che il valore venga digitato o selezionato nell'elenco del contenuto dinamico.

Tipo di campo  |Utilizzo  |Posizione  |NOME  |Tipo di dati  
---------|---------|---------|---------|---------
Campi di testo|I campi di testo richiedono una singola riga di testo oppure contenuto dinamico costituito da un campo di tipo testo. Ad esempio, i campi Categoria e Sottocategoria.|Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Attività > Campi |category |Riga di testo singola        
Campi di tipo Integer | Alcuni campi richiedono Integer oppure contenuto dinamico costituito da un campo di tipo Integer. Ad esempio, Percentuale completamento e Durata. |Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Attività > Campi |percentcomplete |Numero intero         
Campi di data | Alcuni campi richiedono una data immessa nel formato mm/gg/aaaa oppure contenuto dinamico costituito da un campo di tipo data. Ad esempio, Data creazione, Data di inizio, Inizio effettivo, Ultimo periodo sospensione, Fine effettiva e Scadenza. | Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Attività > Campi |createdon |Data e ora
Campi che richiedono sia ID record che tipo di ricerca |Alcuni campi che fanno riferimento a un altro record di entità richiedono sia l'ID record che il tipo di ricerca. |Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Account > Campi  | accountid  | Chiave primaria

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Alcuni esempi di campi che richiedono sia ID record che tipo di ricerca
A integrazione della tabella precedente, di seguito sono riportati alcuni esempi di campi a cui non sono applicabili valori selezionati dall'elenco di contenuto dinamico. È invece necessario immettere sia un ID record che un tipo di ricerca nei campi in PowerApps.  
* Proprietario e Tipo di proprietario. Il campo Proprietario deve contenere un ID record utente o team valido. Tipo di proprietario deve contenere **systemusers** o **teams**.
* Cliente e Tipo di cliente. Il campo Cliente deve contenere un ID record account o contatto valido. Tipo di proprietario deve contenere **accounts** o **contacts**.
* Tema e Tipo relativo. Il campo Tema deve contenere un ID record valido, ad esempio un ID record account o contatto. Tipo relativo deve contenere il tipo di ricerca per il record, ad esempio **accounts** o **contacts**.

L'esempio seguente di azione di creazione di un'attività aggiunge un record account corrispondente all'ID record aggiungendolo al campo Tema dell'attività.

![ID record e tipo account nel flusso](./media/connectors-create-api-crmonline/recordid-type-account.png)

Questo esempio assegna anche l'attività a un utente specifico in base all'ID record dell'utente.

![ID record e tipo account nel flusso](./media/connectors-create-api-crmonline/recordid-type-user.png)

Per trovare l'ID di un record, vedere la sezione *Trovare l'ID record*

## <a name="find-the-record-id"></a>Trovare l'ID record

1. Aprire un record, ad esempio un record account.

2. Sulla barra degli strumenti Azioni fare clic su **Apri nuova finestra** ![Apertura di una nuova finestra nel record](./media/connectors-create-api-crmonline/popout-record.png).
In alternativa, sulla barra degli strumenti delle azioni fare clic su **Invia link tramite messaggio e-mail** per copiare l'URL completo nel programma di posta elettronica predefinito.

   L'ID record viene visualizzato tra i caratteri di codifica %7b e %7d dell'URL.

   ![ID record e tipo account nel flusso](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>risoluzione dei problemi
Per risolvere i problemi relativi a un passaggio non riuscito in un'app per la logica, visualizzare i dettagli dello stato dell'evento.

1. In **App per la logica**, selezionare l'app per la logica e quindi fare clic su **Panoramica**. 

   Verrà visualizzata l'area Riepilogo, in cui è riportato lo stato di esecuzione dell'app per la logica. 

   ![Stato di esecuzione dell'app per la logica](./media/connectors-create-api-crmonline/tshoot1.png)

2. Per altre informazioni su esecuzioni non riuscite, fare clic sull'evento con esito negativo. Per espandere un passaggio non riuscito, fare clic su tale passaggio.

   ![Espandere un passaggio non riuscito](./media/connectors-create-api-crmonline/tshoot2.png)

   Vengono visualizzati i dettagli del passaggio, che consentono di risolvere la causa dell'errore.

   ![Dettagli del passaggio non riuscito](./media/connectors-create-api-crmonline/tshoot3.png)

Per altre informazioni sulla risoluzione dei problemi delle app per la logica, vedere [Diagnosi degli errori delle app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/crm/). 

## <a name="next-steps"></a>Passaggi successivi
Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).
