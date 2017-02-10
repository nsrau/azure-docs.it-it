---
title: Trello | Microsoft Docs
description: "Creare app per la logica in Servizio app di Azure. Trello offre una panoramica di tutti i progetti, al lavoro e a casa.  È un modo semplice, gratuito, flessibile per gestire visivamente i progetti e organizzare qualsiasi cosa.  Connettersi a Trello per gestire bacheche, elenchi e schede"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2616db980cb37c1c3759096ec3c05b98d687e047


---
# <a name="get-started-with-the-trello-connector"></a>Introduzione al connettore Trello
Trello offre una panoramica di tutti i progetti, al lavoro e a casa.  È un modo semplice, gratuito, flessibile per gestire visivamente i progetti e organizzare qualsiasi cosa.  Connettersi a Trello per gestire bacheche, elenchi e schede.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica.
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore Trello può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore Trello sono disponibili le azioni e/o i trigger seguenti:

### <a name="trello-actions"></a>Azioni di Trello
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [ListCards](connectors-create-api-trello.md#listcards) |Elenca le schede nella bacheca |
| [GetCard](connectors-create-api-trello.md#getcard) |Ottiene la scheda in base all'ID |
| [UpdateCard](connectors-create-api-trello.md#updatecard) |Aggiorna la scheda |
| [DeleteCard](connectors-create-api-trello.md#deletecard) |Elimina la scheda |
| [CreateCard](connectors-create-api-trello.md#createcard) |Crea una nuova scheda nell'account Trello |
| [ListBoards](connectors-create-api-trello.md#listboards) |Elenca le bacheche |
| [GetBoard](connectors-create-api-trello.md#getboard) |Ottiene la bacheca in base all'ID |
| [ListLists](connectors-create-api-trello.md#listlists) |Elenca gli elenchi di schede nella bacheca |
| [GetList](connectors-create-api-trello.md#getlist) |Ottiene l'elenco in base all'ID |

### <a name="trello-triggers"></a>Trigger di Trello
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando una nuova scheda viene aggiunta a una bacheca |Attiva un flusso quando una scheda viene aggiunta una bacheca |
| Quando una nuova scheda viene aggiunta a un elenco |Attiva un flusso quando una scheda viene aggiunta a un elenco |

## <a name="create-a-connection-to-trello"></a>Creare una connessione a Trello
Per creare app per la logica con Trello, è prima necessario creare una **connessione** e successivamente fornire i dettagli per le proprietà seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornire le credenziali di Trello |

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

> [!INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="reference-for-trello"></a>Riferimento per Trello
Si applica alla versione 1.0

## <a name="onnewcardinboard"></a>OnNewCardInBoard
Quando una nuova scheda viene aggiunta a una bacheca: attiva un flusso quando una nuova scheda viene aggiunta una bacheca

```GET: /trigger/boards/{board_id}/cards```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |Sì |path |nessuno |ID univoco della bacheca in cui recuperare le schede |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="onnewcardinlist"></a>OnNewCardInList
Quando una nuova scheda viene aggiunta a un elenco: attiva un flusso quando una scheda viene aggiunta a un elenco

```GET: /trigger/lists/{list_id}/cards```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |yes |query |nessuno |ID univoco della bacheca in cui recuperare le schede |
| list_id |string |Sì |path |nessuno |ID univoco dell'elenco in cui recuperare le schede |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="listcards"></a>ListCards
Elenca le schede nella bacheca: elenca le schede nella bacheca

```GET: /boards/{board_id}/cards```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |Sì |path |nessuno |ID della bacheca in cui recuperare tutte le schede |
| actions |string |no |query |nessuno |Elenca le azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| attachments |boolean |no |query |nessuno |Visualizza gli allegati |
| attachment_fields |stringa |no |query |nessuno |Elenca i campi degli allegati da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| stickers |boolean |no |query |nessuno |Visualizza le etichette |
| Membri di |boolean |no |query |nessuno |Visualizza i membri |
| memeber_fields |string |no |query |nessuno |Elenca i campi dei membri da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| CheckItemStates |boolean |no |query |nessuno |Restituisce gli stati delle schede |
| Checklists |stringa |no |query |nessuno |Visualizza gli elenchi di controllo |
| limit |integer |no |query |nessuno |Numero massimo di risultati da restituire, compreso tra 1 e 1000 |
| since |stringa |no |query |nessuno |Recupera tutte le schede dopo questa data |
| prima di |stringa |no |query |nessuno |Recupera tutte le schede prima di questa data |
| filter |string |no |query |nessuno |Filtra la risposta |
| fields |string |no |query |nessuno |Elenca i campi delle schede da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="getcard"></a>GetCard
Ottiene la scheda in base all'ID: ottiene la scheda in base all'ID

```GET: /cards/{card_id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |yes |query |nessuno |ID della bacheca in cui recuperare le schede |
| card_id |stringa |Sì |path |nessuno |ID della scheda da recuperare |
| actions |string |no |query |nessuno |Elenca le azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| actions_entities |boolean |no |query |nessuno |Restituisce le entità delle azioni |
| actions_display |boolean |no |query |nessuno |Restituisce le visualizzazioni delle azioni |
| actions_limit |integer |no |query |nessuno |Numero massimo di azioni da restituire |
| action_fields |stringa |no |query |nessuno |Elenco di campi dell'azione da restituire per ogni azione. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| action_memberCreator_fields |string |no |query |nessuno |Elenca i campi dell'autore membro dell'azione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| attachments |boolean |no |query |nessuno |Restituisce gli allegati |
| attachement_fields |stringa |no |query |nessuno |Elenca i campi dell'allegato da restituire per ogni allegato. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| Membri di |boolean |no |query |nessuno |Restituisce i membri |
| member_fields |string |no |query |nessuno |Elenca i campi dell'azione da restituire per ogni membro. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| membersVoted |boolean |no |query |nessuno |Restituisce i membri votati |
| memberVoted_fields |stringa |no |query |nessuno |Elenco dei campi membri votati da restituire per ogni membro votato. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| checkItemStates |boolean |no |query |nessuno |Restituisce gli stati delle schede |
| checkItemState_fields |stringa |no |query |nessuno |Elenco di campi di stato da restituire per ogni stato degli elementi della scheda. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| checklists |stringa |no |query |nessuno |Restituire gli elenchi di controllo |
| checklist_fields |stringa |no |query |nessuno |Elenco di campi di elenco di controllo da restituire per ogni elenco di controllo. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| board |boolean |no |query |nessuno |Restituisce la bacheca a cui appartiene la scheda |
| board_fields |stringa |no |query |nessuno |Elenca i campi della bacheca da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| list |boolean |no |query |nessuno |Restituisce l'elenco a cui appartiene la scheda |
| list_fields |string |no |query |nessuno |Elenca i campi dell'elenco da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| stickers |boolean |no |query |nessuno |Restituisce le etichette |
| sticker_fields |stringa |no |query |nessuno |Elenca i campi dell'etichetta da restituire per ogni etichetta. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| fields |string |no |query |nessuno |Elenca i campi delle schede da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="updatecard"></a>UpdateCard
Aggiorna la scheda: aggiorna la scheda

```PUT: /cards/{card_id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |yes |query |nessuno |ID della bacheca da cui recuperare le schede |
| card_id |stringa |Sì |path |nessuno |ID della scheda da aggiornare |
| updateCard | |sì |body |nessuno |Valori aggiornati della scheda |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="deletecard"></a>DeleteCard
Elimina la scheda: elimina la scheda

```DELETE: /cards/{card_id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |yes |query |nessuno |ID della bacheca da cui recuperare le schede |
| card_id |stringa |Sì |path |nessuno |ID della scheda da eliminare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="createcard"></a>CreateCard
Crea una scheda: crea una nuova scheda nell'account Trello

```POST: /cards```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |yes |query |nessuno |ID univoco della bacheca in cui creare le schede |
| newCard | |sì |body |nessuno |Nuova scheda da aggiungere alla bacheca di Trello |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="listboards"></a>ListBoards
Elenca le bacheche: elenca le bacheche

```GET: /member/me/boards```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| filter |string |no |query |nessuno |Elenca i filtri da applicare ai risultati della bacheca. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| fields |string |no |query |nessuno |Elenca i campi della bacheca da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| actions |string |no |query |nessuno |Elenca i campi delle azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| actions_entities |boolean |no |query |nessuno |Restituisce le entità delle azioni |
| actions_limit |integer |no |query |nessuno |Numero massimo di azioni da restituire |
| actions_format |stringa |no |query |nessuno |Specifica il formato delle azioni da restituire |
| actions_since |stringa |no |query |nessuno |Restituisce le azioni dopo la data specificata |
| action_fields |stringa |no |query |nessuno |Elenca i campi dell'azione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| memberships |stringa |no |query |nessuno |Specifica le informazioni di appartenenza da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| Organizzazione |boolean |no |query |nessuno |Specifica di restituire informazioni sull'organizzazione |
| organization_fields |stringa |no |query |nessuno |Elenca i campi dell'organizzazione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| lists |stringa |no |query |nessuno |Specifica se restituire elenchi che appartengono alla bacheca |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="getboard"></a>GetBoard
Ottiene la bacheca in base all'ID: ottiene la bacheca in base all'ID

```GET: /boards/{board_id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |Sì |path |nessuno |ID univoco della bacheca da recuperare |
| actions |string |no |query |nessuno |Elenca le azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| action_entities |boolean |no |query |nessuno |Specifica se restituire le entità dell'azione |
| actions_display |boolean |no |query |nessuno |Specifica se visualizzare le entità dell'azione |
| actions_format |stringa |no |query |nessuno |Specifica il formato delle azioni da restituire |
| actions_since |stringa |no |query |nessuno |Restituisce solo le azioni dopo questa data |
| actions_limit |integer |no |query |nessuno |Numero massimo di azioni da restituire |
| action_fields |stringa |no |query |nessuno |Elenca i campi da restituire con ogni campo. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| action_memeber |boolean |no |query |nessuno |Specifica se restituire i membri dell'azione |
| action_member_fields |stringa |no |query |nessuno |Elenca i campi dei membri da restituire con ogni membro dell'azione. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| action_memberCreator |boolean |no |query |nessuno |Specifica se restituire l'autore membro dell'azione |
| action_memberCreator_fields |string |no |query |nessuno |Elenca i campi dell'autore membro dell'azione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| cards |string |no |query |nessuno |Specifica le schede da restituire |
| card_fields |stringa |no |query |nessuno |Elenca i campi da restituire per ogni scheda. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| card_attachments |boolean |sì |query |nessuno |Specifica se restituire gli allegati nelle schede |
| card_attachment_fields |string |no |query |nessuno |Elenca i campi dell'allegato da restituire per ogni allegato. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| card_checklists |stringa |no |query |nessuno |Specifica gli elenchi di controllo da restituire per ogni scheda |
| card_stickers |boolean |no |query |nessuno |Specifica se restituire le etichette dell'azione |
| boardStarts |stringa |no |query |nessuno |Specifica gli avvii della bacheca da restituire |
| Etichette |stringa |no |query |nessuno |Specifica le etichette da restituire |
| label_fields |stringa |no |query |nessuno |Elenca i campi delle etichette da restituire per ogni scheda. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| labels_limits |integer |no |query |nessuno |Numero massimo di etichette da restituire |
| lists |stringa |no |query |nessuno |Specifica gli elenchi da restituire |
| list_fields |string |no |query |nessuno |Elenca i campi degli elenchi da restituire per ogni elenco. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| memberships |stringa |no |query |nessuno |Elenca le appartenenze da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| memberships_member |boolean |no |query |nessuno |Specifica se restituire i membri delle appartenenze |
| memberships_member_fields |stringa |no |query |nessuno |Elenca i campi dei membri da restituire per ogni membro dell'appartenenza. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| Membri di |stringa |no |query |nessuno |Elenca i membri da restituire. |
| member_fields |string |no |query |nessuno |Elenca i campi dei membri da restituire per ogni membro. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| membersInvited |string |no |query |nessuno |Specifica i membri invitati da restituire |
| membersInvited_fields |stringa |no |query |nessuno |Elenca i campi da restituire per ognuno. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| checklists |stringa |no |query |nessuno |Specifica gli elenchi di controllo da restituire |
| checklist_fields |stringa |no |query |nessuno |Elenca i campi di elenco di controllo da restituire per ogni elenco di controllo. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| Organizzazione |boolean |no |query |nessuno |Specifica se restituire le informazioni sull'organizzazione |
| organization_fields |stringa |no |query |nessuno |Elenca i campi dell'organizzazione da restituire per ogni organizzazione. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| organization_memberships |stringa |no |query |nessuno |Elenca le appartenenze dell'organizzazione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| myPerfs |boolean |no |query |nessuno |Specifica se restituire le prestazioni |
| fields |string |no |query |nessuno |Elenca i campi da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="listlists"></a>ListLists
Elenca gli elenchi di schede nella bacheca: elenca gli elenchi di schede nella bacheca

```GET: /boards/{board_id}/lists```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |Sì |path |nessuno |ID univoco della bacheca in cui recuperare gli elenchi |
| cards |string |no |query |nessuno |Specifica le schede da restituire |
| card_fields |stringa |no |query |nessuno |Elenca i campi delle schede da restituire informazioni. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| filter |string |no |query |nessuno |Specificare la proprietà di filtro per gli elenchi |
| fields |string |no |query |nessuno |Elenca i campi da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="getlist"></a>GetList
Ottiene l'elenco in base all'ID: ottiene l'elenco in base all'ID

```GET: /lists/{list_id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| board_id |stringa |yes |query |nessuno |ID univoco della bacheca da cui recuperare gli elenchi |
| list_id |string |Sì |path |nessuno |ID univoco dell'elenco da recuperare |
| cards |string |no |query |nessuno |Specifica le schede da restituire |
| card_fields |stringa |no |query |nessuno |Elenca i campi delle schede da restituire per ogni scheda. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| board |boolean |no |query |nessuno |Specifica se restituire informazioni sulla bacheca |
| board_fields |stringa |no |query |nessuno |Elenca i campi della bacheca da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |
| fields |string |no |query |nessuno |Elenca i campi dell'elenco da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole. |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita |

## <a name="object-definitions"></a>Definizioni oggetti
### <a name="card"></a>Card
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |No |
| checkItemStates |array |No |
| closed |boolean |No |
| dateLastActivity |stringa |No |
| desc |string |No |
| idBoard |stringa |No |
| idList |string |No |
| idMembersVoted |array |No |
| idShort |integer |No |
| idAttachmentCover |stringa |No |
| manualCoverAttachment |boolean |No |
| idLabels |array |No |
| name |stringa |No |
| pos |integer |No |
| shortLink |string |No |
| badges |non definito |No |
| due |string |No |
| email |string |No |
| shortUrl |stringa |No |
| subscribed |boolean |No |
| URL |stringa |No |

### <a name="badges"></a>Badges
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Votes |integer |No |
| ViewingMemberVoted |boolean |No |
| Subscribed |boolean |No |
| Fogbugz |string |No |
| CheckItems |integer |No |
| CheckItemsChecked |integer |No |
| Commenti |integer |No |
| Attachments |integer |No |
| Description |boolean |No |
| Due |string |No |

### <a name="object"></a>Oggetto
### <a name="createcard"></a>CreateCard
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| idList |stringa |Sì |
| name |string |Sì |
| desc |string |No |
| pos |string |No |
| idMembers |stringa |No |
| idLabels |string |No |
| urlSource |string |No |
| fileSource |stringa |No |
| idCardSource |string |No |
| keepFromSource |stringa |No |

### <a name="updatecard"></a>UpdateCard
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |stringa |No |
| desc |string |No |
| closed |boolean |No |
| idMembers |stringa |No |
| idAttachmentCover |stringa |No |
| idList |string |No |
| idBoard |stringa |No |
| pos |string |No |
| due |string |No |
| subscribed |boolean |No |

### <a name="board"></a>Board
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |No |
| closed |boolean |No |
| dateLastActivity |stringa |No |
| dateLastView |stringa |No |
| desc |string |No |
| idOrganization |stringa |No |
| invitations |array |No |
| invited |boolean |No |
| labelNames |non definito |No |
| memberships |array |No |
| name |stringa |No |
| pinned |boolean |No |
| powerUps |array |No |
| perfs |non definito |No |
| shortLink |string |No |
| shortUrl |stringa |No |
| starred |string |No |
| subscribed |string |No |
| URL |stringa |No |

### <a name="label"></a>Etichetta
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| green |stringa |No |
| yellow |stringa |No |
| orange |stringa |No |
| red |string |No |
| purple |string |No |
| blue |stringa |No |
| sky |string |No |
| lime |string |No |
| pink |stringa |No |
| black |stringa |No |

### <a name="membership"></a>Appartenenza
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |No |
| idMember |string |No |
| memberType |string |No |
| unconfirmed |boolean |No |

### <a name="perfs"></a>Perfs
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| permissionLevel |string |No |
| voting |stringa |No |
| commenti |stringa |No |
| invitations |stringa |No |
| selfJoin |boolean |No |
| cardCovers |boolean |No |
| calendarFeedEnabled |boolean |No |
| background |stringa |No |
| backgroundColor |string |No |
| backgroundImage |string |No |
| backgroundImageScaled |string |No |
| backgroundTile |boolean |No |
| backgroundBrightness |stringa |No |
| canBePublic |boolean |No |
| canBeOrg |boolean |No |
| canBePrivate |boolean |No |
| canInvite |boolean |No |

### <a name="list"></a>Elenco
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |No |
| name |stringa |No |
| closed |boolean |No |
| idBoard |stringa |No |
| pos |number |No |
| subscribed |boolean |No |
| cards |array |No |
| board |non definito |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


