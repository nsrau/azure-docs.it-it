<properties
	pageTitle="Come ottenere informazioni dettagliate con i report di gestione delle password di Azure AD | Microsoft Azure"
	description="Questo articolo descrive come usare i report per ottenere informazioni dettagliate sulle operazioni di gestione delle password nell'organizzazione."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="asteen"/>

# Come ottenere informazioni dettagliate con i report di gestione delle password

> [AZURE.IMPORTANT] **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).

Questa sezione descrive come è possibile usare i report di gestione delle password di Azure Active Directory per visualizzare come gli utenti usano la modifica e la reimpostazione delle password nell'organizzazione.

- [**Informazioni generali sui report di gestione delle password**](#overview-of-password-management-reports)
- [**Come visualizzare i report di gestione delle password**](#how-to-view-password-management-reports)
- [**Visualizzare le attività di registrazione per la reimpostazione delle password nell'organizzazione**](#view-password-reset-registration-activity)
- [**Visualizzare le attività di reimpostazione delle password nell'organizzazione**](#view-password-reset-activity)

## Informazioni generali sui report di gestione delle password
Dopo aver distribuito la funzionalità di reimpostazione delle password, uno dei passaggi successivi più comuni consiste nel verificarne l'uso nell'organizzazione. Ad esempio, è possibile ottenere informazioni dettagliate sulla modalità di registrazione degli utenti alla funzionalità di reimpostazione delle password o sul numero di reimpostazioni delle password eseguite negli ultimi giorni. Di seguito sono elencate alcune delle domande più comuni a cui sarà possibile rispondere con i report di gestione delle password presenti nel [portale di gestione di Azure](https://manage.windowsazure.com):

- Quante persone si sono registrate per la reimpostazione delle password?
- Chi ha eseguito la registrazione per la reimpostazione delle password?
- Quali dati vengono registrati?
- Quante persone hanno reimpostato le proprie password negli ultimi 7 giorni?
- Quali sono i metodi più comuni usati da utenti o amministratori per reimpostare le proprie password?
- Quali sono i problemi più comuni affrontati da utenti o amministratori quando provano a usare la reimpostazione delle password?
- Con quale frequenza gli amministratori reimpostano le proprie password?
- Sono state rilevate eventuali attività sospette nell'ambito della reimpostazione delle password?


## Come visualizzare i report di gestione delle password
Per trovare i report di gestione delle password, seguire questa procedura:

1.	Fare clic sull'estensione **Active Directory** nel [portale di gestione di Azure](https://manage.windowsazure.com).
2.	Selezionare la directory dall'elenco visualizzato nel portale.
3.	Fare clic sulla scheda **Report**.
4.	Controllare la sezione **Log attività**.
5.	Selezionare il report **Attività di reimpostazione password** o **Attività di registrazione reimpostazione password**.

    ![][001]

## Come accedere ai report di gestione delle password da un'API
A partire da agosto 2015, Report ed eventi di Azure AD supporta il recupero di tutte le informazioni incluse nei report di reimpostazione della password e di registrazione per la reimpostazione della password.

Per accedere a questi dati, è necessario scrivere una piccola applicazione o uno script per recuperarli dal server. [Introduzione all'API di creazione report di Azure AD](active-directory-reporting-api-getting-started.md).

Dopo aver creato uno script di lavoro, è opportuno esaminare gli eventi di registrazione e di reimpostazione password che è possibile recuperare per soddisfare i requisiti degli scenari in uso.

- [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): elenca le colonne disponibili per gli eventi di reimpostazione della password
- [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): elenca le colonne disponibili per gli eventi di registrazione per la reimpostazione della password

## Visualizzare le attività di registrazione per la reimpostazione delle password nell'organizzazione

Il report relativo alle attività di registrazione per la reimpostazione delle password illustra tutte le registrazioni per la reimpostazione delle password che si sono verificate nell'organizzazione. Questo report consente di visualizzare le registrazioni per la reimpostazione delle password di tutti gli utenti che hanno registrato correttamente le informazioni di autenticazione nel portale di registrazione per la reimpostazione delle password ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Intervallo di tempo massimo**: 1 mese
- **Numero massimo di righe**: illimitato
- **Scaricabile**: Sì, tramite file CSV

    ![][002]

### Descrizione delle colonne del report
L'elenco seguente descrive le colonne del report in modo dettagliato:

- **Utente**: l'utente che ha provato a eseguire un'operazione di registrazione per la reimpostazione delle password.
- **Ruolo**: il ruolo dell'utente nella directory.
- **Data e ora**: la data e l'ora del tentativo.
- **Dati registrati**: i dati di autenticazione specificati dall'utente durante la registrazione per la reimpostazione delle password.

### Descrizione dei valori del report
La tabella seguente descrive i valori consentiti per ogni colonna:

Colonna|Valori consentiti e relativi significati
---|---
Dati registrati| **Indirizzo di posta elettronica alternativo** – l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<p><p>** Telefono ufficio** – l'utente ha usato il telefono ufficio per autenticarsi<p>**Cellulare** – l'utente ha usato un numero di cellulare per autenticarsi<p>**Domande di sicurezza** – domande di sicurezza usate dall'utente per l'autenticazione<p>**Qualsiasi combinazione delle precedenti (ad esempio posta elettronica alternativo + cellulare)** – si verifica quando vengono specificati 2 diversi criteri e indica i due metodi usati dall'utente nell'autenticazione per richiedere la reimpostazione della password.

## Visualizzare le attività di reimpostazione delle password nell'organizzazione

Questo report illustra tutti i tentativi di reimpostazione delle password che si sono verificati nell'organizzazione.

- **Intervallo di tempo massimo**: 1 mese
- **Numero massimo di righe**: illimitato
- **Scaricabile**: Sì, tramite file CSV

    ![][003]

### Descrizione delle colonne del report
L'elenco seguente descrive le colonne del report in modo dettagliato:

1. **Utente**: l'utente che ha provato a eseguire un'operazione di reimpostazione della password (in base al campo ID utente specificato quando l'utente accede per reimpostare una password).
2. **Ruolo**: il ruolo dell'utente nella directory.
3. **Data e ora**: la data e l'ora del tentativo.
4. **Metodi usati**: i metodi di autenticazione usati dall'utente per questa operazione di reimpostazione.
5. **Risultato**: il risultato finale dell'operazione di reimpostazione della password.
6. **Dettagli**: i dettagli del motivo per cui la reimpostazione della password ha restituito un determinato valore. Sono inoltre inclusi eventuali passaggi che è possibile eseguire per risolvere un errore imprevisto.

### Descrizione dei valori del report
La tabella seguente descrive i valori consentiti per ogni colonna:

Colonna|Valori consentiti e relativi significati
---|---
Metodi usati|**Indirizzo di posta elettronica alternativo** – l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi<p>**Telefono ufficio** – l'utente ha usato il telefono ufficio per autenticarsi<p>**Cellulare** – l'utente ha usato un numero di cellulare per autenticarsi<p>**Domande di sicurezza** – domande di sicurezza usate dall'utente per l'autenticazione<p>**Qualsiasi combinazione delle precedenti (ad esempio posta elettronica alternativo + cellulare)** – si verifica quando vengono specificati 2 diversi criteri e indica i due metodi usati dall'utente nell'autenticazione per richiedere la reimpostazione della password.
Risultato|**Operazione abbandonata** – l'utente ha iniziato l'operazione di reimpostazione della password, ma l'ha interrotta senza completarla<p>**Operazione bloccata** – è stato impedito all'account utente di usare la pagina di reimpostazione della password poiché ha tentato troppe volte di usare tale pagina o un singolo criterio di reimpostazione password in 24 ore<p>**Operazione annullata** – l'utente ha avviato la reimpostazione della password, ma ha poi annullato la sessione tramite il pulsante Annulla<p>**È stato contattato l'amministratore** – l'utente ha riscontrato problemi durante la sessione che non è stato in grado di risolvere e ha contattato l'amministratore tramite il collegamento "Contatta l'amministratore" invece di terminare la sessione di reimpostazione della password<p>**Operazione non riuscita** – l'utente non è riuscito a reimpostare la password, probabilmente perché non era configurato per usare tale funzionalità (ad esempio mancanza di licenza o di informazioni di autenticazione, gestione della password locale ma senza writeback).<p>**Operazione riuscita** – la reimpostazione della password è stata eseguita correttamente.
Dettagli|Vedere la tabella seguente

### Valori consentiti per la colonna dettagli
Di seguito è riportato l'elenco dei tipi di risultati previsti quando si usa il report relativo alle attività per la reimpostazione delle password:

Dettagli | Tipo di risultato
----|----
L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite posta elettronica. | Operazione abbandonata
L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite SMS al numero di cellulare.|Operazione abbandonata
L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite chiamata vocale al numero di cellulare. | Operazione abbandonata
L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio. | Operazione abbandonata
L'utente ha abbandonato dopo avere completato l'opzione di verifica tramite risposta alle domande di sicurezza.|Operazione abbandonata
L'utente ha abbandonato dopo avere immesso il proprio ID utente.| Operazione abbandonata
L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite posta elettronica.|Operazione abbandonata
L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite SMS al numero di cellulare.|Operazione abbandonata
L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite chiamata vocale al numero di cellulare.|Operazione abbandonata
L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio.|Operazione abbandonata
L'utente ha abbandonato dopo avere avviato l'opzione di verifica tramite risposta alle domande di sicurezza.| Operazione abbandonata
L'utente ha abbandonato prima di selezionare una nuova password.| Operazione abbandonata
L'utente ha abbandonato durante la selezione di una nuova password.| Operazione abbandonata
L'utente ha immesso troppi codici di verifica SMS non validi a e rimarrà bloccato per 24 ore.|Bloccato
L'utente ha superato il numero di tentativi consentiti per la verifica tramite chiamata vocale al telefono cellulare e rimarrà bloccato per 24 ore.|Bloccato
L'utente ha superato il numero di tentativi consentiti per la verifica tramite chiamata vocale al telefono dell'ufficio e rimarrà bloccato per 24 ore. |Bloccato
L'utente ha superato il numero di tentativi consentiti per le risposte alle domande di sicurezza e rimarrà bloccato per 24 ore.| Bloccato
L'utente ha superato il numero di tentativi consentiti per la verifica del numero di telefono e rimarrà bloccato per 24 ore.|Bloccato
L'utente ha annullato prima di passare i metodi di autenticazione necessari.|Operazione annullata
L'utente ha annullato prima di inviare una nuova password.|Operazione annullata
L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite posta elettronica. |Amministratore contattato
L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite SMS al numero di cellulare.|Amministratore contattato
L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite chiamata vocale al numero di cellulare.|Amministratore contattato
L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica tramite chiamata vocale al numero dell'ufficio. |Amministratore contattato
L'utente ha contattato un amministratore dopo aver tentato l'opzione di verifica della domanda di sicurezza.|Amministratore contattato
La reimpostazione della password non è abilitata per questo utente. Per risolvere il problema, abilitare la reimpostazione della password nella scheda Configura.| Operazione non riuscita
All'utente non sono assegnate licenze. Per risolvere il problema, assegnare una licenza all'utente.|Operazione non riuscita
L'utente ha tentato di reimpostare la password da un dispositivo senza i cookie abilitati.| Operazione non riuscita
Il numero di metodi di autenticazione definito per l'account dell'utente non è sufficiente. Per risolvere il problema, aggiungere le informazioni di autenticazione.|Operazione non riuscita
La password dell'utente è gestita in locale. Per risolvere il problema, è possibile abilitare il writeback delle password.|Operazione non riuscita
Non è stato possibile raggiungere il servizio di reimpostazione della password locale. Verificare il log eventi del computer di sincronizzazione.|Operazione non riuscita
Si è verificato un problema durante la reimpostazione della password locale dell'utente. Verificare il log eventi del computer di sincronizzazione. | Operazione non riuscita
Questo utente non è un membro del gruppo di utenti autorizzati alla reimpostazione della password. Per risolvere il problema, aggiungere l'utente al gruppo.|Operazione non riuscita
La reimpostazione della password è stata completamente disabilitata per questo tenant. Vedere[qui](http://aka.ms/ssprtroubleshoot)per risolvere il problema. | Failed
La reimpostazione della password dell'utente è riuscita.|Succeeded

## Collegamenti alla documentazione relativa alla reimpostazione della password
Di seguito vengono forniti collegamenti a tutte le pagine della documentazione relative alla reimpostazione della password in Azure AD:

* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
* [**Funzionamento**](active-directory-passwords-how-it-works.md): informazioni sui sei diversi componenti del servizio e sulle relative funzioni
* [**Introduzione**](active-directory-passwords-getting-started.md): informazioni su come consentire agli utenti di reimpostare e modificare le password cloud o locali
* [**Personalizzazione**](active-directory-passwords-customize.md): informazioni su come personalizzare l'aspetto e il comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md): informazioni su come distribuire rapidamente e gestire in modo efficace le password nell'organizzazione
* [**Domande frequenti**](active-directory-passwords-faq.md): risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere rapidamente eventuali problemi con il servizio
* [**Ulteriori informazioni**](active-directory-passwords-learn-more.md): approfondimenti sui dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"

<!---HONumber=AcomDC_0713_2016-->