---
title: Come ottenere informazioni dettagliate con i report di gestione delle password di Azure AD | Documentazione Microsoft
description: Questo articolo descrive come usare i report per ottenere informazioni dettagliate sulle operazioni di gestione delle password nell&quot;organizzazione.
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 1472b51d-53f4-4b0f-b1be-57f6fa88fa65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: b010891a177f27eef99bd65e0d940c83375aa5ec
ms.lasthandoff: 04/27/2017


---
# <a name="how-to-get-operational-insights-with-password-management-reports"></a>Come ottenere informazioni dettagliate con i report di gestione delle password
> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account).
>
>

Questa sezione descrive come è possibile usare i report di gestione delle password di Azure Active Directory per visualizzare come gli utenti usano la modifica e la reimpostazione della password nell'organizzazione.

* [**Panoramica dei report di gestione delle password**](#overview-of-password-management-reports)
* [**Come visualizzare i report di gestione delle password nel nuovo portale di Azure**](#how-to-view-password-management-reports)
 * [Ruoli della directory che possono leggere i report](#directory-roles-allowed-to-read-reports)
* [**Tipi di attività di gestione delle password self-service nel nuovo portale di Azure**](#self-service-password-management-activity-types)
 * [Bloccato dalla reimpostazione self-service delle password](#activity-type-blocked-from-self-service-password-reset)
 * [Modificare la password (self-service)](#activity-type-change-password-self-service)
 * [Reimpostare la password (amministratore)](#activity-type-reset-password-by-admin)
 * [Reimpostare la password (self-service)](#activity-type-reset-password-self-service)
 * [Stato di avanzamento dell'attività di reimpostazione della password self-service](#activity-type-self-serve-password-reset-flow-activity-progress)
 * [Sbloccare l'account utente (self-service)](#activity-type-unlock-user-account-self-service)
 * [Utente registrato per la reimpostazione della password self-service](#activity-type-user-registered-for-self-service-password-reset)
* [**Come recuperare eventi di gestione delle password dall'API relativa a report ed eventi di Azure AD**](#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
 * [Segnalazione dei limiti di recupero dati API](#reporting-api-data-retrieval-limitations)
* [**Come scaricare rapidamente gli eventi di registrazione per la reimpostazione della password con PowerShell**](#how-to-download-password-reset-registration-events-quickly-with-powershell)
* [**Come visualizzare i report di gestione delle password nel portale classico**](#how-to-view-password-management-reports-in-the-classic-portal)
* [**Visualizzare le attività di registrazione per la reimpostazione delle password nell'organizzazione nel portale classico**](#view-password-reset-registration-activity-in-the-classic-portal)
* [**Visualizzare le attività di reimpostazione delle password nell'organizzazione nel portale classico**](#view-password-reset-activity-in-the-classic-portal)


## <a name="overview-of-password-management-reports"></a>Informazioni generali sui report di gestione delle password
Dopo aver distribuito la funzionalità di reimpostazione delle password, uno dei passaggi successivi più comuni consiste nel verificarne l'uso nell'organizzazione.  Ad esempio, è possibile ottenere informazioni dettagliate sulla modalità di registrazione degli utenti alla funzionalità di reimpostazione delle password o sul numero di reimpostazioni delle password eseguite negli ultimi giorni.  Di seguito sono elencate alcune domande comuni a cui sarà possibile rispondere con i report di gestione delle password presenti attualmente nel [portale di gestione di Azure](https://manage.windowsazure.com):

* Quante persone si sono registrate per la reimpostazione delle password?
* Chi ha eseguito la registrazione per la reimpostazione delle password?
* Quali dati vengono registrati?
* Quante persone hanno reimpostato le proprie password negli ultimi 7 giorni?
* Quali sono i metodi più comuni usati da utenti o amministratori per reimpostare le proprie password?
* Quali sono i problemi più comuni affrontati da utenti o amministratori quando provano a usare la reimpostazione delle password?
* Con quale frequenza gli amministratori reimpostano le proprie password?
* Sono state rilevate eventuali attività sospette nell'ambito della reimpostazione delle password?

## <a name="how-to-view-password-management-reports"></a>Come visualizzare i report di gestione delle password
Nel nuovo [portale di Azure](https://portal.azure.com) offriamo un modo migliore per visualizzare la reimpostazione delle password e l'attività di registrazione della reimpostazione delle password.  Attenersi alla procedura seguente per trovare gli eventi correlati alla reimpostazione delle password e alla registrazione della reimpostazione delle password nel nuovo [portale di Azure](https://portal.azure.com):

1. Passare all'indirizzo [**portal.azure.com**](https://portal.azure.com)
2. Fare clic sul menu **Altri servizi** nella parte sinistra della pagina principale del portale di Azure.
3. Cercare **Azure Active Directory** nell'elenco dei servizi e selezionarlo.
4. Fare clic su **Utenti e gruppi** dal menu di navigazione di Azure Active Directory.
5. Fare clic sull'elemento di navigazione **Log di controllo** dal menu di navigazione Utenti e gruppi. Vengono visualizzati tutti gli eventi di controllo che si verificano per tutti gli utenti nella directory. È possibile filtrare la visualizzazione per vedere anche tutti gli eventi correlati alle password.
6. Per filtrare questa visualizzazione esclusivamente sugli eventi correlati alla gestione delle password, fare clic su **Filtra** nella parte superiore del pannello.
7. Dal menu **Filtra** selezionare l'elenco a discesa **Categoria** e impostare l'elemento su **Self-service Password Management** (Gestione delle password self-service).
8. Facoltativamente, è possibile filtrare ulteriormente l'elenco scegliendo l'**Attività** specifica di interesse.
### <a name="direct-link-to-user-audit-blade"></a>Collegamento diretto al pannello di controllo dell'utente
Se è stato eseguito l'accesso al portale, di seguito è riportato un collegamento diretto al pannello di controllo utente in cui è possibile visualizzare questi eventi:

* [Passare direttamente alla visualizzazione di controllo della gestione dell'utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit)

### <a name="directory-roles-allowed-to-read-reports"></a>Ruoli della directory che possono leggere i report
Attualmente, i ruoli della directory seguenti possono leggere i report di gestione delle password di Azure AD nel portale di Azure classico:

* Amministratore globale

Prima di poter leggere questi report, un amministratore globale della società deve aver attivato il recupero di questi dati per conto dell'organizzazione, visitando almeno una volta la scheda relativa ai report o ai log di controllo. Fino a quel momento, i dati per l'organizzazione non verranno raccolti.

Per altre informazioni sui ruoli della directory e sulle operazioni a essi consentite, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles).

## <a name="self-service-password-management-activity-types"></a>Tipi di attività di gestione delle password self-service
Vengono visualizzati i seguenti tipi di attività nella categoria degli eventi di controllo **Self-Service Password Management** (Gestione delle password self-service).  Segue una descrizione per ogni tipo.

* [**Bloccato dalla reimpostazione self-service delle password**](#activity-type-blocked-from-self-service-password-reset): indica che un utente ha tentato di reimpostare una password, usato un'attività di controllo specifica o convalidato un numero di telefono più di 5 volte in 24 ore.
* [**Modificare la password (self-service)**](#activity-type-change-password-self-service): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* [**Reimpostare la password (amministratore)**](#activity-type-reset-password-by-admin): indica che un amministratore ha eseguito una reimpostazione della password per conto di un utente dal portale di Azure.
* [**Reimpostare la password (self-service)**](#activity-type-reset-password-self-service): indica che un utente ha reimpostato correttamente la propria password nel [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* [**Stato di avanzamento dell'attività di reimpostazione della password self-service**](#activity-type-self-serve-password-reset-flow-activity-progress): indica ogni passaggio specifico seguito da un utente (come il passaggio dell'attività di controllo dell'autenticazione per la reimpostazione della password) come parte del processo di reimpostazione della password.
* [**Sbloccare l'account utente (self-service)**](#activity-type-unlock-user-account-self-service): indica che un utente ha sbloccato il proprio account Active Directory senza reimpostare la password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità [AD account unlock without reset](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password) (Sblocco dell'account AD senza reimpostazione).
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

* **Activity Description** (Descrizione attività): indica che un utente ha sbloccato il proprio account Active Directory senza reimpostare la password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità [AD account unlock without reset](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password) (Sblocco dell'account AD senza reimpostazione).
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

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Come recuperare eventi di gestione delle password dall'API relativa a report ed eventi di Azure AD
A partire da agosto 2015, l'API di creazione di report ed eventi di Azure AD supporta il recupero di tutte le informazioni incluse nei report di reimpostazione della password e di registrazione per la reimpostazione della password. Tramite questa API è possibile scaricare eventi singoli di reimpostazione della password e di registrazione per la reimpostazione della password per l'integrazione con la tecnologia di creazione di report in uso.

### <a name="how-to-get-started-with-the-reporting-api"></a>Come iniziare a usare l'API di creazione report
Per accedere a questi dati, è necessario scrivere una piccola applicazione o uno script per recuperarli dal server. [Introduzione all'API di creazione report di Azure AD](active-directory-reporting-api-getting-started.md).

Dopo aver creato uno script di lavoro, è opportuno esaminare gli eventi di registrazione e di reimpostazione password che è possibile recuperare per soddisfare i requisiti degli scenari in uso.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): elenca le colonne disponibili per gli eventi di reimpostazione della password
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): elenca le colonne disponibili per gli eventi di registrazione per la reimpostazione della password

### <a name="reporting-api-data-retrieval-limitations"></a>Segnalazione dei limiti di recupero dati API
Attualmente, l'API di creazione di report ed eventi di Azure AD recupera fino a **75.000 eventi singoli** del tipo [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) e [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent), coprendo gli **ultimi 30 giorni**.

Se è necessario recuperare o archiviare dati oltre questo intervallo, è consigliabile salvarli in modo permanente in un database esterno e usare l'API per eseguire una query sui delta risultanti. Una procedura consigliata consiste nell'avviare il recupero dei dati contemporaneamente al processo di registrazione per la reimpostazione della password all'interno dell'organizzazione, archiviare i dati esternamente e quindi continuare a monitorare i delta da questo punto in poi.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Come scaricare rapidamente gli eventi di registrazione per la reimpostazione della password con PowerShell
Oltre a usare l'API di creazione di report ed eventi direttamente, è anche possibile impiegare lo script di PowerShell seguente per eventi di registrazione recenti nella directory. Ciò risulta utile nel caso in cui si desidera vedere chi si è registrato di recente o per verificare che l'implementazione della reimpostazione della password stia avendo luogo come previsto.

* [Script di PowerShell per le attività di registrazione della reimpostazione della password self-service di Azure AD](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

## <a name="how-to-view-password-management-reports-in-the-classic-portal"></a>Come visualizzare i report di gestione delle password nel portale classico
Per trovare i report di gestione delle password, seguire questa procedura:

1. Fare clic sull'estensione **Active Directory** nel [portale di Azure classico](https://manage.windowsazure.com).
2. Selezionare la directory dall'elenco visualizzato nel portale.
3. Fare clic sulla scheda **Report** .
4. Controllare la sezione **Log attività** .
5. Selezionare il report **Attività di reimpostazione password** o **Attività di registrazione reimpostazione password**.

   ![][001]

## <a name="view-password-reset-registration-activity-in-the-classic-portal"></a>Visualizzare le attività di registrazione per la reimpostazione delle password nel portale classico
Il report relativo alle attività di registrazione per la reimpostazione delle password illustra tutte le registrazioni per la reimpostazione delle password che si sono verificate nell'organizzazione.  Questo report consente di visualizzare le registrazioni per la reimpostazione delle password di tutti gli utenti che hanno registrato correttamente le informazioni di autenticazione nel portale di registrazione per la reimpostazione delle password ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

* **Intervallo di tempo massimo**: 30 giorni
* **Numero massimo di righe**: 75.000
* **Scaricabile**: Sì, tramite file CSV

    ![][002]

### <a name="description-of-report-columns"></a>Descrizione delle colonne del report
L'elenco seguente descrive le colonne del report in modo dettagliato:

* **Utente** : l'utente che ha provato a eseguire un'operazione di registrazione per la reimpostazione delle password.
* **Ruolo** : il ruolo dell'utente nella directory.
* **Data e ora** : la data e l'ora del tentativo.
* **Dati registrati** : i dati di autenticazione specificati dall'utente durante la registrazione per la reimpostazione delle password.

### <a name="description-of-report-values"></a>Descrizione dei valori del report
La tabella seguente descrive i valori consentiti per ogni colonna:

| Colonna | Valori consentiti e relativi significati |
| --- | --- |
| Dati registrati |**Indirizzo di posta elettronica alternativo**: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<p><p>**Telefono ufficio**: l'utente ha usato il telefono dell'ufficio per autenticarsi<p>**Telefono cellulare**: l'utente ha usato il telefono cellulare o di autenticazione per autenticarsi<p>**Domande di sicurezza**: l'utente ha usato le domande di sicurezza per autenticarsi<p>**Qualsiasi combinazione delle precedenti (ad esempio posta elettronica alternativo + cellulare)** – si verifica quando vengono specificati 2 diversi criteri e indica i due metodi usati dall'utente nell'autenticazione per richiedere la reimpostazione della password. |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>Visualizzare le attività di reimpostazione delle password nel portale classico
Questo report illustra tutti i tentativi di reimpostazione delle password che si sono verificati nell'organizzazione.

* **Intervallo di tempo massimo**: 30 giorni
* **Numero massimo di righe**: 75.000
* **Scaricabile**: Sì, tramite file CSV

    ![][003]

### <a name="description-of-report-columns"></a>Descrizione delle colonne del report
L'elenco seguente descrive le colonne del report in modo dettagliato:

1. **Utente** : l'utente che ha provato a eseguire un'operazione di reimpostazione della password (in base al campo ID utente specificato quando l'utente accede per reimpostare una password).
2. **Ruolo** : il ruolo dell'utente nella directory.
3. **Data e ora** : la data e l'ora del tentativo.
4. **Metodi usati** : i metodi di autenticazione usati dall'utente per questa operazione di reimpostazione.
5. **Risultato** : il risultato finale dell'operazione di reimpostazione della password.
6. **Dettagli** : i dettagli del motivo per cui la reimpostazione della password ha restituito un determinato valore.  Sono inoltre inclusi eventuali passaggi che è possibile eseguire per risolvere un errore imprevisto.

### <a name="description-of-report-values"></a>Descrizione dei valori del report
La tabella seguente descrive i valori consentiti per ogni colonna:

| Colonna | Valori consentiti e relativi significati |
| --- | --- |
| Metodi usati |**Indirizzo di posta elettronica alternativo**: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<p>**Telefono ufficio**: l'utente ha usato il telefono dell'ufficio per autenticarsi<p>**Telefono cellulare**: l'utente ha usato il telefono cellulare o di autenticazione per autenticarsi<p>**Domande di sicurezza**: l'utente ha usato le domande di sicurezza per autenticarsi<p>**Qualsiasi combinazione delle precedenti (ad esempio posta elettronica alternativo + cellulare)** – si verifica quando vengono specificati 2 diversi criteri e indica i due metodi usati dall'utente nell'autenticazione per richiedere la reimpostazione della password. |
| Risultato |**Operazione abbandonata**: l'utente ha iniziato l'operazione di reimpostazione delle password, ma l'ha interrotta senza completarla<p>**Bloccato**: all'account utente è stato impedito l'uso della reimpostazione delle password per aver provato a usare la pagina di reimpostazione delle password o un singolo controllo di reimpostazione delle password troppe volte in un periodo di 24 ore<p>**Operazione annullata**: l'utente ha iniziato l'operazione di reimpostazione delle password, ma poi ha fatto clic sul pulsante Annulla per annullare la sessione mentre era in corso <p>**Amministratore contattato**: l'utente ha riscontrato un problema durante la sessione che non è riuscito a risolvere e ha quindi selezionato il collegamento "Contattare l'amministratore" invece di portare a termine il flusso di reimpostazione delle password<p>**Operazione non riuscita**: l'utente non è stato in grado di reimpostare una password, probabilmente perché non è stato configurato per usare questa funzionalità (ad esempio, non dispone di licenza, non sono presenti informazioni di autenticazione, la password è gestita in locale, ma il writeback non è attivo).<p>**Operazione riuscita** – la reimpostazione della password è stata eseguita correttamente. |
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
| L'utente ha annullato prima di passare i metodi di autenticazione necessari. |Operazione annullata |
| L'utente ha annullato prima di inviare una nuova password. |Operazione annullata |
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

## <a name="next-steps"></a>Passaggi successivi
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account).
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Domande frequenti**](active-directory-passwords-faq.md) : risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Altre informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio

[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"

