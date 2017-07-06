---
title: 'Creazione di rapporti: reimpostazione password self-service di Azure AD | Documentazione Microsoft'
description: Creazione di rapporti in reimpostazione self-service della password di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4d7f05d626e6f718ca3597dff00715af4055f046
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opzioni di creazione di rapporti per la gestione delle password di Azure AD

Dopo la distribuzione, molte organizzazioni vogliono sapere come o se SSPR è effettivamente in uso. Azure AD fornisce delle funzionalità di rapporti che consentono di rispondere alle domande usando dei rapporti predefiniti e, se si dispone della licenza appropriata, consente di creare delle query personalizzate.

È possibile rispondere alle domande seguenti da rapporti presenti nel [portale di Azure] (https://portal.azure.com/).

> [!NOTE]
> È necessario essere [un amministratore globale](active-directory-assign-admin-roles.md#assign-or-remove-administrator-roles) e si deve acconsentire esplicitamente alla raccolta di questi dati per conto dell'organizzazione, visitando le schede di rapporti o i log di controllo almeno una volta. Fino a quel momento, i dati per l'organizzazione non verranno raccolti

* Quante persone si sono registrate per la reimpostazione delle password?
* Chi ha eseguito la registrazione per la reimpostazione delle password?
* Quali dati vengono registrati?
* Quante persone hanno reimpostato le proprie password negli ultimi sette giorni?
* Quali sono i metodi più comuni usati da utenti o amministratori per reimpostare le proprie password?
* Quali sono i problemi più comuni affrontati da utenti o amministratori quando provano a usare la reimpostazione delle password?
* Con quale frequenza gli amministratori reimpostano le proprie password?
* Sono state rilevate eventuali attività sospette nell'ambito della reimpostazione delle password?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Come visualizzare i rapporti di gestione delle password nel portale di Azure

Nel portale di Azure offriamo un modo migliore per visualizzare la reimpostazione delle password e l'attività di registrazione della reimpostazione delle password.  Attenersi alla procedura seguente per trovare gli eventi correlati alla reimpostazione delle password e alla registrazione della reimpostazione delle password:

1. Passare all'indirizzo [**portal.azure.com**](https://portal.azure.com)
2. Fare clic sul menu **Altri servizi** nella parte sinistra della pagina principale del portale di Azure
3. Cercare **Azure Active Directory** nell'elenco dei servizi e selezionarlo.
4. Fare clic su **Utenti e gruppi** dal menu di navigazione di Azure Active Directory
5. Fare clic sull'elemento di navigazione **Log di controllo** dal menu di navigazione Utenti e gruppi. Vengono visualizzati tutti gli eventi di controllo che si verificano per tutti gli utenti nella directory. È possibile filtrare la visualizzazione per vedere anche tutti gli eventi correlati alle password.
6. Per filtrare questa visualizzazione esclusivamente sugli eventi correlati alla reimpostazione delle password, fare clic sul pulsante **Filtra** nella parte superiore del pannello.
7. Dal menu **Filtra** selezionare l'elenco a discesa **Categoria** e impostare l'elemento su **Self-service Password Management** (Gestione delle password self-service).
8. Facoltativamente, è possibile filtrare ulteriormente l'elenco scegliendo l'**Attività** specifica di interesse.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Come recuperare eventi di gestione delle password dall'API relativa a report ed eventi di Azure AD

L'API di creazione di rapporti ed eventi di Azure AD supporta il recupero di tutte le informazioni incluse nei rapporti di reimpostazione della password e di registrazione per la reimpostazione della password. Tramite questa API è possibile scaricare eventi singoli di reimpostazione della password e di registrazione per la reimpostazione della password per l'integrazione con la tecnologia di creazione di rapporti in uso.

### <a name="how-to-get-started-with-the-reporting-api"></a>Come iniziare a usare l'API di creazione report

Per accedere a questi dati, è necessario scrivere una piccola applicazione o uno script per recuperarli dal server. [Introduzione all'API di creazione report di Azure AD](active-directory-reporting-api-getting-started.md).

Dopo aver creato uno script di lavoro, è opportuno esaminare gli eventi di registrazione e di reimpostazione password che è possibile recuperare per soddisfare i requisiti degli scenari in uso.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): elenca le colonne disponibili per gli eventi di reimpostazione della password
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): elenca le colonne disponibili per gli eventi di registrazione per la reimpostazione della password

### <a name="reporting-api-data-retrieval-limitations"></a>Segnalazione dei limiti di recupero dati API

Attualmente, l'API di creazione di report ed eventi di Azure AD recupera fino a **75.000 eventi singoli** del tipo [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent), coprendo gli **ultimi 30 giorni**.

Se è necessario recuperare o archiviare dati oltre questo intervallo, è consigliabile salvarli in modo permanente in un database esterno e usare l'API per eseguire una query sui delta risultanti. Un consiglio è quello di avviare il recupero dei dati quando si inizia a usare la reimpostazione della password in modalità self-service all'interno dell'organizzazione, archiviare i dati esternamente e quindi continuare a monitorare i delta da questo punto in poi.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Come scaricare rapidamente gli eventi di registrazione per la reimpostazione della password con PowerShell

Oltre a usare l'API di creazione di report ed eventi direttamente, è anche possibile impiegare lo script di PowerShell seguente per eventi di registrazione recenti nella directory. Ciò risulta utile nel caso in cui si desidera vedere chi si è registrato di recente o per verificare che l'implementazione della reimpostazione della password stia avendo luogo come previsto.

* [Script di PowerShell per le attività di registrazione della reimpostazione della password self-service di Azure AD](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

### <a name="description-of-report-columns-in-azure-portal"></a>Descrizione delle colonne del rapporto nel Portale di Azure

L'elenco seguente descrive le colonne del report in modo dettagliato:

* **Utente** : l'utente che ha provato a eseguire un'operazione di registrazione per la reimpostazione delle password.
* **Ruolo** : il ruolo dell'utente nella directory.
* **Data e ora** : la data e l'ora del tentativo.
* **Dati registrati** : i dati di autenticazione specificati dall'utente durante la registrazione per la reimpostazione delle password.

### <a name="description-of-report-values-in-azure-portal"></a>Descrizione dei valori del rapporto nel Portale di Azure

La tabella seguente descrive i valori consentiti per ogni colonna:

| Colonna | Valori consentiti e relativi significati |
| --- | --- |
| Dati registrati |**Indirizzo di posta elettronica alternativo**: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<p><p>**Telefono ufficio**: l'utente ha usato il telefono dell'ufficio per autenticarsi<p>**Telefono cellulare**: l'utente ha usato il telefono cellulare o di autenticazione per autenticarsi<p>**Domande di sicurezza**: l'utente ha usato le domande di sicurezza per autenticarsi<p>**Qualsiasi combinazione delle precedenti (ad esempio, indirizzo di posta elettronica alternativo + cellulare)** – si verifica quando vengono specificati 2 diversi criteri che indicano i due metodi usati dall'utente nell'autenticazione per richiedere la reimpostazione della password. |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>Visualizzare le attività di reimpostazione delle password nel portale classico

Questo report illustra tutti i tentativi di reimpostazione delle password che si sono verificati nell'organizzazione.

* **Intervallo di tempo massimo**: 30 giorni
* **Numero massimo di righe**: 75.000
* **Scaricabile**: Sì, tramite file CSV

### <a name="description-of-report-columns-in-azure-classic-portal"></a>Descrizione delle colonne del rapporto nel Portale di Azure classico

L'elenco seguente descrive le colonne del report in modo dettagliato:

1. **Utente** : l'utente che ha provato a eseguire un'operazione di reimpostazione della password (in base al campo ID utente specificato quando l'utente accede per reimpostare una password).
2. **Ruolo** : il ruolo dell'utente nella directory.
3. **Data e ora** : la data e l'ora del tentativo.
4. **Metodi usati** - i metodi di autenticazione usati dall'utente per questa operazione di reimpostazione.
5. **Risultato** - il risultato dell'operazione di reimpostazione della password.
6. **Dettagli** : i dettagli del motivo per cui la reimpostazione della password ha restituito un determinato valore.  Sono inoltre inclusi eventuali passaggi che è possibile eseguire per risolvere un errore imprevisto.

### <a name="description-of-report-values-in-azure-classic-portal"></a>Descrizione dei valori del rapporto nel Portale di Azure classico

La tabella seguente descrive i valori consentiti per ogni colonna:

| Colonna | Valori consentiti e relativi significati |
| --- | --- |
| Metodi usati |**Indirizzo di posta elettronica alternativo**: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<p>**Telefono ufficio**: l'utente ha usato il telefono dell'ufficio per autenticarsi<p>**Telefono cellulare**: l'utente ha usato il telefono cellulare o di autenticazione per autenticarsi<p>**Domande di sicurezza**: l'utente ha usato le domande di sicurezza per autenticarsi<p>**Qualsiasi combinazione delle precedenti (ad esempio, indirizzo di posta elettronica alternativo + cellulare)** – si verifica quando vengono specificati 2 diversi criteri che indicano i due metodi usati dall'utente nell'autenticazione per richiedere la reimpostazione della password. |
| Risultato |**Operazione abbandonata**: l'utente ha iniziato l'operazione di reimpostazione delle password, ma l'ha interrotta senza completarla<p>**Bloccato** - all'account utente è stato impedito l'uso della reimpostazione delle password per aver provato a usare la pagina di reimpostazione delle password o un singolo controllo di reimpostazione delle password troppe volte in un periodo di 24 ore<p>**Operazione annullata** - l'utente ha iniziato l'operazione di reimpostazione delle password, ma poi ha fatto clic sul pulsante Annulla per annullare la sessione mentre era in corso <p>**Amministratore contattato**: l'utente ha riscontrato un problema durante la sessione che non è riuscito a risolvere e ha quindi selezionato il collegamento "Contattare l'amministratore" invece di portare a termine il flusso di reimpostazione delle password<p>**Operazione non riuscita** - l'utente non è stato in grado di reimpostare una password, probabilmente perché non è stato configurato per usare questa funzionalità (ad esempio, non dispone di licenza, non sono presenti informazioni di autenticazione, la password è gestita in locale ma il writeback non è attivo).<p>**Operazione riuscita** – la reimpostazione della password è stata eseguita correttamente. |
| Dettagli |Vedere la tabella seguente |

### <a name="allowed-values-for-details-column"></a>Valori consentiti per la colonna dettagli

Di seguito è riportato l'elenco dei tipi di risultati previsti quando si usa il report relativo alle attività per la reimpostazione delle password:

| Dettagli | Tipo di risultato |
| --- | --- |
| L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite posta elettronica. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite SMS al numero di cellulare. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite chiamata vocale al numero di cellulare. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite risposta alle domande di sicurezza. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere immesso il proprio ID utente. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite posta elettronica. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite SMS al numero di cellulare. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite chiamata vocale al numero di cellulare. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio. |Operazione abbandonata |
| L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite risposta alle domande di sicurezza. |Operazione abbandonata |
| L'utente ha abbandonato prima di selezionare una nuova password. |Operazione abbandonata |
| L'utente ha abbandonato durante la selezione di una nuova password. |Operazione abbandonata |
| L'utente ha immesso troppi codici di verifica SMS non validi a e rimarrà bloccato per 24 ore. |Bloccato |
| L'utente ha superato il numero di tentativi consentiti per la verifica tramite chiamata vocale al telefono cellulare e rimarrà bloccato per 24 ore. |Bloccato |
| L'utente ha superato il numero di tentativi consentiti per la verifica tramite chiamata vocale al telefono dell'ufficio e rimarrà bloccato per 24 ore. |Bloccato |
| L'utente ha superato il numero di tentativi consentiti per le risposte alle domande di sicurezza e rimarrà bloccato per 24 ore. |Bloccato |
| L'utente ha superato il numero di tentativi consentiti per la verifica del numero di telefono e rimarrà bloccato per 24 ore. |Bloccato |
| L'utente ha annullato prima di passare i metodi di autenticazione necessari |Canceled |
| L'utente ha annullato prima di inviare una nuova password |Canceled |
| L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite posta elettronica. |Amministratore contattato |
| L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite SMS al numero di cellulare. |Amministratore contattato |
| L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite chiamata vocale al numero di cellulare. |Amministratore contattato |
| L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio. |Amministratore contattato |
| L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica della domanda di sicurezza. |Amministratore contattato |
| La reimpostazione della password non è abilitata per questo utente. Per risolvere il problema, abilitare la reimpostazione della password nella scheda Configura. |Operazione non riuscita |
| All'utente non sono assegnate licenze. Per risolvere il problema, assegnare una licenza all'utente. |Operazione non riuscita |
| L'utente ha tentato di reimpostare la password da un dispositivo senza i cookie abilitati. |Operazione non riuscita |
| Il numero di metodi di autenticazione definito per l'account dell'utente non è sufficiente. Per risolvere il problema, aggiungere le informazioni di autenticazione. |Operazione non riuscita |
| La password dell'utente è gestita in locale. Per risolvere il problema, è possibile abilitare il writeback delle password. |Operazione non riuscita |
| Non è stato possibile raggiungere il servizio di reimpostazione della password locale. Verificare il log eventi del computer di sincronizzazione. |Operazione non riuscita |
| Si è verificato un problema durante la reimpostazione della password locale dell'utente. Verificare il log eventi del computer di sincronizzazione. |Operazione non riuscita |
| Questo utente non è un membro del gruppo di utenti autorizzati alla reimpostazione della password. Per risolvere il problema, aggiungere l'utente al gruppo. |Operazione non riuscita |
| La reimpostazione della password è stata completamente disabilitata per questo tenant. Vedere [qui](http://aka.ms/ssprtroubleshoot) per risolvere il problema. |Operazione non riuscita |
| La reimpostazione della password dell'utente è riuscita. |Operazione completata |

## <a name="self-service-password-management-activity-types"></a>Tipi di attività di gestione delle password self-service

Vengono visualizzati i seguenti tipi di attività nella categoria degli eventi di controllo **Self-Service Password Management** (Gestione delle password self-service).  Segue una descrizione per ogni tipo.

* [**Bloccato dalla reimpostazione self-service delle password**](#activity-type-blocked-from-self-service-password-reset): indica che un utente ha tentato di reimpostare una password, usato un'attività di controllo specifica o convalidato un numero di telefono più di 5 volte in 24 ore.
* [**Modificare la password (self-service)**](#activity-type-change-password-self-service): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* [**Reimpostare la password (amministratore)**](#activity-type-reset-password-by-admin): indica che un amministratore ha eseguito una reimpostazione della password per conto di un utente dal portale di Azure.
* [**Reimpostare la password (self-service)**](#activity-type-reset-password-self-service): indica che un utente ha reimpostato correttamente la propria password nel [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* [**Stato di avanzamento dell'attività di reimpostazione della password self-service**](#activity-type-self-serve-password-reset-flow-activity-progress): indica ogni passaggio specifico seguito da un utente (come il passaggio dell'attività di controllo dell'autenticazione per la reimpostazione della password) come parte del processo di reimpostazione della password.
* [**Sbloccare l'account utente (self-service)**](#activity-type-unlock-user-account-self-service): indica che un utente ha sbloccato il proprio account Active Directory senza reimpostare la password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità AD account unlock without reset (Sblocco dell'account AD senza reimpostazione).
* [**Utente registrato per la reimpostazione della password self-service**](#activity-type-user-registered-for-self-service-password-reset): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo di attività: Bloccato dalla reimpostazione self-service delle password

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha tentato di reimpostare una password, usato un'attività di controllo specifica o convalidato un numero di telefono più di 5 volte in 24 ore.
* **Activity Actor** (Attore attività): l'utente su cui è stata imposta una limitazione per ulteriori operazioni di reimpostazione. Può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente su cui è stata imposta una limitazione per ulteriori operazioni di reimpostazione. Può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite)
  * _Success_ (Operazione riuscita): indica che un utente è stato limitato dall'esecuzione di qualsiasi reimpostazione, tentativo di metodi di autenticazione alternativi o convalida di numeri di telefono aggiuntivi per le 24 ore successive.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività): non applicabile

### <a name="activity-type-change-password-self-service"></a>Tipo di attività: Modificare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* **Activity Actor** (Attore attività): l'utente che ha modificato la propria password. Può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha modificato la propria password. Può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite)
  * _Success_ (Operazione riuscita): indica che un utente ha modificato correttamente la propria password.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a modificare correttamente la propria password. Facendo clic sulla riga è possibile visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** - (Motivo dell'errore sullo stato dell'attività) 
  * _FuzzyPolicyViolationInvalidPassword_: l'utente ha selezionato una password automaticamente esclusa dalle funzionalità di rilevamento delle password da escludere di Microsoft, che hanno riscontrato una password troppo comune o particolarmente debole.

### <a name="activity-type-reset-password-by-admin"></a>Tipo di attività: Reimpostare la password (amministratore)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un amministratore ha eseguito una reimpostazione della password per conto di un utente dal portale di Azure.
* **Activity Actor** (Attore attività): l'amministratore che ha eseguito la reimpostazione della password per conto di un altro utente finale o amministratore. Deve essere un amministratore globale, un amministratore di password, un amministratore utenti o un amministratore supporto tecnico.
* **Activity Target** (Destinatario attività): l'utente la cui password è stata reimpostata. Può essere un utente finale o un altro amministratore.
* **Allowed Activity Statuses** (Stati attività consentite)
  * _Success_ (Operazione riuscita): indica che un amministratore ha reimpostato correttamente la password dell'utente.
  * _Failure_ (Operazione non riuscita): indica che un amministratore non è riuscito a modificare correttamente la password di un utente. Facendo clic sulla riga è possibile visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-reset-password-self-service"></a>Tipo di attività: Reimpostare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha reimpostato correttamente la propria password nel [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* **Activity Actor** (Attore attività): l'utente che ha reimpostato la propria password. Può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha reimpostato la propria password. Può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite)
  * _Success_ (Operazione riuscita): indica che un utente ha reimpostato correttamente la propria password.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a reimpostare correttamente la propria password. Facendo clic sulla riga è possibile visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** - (Motivo dell'errore sullo stato dell'attività)
  * _FuzzyPolicyViolationInvalidPassword_: l'amministratore ha selezionato una password automaticamente esclusa dalle funzionalità di rilevamento delle password da escludere di Microsoft, che hanno riscontrato una password troppo comune o particolarmente debole.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo di attività: Stato di avanzamento dell'attività di reimpostazione della password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica ogni passaggio specifico seguito da un utente (come il passaggio dell'attività di controllo dell'autenticazione per la reimpostazione della password) come parte del processo di reimpostazione della password.
* **Activity Actor** (Attore attività): l'utente che ha eseguito parte della password del flusso di reimpostazione della password. Può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha eseguito parte della password del flusso di reimpostazione della password. Può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite)
  * _Success_ (Operazione riuscita): indica che un utente ha completato un passaggio specifico del flusso di reimpostazione della password.
  * _Failure_ (Operazione non riuscita): indica che un passaggio specifico del flusso di reimpostazione della password non è riuscito. Facendo clic sulla riga è possibile visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Allowed Activity Status Reasons** (Motivi degli stati attività consentite)
  * Vedere la tabella seguente per [tutti i motivi degli stati relativi alle attività di reimpostazione consentite](#allowed-values-for-details-column).

### <a name="activity-type-unlock-user-account-self-service"></a>Tipo di attività: Sbloccare l'account utente (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha sbloccato il proprio account Active Directory senza reimpostare la password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità AD account unlock without reset (Sblocco dell'account AD senza reimpostazione).
* **Activity Actor** (Attore attività): l'utente che ha sbloccato il proprio account senza reimpostare la password. Può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha sbloccato il proprio account senza reimpostare la password. Può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite)
  * _Success_ (Operazione riuscita): indica che un utente ha sbloccato correttamente il proprio account.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a sbloccare correttamente il proprio account. Facendo clic sulla riga è possibile visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo di attività: Utente registrato per la reimpostazione della password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati. 
* **Activity Actor** (Attore attività): l'utente che ha eseguito la registrazione per la reimpostazione della password. Può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha eseguito la registrazione per la reimpostazione della password. Può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite)
  * _Success_ (Operazione riuscita): indica che un utente è riuscito a eseguire la registrazione per la reimpostazione della password in base ai criteri correnti. 
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a eseguire la registrazione per la reimpostazione della password. Facendo clic sulla riga è possibile visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore. Nota: questo non significa che l'utente non sia in grado di reimpostare la propria password, ma solo che non ha completato il processo di registrazione. Se sull'account dell'utente sono presenti dati non verificati e corretti, ad esempio un numero di telefono non convalidato, anche se non si verifica questo numero di telefono, l'utente può comunque usarlo per reimpostare la password. Per altre informazioni, vedere [Cosa accade quando un utente si registra?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione delle password con Azure AD

* [Collegamento ai log di controllo di gestione dell'utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit) - andare direttamente ai log di controllo di gestione dell'utente del tenant
* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - iniziare a usare la gestione self-service delle password di Azure AD 
* [**Licenze**](active-directory-passwords-licensing.md) - configurare le licenze di Azure AD
* [**Dati** ](active-directory-passwords-data.md) - informazioni sui dati necessari e su come vengono usati per la gestione delle password
* [**Implementazione**](active-directory-passwords-best-practices.md) - pianificare e distribuire agli utenti la reimpostazione password self-service usando le istruzioni disponibili in questo articolo
* [**Personalizzazione**](active-directory-passwords-customize.md) - personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md) - approfondimento sul funzionamento
* [**Domande frequenti**](active-directory-passwords-faq.md) - come? Perché? Cosa? Dove? Chi? Quando? - Risposte alle domande che si sarebbero sempre volute porre
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md) - informazioni su come risolvere i problemi comuni con la reimpostazione password self-service
* [**Criteri**](active-directory-passwords-policy.md) - comprendere e impostare i criteri password di Azure AD

