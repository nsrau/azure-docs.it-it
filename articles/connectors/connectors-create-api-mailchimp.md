<properties
pageTitle="MailChimp | Microsoft Azure"
description="Creare app per la logica in Servizio app di Azure. MailChimp è un servizio SaaS che consente alle aziende di gestire e automatizzare le attività di marketing via posta elettronica, tra cui l'invio di messaggi di posta elettronica e di messaggi automatizzati, nonché la diffusione di campagne mirate."
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

# Introduzione al connettore MailChimp

MailChimp è un servizio SaaS che consente alle aziende di gestire e automatizzare le attività di marketing via posta elettronica, tra cui l'invio di messaggi di posta elettronica e di messaggi automatizzati, nonché la diffusione di campagne mirate.


>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Il connettore MailChimp può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore MailChimp sono disponibili le azioni e/o i trigger seguenti:

### Azioni di MailChimp
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Crea una nuova campagna basata su tipo di campagna, elenco di destinatari e impostazioni della campagna (subject\_line, title, from\_name e reply\_to)|
|[newlist](connectors-create-api-mailchimp.md#newlist)|Crea un nuovo elenco nell'account di MailChimp|
|[addmember](connectors-create-api-mailchimp.md#addmember)|Aggiunge o aggiorna un membro di un elenco|
|[removemember](connectors-create-api-mailchimp.md#removemember)|Elimina un membro da un elenco.|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Aggiorna le informazioni per un membro specifico dell'elenco|
### Trigger di MailChimp
È possibile ascoltare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando un membro viene aggiunto a un elenco|Attiva un flusso di lavoro quando un nuovo membro viene aggiunto a un elenco|
|Quando viene creato un nuovo elenco|Attiva un flusso di lavoro quando viene creato un nuovo elenco|


## Creare una connessione a MailChimp
Per creare app per la logica con MailChimp, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornisce le credenziali per MailChimp|

>[AZURE.INCLUDE [Passaggi per creare una connessione a MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento per MailChimp
Si applica alla versione 1.0

## newcampaign
Nuova campagna: crea una nuova campagna basata su tipo di campagna, elenco di destinatari e impostazioni della campagna (subject\_line, title, from\_name e reply\_to)

```POST: /campaigns```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|newCampaignRequest| |sì|body|nessuno|Oggetto JSON da inviare all'interno del corpo con i parametri della richiesta di una nuova campagna|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## newlist
Nuovo elenco: crea un nuovo elenco nell'account di MailChimp

```POST: /lists```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Description|
| ---|---|---|---|---|---|
|newListRequest| |sì|body|nessuno|Oggetto JSON da inviare all'interno del corpo con i parametri della richiesta di una nuova campagna|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## addmember
Aggiunta di membri all'elenco: aggiunge un membro all'elenco o ne aggiorna uno già presente

```POST: /lists/{list_id}/members```

| Name| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|stringa|yes|path|nessuno|ID univoco per l'elenco|
|newMemberInList| |sì|body|nessuno|Oggetto JSON da inviare nel corpo con le informazioni del nuovo membro|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## removemember
Rimozione di un membro dall'elenco: elimina un membro da un elenco.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|string|yes|path|nessuno|ID univoco per l'elenco|
|member\_email|stringa|yes|path|nessuno|Indirizzo di posta elettronica del membro da eliminare|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## updatemember
Aggiornamento delle informazioni di un membro: aggiorna le informazioni per un membro specifico dell'elenco

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|stringa|yes|path|nessuno|ID univoco per l'elenco|
|member\_email|string|yes|path|nessuno|Indirizzo di posta elettronica univoco del membro da aggiornare|
|updateMemberInListRequest| |sì|body|nessuno|Oggetto JSON da inviare all'interno del corpo con le informazioni del membro aggiornato|

#### Response

|Name|Descrizione|
|---|---|
|200|OK|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## OnMemberSubscribed
Quando un membro viene aggiunto a un elenco: attiva un flusso di lavoro quando viene aggiunto un nuovo membro a un elenco

```GET: /trigger/lists/{list_id}/members```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|string|yes|path|nessuno|ID univoco per l'elenco|

#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## OnCreateList
Quando viene creato un nuovo elenco: attiva un flusso di lavoro quando viene creato un nuovo elenco

```GET: /trigger/lists```

Non sono disponibili parametri per questa chiamata
#### Response

|Nome|Descrizione|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Non autorizzata|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del server. Si è verificato un errore sconosciuto|
|default|Operazione non riuscita.|


## Definizioni oggetti 

### NewCampaignRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|type|stringa|Sì |
|recipients|non definito|Sì |
|Scheda Impostazioni|non definito|Sì |
|variate\_settings|non definito|No |
|tracking|non definito|No |
|rss\_opts|non definito|No |
|social\_card|non definito|No |



### Recipient


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|stringa|Sì |
|segment\_opts|non definito|No |



### Impostazioni


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|subject\_line|stringa|Sì |
|title|string|No |
|from\_name|stringa|Sì |
|reply\_to|stringa|Sì |
|use\_conversation|boolean|No |
|to\_name|stringa|No |
|folder\_id|integer|No |
|authenticate|boolean|No |
|auto\_footer|boolean|No |
|inline\_css|boolean|No |
|auto\_tweet|boolean|No |
|auto\_fb\_post|array|No |
|fb\_comments|boolean|No |



### Variate\_Settings


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|winner\_criteria|string|No |
|wait\_time|integer|No |
|test\_size|integer|No |
|subject\_lines|array|No |
|send\_times|array|No |
|from\_names|array|No |
|reply\_to\_addresses|array|No |



### Rilevamento


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|opens|boolean|No |
|html\_clicks|boolean|No |
|text\_clicks|boolean|No |
|goal\_tracking|boolean|No |
|ecomm360|boolean|No |
|google\_analytics|stringa|No |
|clicktale|string|No |
|salesforce|non definito|No |
|highrise|non definito|No |
|capsule|non definito|No |



### RSS\_Opts


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|feed\_url|stringa|No |
|frequency|stringa|No |
|constrain\_rss\_img|string|No |
|schedule|non definito|No |



### Social\_Card


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|image\_url|stringa|No |
|description|stringa|No |
|title|string|No |



### Segment\_Opts


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|saved\_segment\_id|integer|No |
|match|stringa|No |



### Salesforce


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|campaign|boolean|No |
|di HDInsight|boolean|No |



### Highrise


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|campaign|boolean|No |
|di HDInsight|boolean|No |



### Capsule


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|di HDInsight|boolean|No |



### Pianificazione


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|hour|integer|No |
|daily\_send|non definito|No |
|weekly\_send\_day|string|No |
|monthly\_send\_date|number|No |



### Daily\_Send


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|sunday|boolean|No |
|monday|boolean|No |
|tuesday|boolean|No |
|wednesday|boolean|No |
|thursday|boolean|No |
|friday|boolean|No |
|saturday|boolean|No |



### CampaignResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|No |
|type|stringa|No |
|create\_time|stringa|No |
|archive\_url|stringa|No |
|status|stringa|No |
|emails\_sent|integer|No |
|send\_time|stringa|No |
|content\_type|stringa|No |
|recipient|array|No |
|Scheda Impostazioni|non definito|No |
|variate\_settings|non definito|No |
|tracking|non definito|No |
|rss\_opts|non definito|No |
|ab\_split\_opts|non definito|No |
|social\_card|non definito|No |
|report\_summary|non definito|No |
|delivery\_status|non definito|No |
|\_links|array|No |



### AB\_Split\_Opts


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|split\_test|string|No |
|pick\_winner|string|No |
|wait\_units|stringa|No |
|wait\_time|integer|No |
|split\_size|integer|No |
|from\_name\_a|stringa|No |
|from\_name\_b|stringa|No |
|reply\_email\_a|string|No |
|reply\_email\_b|stringa|No |
|subject\_a|string|No |
|subject\_b|stringa|No |
|send\_time\_a|stringa|No |
|send\_time\_b|stringa|No |
|send\_time\_winner|stringa|No |



### Report\_Summary


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|opens|integer|No |
|unique\_opens|integer|No |
|open\_rate|number|No |
|clicks|integer|No |
|subscriber\_clicks|number|No |
|click\_rate|number|No |



### Delivery\_Status


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|enabled|boolean|No |
|can\_cancel|boolean|No |
|status|string|No |
|emails\_sent|integer|No |
|emails\_canceled|integer|No |



### Collegamento


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|rel|string|No |
|href|string|No |
|statico|stringa|No |
|targetSchema|stringa|No |
|schema|stringa|No |



### NewListRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|name|stringa|Sì |
|contact|non definito|Sì |
|permission\_reminder|stringa|Sì |
|use\_archive\_bar|boolean|No |
|campaign\_defaults|non definito|Sì |
|notify\_on\_subscribe|stringa|No |
|notify\_on\_unsubscribe|string|No |
|email\_type\_option|boolean|Sì |
|visibility|stringa|No |



### Contatto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|company|string|Sì |
|address1|stringa|Sì |
|address2|string|No |
|city|string|Sì |
|state|string|Sì |
|zip|string|Sì |
|country|stringa|Sì |
|phone|stringa|Sì |



### Campaign\_Defaults


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|from\_name|stringa|Sì |
|from\_email|stringa|Sì |
|subject|string|No |
|Lingua|stringa|Sì |



### CreateNewListResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|Sì |
|name|stringa|Sì |
|contact|non definito|Sì |
|permission\_reminder|string|Sì |
|use\_archive\_bar|boolean|No |
|campaign\_defaults|non definito|Sì |
|notify\_on\_subscribe|stringa|No |
|notify\_on\_unsubscribe|string|No |
|date\_created|stringa|No |
|list\_rating|integer|No |
|email\_type\_option|boolean|Sì |
|subscribe\_url\_short|stringa|No |
|subscribe\_url\_long|string|No |
|beamer\_address|string|No |
|visibility|stringa|No |
|modules|array|No |
|Statistiche|non definito|No |
|\_links|array|No |



### Statistiche


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|member\_count|integer|No |
|unsubscribe\_count|integer|No |
|cleaned\_count|integer|No |
|member\_count\_since\_send|integer|No |
|unsubscribe\_count\_since\_send|numero intero|No |
|cleaned\_count\_since\_send|integer|No |
|campaign\_count|integer|No |
|campaign\_last\_sent|integer|No |
|merge\_field\_count|integer|No |
|avg\_sub\_rate|number|No |
|avg\_unsub\_rate|number|No |
|target\_sub\_rate|number|No |
|open\_rate|number|No |
|click\_rate|number|No |
|last\_sub\_date|stringa|No |
|last\_unsub\_date|string|No |



### GetListsResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|lists|array|No |
|total\_items|integer|No |



### NewMemberInListRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|email\_type|stringa|No |
|status|string|Sì |
|merge\_fields|non definito|No |
|interests|string|No |
|Lingua|stringa|No |
|vip|boolean|No |
|location|non definito|No |
|email\_address|stringa|Sì |



### FirstAndLastName


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|FNAME|string|No |
|LNAME|stringa|No |



### Percorso


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|latitudine|number|No |
|longitudine|number|No |



### MemberResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|No |
|email\_address|stringa|No |
|unique\_email\_id|stringa|No |
|email\_type|string|No |
|status|string|No |
|merge\_fields|non definito|No |
|interests|stringa|No |
|Statistiche|non definito|No |
|ip\_signup|stringa|No |
|timestamp\_signup|stringa|No |
|ip\_opt|stringa|No |
|timestamp\_opt|stringa|No |
|member\_rating|integer|No |
|last\_changed|string|No |
|Lingua|string|No |
|vip|boolean|No |
|email\_client|stringa|No |
|location|non definito|No |
|last\_note|non definito|No |
|list\_id|stringa|No |
|\_links|array|No |



### Last\_Note


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|note\_id|integer|No |
|created\_at|string|No |
|created\_by|string|No |
|note|stringa|No |



### GetAllMembersResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Membri di|array|No |
|list\_id|stringa|No |
|total\_items|integer|No |



### Oggetto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|



### UpdateMemberInListRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|email\_address|string|No |
|email\_type|stringa|No |
|status|stringa|Sì |
|merge\_fields|non definito|No |
|interests|stringa|No |
|Lingua|string|No |
|vip|boolean|No |
|location|non definito|No |



### GetMembersResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Membri di|array|No |
|list\_id|string|No |
|total\_items|integer|No |



### AddUserResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|stringa|Sì |
|email\_address|stringa|Sì |
|unique\_email\_id|stringa|No |
|email\_type|stringa|No |
|status|stringa|No |
|merge\_fields|non definito|Sì |
|interests|stringa|No |
|Statistiche|non definito|No |
|ip\_signup|string|No |
|timestamp\_signup|stringa|No |
|ip\_opt|stringa|No |
|timestamp\_opt|string|No |
|member\_rating|integer|No |
|last\_changed|stringa|No |
|Lingua|stringa|No |
|vip|boolean|No |
|email\_client|stringa|No |
|location|non definito|No |
|last\_note|non definito|No |
|list\_id|stringa|No |
|\_links|array|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->