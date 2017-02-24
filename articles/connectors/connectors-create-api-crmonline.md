---
title: Aggiungere il connettore Dynamics 365 (online) alle app per la logica di Azure | Microsoft Docs
description: "Creare app per la logica con Servizio app di Azure. Il provider di connessioni di Dynamics 365 (online) offre un&quot;API per usare le entità in Dynamics 365 (online)."
services: logic-apps
cloud: Azure Stack
documentationcenter: 
author: Mattp123
manager: anneta
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp
translationtype: Human Translation
ms.sourcegitcommit: fa426f474f4efd4023da5dfd9954dacf96f635ab
ms.openlocfilehash: 99d5379ad1e6965dd9ed88de456cc850d7e40d5a


---
# <a name="create-a-logic-app-with-the-dynamics-365-connector"></a>Creare un'app per la logica con il connettore Dynamics 365

App per la logica consente di connettersi a Dynamics 365 (online) e creare utili flussi aziendali per creare nuovi record, aggiornare elementi o restituire un elenco di record. Con il connettore Dynamics 365, è possibile:

* Creare un flusso aziendale in base ai dati ottenuti da Dynamics 365 (online).
* Usare azioni che ottengono una risposta e quindi rendono disponibile l'output per altre azioni. Ad esempio, quando viene aggiornato un elemento in Dynamics 365 (online), è possibile inviare un messaggio di posta elettronica con Office 365.

Questo argomento illustra come creare un'app per la logica che crea un'attività in Dynamics 365 ogni volta che viene creato un nuovo lead in Dynamics 365.

## <a name="prerequisites"></a>Prerequisiti
* Un account Azure.
* Un account Dynamics 365 (online).

## <a name="walkthrough-create-a-task-whenever-a-new-lead-is-created-in-dynamics-365"></a>Procedura dettagliata: Creare un'attività ogni volta che viene creato un nuovo lead in Dynamics 365
1.    [Accedere ad Azure](https://portal.azure.com).
2.    Digitare *App per la logica* nella **casella di ricerca** e quindi premere INVIO.
3.    Nell'area del servizio App per la logica fare clic su **Aggiungi**.

  ![Aggiungere un'app per la logica](./media/connectors-create-api-crmonline/add-logic-app.png)

4.    Completare i campi **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località** per creare l'oggetto app per la logica e quindi fare clic su **Crea**.

5.    Selezionare la nuova app per la logica. Quando viene visualizzata la notifica **La distribuzione è riuscita** fare clic su **Aggiorna**.

6.    In Strumenti di sviluppo fare clic su **Progettazione app per la logica** e quindi nell'elenco dei modelli disponibili fare clic su **LogicApp vuoto**.

7.    Digitare *Dynamics 365*. Vengono elencati diversi trigger per Dynamics 365. Fare clic su **Dynamics 365 - Quando un record viene creato**.

8.    Se viene richiesto di accedere a Dynamics 365, eseguire l'accesso.

9.    Nei dettagli del trigger immettere le informazioni seguenti.

  * **Nome organizzazione**. Selezionare l'istanza di Dynamics 365 di cui l'app per la logica dovrà essere in ascolto.

  * **Nome entità**. Selezionare l'entità di cui essere in ascolto, che fungerà da trigger per avviare l'app per la logica. In questa procedura guidata si seleziona **Lead**.

  * **Specificare la frequenza in base a cui verificare gli elementi**. Questi valori impostano la frequenza con cui l'app per la logica verificherà la presenza di aggiornamenti correlati al trigger. L'impostazione predefinita prevede la verifica della presenza di aggiornamenti ogni tre minuti.

    * **Frequenza**. Selezionare secondi, minuti, ore o giorni.

    * **Intervallo**. Immettere un numero che indica il numero di secondi, minuti, ore o giorni che deve trascorrere prima della verifica successiva.

    ![Dettagli del trigger dell'app per la logica](./media/connectors-create-api-crmonline/trigger-details.png)

10.    Fare clic su **Nuovo passaggio** e quindi su **Aggiungi un'azione**.

11.    Digitare *Dynamics 365* e quindi nell'elenco fare clic su **Dynamics 365 - Crea un nuovo record**.

12.    Immettere le seguenti informazioni.
  * **Nome organizzazione**. Selezionare l'istanza di Dynamics 365 in cui si vuole che il flusso crei il record. Si noti che non deve necessariamente essere la stessa istanza da cui viene attivato l'evento.
  * **Nome entità**. Selezionare l'entità in cui si vuole che venga creato un record quando viene attivato l'evento. In questa procedura guidata si seleziona **Attività**.

13.    Viene visualizzata una casella Oggetto. Facendo clic sulla casella, viene visualizzato un riquadro di contenuto dinamico in cui è possibile selezionare uno dei campi seguenti.
  * **Cognome**. Selezionando questo campo, alla creazione del record attività verrà inserito il cognome del lead nel campo Oggetto dell'attività.
  * **Argomento**. Selezionando questo campo, alla creazione del record attività verrà inserito il campo Argomento del lead nel campo Oggetto dell'attività.
Fare clic su **Argomento** per aggiungere questo campo alla casella **Oggetto**.

  ![Dettagli per la creazione di un nuovo record nell'app per la logica](./media/connectors-create-api-crmonline/create-record-details.png)

14.    Fare clic su **Salva** sulla barra degli strumenti di Progettazione app per la logica.

  ![Salva nella barra degli strumenti di Progettazione app per la logica](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15.    Per avviare l'app per la logica, fare clic su **Esegui**.

  ![Salva nella barra degli strumenti di Progettazione app per la logica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Creare ora un record lead in Dynamics 365 for Sales e verificare il funzionamento del flusso.

## <a name="using-advanced-options"></a>Uso di opzioni avanzate
Quando si aggiunge un passaggio a un'app per la logica, facendo clic su **Mostra le opzioni avanzate** è possibile controllare come verranno filtrati i dati nel passaggio aggiungendo una query di filtro o di ordinamento.

Ad esempio, è possibile usare una query di filtro per recuperare solo gli account attivi ed eseguire l'ordinamento in base al nome dell'account. A tale scopo, immettere la query di filtro OData **statuscode eq 1** e selezionare **Nome account** nel riquadro di contenuto dinamico. Per altre informazioni, vedere [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) e [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

  ![Opzioni avanzate per le app per la logica](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Procedure consigliate per l'uso delle opzioni avanzate
Quando si aggiunge un valore a un campo, è necessario che corrisponda al tipo di campo, indipendentemente dal fatto che il valore venga digitato o selezionato nel contenuto dinamico visualizzato.

Tipo di campo  |Utilizzo  |Posizione  |Nome  |Tipo di dati  
---------|---------|---------|---------|---------
Campi di testo|I campi di testo richiedono una singola riga di testo oppure contenuto dinamico costituito da un campo di tipo testo. Ad esempio, i campi Categoria e Sottocategoria.|Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Attività > Campi |category |Singola riga di testo       
Campi di tipo Integer | Alcuni campi richiedono Integer oppure contenuto dinamico costituito da un campo di tipo Integer. Ad esempio, Percentuale completamento e Durata. |Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Attività > Campi |percentcomplete |Numero intero         
Campi di data | Alcuni campi richiedono una data immessa nel formato mm/gg/aaaa oppure contenuto dinamico costituito da un campo di tipo data. Ad esempio, Data creazione, Data di inizio, Inizio effettivo, Ultimo periodo sospensione, Fine effettiva e Scadenza. | Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Attività > Campi |createdon |Data e ora         
Campi che richiedono sia ID record che tipo di ricerca |Alcuni campi che fanno riferimento a un altro record di entità richiedono sia l'ID record che il tipo di ricerca. |Impostazioni > Personalizzazioni > Personalizza il sistema > Entità > Account > Campi  | accountid   | Chiave primaria

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Alcuni esempi di campi che richiedono sia ID record che tipo di ricerca
A integrazione della tabella precedente, di seguito sono riportati alcuni esempi di campi a cui non sono applicabili valori selezionati dall'elenco di contenuto dinamico. È invece necessario immettere sia un ID record che un tipo di ricerca nei campi in PowerApps.  
*  Proprietario e Tipo di proprietario. Il campo Proprietario deve contenere un ID record utente o team valido. Tipo di proprietario deve contenere **systemusers** o **teams**.
* Cliente e Tipo di cliente. Il campo Cliente deve contenere un ID record account o contatto valido. Tipo di proprietario deve contenere **accounts** o **contacts**.
* Tema e Tipo relativo. Il campo Tema deve contenere un ID record valido, ad esempio un ID record account o contatto. Tipo relativo deve contenere il tipo di ricerca per il record, ad esempio **accounts** o **contacts**.

L'esempio seguente di azione di creazione di un'attività aggiunge un record account corrispondente all'ID record aggiungendolo al campo Tema dell'attività.

  ![ID record e tipo account nel flusso](./media/connectors-create-api-crmonline/recordid-type-account.png)

Questo esempio assegna anche l'attività a un utente specifico in base all'ID record dell'utente.
  ![ID record e tipo account nel flusso](./media/connectors-create-api-crmonline/recordid-type-user.png)

Per trovare l'ID di un record, vedere la sezione *Trovare l'ID record* riportata di seguito.

## <a name="find-the-record-id"></a>Trovare l'ID record
1. Aprire un record, ad esempio un record account.

2. Sulla barra degli strumenti Azioni fare clic su **Apri nuova finestra** ![Apertura di una nuova finestra nel record](./media/connectors-create-api-crmonline/popout-record.png).
In alternativa, sulla barra degli strumenti Azioni fare clic su **Invia link tramite messaggio e-mail** per copiare l'URL completo nel programma di posta elettronica predefinito.

3. L'ID record viene visualizzato tra i caratteri di codifica %7b e %7d dell'URL.

  ![ID record e tipo account nel flusso](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi
Per risolvere i problemi relativi a un passaggio non riuscito in un'app per la logica, visualizzare i dettagli dello stato dell'evento.

1. Nell'area App per la logica fare clic sull'app per la logica e quindi su **Panoramica**. Verrà visualizzata l'area Riepilogo, in cui è riportato lo stato delle esecuzioni dell'app per la logica. Se sono presenti esecuzioni non riuscite, fare clic sull'evento non riuscito di cui si vogliono visualizzare altre informazioni.

  ![Risoluzione dei problemi delle app per la logica - Passaggio 1](./media/connectors-create-api-crmonline/tshoot1.png)

2. Fare clic sul passaggio non riuscito per espanderlo.

  ![Risoluzione dei problemi delle app per la logica - Passaggio 2](./media/connectors-create-api-crmonline/tshoot2.png)

3. Verranno visualizzati i dettagli del passaggio, che consentono di risolvere la causa dell'errore.

    ![Risoluzione dei problemi delle app per la logica - Passaggio 2](./media/connectors-create-api-crmonline/tshoot3.png)

Per altre informazioni sulla risoluzione dei problemi delle app per la logica, vedere [Diagnosi degli errori delle app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="technical-details"></a>Dettagli tecnici
## <a name="triggers"></a>Trigger
| Trigger | Descrizione |
| --- | --- |
| Quando un record viene creato |Attiva un flusso quando viene creato un oggetto in Dynamics 365. |
| Quando un record viene aggiornato |Attiva un flusso quando viene modificato un oggetto in Dynamics 365. |
| Quando un record viene eliminato |Attiva un flusso quando viene eliminato un oggetto in Dynamics 365. |

## <a name="actions"></a>Azioni
| Azione | Descrizione |
| --- | --- |
| Elenca i record |Questa operazione ottiene i record da un'entità. |
| Crea un nuovo record |Questa operazione crea un nuovo record di un'entità. |
| Recupera il record |Questa operazione ottiene il record specificato da un'entità. |
| Elimina un record |Questa operazione elimina un record da una raccolta di entità. |
| Aggiornare un record |Questa operazione aggiorna un record esistente per un'entità. |

### <a name="trigger-and-action-details"></a>Dettagli sui trigger e le azioni
In questa sezione sono riportati i dettagli relativi a ogni trigger e azione, incluse le proprietà di input obbligatorie o facoltative e quelle di output corrispondenti associate al connettore.

#### <a name="when-a-record-is-created"></a>Quando un record viene creato
Attiva un flusso quando viene creato un oggetto in Dynamics 365.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>Quando un record viene aggiornato
Attiva un flusso quando viene modificato un oggetto in Dynamics 365.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>Quando un record viene eliminato
Attiva un flusso quando viene eliminato un oggetto in Dynamics 365.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |


L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

#### <a name="list-records"></a>Elenca i record
Questa operazione ottiene i record da un'entità.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

#### <a name="create-a-new-record"></a>Crea un nuovo record
Questa operazione crea un nuovo record di un'entità.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuno.

#### <a name="get-record"></a>Recupera il record
Questa operazione ottiene il record specificato da un'entità.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| id* |Identificatore dell'elemento |Specifica l'identificatore del record |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuno.

#### <a name="delete-a-record"></a>Elimina un record
Questa operazione elimina un record da una raccolta di entità.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| id* |Identificatore dell'elemento |Specifica l'identificatore del record |

L'asterisco (*) indica che la proprietà è obbligatoria.

#### <a name="update-a-record"></a>Aggiornare un record
Questa operazione aggiorna un record esistente per un'entità.

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione Dynamics 365, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| id* |Identificatore del record |Specifica l'identificatore del record |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuno.

## <a name="http-responses"></a>Risposte HTTP
Le azioni e i trigger possono restituire uno o più dei codici di stato HTTP seguenti:

| Name | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto. |
| default |Operazione non riuscita. |

## <a name="next-steps"></a>Passaggi successivi
Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).



<!--HONumber=Feb17_HO2-->


