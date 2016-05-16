<properties
pageTitle="MailChimp | Microsoft Azure"
description="Creare app per la logica con Servizio app di Azure. MailChimp è un servizio SaaS che consente alle aziende di gestire e automatizzare le attività di marketing via posta elettronica, tra cui l'invio di messaggi di posta elettronica e di messaggi automatizzati, nonché la diffusione di campagne mirate."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# Introduzione al connettore MailChimp



Il connettore MailChimp può essere usato da:

- [App per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flussi](http://flows.microsoft.com)  

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.

Per iniziare subito a creare un'app per la logica, vedere [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

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
Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento per MailChimp
Si applica alla versione: 1.0

## newcampaign
Nuova campagna: crea una nuova campagna basata su tipo di campagna, elenco di destinatari e impostazioni della campagna (subject\_line, title, from\_name e reply\_to)

```POST: /campaigns```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|newCampaignRequest| |sì|body|nessuno|Oggetto JSON da inviare all'interno del corpo con i parametri della richiesta di una nuova campagna|

#### Risposta

|Nome|Descrizione|
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

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|newListRequest| |sì|body|nessuno|Oggetto JSON da inviare all'interno del corpo con i parametri della richiesta di una nuova campagna|

#### Risposta

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

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|list\_id|string|yes|path|nessuno|ID univoco per l'elenco|
|newMemberInList| |sì|body|nessuno|Oggetto JSON da inviare nel corpo con le informazioni del nuovo membro|

#### Risposta

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
|member\_email|string|yes|path|nessuno|Indirizzo di posta elettronica del membro da eliminare|

#### Risposta

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
|list\_id|string|yes|path|nessuno|ID univoco per l'elenco|
|member\_email|string|yes|path|nessuno|Indirizzo di posta elettronica univoco del membro da aggiornare|
|updateMemberInListRequest| |sì|body|nessuno|Oggetto JSON da inviare all'interno del corpo con le informazioni del membro aggiornato|

#### Risposta

|Nome|Descrizione|
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

#### Risposta

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
|type|string|Sì |
|recipients|non definito|Sì |
|Scheda Impostazioni|non definito|Sì |
|variate\_settings|non definito|No |
|tracking|non definito|No |
|rss\_opts|non definito|No |
|social\_card|non definito|No |



### Recipient


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|list\_id|string|Sì |
|segment\_opts|non definito|No |



### Impostazioni


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|subject\_line|string|Sì |
|title|string|No |
|from\_name|string|Sì |
|reply\_to|string|Sì |
|use\_conversation|boolean|No |
|to\_name|string|No |
|folder\_id|numero intero|No |
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
|wait\_time|numero intero|No |
|test\_size|numero intero|No |
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
|google\_analytics|string|No |
|clicktale|string|No |
|salesforce|non definito|No |
|highrise|non definito|No |
|capsule|non definito|No |



### RSS\_Opts


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|feed\_url|string|No |
|frequency|string|No |
|constrain\_rss\_img|string|No |
|schedule|non definito|No |



### Social\_Card


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|image\_url|string|No |
|description|string|No |
|title|string|No |



### Segment\_Opts


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|saved\_segment\_id|numero intero|No |
|match|string|No |



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
|hour|numero intero|No |
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
|type|string|No |
|create\_time|string|No |
|archive\_url|string|No |
|status|string|No |
|emails\_sent|numero intero|No |
|send\_time|string|No |
|content\_type|string|No |
|recipient|array|No |
|Scheda Impostazioni|non definito|No |
|variate\_settings|non definito|No |
|tracking|non definito|No |
|rss\_opts|non definito|No |
|ab\_split\_opts|non definito|No |
|social\_card|non definito|No |
|report\_summary|non definito|No |
|delivery\_status|non definito|No |
|_\_links|array|No |



### AB\_Split\_Opts


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|split\_test|string|No |
|pick\_winner|string|No |
|wait\_units|string|No |
|wait\_time|numero intero|No |
|split\_size|numero intero|No |
|from\_name\_a|string|No |
|from\_name\_b|string|No |
|reply\_email\_a|string|No |
|reply\_email\_b|string|No |
|subject\_a|string|No |
|subject\_b|string|No |
|send\_time\_a|string|No |
|send\_time\_b|string|No |
|send\_time\_winner|string|No |



### Report\_Summary


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|opens|numero intero|No |
|unique\_opens|numero intero|No |
|open\_rate|number|No |
|clicks|numero intero|No |
|subscriber\_clicks|number|No |
|click\_rate|number|No |



### Delivery\_Status


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|enabled|boolean|No |
|can\_cancel|boolean|No |
|status|string|No |
|emails\_sent|numero intero|No |
|emails\_canceled|numero intero|No |



### Collegamento


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|rel|string|No |
|href|string|No |
|statico|string|No |
|targetSchema|string|No |
|schema|string|No |



### NewListRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|name|string|Sì |
|contact|non definito|Sì |
|permission\_reminder|string|Sì |
|use\_archive\_bar|boolean|No |
|campaign\_defaults|non definito|Sì |
|notify\_on\_subscribe|string|No |
|notify\_on\_unsubscribe|string|No |
|email\_type\_option|boolean|Sì |
|visibility|string|No |



### Contatto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|company|string|Sì |
|address1|string|Sì |
|address2|string|No |
|city|string|Sì |
|state|string|Sì |
|zip|string|Sì |
|country|string|Sì |
|phone|string|Sì |



### Campaign\_Defaults


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|from\_name|string|Sì |
|from\_email|string|Sì |
|subject|string|No |
|Lingua|string|Sì |



### CreateNewListResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|Sì |
|name|string|Sì |
|contact|non definito|Sì |
|permission\_reminder|string|Sì |
|use\_archive\_bar|boolean|No |
|campaign\_defaults|non definito|Sì |
|notify\_on\_subscribe|string|No |
|notify\_on\_unsubscribe|string|No |
|date\_created|string|No |
|list\_rating|numero intero|No |
|email\_type\_option|boolean|Sì |
|subscribe\_url\_short|string|No |
|subscribe\_url\_long|string|No |
|beamer\_address|string|No |
|visibility|string|No |
|modules|array|No |
|Statistiche|non definito|No |
|_\_links|array|No |



### Statistiche


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|member\_count|numero intero|No |
|unsubscribe\_count|numero intero|No |
|cleaned\_count|numero intero|No |
|member\_count\_since\_send|numero intero|No |
|unsubscribe\_count\_since\_send|numero intero|No |
|cleaned\_count\_since\_send|numero intero|No |
|campaign\_count|numero intero|No |
|campaign\_last\_sent|numero intero|No |
|merge\_field\_count|numero intero|No |
|avg\_sub\_rate|number|No |
|avg\_unsub\_rate|number|No |
|target\_sub\_rate|number|No |
|open\_rate|number|No |
|click\_rate|number|No |
|last\_sub\_date|string|No |
|last\_unsub\_date|string|No |



### GetListsResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|lists|array|No |
|total\_items|numero intero|No |



### NewMemberInListRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|email\_type|string|No |
|status|string|Sì |
|merge\_fields|non definito|No |
|interests|string|No |
|Lingua|string|No |
|vip|boolean|No |
|location|non definito|No |
|email\_address|string|Sì |



### FirstAndLastName


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|FNAME|string|No |
|LNAME|string|No |



### Percorso


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|latitudine|number|No |
|longitudine|number|No |



### MemberResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|No |
|email\_address|string|No |
|unique\_email\_id|string|No |
|email\_type|string|No |
|status|string|No |
|merge\_fields|non definito|No |
|interests|string|No |
|Statistiche|non definito|No |
|ip\_signup|string|No |
|timestamp\_signup|string|No |
|ip\_opt|string|No |
|timestamp\_opt|string|No |
|member\_rating|numero intero|No |
|last\_changed|string|No |
|Lingua|string|No |
|vip|boolean|No |
|email\_client|string|No |
|location|non definito|No |
|last\_note|non definito|No |
|list\_id|string|No |
|_\_links|array|No |



### Last\_Note


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|note\_id|numero intero|No |
|created\_at|string|No |
|created\_by|string|No |
|note|string|No |



### GetAllMembersResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Membri di|array|No |
|list\_id|string|No |
|total\_items|numero intero|No |



### Oggetto


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|



### UpdateMemberInListRequest


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|email\_address|string|No |
|email\_type|string|No |
|status|string|Sì |
|merge\_fields|non definito|No |
|interests|string|No |
|Lingua|string|No |
|vip|boolean|No |
|location|non definito|No |



### GetMembersResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|Membri di|array|No |
|list\_id|string|No |
|total\_items|numero intero|No |



### AddUserResponseModel


| Nome proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|id|string|Sì |
|email\_address|string|Sì |
|unique\_email\_id|string|No |
|email\_type|string|No |
|status|string|No |
|merge\_fields|non definito|Sì |
|interests|string|No |
|Statistiche|non definito|No |
|ip\_signup|string|No |
|timestamp\_signup|string|No |
|ip\_opt|string|No |
|timestamp\_opt|string|No |
|member\_rating|numero intero|No |
|last\_changed|string|No |
|Lingua|string|No |
|vip|boolean|No |
|email\_client|string|No |
|location|non definito|No |
|last\_note|non definito|No |
|list\_id|string|No |
|_\_links|array|No |


## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->