---
title: Risolvere i problemi di reimpostazione della password self-service - Azure Active Directory
description: Informazioni su come risolvere i problemi comuni e i passaggi di risoluzione per la reimpostazione della password self-service in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e661bd24acbb15ced9f5bb0a0fba7eec51eae06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363539"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Risolvere i problemi di reimpostazione della password self-service in Azure Active Directory

Azure Active Directory (Azure AD) la reimpostazione della password self-service (SSPR) consente agli utenti di reimpostare le password nel cloud.

Se si verificano problemi con SSPR, è possibile che si verifichino i seguenti passaggi di risoluzione dei problemi ed errori comuni. Se non si riesce a trovare la risposta al problema, [i team di supporto sono sempre disponibili](#contact-microsoft-support) per assistenza.

## <a name="sspr-configuration-in-the-azure-portal"></a>Configurazione di SSPR nel portale di Azure

In caso di problemi durante la visualizzazione o la configurazione delle opzioni di SSPR nel portale di Azure, esaminare i passaggi per la risoluzione dei problemi seguenti:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Non viene visualizzata la sezione **Reimpostazione password** in Azure AD nel portale di Azure.

Se non si dispone di una licenza di Azure AD assegnata all'amministratore che esegue l'operazione, non verrà visualizzata l'opzione di menu di **reimpostazione della password** .

Per assegnare una licenza all'account amministratore in questione, attenersi alla procedura per [assegnare, verificare e risolvere i problemi relativi alle licenze](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="i-dont-see-a-particular-configuration-option"></a>Non è possibile visualizzare un'opzione di configurazione specifica.

Molti elementi dell'interfaccia utente sono nascosti fino a quando non sono necessari. Assicurarsi che l'opzione sia abilitata prima di cercare le opzioni di configurazione specifiche.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Non possibile visualizzare la scheda **Integrazione locale**.

Il writeback delle password locale è visibile solo se è stato scaricato Azure AD Connect e la funzionalità è stata configurata.

Per ulteriori informazioni, vedere [Introduzione a Azure ad Connect](../hybrid/how-to-connect-install-express.md).

## <a name="sspr-reporting"></a>Creazione di report SSPR

In caso di problemi con la creazione di report di SSPR nel portale di Azure, esaminare i passaggi per la risoluzione dei problemi seguenti:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>Non viene visualizzato alcun tipo di attività di gestione delle password nella categoria di eventi controllo della **password self-service** .

Questo problema può verificarsi se all'amministratore che esegue l'operazione non è stata assegnata una licenza di Azure AD.

Per assegnare una licenza all'account amministratore in questione, attenersi alla procedura per [assegnare, verificare e risolvere i problemi relativi alle licenze](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="user-registrations-show-multiple-times"></a>Le registrazioni utente vengono visualizzate più volte.

Quando un utente esegue la registrazione, si registra ogni singolo dato registrato come un evento distinto.

Per aggregare i dati e avere più flessibilità nelle modalità di visualizzazione, è possibile scaricare il report e aprire i dati come tabella pivot in Excel.

## <a name="sspr-registration-portal"></a>Portale di registrazione di SSPR

Se gli utenti riscontrano problemi di registrazione per SSPR, vedere i passaggi per la risoluzione dei problemi seguenti:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>La directory non è abilitata per la reimpostazione della password. È possibile che venga visualizzato un errore che segnala che l'amministratore non ha abilitato l'uso di questa funzionalità.

È possibile abilitare SSPR per tutti gli utenti, nessun utente o per gruppi di utenti selezionati. È attualmente possibile abilitare un solo gruppo di Azure AD per la reimpostazione della password self-service usando il portale di Azure. Come parte di una distribuzione più ampia della reimpostazione della password self-service, sono supportati i gruppi annidati. Verificare che agli utenti del gruppo scelto siano assegnate le licenze appropriate.

Nella portale di Azure modificare la configurazione della **reimpostazione della password self-service abilitata** su *selezionato* o *tutti* , quindi selezionare **Salva**.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>L'utente non dispone di una licenza Azure AD assegnata. È possibile che venga visualizzato un errore che segnala che l'amministratore non ha abilitato l'uso di questa funzionalità.

È attualmente possibile abilitare un solo gruppo di Azure AD per la reimpostazione della password self-service usando il portale di Azure. Come parte di una distribuzione più ampia della reimpostazione della password self-service, sono supportati i gruppi annidati. Verificare che agli utenti del gruppo scelto siano assegnate le licenze appropriate. Esaminare il passaggio precedente per la risoluzione dei problemi per abilitare SSPR come richiesto.

Esaminare anche i passaggi per la risoluzione dei problemi per assicurarsi che l'amministratore che esegue le opzioni di configurazione disponga di una licenza assegnata. Per assegnare una licenza all'account amministratore in questione, attenersi alla procedura per [assegnare, verificare e risolvere i problemi relativi alle licenze](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="theres-an-error-processing-the-request"></a>Si è verificato un errore durante l'elaborazione della richiesta.

Gli errori di registrazione SSPR generici possono essere causati da molti problemi, ma in genere questo errore è causato da un'interruzione del servizio o da un problema di configurazione. Se si continua a visualizzare questo errore generico quando si tenta di eseguire il processo di registrazione SSPR, [contattare il supporto tecnico Microsoft](#contact-microsoft-support) per richiedere assistenza aggiuntiva.

## <a name="sspr-usage"></a>Utilizzo di SSPR

Se si riscontrano problemi con SSPR, vedere gli scenari di risoluzione dei problemi e i passaggi di risoluzione seguenti:

| Errore | Soluzione |
| --- | --- |
| La directory non è abilitata per la reimpostazione della password. | Nella portale di Azure modificare la configurazione della **reimpostazione della password self-service abilitata** su *selezionato* o *tutti* , quindi selezionare **Salva**. |
| L'utente non dispone di una licenza Azure AD assegnata. | Questo problema può verificarsi se non si ha una licenza di Azure AD assegnata all'utente desiderato. Per assegnare una licenza all'account amministratore in questione, attenersi alla procedura per [assegnare, verificare e risolvere i problemi relativi alle licenze](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| La directory è abilitata per la reimpostazione della password, ma le informazioni di autenticazione dell'utente sono mancanti o errate. | Verificare che l'utente abbia formato correttamente i dati di contatto per il file nella directory. Per altre informazioni, vedere l'articolo relativo ai [dati usati per la reimpostazione password self-service di Azure AD](howto-sspr-authenticationdata.md). |
| La directory è abilitata per la reimpostazione della password, ma per l'utente è archiviato un solo metodo di contatto mentre i criteri sono impostati in modo da richiedere due metodi di verifica. | Verificare che l'utente disponga di almeno due metodi di contatto configurati correttamente. Sono ad esempio necessari un numero di telefono cellulare *e* un numero di telefono dell'ufficio. |
| La directory è abilitata per la reimpostazione della password, ma non è possibile contattare l'utente anche se questo è configurato correttamente. | Questo può essere il risultato di un errore temporaneo del servizio o se non sono presenti dati di contatto errati che non è possibile rilevare correttamente. <br> <br> Se l'utente attende 10 secondi, viene visualizzato un collegamento "Riprova" e "Contatta l'amministratore". Se l'utente seleziona "Riprova", ripete la chiamata. Se l'utente seleziona "Contatta l'amministratore", invia un messaggio di posta elettronica di un modulo agli amministratori che richiedono la reimpostazione della password per l'account utente. |
| L'utente non riceve mai un SMS o una telefonata per la reimpostazione della password. | Questo errore può essere causato da un numero di telefono non corretto nella directory. Verificare che il numero di telefono sia nel formato "+ 1 4251234567". <br> <br>La reimpostazione della password non supporta le estensioni, anche se ne viene specificata una nella directory. Le estensioni vengono rimosse prima che venga effettuata la chiamata. Usare un numero senza estensione oppure integrare l'estensione nel numero di telefono nel PBX (Private Branch Exchange). |
| L'utente non riceve mai il messaggio di posta elettronica relativo alla reimpostazione della password. | La causa più comune di questo problema è il rifiuto del messaggio da parte di un filtro protezione da posta indesiderata. Cercare il messaggio di posta elettronica nella cartella della posta indesiderata o della posta eliminata. <br> <br> Assicurarsi inoltre che l'utente assegni l'account di posta elettronica corretto come registrato con SSPR. |
| Ho impostato un criterio di reimpostazione della password, ma quando un account amministratore usa la reimpostazione della password, il criterio non viene applicato. | Microsoft gestisce e controlla i criteri di reimpostazione delle password amministratore per assicurare il massimo livello di sicurezza. |
| All'utente viene impedito di provare a reimpostare la password troppe volte in uno stesso giorno. | Un meccanismo di limitazione automatico viene usato per impedire agli utenti di provare a reimpostare le password troppe volte in un breve periodo di tempo. La limitazione si verifica negli scenari seguenti: <br><ul><li>L'utente cerca di convalidare un numero di telefono cinque volte in un'ora.</li><li>L'utente cerca di usare il controllo per le domande di sicurezza cinque volte in un'ora.</li><li>L'utente cerca di reimpostare una password per lo stesso account utente cinque volte in un'ora.</li></ul>Se un utente incontra questo problema, deve attendere 24 ore dopo l'ultimo tentativo. A quel punto, sarà possibile reimpostare la password. |
| L'utente visualizza un errore quando convalida il numero di telefono. | Questo errore si verifica quando il numero di telefono immesso non corrisponde al numero di telefono sul file. Assicurarsi che l'utente immetta il numero di telefono completo, inclusi il prefisso internazionale e locale, quando cerca di usare un metodo basato sul telefono per la reimpostazione della password. |
| Si è verificato un errore durante l'elaborazione della richiesta. | Gli errori di registrazione SSPR generici possono essere causati da molti problemi, ma in genere questo errore è causato da un'interruzione del servizio o da un problema di configurazione. Se si continua a visualizzare questo errore generico quando si prova a eseguire di nuovo il processo di registrazione SSPR, [contattare il supporto tecnico Microsoft](#contact-microsoft-support) per richiedere assistenza aggiuntiva. |
| Violazione dei criteri locali | La password non soddisfa i criteri di Active Directory password locali. L'utente deve definire una password che soddisfi i requisiti di complessità o di attendibilità. |
| La password non è conforme ai criteri fuzzy | La password usata viene visualizzata nell'elenco delle [password escluse](./concept-password-ban-bad.md#how-are-passwords-evaluated) e non può essere usata. L'utente deve definire una password che soddisfi o superi i criteri per l'elenco delle password escluse. |

## <a name="sspr-errors-that-a-user-might-see"></a>Errori SSPR che possono essere visualizzati da un utente

Gli errori e i dettagli tecnici riportati di seguito possono essere visualizzati a un utente come parte del processo SSPR. Spesso l'errore non è un problema che può risolversi, perché la funzionalità SSPR deve essere abilitata, configurata o registrata per il proprio account.

Usare le informazioni seguenti per comprendere il problema e quali elementi devono essere corretti nel tenant Azure AD o in un account utente singolo.

| Errore | Dettagli | Dettagli tecnici |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Non è possibile reimpostare la password in questo momento perché l'amministratore ha disabilitato la reimpostazione della password per l'organizzazione. Non c'è alcuna azione aggiuntiva da eseguire per risolvere questa situazione. Contattare l'amministratore e chiedere di abilitare questa funzionalità.<br /><br />Per altre informazioni, vedere [Password di Azure AD dimenticata](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: È stato rilevato che la reimpostazione della password non è stata abilitata dall'amministratore. Contattare l'amministratore e richiedere l'abilitazione della reimpostazione delle password per l'organizzazione. |
| WritebackNotEnabled = 10 |Non è possibile reimpostare la password in questo momento perché l'amministratore non ha abilitato un servizio necessario per l'organizzazione. Non c'è alcuna azione aggiuntiva da eseguire per risolvere questa situazione. Contattare l'amministratore e chiedere di controllare la configurazione dell'organizzazione.<br /><br />Per altre informazioni su questo servizio necessario, vedere [Configurare il writeback delle password](./tutorial-enable-sspr-writeback.md). | SSPR_0010: È stato rilevato che il writeback delle password non è stato abilitato. Contattare l'amministratore e richiedere l'abilitazione del writeback delle password. |
| SsprNotEnabledInUserPolicy = 11 | Non è possibile reimpostare la password in questo momento perché l'amministratore non ha configurato la reimpostazione della password per l'organizzazione. Non c'è alcuna azione aggiuntiva da eseguire per risolvere questa situazione. Contattare l'amministratore e richiedere la configurazione della reimpostazione della password.<br /><br />Per altre informazioni sulla configurazione della reimpostazione della password, vedere [Avvio rapido: Reimpostazione della password self-service di Azure AD](./tutorial-enable-sspr.md). | SSPR_0011: L'organizzazione non ha definito un criterio di reimpostazione della password. Contattare l'amministratore e chiedere di definire i criteri di reimpostazione della password. |
| UserNotLicensed = 12 | Non è possibile reimpostare la password in questo momento perché mancano le licenze richieste dall'organizzazione. Non c'è alcuna azione aggiuntiva da eseguire per risolvere questa situazione. Contattare l'amministratore e richiedere una verifica delle assegnazioni delle licenze.<br /><br />Per altre informazioni sulle licenze, vedere [Requisiti di licenza per la reimpostazione password self-service di Azure AD](./concept-sspr-licensing.md). | SSPR_0012: L'organizzazione non ha le licenze necessarie per la reimpostazione della password. Contattare l'amministratore e richiedere una verifica delle assegnazioni delle licenze. |
| UserNotMemberOfScopedAccessGroup = 13 | Non è possibile reimpostare la password in questo momento perché l'amministratore non ha configurato l'account per l'uso della reimpostazione della password. Non c'è alcuna azione aggiuntiva da eseguire per risolvere questa situazione. Contattare l'amministratore e chiedere di configurare l'account per la reimpostazione della password.<br /><br />Per altre informazioni sulla configurazione dell'account per la reimpostazione della password, vedere [Implementare la reimpostazione della password per gli utenti](./howto-sspr-deployment.md). | SSPR_0013: Non si è membri di un gruppo abilitato per la reimpostazione della password. Contattare l'amministratore e richiedere di essere aggiunti al gruppo. |
| UserNotProperlyConfigured = 14 | Non è possibile reimpostare la password in questo momento perché nell'account mancano le informazioni necessarie. Non c'è alcuna azione aggiuntiva da eseguire per risolvere questa situazione. Contattare l'amministratore e chiedergli di reimpostare la password. Dopo avere recuperato l'accesso all'account, è necessario registrare le informazioni necessarie.<br /><br />Per registrare le informazioni, seguire i passaggi nell'articolo [Eseguire la registrazione per la reimpostazione password self-service](../user-help/active-directory-passwords-reset-register.md). | SSPR_0014: Sono necessarie informazioni di sicurezza aggiuntive per reimpostare la password. Per procedere, contattare l'amministratore e chiedergli di reimpostare la password. Dopo avere recuperato l'accesso all'account, è possibile registrare informazioni di sicurezza aggiuntive all'indirizzo https://aka.ms/ssprsetup. L'amministratore può aggiungere altre informazioni di sicurezza all'account seguendo la procedura illustrata in [Impostare e leggere i dati di autenticazione per la reimpostazione della password](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Non è possibile reimpostare la password in questo momento a causa di un problema con la configurazione della reimpostazione della password dell'organizzazione. Non c'è alcuna azione aggiuntiva da eseguire per risolvere questa situazione. Contattare l'amministratore e chiedere di esaminare la situazione. <br /><br />Oppure<br /><br />Non è possibile reimpostare la password in questo momento a causa di un problema con la configurazione della reimpostazione della password dell'organizzazione. Non è possibile eseguire altre operazioni per risolvere questo problema. Contattare l'amministratore e chiedere di esaminare la situazione.<br /><br />Per altre informazioni sul problema potenziale, vedere [Risolvere i problemi relativi al writeback delle password](troubleshoot-sspr-writeback.md). | SSPR_0029: Non è possibile reimpostare la password a causa di un errore nella configurazione locale. Contattare l'amministratore e chiedere di esaminare la situazione. |
| OnPremisesConnectivityError = 30 | Non è possibile reimpostare la password in questo momento a causa di problemi di connettività con l'organizzazione. Non è necessario eseguire alcuna azione in questo momento, ma è possibile che il problema risulti risolto se si prova più tardi. Se il problema persiste, contattare l'amministratore e chiedere di esaminare la situazione.<br /><br />Per altre informazioni sui problemi di connettività, vedere [Risolvere i problemi relativi alla connettività di writeback della password](troubleshoot-sspr-writeback.md). | SSPR_0030: Non è possibile reimpostare la password a causa di una connessione di qualità scadente con l'ambiente locale. Contattare l'amministratore e richiedere una verifica.|

## <a name="azure-ad-forums"></a>Forum di Azure AD

Per domande di carattere generale su Azure AD e la reimpostazione della password self-service, è possibile richiedere assistenza alla community nella [pagina Domande e risposte di Microsoft&per Azure Active Directory](/answers/topics/azure-active-directory.html). La community è composta da ingegneri, responsabili di prodotto, MVP e informatici.

## <a name="contact-microsoft-support"></a>Contattare il supporto Microsoft

Se non si riesce a trovare la risposta a un problema, i team di supporto Microsoft sono sempre disponibili per offrire maggiore assistenza.

Per garantire un supporto adeguato, verrà richiesto il maggior numero di dettagli possibile al momento dell'apertura di un caso. Questi dettagli includono quanto segue:

* **Descrizione generale dell'errore**: indicare il tipo di errore, il comportamento notato, e le modalità in cui è possibile riprodurre l'errore. Fornire il maggior numero di dettagli possibili.
* **Pagina**: indicare la pagina che si stava consultando quando è stato visualizzato l'errore. Includere l'URL, se possibile, e uno screenshot della pagina.
* **Codice di supporto**: indicare il codice di supporto generato quando è stato visualizzato l'errore.
   * Per trovare questo codice, riprodurre l'errore, quindi fare clic sul collegamento **Codice di supporto** nella parte inferiore della schermo e inviare al personale del supporto tecnico il GUID risultante.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Il codice di supporto si trova nella parte inferiore destra della finestra del browser Web.":::

  * Se è visualizzata una pagina senza un codice di supporto nella parte inferiore, premere F12 ed eseguire una ricerca di SID e CID, quindi inviare i due risultati al personale del supporto tecnico.
* **Data, ora e fuso orario**: includere la data e l'ora precise *con il fuso orario* di quando si è verificato l'errore.
* **ID utente**: indicare l'ID dell'utente che ha visualizzato l'errore. Ad esempio, *utente\@contoso.com*.
   * Indicare se si tratta di un utente federato,
   * Si tratta di un utente con autenticazione pass-through?
   * Si tratta di un utente con sincronizzazione di hash della password?
   * Si tratta di un utente solo cloud?
* **Licenze**: all'utente è stata assegnata una licenza di Azure AD?
* **Log eventi dell'applicazione**: se si usa il writeback delle password e l'errore si verifica nell'infrastruttura locale, includere una copia compressa del log eventi dell'applicazione del server Azure AD Connect.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su SSPR, vedere [come funziona: Azure ad la reimpostazione della password self-service](concept-sspr-howitworks.md) o [come funziona il writeback della reimpostazione della password self-service in Azure ad?](concept-sspr-writeback.md).
