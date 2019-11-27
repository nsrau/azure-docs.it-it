---
title: Distribuzione della reimpostazione della password self-service-Azure Active Directory
description: Strategia per la corretta implementazione di Azure AD la reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9f340ad12fbf26190a17bc4df97bfc95473093c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381296"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Distribuire la reimpostazione della password self-service di Azure AD

> [!NOTE]
> Questa guida illustra la reimpostazione della password self-service e come distribuirla. Se si sta cercando lo strumento di reimpostazione della password self-service per tornare all'account, passare a [https://aka.ms/sspr](https://aka.ms/sspr). 

La reimpostazione della password self-service (SSPR) è una funzionalità Azure Active Directory che consente ai dipendenti di reimpostare le proprie password senza dover contattare il personale IT. Prima di usare il servizio, i dipendenti devono registrarsi per la reimpostazione della password self-service o essere registrati. Durante la registrazione, il dipendente sceglie uno o più metodi di autenticazione abilitati dall'organizzazione.

SSPR consente ai dipendenti di essere sbloccati rapidamente e continuare a lavorare indipendentemente da dove si trovano o dall'ora del giorno. Consentendo agli utenti di sbloccare autonomamente, l'organizzazione può ridurre il tempo non produttivo e i costi di supporto elevati per la maggior parte dei problemi comuni relativi alle password.

Consentire agli utenti di registrarsi rapidamente distribuendo SSPR insieme a un'altra applicazione o servizio nell'organizzazione. Questa azione genererà un volume elevato di accessi e guiderà la registrazione.

Prima di distribuire SSPR, è possibile che le organizzazioni desiderino determinare il numero di richieste di reimpostazione della password correlate help desk eseguite nel tempo e il costo medio di ogni chiamata. Possono usare questi dati dopo la distribuzione per mostrare il valore che SSPR sta portando all'organizzazione.  

## <a name="how-sspr-works"></a>Funzionamento di SSPR

1. Quando un utente tenta di reimpostare una password, deve verificare il metodo o i metodi di autenticazione precedentemente registrati per dimostrare la propria identità.
1. Quindi, l'utente immette una nuova password.
   1. Per gli utenti solo cloud, la nuova password viene archiviata in Azure Active Directory. Per ulteriori informazioni, vedere l'articolo funzionamento di [SSPR](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Per gli utenti ibridi, la password viene riscritta nell'Active Directory locale tramite il servizio Azure AD Connect. Per ulteriori informazioni, vedere l'articolo relativo al [writeback delle password](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Considerazioni sulle licenze

Azure Active Directory viene concesso in licenza per ogni utente, il che significa che ogni utente deve disporre di una licenza appropriata per le funzionalità che usano.

Ulteriori informazioni sulle licenze sono disponibili nella pagina relativa ai [prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Abilita la registrazione combinata per SSPR e multi-factor authentication

Microsoft consiglia alle organizzazioni di abilitare l'esperienza di registrazione combinata per SSPR e la funzionalità di autenticazione a più fattori. Quando si abilita questa esperienza di registrazione combinata, gli utenti devono solo selezionare le informazioni di registrazione una sola volta per abilitare entrambe le funzionalità.

![Registrazione di informazioni di sicurezza combinate](./media/howto-sspr-deployment/combined-security-info.png)

L'esperienza di registrazione combinata non richiede che le organizzazioni consentano l'uso di SSPR e Azure Multi-Factor Authentication. L'esperienza di registrazione combinata fornisce alle organizzazioni un'esperienza utente migliore rispetto ai singoli componenti tradizionali. Altre informazioni sulla registrazione combinata e su come abilitarle sono disponibili nell'articolo [registrazione di informazioni di sicurezza combinate (anteprima)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Pianificare la configurazione

Le impostazioni seguenti sono necessarie per abilitare SSPR insieme ai valori consigliati.

| Area | Impostazione | Valore |
| --- | --- | --- |
| **Proprietà di SSPR** | Reimpostazione password self-service abilitata | Gruppo **selezionato** per Pilot/ **All** per la produzione |
| **Metodi di autenticazione** | Metodi di autenticazione necessari per la registrazione | Sempre 1 più del necessario per la reimpostazione |
|   | Metodi di autenticazione necessari per la reimpostazione | Uno o due |
| **Registrazione** | Richiedere agli utenti di registrarsi all'accesso | Sì |
|   | Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione | 90 – 180 giorni |
| **Notifiche** | Inviare notifiche agli utenti al momento della reimpostazione della password | Sì |
|   | Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password | Sì |
| **Personalizzazione** | Personalizzare il collegamento al supporto tecnico | Sì |
|   | Indirizzo di posta elettronica o URL del supporto tecnico personalizzato | Sito di supporto o indirizzo di posta elettronica |
| **Integrazione locale** | Eseguire il writeback delle password in Active Directory locale | Sì |
|   | Consenti agli utenti di sbloccare l'account senza reimpostare la password | Sì |

### <a name="sspr-properties-recommendations"></a>Raccomandazioni sulle proprietà di SSPR

Quando si Abilita la reimpostazione della password self-service, scegliere un gruppo di sicurezza da utilizzare durante il progetto pilota.

Quando si prevede di avviare il servizio in modo più ampio, è consigliabile usare l'opzione all per applicare SSPR per tutti gli utenti dell'organizzazione. Se non è possibile impostare su tutti, selezionare il gruppo di sicurezza Azure AD o il gruppo di Active Directory appropriato sincronizzato con Azure AD.

### <a name="authentication-methods"></a>Metodi di autenticazione

Impostare i metodi di autenticazione necessari per la registrazione ad almeno un numero maggiore del numero necessario per la reimpostazione. Consentire a più utenti di fornire flessibilità quando è necessario reimpostarli.

Impostare il **numero di metodi necessari per la reimpostazione** a un livello appropriato per l'organizzazione. Uno richiede il minor attrito, mentre due può aumentare il comportamento di sicurezza.

Vedere [che cosa sono i metodi di autenticazione](concept-authentication-methods.md) per informazioni dettagliate sui metodi di autenticazione disponibili per SSPR, domande di sicurezza predefinite e su come creare domande di sicurezza personalizzate.

### <a name="registration-settings"></a>Impostazioni di registrazione

Impostare **Richiedi agli utenti di registrarsi all'accesso** a **Sì**. Questa impostazione indica che gli utenti devono eseguire la registrazione al momento dell'accesso, assicurandosi che tutti gli utenti siano protetti.

Impostare il **numero di giorni prima che agli utenti venga richiesto di riconfermare le informazioni di autenticazione** a un intervallo compreso tra **90** e **180** giorni, a meno che l'organizzazione non abbia bisogno di un intervallo di tempo più breve.

### <a name="notifications-settings"></a>Impostazioni notifiche

Configurare la reimpostazione della **password invia notifiche agli utenti** e **Invia notifiche a tutti gli amministratori quando altri amministratori reimpostano la propria password** su **Sì**. Se **si seleziona Sì, si** aumenta la sicurezza garantendo che gli utenti siano consapevoli della reimpostazione della password e che tutti gli amministratori siano consapevoli quando un amministratore modifica una password. Se gli utenti o gli amministratori ricevono una notifica di questo tipo e non hanno avviato la modifica, possono segnalare immediatamente una potenziale violazione della sicurezza.

### <a name="customization"></a>Personalizzazione

È fondamentale personalizzare l' **URL o l'indirizzo di posta elettronica del supporto tecnico** per assicurarsi che gli utenti che riscontrano problemi possano ottenere rapidamente la guida. Impostare questa opzione su un indirizzo di posta elettronica o una pagina Web del supporto tecnico comune con cui gli utenti hanno familiarità.

### <a name="on-premises-integration"></a>Integrazione locale

Se si dispone di un ambiente ibrido, verificare che il **writeback delle password in Active Directory locale** sia impostato su **Sì**. Impostare anche l'opzione Consenti agli utenti di sbloccare l'account senza reimpostare la password su Sì, perché offre una maggiore flessibilità.

### <a name="changingresetting-passwords-of-administrators"></a>Modifica/reimpostazione delle password degli amministratori

Gli account amministratore sono account speciali con autorizzazioni elevate. Per proteggerli, si applicano le restrizioni seguenti alla modifica delle password degli amministratori:

- Gli amministratori dell'organizzazione locale o gli amministratori di dominio non possono reimpostare la password tramite SSPR. Possono solo modificare la password nell'ambiente locale. Pertanto, è consigliabile non sincronizzare gli account amministratori di AD locali per Azure AD.
- Un amministratore non può usare domande segrete & risposte come metodo per reimpostare la password.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambienti con più sistemi di gestione delle identità

Se sono presenti più sistemi di gestione delle identità all'interno di un ambiente, ad esempio gestori di identità locali come Oracle AM, SiteMinder o altri sistemi, è possibile che le password scritte in Active Directory debbano essere sincronizzate con gli altri sistemi usando uno strumento come il servizio di notifica di modifica della password (PCNS) con Microsoft Identity Manager (MIM). Per trovare informazioni su questo scenario più complesso, vedere l'articolo [distribuire il servizio di notifica di modifica della password di MIM in un controller di dominio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Pianificare la distribuzione e il supporto per SSPR

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere lo fanno a causa di una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, assicurarsi di coinvolgere gli stakeholder appropriati e che i ruoli delle parti interessate nel progetto siano ben comprensibili documentando gli stakeholder e i rispettivi input e responsabilità del progetto.

### <a name="communications-plan"></a>Piano di comunicazione

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunicare in modo proattivo con gli utenti come usare il servizio e cosa possono fare per ottenere assistenza se qualcosa non funziona come previsto. Esaminare i [materiali di implementazione della reimpostazione della password self-service nell'area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) per idee su come pianificare la strategia di comunicazione dell'utente finale.

### <a name="testing-plan"></a>Piano di test

Per assicurarsi che la distribuzione funzioni come previsto, è necessario pianificare un set di test case che verranno usati per convalidare l'implementazione. La tabella seguente include alcuni utili scenari di test che è possibile usare per documentare i risultati previsti dalle organizzazioni in base ai criteri.

| Business case | Risultato previsto |
| --- | --- |
| Il portale di SSPR è accessibile dall'interno della rete aziendale | Determinato dall'organizzazione |
| Il portale di SSPR è accessibile dall'esterno della rete aziendale | Determinato dall'organizzazione |
| Reimposta la password utente dal browser quando l'utente non è abilitato per la reimpostazione della password | L'utente non è in grado di accedere al flusso di reimpostazione della password |
| Reimposta la password utente dal browser quando l'utente non ha effettuato la registrazione per la reimpostazione della password | L'utente non è in grado di accedere al flusso di reimpostazione della password |
| L'utente accede quando viene applicata la registrazione per la reimpostazione della password | All'utente viene richiesto di registrare le informazioni di sicurezza |
| L'utente accede quando è stata completata la registrazione per la reimpostazione della password | All'utente non viene richiesto di registrare le informazioni di sicurezza |
| Il portale di SSPR è accessibile quando l'utente non dispone di una licenza | Accessibile |
| Reimposta la password dell'utente da Windows 10 Azure AD la schermata di blocco del dispositivo aggiunta o ibrida Azure AD aggiunta dopo che l'utente ha eseguito la registrazione | L'utente può reimpostare la password |
| I dati di registrazione e di utilizzo di SSPR sono disponibili per gli amministratori quasi in tempo reale | È disponibile tramite i log di controllo |

### <a name="support-plan"></a>Piano di supporto

Sebbene in genere SSPR non crei problemi con gli utenti, è importante che il personale di supporto sia pronto per gestire i problemi che possono verificarsi.

Sebbene un amministratore possa modificare o reimpostare la password per gli utenti finali tramite il portale di Azure AD, è preferibile risolvere il problema tramite un processo di supporto self-service.

Nella sezione Guida operativa di questo documento creare un elenco di casi di supporto e le relative cause probabili e creare una guida per la risoluzione.

### <a name="auditing-and-reporting"></a>Controllo e creazione di report

Dopo la distribuzione molte organizzazioni vogliono sapere come o se il servizio Reimpostazione password self-service viene effettivamente usato. La funzionalità di creazione di report disponibile in Azure Active Directory (Azure AD) consente di rispondere a domande specifiche grazie a report predefiniti.

I log di controllo per la registrazione e la reimpostazione della password sono disponibili per 30 giorni. Se pertanto il controllo della sicurezza in un'azienda richiede un periodo di conservazione più lungo, i log devono essere esportati e utilizzati in uno strumento SIEM, ad esempio [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk o ArcSight.

In una tabella, come quella riportata di seguito, documentare la pianificazione del backup, il sistema e le parti responsabili. Potrebbero non essere necessari backup distinti per il controllo e la creazione di report, ma è necessario disporre di un backup separato da cui è possibile eseguire il ripristino da un problema.

|   | Frequenza di download | Sistema di destinazione | Parte responsabile |
| --- | --- | --- | --- |
| Backup del controllo |   |   |   |
| Backup dei report |   |   |   |
| Backup del ripristino di emergenza |   |   |   |

## <a name="implementation"></a>Implementazione

L'implementazione si verifica in tre fasi:

- Configurare gli utenti e le licenze
- Configurare Azure AD SSPR per la registrazione e la gestione self-service
- Configurare Azure AD Connect per il writeback delle password

### <a name="communicate-the-change"></a>Comunicare la modifica

Iniziare l'implementazione del piano di comunicazione sviluppato nella fase di pianificazione.

### <a name="ensure-groups-are-created-and-populated"></a>Verificare che i gruppi siano creati e popolati

Fare riferimento alla sezione Planning Password Authentication Methods e verificare che i gruppi per l'implementazione pilota o di produzione siano disponibili e che tutti gli utenti appropriati vengano aggiunti ai gruppi.

### <a name="apply-licenses"></a>Applica licenze

Ai gruppi che si intende implementare deve essere assegnata la licenza Azure AD Premium. È possibile assegnare le licenze direttamente al gruppo oppure usare i criteri di licenza esistenti, ad esempio tramite PowerShell o le licenze basate sui gruppi.

Per informazioni sull'assegnazione delle licenze ai gruppi di utenti, vedere l'articolo assegnare le [licenze agli utenti in base all'appartenenza al gruppo in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Configurare SSPR

#### <a name="enable-groups-for-sspr"></a>Abilitare i gruppi per SSPR

1. Accedere al portale di Azure con un account amministratore.
1. Selezionare tutti i servizi e nella casella filtro digitare Azure Active Directory e quindi selezionare Azure Active Directory.
1. Nel pannello Active Directory selezionare reimpostazione password.
1. Nel riquadro Proprietà selezionare selezionato. Se si desidera che tutti gli utenti siano abilitati, selezionare tutti.
1. Nel pannello criteri di reimpostazione password predefiniti digitare il nome del primo gruppo, selezionarlo, quindi fare clic su Seleziona nella parte inferiore della schermata e selezionare Salva nella parte superiore della schermata.
1. Ripetere questo processo per ogni gruppo.

#### <a name="configure-the-authentication-methods"></a>Configurare i metodi di autenticazione

Fare riferimento alla pianificazione dalla sezione relativa alla pianificazione dei metodi di autenticazione della password di questo documento.

1. Selezionare registrazione, in Richiedi utente per la registrazione al momento dell'accesso, selezionare Sì, quindi impostare il numero di giorni prima della scadenza e quindi selezionare Salva.
1. Selezionare Notification (notifica) e configurare in base al piano e quindi selezionare Save (Salva).
1. Selezionare personalizzazione e configurare in base al piano e quindi selezionare Salva.
1. Selezionare integrazione locale e configurare in base al piano e quindi selezionare Salva.

### <a name="enable-sspr-in-windows"></a>Abilitare SSPR in Windows

I dispositivi Windows 10 che eseguono la versione 1803 o una versione successiva, che sono Azure AD aggiunti o ibridi Azure AD Uniti possono reimpostare le proprie password nella schermata di accesso di Windows. Informazioni e procedure per la configurazione di questa funzionalità sono disponibili nell'articolo [Azure ad la reimpostazione della password dalla schermata di accesso](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Configurare il writeback delle password

I passaggi per configurare il writeback delle password per l'organizzazione sono disponibili nell'articolo [procedura: configurare il writeback delle password](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Gestisci SSPR

Ruoli necessari per gestire le funzionalità associate alla reimpostazione della password self-service.

| Ruolo aziendale/persona | Ruolo Azure AD (se necessario) |
| :---: | :---: |
| Supporto tecnico di livello 1 | Amministratore password |
| Supporto tecnico di livello 2 | Amministratore utenti |
| Amministratore di SSPR | Amministratore globale |

### <a name="support-scenarios"></a>Scenari di supporto

Per consentire al team di supporto di avere esito positivo, è possibile creare domande frequenti in base alle domande ricevute dagli utenti. La tabella seguente contiene scenari di supporto comuni.

| Scenari | DESCRIZIONE |
| --- | --- |
| Nessun metodo di autenticazione registrato disponibile per l'utente | Un utente sta tentando di reimpostare la password, ma non dispone di alcun metodo di autenticazione registrato disponibile (ad esempio, ha lasciato il telefono cellulare a casa e non può accedere alla posta elettronica) |
| L'utente non riceve un SMS o una chiamata sul proprio ufficio o telefono cellulare | Un utente sta provando a verificare la propria identità tramite il testo o la chiamata ma non riceve un testo/chiamata. |
| L'utente non può accedere al portale di reimpostazione della password | Un utente desidera reimpostare la password, ma non è abilitata per la reimpostazione della password e pertanto non può accedere alla pagina per aggiornare le password. |
| L'utente non può impostare una nuova password | Un utente completa la verifica durante il flusso di reimpostazione della password, ma non può impostare una nuova password. |
| L'utente non visualizza un collegamento per la reimpostazione della password in un dispositivo Windows 10 | Un utente sta tentando di reimpostare la password dalla schermata di blocco di Windows 10, ma il dispositivo non è stato aggiunto a Azure AD oppure i criteri dei dispositivi di Intune non sono abilitati |

Per ulteriori informazioni sulla risoluzione dei problemi, è anche possibile includere informazioni come la seguente.

- Gruppi abilitati per SSPR.
- Quali metodi di autenticazione sono configurati.
- Criteri di accesso correlati a in o alla rete aziendale.
- Procedura di risoluzione dei problemi per gli scenari comuni.

È anche possibile fare riferimento alla documentazione online sulla risoluzione dei problemi relativi alla reimpostazione della password self-service per comprendere le procedure generali per la risoluzione dei problemi per gli scenari SSPR più comuni.

## <a name="next-steps"></a>Passaggi successivi

- [Prendere in considerazione l'implementazione della protezione Azure AD password](concept-password-ban-bad.md)

- [Prendere in considerazione l'implementazione di Azure AD blocco intelligente](howto-password-smart-lockout.md)
