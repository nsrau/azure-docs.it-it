---
title: Aggiungere il connettore Dynamics CRM Online alle app per la logica | Microsoft Docs
description: "Creare app per la logica in Servizio app di Azure. Il provider di connessioni di Dynamics CRM Online fornisce un&quot;API per usare le entità in Dynamics CRM Online."
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: a1ea6c09621aeeb1e98bbbf5edf1d5deb5e4b721


---
# <a name="get-started-with-the-dynamics-crm-online-connector"></a>Introduzione al connettore Dynamics CRM Online
Connettersi a Dynamics CRM Online per creare un nuovo record, aggiornare un elemento e così via. Con CRM Online è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da CRM Online. 
* Usare azioni per eliminare un record, recuperare entità e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando un elemento viene aggiornato in CRM, è possibile inviare un messaggio di posta elettronica tramite Office 365.

Questo argomento illustra come usare il connettore Dynamics CRM Online in un'app per la logica ed elenca i trigger e le azioni.

> [!NOTE]
> Questa versione dell'articolo si applica alla la disponibilità generale delle app per la logica.
> 
> 

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) e [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dynamics-crm-online"></a>Connettersi a Dynamics CRM Online
Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a Dynamics, è prima di tutto necessaria una *connessione* a Dynamics CRM Online. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Quindi, per creare la connessione a Dynamics, immettere le credenziali dell'account Dynamics CRM Online.

### <a name="create-the-connection"></a>Creare la connessione
> [!INCLUDE [Steps to create a connection to Dynamics CRM Online Connection Provider](../../includes/connectors-create-api-crmonline.md)]
> 
> 

## <a name="use-a-trigger"></a>Usare un trigger
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. I trigger eseguono il "polling" del servizio agli intervalli e con la frequenza desiderati. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Nell'app per la logica digitare "dynamics" per ottenere l'elenco dei trigger:  
   
    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Selezionare **Dynamics CRM Online - Quando un record viene creato**. Se esiste già una connessione, selezionare un'organizzazione e un'entità dall'elenco a discesa.
   
    ![](./media/connectors-create-api-crmonline/select-organization.png)
   
    Se viene chiesto di effettuare l'accesso, immettere i dettagli di accesso per creare la connessione. La sezione [Creare la connessione](connectors-create-api-crmonline.md#create-the-connection) di questo argomento elenca i passaggi necessari. 
   
   > [!NOTE]
   > In questo esempio l'app per la logica viene eseguita quando viene creato un record. Per vedere i risultati del trigger, aggiungere un'altra azione che invia un messaggio di posta elettronica al proprio indirizzo. Ad esempio, aggiungere l'azione di Office 365 *Inviare un'e-mail* che consente di inviare un'e-mail al proprio indirizzo quando viene aggiunto un nuovo record. 
   > 
   > 
3. Selezionare il pulsante **Modifica** e impostare i valori **Frequenza** e **Intervallo**. Ad esempio, se si vuole che il trigger esegua il poll ogni 15 minuti, impostare **Frequenza** su **Minuto** e **Intervallo** su **15**. 
   
    ![](./media/connectors-create-api-crmonline/edit-properties.png)
4. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="use-an-action"></a>Usare un'azione
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.
   
    ![](./media/connectors-create-api-crmonline/add-action.png)
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di testo digitare "dynamics" per ottenere l'elenco di tutte le azioni disponibili.
   
    ![](./media/connectors-create-api-crmonline/dynamics-actions.png)
4. In questo esempio scegliere **Dynamics CRM Online - Aggiornare un record**. Se esiste già una connessione, scegliere i valori per **Nome organizzazione**, **Nome entità** e le altre proprietà:  
   
    ![](./media/connectors-create-api-crmonline/sample-action.png)
   
    Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-crmonline.md#create-the-connection) di questo argomento descrive queste proprietà. 
   
   > [!NOTE]
   > In questo esempio viene aggiornato un record esistente in CRM Online. Per aggiornare il record, è possibile usare l'output di un altro trigger. Ad esempio, aggiungere il trigger di SharePoint *Quando viene modificato un elemento esistente*. Aggiungere quindi l'azione *Aggiornare un record* di CRM Online che usa i campi di SharePoint per aggiornare il record esistente in CRM Online. 
   > 
   > 
5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="technical-details"></a>Dettagli tecnici
## <a name="triggers"></a>Trigger
| Trigger | Descrizione |
| --- | --- |
| [Quando un record viene creato](connectors-create-api-crmonline.md#when-a-record-is-created) |Attiva un flusso quando viene creato un oggetto in CRM. |
| [Quando un record viene aggiornato](connectors-create-api-crmonline.md#when-a-record-is-updated) |Attiva un flusso quando viene modificato un oggetto in CRM. |
| [Quando un record viene eliminato](connectors-create-api-crmonline.md#when-a-record-is-deleted) |Attiva un flusso quando viene eliminato un oggetto in CRM. |

## <a name="actions"></a>Azioni
| Azione | Descrizione |
| --- | --- |
| [Elencare i record](connectors-create-api-crmonline.md#list-records) |Questa operazione ottiene i record da un'entità. |
| [Creare un nuovo record](connectors-create-api-crmonline.md#create-a-new-record) |Questa operazione crea un nuovo record di un'entità. |
| [Ottenere il record](connectors-create-api-crmonline.md#get-record) |Questa operazione ottiene il record specificato da un'entità. |
| [Eliminare un record](connectors-create-api-crmonline.md#delete-a-record) |Questa operazione elimina un record da una raccolta di entità. |
| [Aggiornare un record](connectors-create-api-crmonline.md#update-a-record) |Questa operazione aggiorna un record esistente per un'entità. |

### <a name="trigger-and-action-details"></a>Dettagli sui trigger e le azioni
In questa sezione sono riportati i dettagli relativi a ogni trigger e azione, incluse le proprietà di input obbligatorie o facoltative e quelle di output corrispondenti associate al connettore.

#### <a name="when-a-record-is-created"></a>Quando un record viene creato
Attiva un flusso quando viene creato un oggetto in CRM. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da ottenere (valore predefinito = 256) |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>Quando un record viene aggiornato
Attiva un flusso quando viene modificato un oggetto in CRM. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da ottenere (valore predefinito = 256) |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
ItemsList

| Nome proprietà | Tipo di dati |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>Quando un record viene eliminato
Attiva un flusso quando viene eliminato un oggetto in CRM. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da ottenere (valore predefinito = 256) |
| $filter |Query di filtro |Una query di filtro ODATA per limitare gli elementi restituiti |
| $orderby |Ordina per |Query orderBy ODATA per specificare l'ordine degli elementi |

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
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| $skip |Ignora conteggio |Numero di elementi da ignorare (impostazione predefinita = 0) |
| $top |Numero massimo di Get |Numero massimo di elementi da ottenere (valore predefinito = 256) |
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
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuno.

#### <a name="get-record"></a>Recupera il record
Questa operazione ottiene il record specificato da un'entità. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| id* |Identificatore dell'elemento |Specifica l'identificatore del record |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli dell'output
Nessuno.

#### <a name="delete-a-record"></a>Elimina un record
Questa operazione elimina un record da una raccolta di entità. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
| table* |Entity Name |Nome dell'entità |
| id* |Identificatore dell'elemento |Specifica l'identificatore del record |

L'asterisco (*) indica che la proprietà è obbligatoria.

#### <a name="update-a-record"></a>Aggiornare un record
Questa operazione aggiorna un record esistente per un'entità. 

| Nome proprietà | Nome visualizzato | Description |
| --- | --- | --- |
| dataset* |Nome organizzazione |Nome dell'organizzazione CRM, ad esempio Contoso |
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
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).




<!--HONumber=Jan17_HO3-->


