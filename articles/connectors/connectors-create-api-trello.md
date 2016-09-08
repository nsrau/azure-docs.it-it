<properties
pageTitle="Trello | Microsoft Azure"
description="Creare app per la logica in Servizio app di Azure. Trello offre una panoramica di tutti i progetti, al lavoro e a casa. È un modo semplice, gratuito, flessibile per gestire visivamente i progetti e organizzare qualsiasi cosa. Connettersi a Trello per gestire bacheche, elenchi e schede"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# Introduzione al connettore Trello

Trello offre una panoramica di tutti i progetti, al lavoro e a casa. È un modo semplice, gratuito, flessibile per gestire visivamente i progetti e organizzare qualsiasi cosa. Connettersi a Trello per gestire bacheche, elenchi e schede.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore Trello può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore Trello sono disponibili le azioni e/o i trigger seguenti:

### Azioni di Trello
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|Elenca le schede nella bacheca|
|[GetCard](connectors-create-api-trello.md#getcard)|Ottiene la scheda in base all'ID|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|Aggiorna la scheda|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|Elimina la scheda|
|[CreateCard](connectors-create-api-trello.md#createcard)|Crea una nuova scheda nell'account Trello|
|[ListBoards](connectors-create-api-trello.md#listboards)|Elenca le bacheche|
|[GetBoard](connectors-create-api-trello.md#getboard)|Ottiene la bacheca in base all'ID|
|[ListLists](connectors-create-api-trello.md#listlists)|Elenca gli elenchi di schede nella bacheca|
|[GetList](connectors-create-api-trello.md#getlist)|Ottiene l'elenco in base all'ID|
### Trigger di Trello
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando una nuova scheda viene aggiunta a una bacheca|Attiva un flusso quando una scheda viene aggiunta una bacheca|
|Quando una nuova scheda viene aggiunta a un elenco|Attiva un flusso quando una scheda viene aggiunta a un elenco|


## Creare una connessione a Trello
Per creare app per la logica con Trello, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire le credenziali di Trello|
Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

>[AZURE.INCLUDE [Passaggi per creare una connessione a Trello](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Riferimento per Trello
Si applica alla versione 1.0

## OnNewCardInBoard
Quando una nuova scheda viene aggiunta a una bacheca: attiva un flusso quando una nuova scheda viene aggiunta una bacheca

```GET: /trigger/boards/{board_id}/cards```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|path|nessuno|ID univoco della bacheca in cui recuperare le schede|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## OnNewCardInList
Quando una nuova scheda viene aggiunta a un elenco: attiva un flusso quando una scheda viene aggiunta a un elenco

```GET: /trigger/lists/{list_id}/cards```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|nessuno|ID univoco della bacheca in cui recuperare le schede|
|list\_id|stringa|yes|path|nessuno|ID univoco dell'elenco in cui recuperare le schede|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## ListCards
Elenca le schede nella bacheca: elenca le schede nella bacheca

```GET: /boards/{board_id}/cards```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|path|nessuno|ID della bacheca in cui recuperare tutte le schede|
|actions|stringa|no|query|nessuno|Elenca le azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|attachments|boolean|no|query|nessuno|Visualizza gli allegati|
|attachment\_fields|stringa|no|query|nessuno|Elenca i campi degli allegati da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|stickers|boolean|no|query|nessuno|Visualizza le etichette|
|Membri di|boolean|no|query|nessuno|Visualizza i membri|
|memeber\_fields|string|no|query|nessuno|Elenca i campi dei membri da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|CheckItemStates|boolean|no|query|nessuno|Restituisce gli stati delle schede|
|Checklists|stringa|no|query|nessuno|Visualizza gli elenchi di controllo|
|limit|integer|no|query|nessuno|Numero massimo di risultati da restituire, compreso tra 1 e 1000|
|since|stringa|no|query|nessuno|Recupera tutte le schede dopo questa data|
|prima di|stringa|no|query|nessuno|Recupera tutte le schede prima di questa data|
|filter|string|no|query|nessuno|Filtra la risposta|
|fields|string|no|query|nessuno|Elenca i campi delle schede da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## GetCard
Ottiene la scheda in base all'ID: ottiene la scheda in base all'ID

```GET: /cards/{card_id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|string|yes|query|nessuno|ID della bacheca in cui recuperare le schede|
|card\_id|stringa|yes|path|nessuno|ID della scheda da recuperare|
|actions|string|no|query|nessuno|Elenca le azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|actions\_entities|boolean|no|query|nessuno|Restituisce le entità delle azioni|
|actions\_display|boolean|no|query|nessuno|Restituisce le visualizzazioni delle azioni|
|actions\_limit|integer|no|query|nessuno|Numero massimo di azioni da restituire|
|action\_fields|stringa|no|query|nessuno|Elenco di campi dell'azione da restituire per ogni azione. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|action\_memberCreator\_fields|stringa|no|query|nessuno|Elenca i campi dell'autore membro dell'azione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|attachments|boolean|no|query|nessuno|Restituisce gli allegati|
|attachement\_fields|stringa|no|query|nessuno|Elenca i campi dell'allegato da restituire per ogni allegato. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|Membri di|boolean|no|query|nessuno|Restituisce i membri|
|member\_fields|string|no|query|nessuno|Elenca i campi dell'azione da restituire per ogni membro. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|membersVoted|boolean|no|query|nessuno|Restituisce i membri votati|
|memberVoted\_fields|stringa|no|query|nessuno|Elenco dei campi membri votati da restituire per ogni membro votato. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|checkItemStates|boolean|no|query|nessuno|Restituisce gli stati delle schede|
|checkItemState\_fields|stringa|no|query|nessuno|Elenco di campi di stato da restituire per ogni stato degli elementi della scheda. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|checklists|string|no|query|nessuno|Restituire gli elenchi di controllo|
|checklist\_fields|stringa|no|query|nessuno|Elenco di campi di elenco di controllo da restituire per ogni elenco di controllo. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|board|boolean|no|query|nessuno|Restituisce la bacheca a cui appartiene la scheda|
|board\_fields|string|no|query|nessuno|Elenca i campi della bacheca da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|list|boolean|no|query|nessuno|Restituisce l'elenco a cui appartiene la scheda|
|list\_fields|stringa|no|query|nessuno|Elenca i campi dell'elenco da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|stickers|boolean|no|query|nessuno|Restituisce le etichette|
|sticker\_fields|stringa|no|query|nessuno|Elenca i campi dell'etichetta da restituire per ogni etichetta. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|fields|stringa|no|query|nessuno|Elenca i campi delle schede da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## UpdateCard
Aggiorna la scheda: aggiorna la scheda

```PUT: /cards/{card_id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|query|nessuno|ID della bacheca da cui recuperare le schede|
|card\_id|stringa|yes|path|nessuno|ID della scheda da aggiornare|
|updateCard| |sì|body|nessuno|Valori aggiornati della scheda|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## DeleteCard
Elimina la scheda: elimina la scheda

```DELETE: /cards/{card_id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|query|nessuno|ID della bacheca da cui recuperare le schede|
|card\_id|stringa|yes|path|nessuno|ID della scheda da eliminare|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## CreateCard
Crea una scheda: crea una nuova scheda nell'account Trello

```POST: /cards```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|query|nessuno|ID univoco della bacheca in cui creare le schede|
|newCard| |sì|body|nessuno|Nuova scheda da aggiungere alla bacheca di Trello|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## ListBoards
Elenca le bacheche: elenca le bacheche

```GET: /member/me/boards```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|filter|stringa|no|query|nessuno|Elenca i filtri da applicare ai risultati della bacheca. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|fields|stringa|no|query|nessuno|Elenca i campi della bacheca da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|actions|stringa|no|query|nessuno|Elenca i campi delle azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|actions\_entities|boolean|no|query|nessuno|Restituisce le entità delle azioni|
|actions\_limit|integer|no|query|nessuno|Numero massimo di azioni da restituire|
|actions\_format|string|no|query|nessuno|Specifica il formato delle azioni da restituire|
|actions\_since|stringa|no|query|nessuno|Restituisce le azioni dopo la data specificata|
|action\_fields|stringa|no|query|nessuno|Elenca i campi dell'azione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|memberships|stringa|no|query|nessuno|Specifica le informazioni di appartenenza da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|Organizzazione|boolean|no|query|nessuno|Specifica di restituire informazioni sull'organizzazione|
|organization\_fields|string|no|query|nessuno|Elenca i campi dell'organizzazione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|lists|stringa|no|query|nessuno|Specifica se restituire elenchi che appartengono alla bacheca|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## GetBoard
Ottiene la bacheca in base all'ID: ottiene la bacheca in base all'ID

```GET: /boards/{board_id}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|path|nessuno|ID univoco della bacheca da recuperare|
|actions|string|no|query|nessuno|Elenca le azioni da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|action\_entities|boolean|no|query|nessuno|Specifica se restituire le entità dell'azione|
|actions\_display|boolean|no|query|nessuno|Specifica se visualizzare le entità dell'azione|
|actions\_format|stringa|no|query|nessuno|Specifica il formato delle azioni da restituire|
|actions\_since|stringa|no|query|nessuno|Restituisce solo le azioni dopo questa data|
|actions\_limit|integer|no|query|nessuno|Numero massimo di azioni da restituire|
|action\_fields|stringa|no|query|nessuno|Elenca i campi da restituire con ogni campo. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|action\_memeber|boolean|no|query|nessuno|Specifica se restituire i membri dell'azione|
|action\_member\_fields|stringa|no|query|nessuno|Elenca i campi dei membri da restituire con ogni membro dell'azione. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|action\_memberCreator|boolean|no|query|nessuno|Specifica se restituire l'autore membro dell'azione|
|action\_memberCreator\_fields|string|no|query|nessuno|Elenca i campi dell'autore membro dell'azione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|cards|string|no|query|nessuno|Specifica le schede da restituire|
|card\_fields|stringa|no|query|nessuno|Elenca i campi da restituire per ogni scheda. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|card\_attachments|boolean|sì|query|nessuno|Specifica se restituire gli allegati nelle schede|
|card\_attachment\_fields|string|no|query|nessuno|Elenca i campi dell'allegato da restituire per ogni allegato. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|card\_checklists|stringa|no|query|nessuno|Specifica gli elenchi di controllo da restituire per ogni scheda|
|card\_stickers|boolean|no|query|nessuno|Specifica se restituire le etichette dell'azione|
|boardStarts|stringa|no|query|nessuno|Specifica gli avvii della bacheca da restituire|
|Etichette|stringa|no|query|nessuno|Specifica le etichette da restituire|
|label\_fields|stringa|no|query|nessuno|Elenca i campi delle etichette da restituire per ogni scheda. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|labels\_limits|integer|no|query|nessuno|Numero massimo di etichette da restituire|
|lists|stringa|no|query|nessuno|Specifica gli elenchi da restituire|
|list\_fields|string|no|query|nessuno|Elenca i campi degli elenchi da restituire per ogni elenco. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|memberships|stringa|no|query|nessuno|Elenca le appartenenze da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|memberships\_member|boolean|no|query|nessuno|Specifica se restituire i membri delle appartenenze|
|memberships\_member\_fields|stringa|no|query|nessuno|Elenca i campi dei membri da restituire per ogni membro dell'appartenenza. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|Membri di|stringa|no|query|nessuno|Elenca i membri da restituire.|
|member\_fields|string|no|query|nessuno|Elenca i campi dei membri da restituire per ogni membro. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|membersInvited|string|no|query|nessuno|Specifica i membri invitati da restituire|
|membersInvited\_fields|stringa|no|query|nessuno|Elenca i campi da restituire per ognuno. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|checklists|stringa|no|query|nessuno|Specifica gli elenchi di controllo da restituire|
|checklist\_fields|stringa|no|query|nessuno|Elenca i campi di elenco di controllo da restituire per ogni elenco di controllo. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|Organizzazione|boolean|no|query|nessuno|Specifica se restituire le informazioni sull'organizzazione|
|organization\_fields|stringa|no|query|nessuno|Elenca i campi dell'organizzazione da restituire per ogni organizzazione. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|organization\_memberships|stringa|no|query|nessuno|Elenca le appartenenze dell'organizzazione da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|myPerfs|boolean|no|query|nessuno|Specifica se restituire le prestazioni|
|fields|stringa|no|query|nessuno|Elenca i campi da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## ListLists
Elenca gli elenchi di schede nella bacheca: elenca gli elenchi di schede nella bacheca

```GET: /boards/{board_id}/lists```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|path|nessuno|ID univoco della bacheca in cui recuperare gli elenchi|
|cards|stringa|no|query|nessuno|Specifica le schede da restituire|
|card\_fields|string|no|query|nessuno|Elenca i campi delle schede da restituire informazioni. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|filter|stringa|no|query|nessuno|Specificare la proprietà di filtro per gli elenchi|
|fields|string|no|query|nessuno|Elenca i campi da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## GetList
Ottiene l'elenco in base all'ID: ottiene l'elenco in base all'ID

```GET: /lists/{list_id}```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|board\_id|stringa|yes|query|nessuno|ID univoco della bacheca da cui recuperare gli elenchi|
|list\_id|string|yes|path|nessuno|ID univoco dell'elenco da recuperare|
|cards|string|no|query|nessuno|Specifica le schede da restituire|
|card\_fields|stringa|no|query|nessuno|Elenca i campi delle schede da restituire per ogni scheda. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|board|boolean|no|query|nessuno|Specifica se restituire informazioni sulla bacheca|
|board\_fields|stringa|no|query|nessuno|Elenca i campi della bacheca da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|
|fields|stringa|no|query|nessuno|Elenca i campi dell'elenco da restituire. Specificare "all" o un elenco con valori validi delimitati da virgole.|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita|


## Definizioni oggetti 

### Card


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|stringa|No |
|checkItemStates|array|No |
|closed|boolean|No |
|dateLastActivity|string|No |
|desc|stringa|No |
|idBoard|stringa|No |
|idList|string|No |
|idMembersVoted|array|No |
|idShort|integer|No |
|idAttachmentCover|stringa|No |
|manualCoverAttachment|boolean|No |
|idLabels|array|No |
|name|string|No |
|pos|integer|No |
|shortLink|stringa|No |
|badges|non definito|No |
|due|stringa|No |
|email|string|No |
|shortUrl|stringa|No |
|subscribed|boolean|No |
|URL|stringa|No |



### Badges


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Votes|integer|No |
|ViewingMemberVoted|boolean|No |
|Subscribed|boolean|No |
|Fogbugz|string|No |
|CheckItems|integer|No |
|CheckItemsChecked|integer|No |
|Commenti|integer|No |
|Attachments|integer|No |
|Description|boolean|No |
|Due|string|No |



### Oggetto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|



### CreateCard


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|idList|stringa|Sì |
|name|stringa|Sì |
|desc|string|No |
|pos|stringa|No |
|idMembers|stringa|No |
|idLabels|string|No |
|urlSource|string|No |
|fileSource|stringa|No |
|idCardSource|string|No |
|keepFromSource|stringa|No |



### UpdateCard


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|name|string|No |
|desc|string|No |
|closed|boolean|No |
|idMembers|stringa|No |
|idAttachmentCover|stringa|No |
|idList|string|No |
|idBoard|string|No |
|pos|string|No |
|due|string|No |
|subscribed|boolean|No |



### Board


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|No |
|closed|boolean|No |
|dateLastActivity|stringa|No |
|dateLastView|stringa|No |
|desc|stringa|No |
|idOrganization|stringa|No |
|invitations|array|No |
|invited|boolean|No |
|labelNames|non definito|No |
|memberships|array|No |
|name|string|No |
|pinned|boolean|No |
|powerUps|array|No |
|perfs|non definito|No |
|shortLink|string|No |
|shortUrl|stringa|No |
|starred|string|No |
|subscribed|string|No |
|URL|stringa|No |



### Etichetta


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|green|stringa|No |
|yellow|stringa|No |
|orange|stringa|No |
|red|string|No |
|purple|string|No |
|blue|stringa|No |
|sky|string|No |
|lime|string|No |
|pink|stringa|No |
|black|stringa|No |



### Appartenenza


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|No |
|idMember|string|No |
|memberType|string|No |
|unconfirmed|boolean|No |



### Perfs


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|permissionLevel|string|No |
|voting|stringa|No |
|commenti|stringa|No |
|invitations|stringa|No |
|selfJoin|boolean|No |
|cardCovers|boolean|No |
|calendarFeedEnabled|boolean|No |
|background|stringa|No |
|backgroundColor|string|No |
|backgroundImage|string|No |
|backgroundImageScaled|string|No |
|backgroundTile|boolean|No |
|backgroundBrightness|stringa|No |
|canBePublic|boolean|No |
|canBeOrg|boolean|No |
|canBePrivate|boolean|No |
|canInvite|boolean|No |



### Elenco


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|stringa|No |
|name|stringa|No |
|closed|boolean|No |
|idBoard|stringa|No |
|pos|number|No |
|subscribed|boolean|No |
|cards|array|No |
|board|non definito|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->