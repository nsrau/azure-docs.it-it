---
title: MailChimp | Documentazione Microsoft
description: "Creare app per la logica in Servizio app di Azure. MailChimp è un servizio SaaS che consente alle aziende di gestire e automatizzare le attività di marketing via posta elettronica, tra cui l&quot;invio di messaggi di posta elettronica e di messaggi automatizzati, nonché la diffusione di campagne mirate."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cee2c702c705e1451c0a6d01b140e6291b1e5ce1


---
# <a name="get-started-with-the-mailchimp-connector"></a>Introduzione al connettore MailChimp
MailChimp è un servizio SaaS che consente alle aziende di gestire e automatizzare le attività di marketing via posta elettronica, tra cui l'invio di messaggi di posta elettronica e di messaggi automatizzati, nonché la diffusione di campagne mirate.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica.
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore MailChimp può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore MailChimp sono disponibili le azioni e/o i trigger seguenti:

### <a name="mailchimp-actions"></a>Azioni di MailChimp
È possibile eseguire queste azioni:

| Azione | Description |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |Crea una nuova campagna basata su tipo di campagna, elenco di destinatari e impostazioni della campagna (subject_line, title, from_name e reply_to) |
| [newlist](connectors-create-api-mailchimp.md#newlist) |Crea un nuovo elenco nell'account di MailChimp |
| [addmember](connectors-create-api-mailchimp.md#addmember) |Aggiunge o aggiorna un membro di un elenco |
| [removemember](connectors-create-api-mailchimp.md#removemember) |Elimina un membro da un elenco. |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |Aggiorna le informazioni per un membro specifico dell'elenco |

### <a name="mailchimp-triggers"></a>Trigger di MailChimp
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando un membro viene aggiunto a un elenco |Attiva un flusso di lavoro quando un nuovo membro viene aggiunto a un elenco |
| Quando viene creato un nuovo elenco |Attiva un flusso di lavoro quando viene creato un nuovo elenco |

## <a name="create-a-connection-to-mailchimp"></a>Creare una connessione a MailChimp
Per creare app per la logica con MailChimp, è prima necessario creare una **connessione** e quindi fornire i dettagli per le proprietà seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornisce le credenziali per MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="reference-for-mailchimp"></a>Informazioni di riferimento per MailChimp
Si applica alla versione 1.0

## <a name="newcampaign"></a>newcampaign
Nuova campagna: crea una nuova campagna basata su tipo di campagna, elenco di destinatari e impostazioni della campagna (subject_line, title, from_name e reply_to)

```POST: /campaigns```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |sì |body |nessuno |Oggetto JSON da inviare all'interno del corpo con i parametri della richiesta di una nuova campagna |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="newlist"></a>newlist
Nuovo elenco: crea un nuovo elenco nell'account di MailChimp

```POST: /lists```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |sì |body |nessuno |Oggetto JSON da inviare all'interno del corpo con i parametri della richiesta di una nuova campagna |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="addmember"></a>addmember
Aggiunta di membri all'elenco: aggiunge un membro all'elenco o ne aggiorna uno già presente

```POST: /lists/{list_id}/members```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sì |path |nessuno |ID univoco per l'elenco |
| newMemberInList | |sì |body |nessuno |Oggetto JSON da inviare nel corpo con le informazioni del nuovo membro |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="removemember"></a>removemember
Rimozione di un membro dall'elenco: elimina un membro da un elenco.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sì |path |nessuno |ID univoco per l'elenco |
| member_email |string |Sì |path |nessuno |Indirizzo di posta elettronica del membro da eliminare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="updatemember"></a>updatemember
Aggiornamento delle informazioni di un membro: aggiorna le informazioni per un membro specifico dell'elenco

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sì |path |nessuno |ID univoco per l'elenco |
| member_email |string |Sì |path |nessuno |Indirizzo di posta elettronica univoco del membro da aggiornare |
| updateMemberInListRequest | |sì |body |nessuno |Oggetto JSON da inviare all'interno del corpo con le informazioni del membro aggiornato |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="onmembersubscribed"></a>OnMemberSubscribed
Quando un membro viene aggiunto a un elenco: attiva un flusso di lavoro quando viene aggiunto un nuovo membro a un elenco

```GET: /trigger/lists/{list_id}/members```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sì |path |nessuno |ID univoco per l'elenco |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="oncreatelist"></a>OnCreateList
Quando viene creato un nuovo elenco: attiva un flusso di lavoro quando viene creato un nuovo elenco

```GET: /trigger/lists```

Non sono disponibili parametri per questa chiamata

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetti
### <a name="newcampaignrequest"></a>NewCampaignRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| type |stringa |Sì |
| recipients |non definito |Sì |
| Scheda Impostazioni |non definito |Sì |
| variate_settings |non definito |No |
| tracking |non definito |No |
| rss_opts |non definito |No |
| social_card |non definito |No |

### <a name="recipient"></a>Recipient
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| list_id |stringa |Sì |
| segment_opts |non definito |No |

### <a name="settings"></a>Impostazioni
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| subject_line |stringa |Sì |
| title |string |No |
| from_name |stringa |Sì |
| reply_to |stringa |Sì |
| use_conversation |boolean |No |
| to_name |stringa |No |
| folder_id |integer |No |
| authenticate |boolean |No |
| auto_footer |boolean |No |
| inline_css |boolean |No |
| auto_tweet |boolean |No |
| auto_fb_post |array |No |
| fb_comments |boolean |No |

### <a name="variatesettings"></a>Variate_Settings
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| winner_criteria |string |No |
| wait_time |integer |No |
| test_size |integer |No |
| subject_lines |array |No |
| send_times |array |No |
| from_names |array |No |
| reply_to_addresses |array |No |

### <a name="tracking"></a>Rilevamento
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| opens |boolean |No |
| html_clicks |boolean |No |
| text_clicks |boolean |No |
| goal_tracking |boolean |No |
| ecomm360 |boolean |No |
| google_analytics |stringa |No |
| clicktale |string |No |
| salesforce |non definito |No |
| highrise |non definito |No |
| capsule |non definito |No |

### <a name="rssopts"></a>RSS_Opts
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| feed_url |stringa |No |
| frequency |stringa |No |
| constrain_rss_img |string |No |
| schedule |non definito |No |

### <a name="socialcard"></a>Social_Card
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| image_url |stringa |No |
| description |stringa |No |
| title |string |No |

### <a name="segmentopts"></a>Segment_Opts
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| saved_segment_id |integer |No |
| match |stringa |No |

### <a name="salesforce"></a>Salesforce
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| campaign |boolean |No |
| di HDInsight |boolean |No |

### <a name="highrise"></a>Highrise
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| campaign |boolean |No |
| di HDInsight |boolean |No |

### <a name="capsule"></a>Capsule
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| di HDInsight |boolean |No |

### <a name="schedule"></a>Pianificazione
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| hour |integer |No |
| daily_send |non definito |No |
| weekly_send_day |string |No |
| monthly_send_date |number |No |

### <a name="dailysend"></a>Daily_Send
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| sunday |boolean |No |
| monday |boolean |No |
| tuesday |boolean |No |
| wednesday |boolean |No |
| thursday |boolean |No |
| friday |boolean |No |
| saturday |boolean |No |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |No |
| type |stringa |No |
| create_time |stringa |No |
| archive_url |stringa |No |
| status |stringa |No |
| emails_sent |integer |No |
| send_time |stringa |No |
| content_type |stringa |No |
| recipient |array |No |
| Scheda Impostazioni |non definito |No |
| variate_settings |non definito |No |
| tracking |non definito |No |
| rss_opts |non definito |No |
| ab_split_opts |non definito |No |
| social_card |non definito |No |
| report_summary |non definito |No |
| delivery_status |non definito |No |
| _links |array |No |

### <a name="absplitopts"></a>AB_Split_Opts
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| split_test |string |No |
| pick_winner |string |No |
| wait_units |stringa |No |
| wait_time |integer |No |
| split_size |integer |No |
| from_name_a |stringa |No |
| from_name_b |stringa |No |
| reply_email_a |string |No |
| reply_email_b |stringa |No |
| subject_a |string |No |
| subject_b |stringa |No |
| send_time_a |stringa |No |
| send_time_b |stringa |No |
| send_time_winner |stringa |No |

### <a name="reportsummary"></a>Report_Summary
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| opens |integer |No |
| unique_opens |integer |No |
| open_rate |number |No |
| clicks |integer |No |
| subscriber_clicks |number |No |
| click_rate |number |No |

### <a name="deliverystatus"></a>Delivery_Status
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| enabled |boolean |No |
| can_cancel |boolean |No |
| status |stringa |No |
| emails_sent |integer |No |
| emails_canceled |integer |No |

### <a name="link"></a>Collegamento
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| rel |string |No |
| href |string |No |
| statico |stringa |No |
| targetSchema |stringa |No |
| schema |stringa |No |

### <a name="newlistrequest"></a>NewListRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |Sì |
| contact |non definito |Sì |
| permission_reminder |string |Sì |
| use_archive_bar |boolean |No |
| campaign_defaults |non definito |Sì |
| notify_on_subscribe |stringa |No |
| notify_on_unsubscribe |string |No |
| email_type_option |boolean |Sì |
| visibility |stringa |No |

### <a name="contact"></a>Contatto
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| company |string |Sì |
| address1 |stringa |Sì |
| address2 |string |No |
| city |string |Sì |
| state |string |Sì |
| zip |string |Sì |
| country |stringa |Sì |
| phone |stringa |Sì |

### <a name="campaigndefaults"></a>Campaign_Defaults
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| from_name |stringa |Sì |
| from_email |stringa |Sì |
| subject |string |No |
| Lingua |stringa |Sì |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |Sì |
| name |string |Sì |
| contact |non definito |Sì |
| permission_reminder |string |Sì |
| use_archive_bar |boolean |No |
| campaign_defaults |non definito |Sì |
| notify_on_subscribe |stringa |No |
| notify_on_unsubscribe |string |No |
| date_created |stringa |No |
| list_rating |integer |No |
| email_type_option |boolean |Sì |
| subscribe_url_short |stringa |No |
| subscribe_url_long |string |No |
| beamer_address |string |No |
| visibility |stringa |No |
| modules |array |No |
| Statistiche |non definito |No |
| _links |array |No |

### <a name="stats"></a>Statistiche
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| member_count |integer |No |
| unsubscribe_count |integer |No |
| cleaned_count |integer |No |
| member_count_since_send |integer |No |
| unsubscribe_count_since_send |numero intero |No |
| cleaned_count_since_send |integer |No |
| campaign_count |integer |No |
| campaign_last_sent |integer |No |
| merge_field_count |integer |No |
| avg_sub_rate |number |No |
| avg_unsub_rate |number |No |
| target_sub_rate |number |No |
| open_rate |number |No |
| click_rate |number |No |
| last_sub_date |stringa |No |
| last_unsub_date |string |No |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| lists |array |No |
| total_items |integer |No |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| email_type |stringa |No |
| status |stringa |Sì |
| merge_fields |non definito |No |
| interests |stringa |No |
| Lingua |stringa |No |
| vip |boolean |No |
| location |non definito |No |
| email_address |stringa |Sì |

### <a name="firstandlastname"></a>FirstAndLastName
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| FNAME |string |No |
| LNAME |stringa |No |

### <a name="location"></a>Percorso
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| latitudine |number |No |
| longitudine |number |No |

### <a name="memberresponsemodel"></a>MemberResponseModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |No |
| email_address |string |No |
| unique_email_id |stringa |No |
| email_type |stringa |No |
| status |stringa |No |
| merge_fields |non definito |No |
| interests |stringa |No |
| Statistiche |non definito |No |
| ip_signup |string |No |
| timestamp_signup |stringa |No |
| ip_opt |stringa |No |
| timestamp_opt |string |No |
| member_rating |integer |No |
| last_changed |stringa |No |
| Lingua |stringa |No |
| vip |boolean |No |
| email_client |stringa |No |
| location |non definito |No |
| last_note |non definito |No |
| list_id |string |No |
| _links |array |No |

### <a name="lastnote"></a>Last_Note
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| note_id |integer |No |
| created_at |stringa |No |
| created_by |string |No |
| note |stringa |No |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Membri di |array |No |
| list_id |string |No |
| total_items |integer |No |

### <a name="object"></a>Oggetto
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| email_address |string |No |
| email_type |stringa |No |
| status |stringa |Sì |
| merge_fields |non definito |No |
| interests |stringa |No |
| Lingua |stringa |No |
| vip |boolean |No |
| location |non definito |No |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Membri di |array |No |
| list_id |string |No |
| total_items |integer |No |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |Sì |
| email_address |stringa |Sì |
| unique_email_id |stringa |No |
| email_type |stringa |No |
| status |stringa |No |
| merge_fields |non definito |Sì |
| interests |stringa |No |
| Statistiche |non definito |No |
| ip_signup |string |No |
| timestamp_signup |stringa |No |
| ip_opt |stringa |No |
| timestamp_opt |string |No |
| member_rating |integer |No |
| last_changed |stringa |No |
| Lingua |stringa |No |
| vip |boolean |No |
| email_client |stringa |No |
| location |non definito |No |
| last_note |non definito |No |
| list_id |string |No |
| _links |array |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


