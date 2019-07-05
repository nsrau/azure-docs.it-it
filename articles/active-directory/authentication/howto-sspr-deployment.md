---
title: Piano di distribuzione - Azure Active Directory di reimpostazione della password self-service
description: Reimpostazione della strategia per la corretta implementazione di password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440929"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Distribuire Azure reimpostazione password self-service AD

La reimpostazione della password self-service (SSPR) è una funzionalità di Azure Active Directory che consente ai dipendenti di reimpostare le password senza dover contattare il personale IT. I dipendenti devono registrare per o essere registrati per la prima di usare il servizio di reimpostazione della password self-service. Durante la registrazione, il dipendente sceglie uno o più metodi di autenticazione abilitati dall'organizzazione.

SSPR consente ai dipendenti di essere sbloccati rapidamente e continuare a funzionare indipendentemente dalla loro posizione o l'ora del giorno. Consentendo agli utenti di sbloccare autonomamente, l'organizzazione può ridurre il tempo non produttivo ed elevati costi di assistenza per problemi più comuni correlati alle password.

Consentire agli utenti di ottenere rapidamente registrato tramite la distribuzione di SSPR insieme a un'altra applicazione o il servizio all'interno dell'organizzazione. Questa azione genererà un volume elevato di accessi e determinerà la registrazione.

Prima di distribuire SSPR, le organizzazioni potrebbero essere necessario determinare il numero di reimpostazione della password chiamate all'helpdesk correlati si verificano nel tempo e il costo medio per ogni chiamata. Usano questo dopo la distribuzione dei dati per visualizzare il valore che SSPR innovativo alla propria organizzazione.  

## <a name="how-sspr-works"></a>Funzionamento di SSPR

1. Quando un utente tenta di reimpostare una password, è necessario verificare il metodo di autenticazione registrato in precedenza o i metodi per dimostrare la propria identità.
1. Quindi l'utente immette una nuova password.
   1. Per gli utenti solo cloud, la nuova password viene archiviata in Azure Active Directory. Per altre informazioni, vedere l'articolo [funziona come SSPR](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Per gli utenti ibridi, la password è scritta in Active Directory locale tramite il servizio di Azure AD Connect. Per altre informazioni, vedere l'articolo [What ' s il writeback delle password](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Considerazioni sulle licenze

Azure Active Directory è il significato di ogni utente licenza che ogni utente deve disporre di una licenza appropriata per le funzionalità che utilizzano.

- Self-service delle password per gli utenti solo cloud è disponibile con Azure AD Basic o versione successiva.
- Self-service della password con writeback in locale per gli ambienti ibridi richiede Azure AD Premium P1 o versione successiva.

Altre informazioni sulla gestione delle licenze sono reperibile nel [pagina dei prezzi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Abilitare la registrazione combinata per SSPR e autenticazione a più fattori

È consigliabile che le organizzazioni abilitare l'esperienza di registrazione combinato per SSPR e multi-factor authentication. Quando si abilita questa esperienza di registrazione combinato, gli utenti devono selezionare solo una volta le informazioni di registrazione per abilitare entrambe le funzionalità.

![Registrazione di informazioni di sicurezza combinato](./media/howto-sspr-deployment/combined-security-info.png)

L'esperienza di registrazione combinato non richiede alle organizzazioni di attivare SSPR e Azure multi-Factor Authentication da utilizzare. L'esperienza di registrazione combinato offre alle organizzazioni di migliorare l'esperienza utente rispetto al tradizionali singoli componenti. Altre informazioni sulla registrazione combinata e su come abilitare, sono reperibili nell'articolo [combinata registrazione di informazioni di sicurezza (anteprima)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Pianificare la configurazione

Le impostazioni seguenti sono necessari per abilitare SSPR oltre ai valori consigliati.

| Area | Impostazione | Value |
| --- | --- | --- |
| **Proprietà SSPR** | La reimpostazione della password self-service abilitata | **Selezionata** gruppo pilota / **tutto** per la produzione |
| **Metodi di autenticazione** | Metodi di autenticazione necessari per la registrazione | Sempre 1 altro rispetto a quelle necessarie per la reimpostazione |
|   | Metodi di autenticazione necessari per la reimpostazione | Uno o due |
| **Registrazione** | Richiedere agli utenti di registrarsi all'accesso | Yes |
|   | Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione | 90 – 180 giorni |
| **Notifications** | Inviare notifiche agli utenti al momento della reimpostazione della password | Yes |
|   | Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password | Yes |
| **Personalizzazione** | Personalizza collegamento al supporto tecnico | Yes |
|   | Supporto tecnico personalizzato tramite posta elettronica o URL | Indirizzo di posta elettronica o sito di supporto |
| **Integrazione locale** | Writeback delle password locali Active Directory | Yes |
|   | Consentire agli utenti di sbloccare l'account senza reimpostare la password | Yes |

### <a name="sspr-properties-recommendations"></a>Raccomandazioni di proprietà SSPR

Quando si abilita la reimpostazione della password self-service, scegliere un gruppo di sicurezza da utilizzare durante la fase pilota.

Quando si prevede di avviare il servizio di livello più ampio, è consigliabile usare l'opzione All per applicare le SSPR a chiunque nell'organizzazione. Se non è possibile impostare a tutti, il gruppo di sicurezza selezionate di Azure AD o gruppo di Active Directory sincronizzato con Azure AD.

### <a name="authentication-methods"></a>Metodi di autenticazione

Impostare i metodi di autenticazione necessari per la registrazione ad almeno un maggiore del numero per reimpostare la password. Che consente più flessibilità agli utenti quando sono necessari i reimpostare.

Impostare **numero di metodi da reimpostare** al livello più adatto per l'organizzazione. Una richiede l'attrito minimi, mentre due aumentino le condizioni di sicurezza.

Visualizzare [quali sono i metodi di autenticazione](concept-authentication-methods.md) per informazioni dettagliate su quali l'autenticazione sono disponibili metodi per SSPR, domande di sicurezza predefiniti e come creare domande di sicurezza personalizzato.

### <a name="registration-settings"></a>Impostazioni di registrazione

Impostare **richiedere agli utenti di registrarsi all'accesso** al **Yes**. Questa impostazione significa che gli utenti sono costretti a registrarsi al primo, assicurando che tutti gli utenti siano protetti.

Impostare **numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** a tra **90** e **180** giorni, a meno che l'organizzazione dispone di un'azienda necessità per un intervallo di tempo più breve.

### <a name="notifications-settings"></a>Impostazioni delle notifiche

Configurare entrambe le **notificare agli utenti le reimpostazioni delle password** e il **notificare agli amministratori quando altri amministratori reimpostano la propria password** al **Sì**. Selezionando **Sì** sia garantisce una maggiore sicurezza, garantendo che gli utenti siano a conoscenza quando è stata reimpostata la password e che tutti gli amministratori siano a conoscenza quando un amministratore modifica la password. Se gli utenti o gli amministratori ricevono una notifica e non hanno avviato la modifica, possono segnalare immediatamente una potenziale violazione della sicurezza.

### <a name="customization"></a>Personalizzazione

È fondamentale per personalizzare il **al supporto tecnico tramite posta elettronica o URL** per assicurarsi che gli utenti che si verifichino problemi rapidamente possono ottenere supporto. Impostare questa opzione su un indirizzo di posta elettronica al supporto tecnico comuni o una pagina web che hanno familiari con gli utenti.

### <a name="on-premises-integration"></a>Integrazione locale

Se si dispone di un ambiente ibrido, assicurarsi che **writeback delle password in locale AD** è impostata su **Yes**. Impostare anche di consentire agli utenti di sbloccare l'account senza reimpostare la password su Sì, poiché offre una maggiore flessibilità.

### <a name="changingresetting-passwords-of-administrators"></a>La modifica o reimpostazione password degli amministratori

Gli account di amministratore sono account speciale con autorizzazioni elevate. Per proteggere gli oggetti, le restrizioni seguenti si applicano alla modifica delle password degli amministratori:

- Gli amministratori dell'organizzazione in locale o gli amministratori di dominio non è possibile reimpostare la password tramite il SSPR. È possibile modificare solo la propria password nel proprio ambiente locale. Di conseguenza, è consigliabile la sincronizzazione non in locale gli account amministratore di Active Directory ad Azure AD.
- Un amministratore non può utilizzare secret domande e risposte come metodo per reimpostare la password.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambienti con più sistemi di gestione di identità

Se sono presenti identità più sistemi di gestione all'interno di un ambiente, ad esempio gestori di identità in locale, ad esempio Oracle AM, potrebbe essere necessario SiteMinder, o altri sistemi, quindi le password scritte in Active Directory da sincronizzare con altri sistemi che utilizzano uno strumento come la Password Change Notification Service (PCNS) con Microsoft Identity Manager (MIM). Per informazioni su questo scenario più complesso, vedere l'articolo [distribuire il servizio di notifica di modifica Password di MIM in un controller di dominio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Pianificare la distribuzione e supporto per SSPR

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder a destra

Quando i progetti di tecnologia hanno esito negativo, lo fanno in genere si a causa non corrispondono alle aspettative in impatto, risultati e le responsabilità. Per evitare questi problemi, verificare che si sono coinvolgere gli stakeholder a destra, e che i ruoli delle parti interessate nel progetto sono ben compresa da documentare gli stakeholder e progetto input che di responsabilità.

### <a name="communications-plan"></a>Piano di comunicazione

La comunicazione è fondamentale per il successo di qualsiasi nuovo servizio. Comunicare in modo proattivo con gli utenti come usare il servizio e cosa può fare per ottenere assistenza se qualcosa non funziona come previsto. Rivedere le [materiali di implementazione nell'area download Microsoft di reimpostazione della password self-service](https://www.microsoft.com/download/details.aspx?id=56768) per idee su come pianificare la strategia di comunicazione per l'utente finale.

### <a name="testing-plan"></a>Il piano di test

Per assicurarsi che la distribuzione funzioni come previsto, è consigliabile pianificare un set di test case che si utilizzerà per convalidare questa implementazione. La tabella seguente include alcuni scenari di test utile che è possibile usare per i documenti dell'organizzazione previsto di risultati a seconda dei criteri.

| Business case | Risultato previsto |
| --- | --- |
| Portale SSPR è accessibile dall'interno della rete aziendale | Determinata dall'organizzazione |
| Portale SSPR è accessibile dall'esterno della rete aziendale | Determinata dall'organizzazione |
| Reimpostare la password utente da browser quando l'utente non è abilitato per la reimpostazione della password | Utente non è in grado di accedere il flusso di reimpostazione della password |
| Reimpostare la password utente da browser quando l'utente non è registrato per la reimpostazione della password | Utente non è in grado di accedere il flusso di reimpostazione della password |
| Accesso degli utenti nella registrazione reimpostazione password quando viene applicata | Utente viene richiesto di registrare informazioni di sicurezza |
| Accesso degli utenti nella registrazione reimpostazione password quando è stata completata | Utente non viene richiesto di registrare informazioni di sicurezza |
| Portale SSPR è accessibile quando l'utente non dispone di una licenza | È accessibile |
| Reimpostare la password utente dalla schermata di blocco di dispositivi Windows 10 AADJ o H + AADJ dopo che l'utente ha registrato | È possibile reimpostare la password |
| Sono disponibili per gli amministratori dei dati di utilizzo e di registrazione SSPR quasi in tempo reale | È disponibile tramite i log di controllo |

### <a name="support-plan"></a>Piano di supporto

Mentre SSPR non crea in genere problemi relativi all'utente, è importante disporre di supporto personale preparato per risolvere i problemi che potrebbero verificarsi.

Anche se un amministratore può modificare o reimpostare la password per gli utenti finali tramite il portale di Azure AD, è preferibile risolvere il problema tramite un processo di supporto self-service.

Nella sezione Guida operativa di questo documento, creare un elenco di casi di supporto e le cause più probabili e creare una Guida per la risoluzione.

### <a name="auditing-and-reporting"></a>Controllo e creazione di report

Dopo la distribuzione molte organizzazioni vogliono sapere come o se il servizio Reimpostazione password self-service viene effettivamente usato. La funzionalità di creazione di report disponibile in Azure Active Directory (Azure AD) consente di rispondere a domande specifiche grazie a report predefiniti.

I log di controllo per la registrazione e reimpostazione della password sono disponibili per 30 giorni. Pertanto, se il controllo della protezione all'interno di un'azienda richiede la conservazione più lungo, i log dovranno essere esportati e utilizzato, ad esempio in uno strumento SIEM [Sentinel Azure](../../sentinel/connect-azure-active-directory.md), Splunk, o ArcSight.

In una tabella, come quello riportato di seguito, documentare la pianificazione del backup, il sistema e le entità responsabile. Potrebbe non essere necessario separare reporting di controllo e i backup, ma deve disporre di un backup separato da cui è possibile ripristinare da un problema.

|   | Frequenza di download | Sistema di destinazione | Responsabile |
| --- | --- | --- | --- |
| Controllo backup |   |   |   |
| Creazione di report di backup |   |   |   |
| Backup di ripristino di emergenza |   |   |   |

## <a name="implementation"></a>Implementazione

Implementazione avviene in tre fasi:

- Configurare utenti e licenze
- Configurare Azure AD SSPR per registrazione e la funzionalità Self-Service
- Configurare Azure AD Connect per il writeback delle password

### <a name="communicate-the-change"></a>Comunicare le modifiche

Iniziare l'implementazione del piano di comunicazione che sono stati sviluppati in fase di pianificazione.

### <a name="ensure-groups-are-created-and-populated"></a>Verificare che i gruppi vengono creati e popolati

Fa riferimento la sezione di metodi di autenticazione di password pianificazione e assicurarsi che i gruppi per l'implementazione del progetto pilota o di produzione sono disponibili e tutti gli utenti appropriati vengono aggiunti ai gruppi.

### <a name="apply-licenses"></a>Applicare le licenze

I gruppi di cui si intende implementare devono disporre di Azure AD assegnata licenza premium. È possibile assegnare le licenze direttamente al gruppo oppure è possibile usare criteri di licenza esistenti, ad esempio tramite PowerShell o gestione licenze basate sui gruppi.

Sono disponibili informazioni sull'assegnazione delle licenze ai gruppi di utenti, vedere l'articolo [assegnare le licenze per gli utenti per l'appartenenza al gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Configurare SSPR

#### <a name="enable-groups-for-sspr"></a>Abilitare i gruppi per SSPR

1. Accedere al portale di Azure con un account amministratore.
1. Selezionare tutti i servizi e nella casella del filtro digitare Azure Active Directory e quindi selezionare Azure Active Directory.
1. Nel Pannello di Active Directory, selezionare la reimpostazione della Password.
1. Nel riquadro Proprietà selezionare selezionati. Se si desidera che tutti gli utenti abilitati, Seleziona tutto.
1. La password predefinita pannello criteri di reimpostazione, digitare il nome del primo gruppo, selezionarlo e quindi fare clic sul selettore nella parte inferiore della schermata e seleziona Salva nella parte superiore della schermata.
1. Ripetere questo processo per ogni gruppo.

#### <a name="configure-the-authentication-methods"></a>Configurare i metodi di autenticazione

La pianificazione dalla sezione pianificazione di metodi di autenticazione di Password di questo documento fa riferimento.

1. Selezionare la registrazione, sotto richiedono all'utente di registrarsi all'accesso, selezionare Sì e quindi impostare il numero di giorni prima della scadenza e quindi selezionare Salva.
1. Notifica, selezionare e configurare per il piano e quindi selezionare Salva.
1. Personalizzazione, selezionare e configurare per il piano e quindi selezionare Salva.
1. Integrazione locale, selezionare e configurare per il piano e quindi selezionare Salva.

### <a name="enable-sspr-in-windows"></a>Abilitare la reimpostazione in Windows

I dispositivi Windows 10 versione 1803 o successiva che sono entrambi aggiunti ad Azure AD o ibridi aggiunti ad Azure AD possono reimpostare le password nella schermata di accesso di Windows. Informazioni e istruzioni per configurare questa funzionalità sono disponibili nell'articolo [reimpostazione dalla schermata di accesso della password di Azure AD](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Configurare il writeback delle password

Questa procedura per configurare il writeback delle password per l'organizzazione sono reperibili nell'articolo [procedure: Configurare il writeback delle password](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Gestire SSPR

Ruoli necessari per gestire le caratteristiche associate alla reimpostazione della password self-service.

| Ruolo di business/utente tipo | Ruolo di Azure AD (se necessario) |
| :---: | :---: |
| Livello 1 help desk | Amministratore password |
| Supporto tecnico di livello 2 | Amministratore utenti |
| Amministratore SSPR | Amministratore globale |

### <a name="support-scenarios"></a>Scenari di supporto

Per abilitare il successo del team di supporto, è possibile creare una domanda frequente basata sulle domande che viene visualizzato dagli utenti. Nella tabella seguente contiene i comuni scenari di supporto.

| Scenari | Descrizione |
| --- | --- |
| Utente non ha alcun metodo di autenticazione registrati disponibili | Un utente sta tentando di reimpostare la password, ma non è presente nessuno dei metodi di autenticazione che registrati disponibili (esempio: è lasciato il telefono cellulare a casa e non può accedere alla posta elettronica) |
| Utente non riceve un SMS o chiamata sul telefono cellulare o office | Un utente prova a verificare la propria identità tramite SMS o chiamata ma non riceve una chiamata o di testo. |
| Utente non può accedere al portale di reimpostazione della password | Un utente desidera reimpostare la password, ma non è abilitato per la reimpostazione della password e pertanto non è possibile accedere alla pagina per aggiornare le password. |
| Utente non può impostare una nuova password | Un utente viene completata la verifica durante il flusso di reimpostazione della password, ma non è possibile impostare una nuova password. |
| Utente non visualizza un collegamento di reimpostazione della Password in un dispositivo Windows 10 | Un utente tenta di reimpostare la password dalla schermata di blocco di Windows 10, ma il dispositivo non è sia aggiunto ad Azure AD o i criteri del dispositivo Intune non sono abilitato |

È anche possibile includere informazioni quali le seguenti operazioni per la risoluzione dei problemi aggiuntive.

- I gruppi a cui sono abilitati per SSPR.
- I metodi di autenticazione sono configurati.
- I criteri di accesso correlati a in o della rete aziendale.
- Risoluzione dei problemi per gli scenari comuni.

È anche possibile fare riferimento alla documentazione in linea sulla risoluzione dei problemi di reimpostazione password self-service per comprendere le procedure di risoluzione dei problemi generale per la maggior parte degli scenari SSPR.

## <a name="next-steps"></a>Passaggi successivi

- [È consigliabile implementare la protezione con password Azure AD](concept-password-ban-bad.md)

- [Considerare l'implementazione di blocco Smart per Azure AD](howto-password-smart-lockout.md)
