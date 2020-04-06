---
title: Considerazioni sulla distribuzione per la reimpostazione della password self-service di Azure Active Directory
description: Informazioni sulle considerazioni sulla distribuzione e sulla strategia per la corretta implementazione della reimpostazione della password self-service di Azure ADLearn about deployment considerations and strategy for successful implementation of Azure AD self-service password reset
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b9e1f2640e68f7c819a49ad34d9c051c582c5
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667322"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Pianificare una distribuzione della reimpostazione della password self-service di Azure Active DirectoryPlan an Azure Active Directory self-service password reset deployment

> [!IMPORTANT]
> Questo piano di distribuzione offre indicazioni e procedure consigliate per la distribuzione della reimpostazione della password self-service (SSPR) di Azure AD.
>
> **Se sei e l'utente finale e devi tornare [https://aka.ms/sspr](https://aka.ms/sspr)al tuo account, vai a **.

[Self-Service Password Reset (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) è una funzionalità di Azure Active Directory (AD) che consente agli utenti di reimpostare le password senza contattare il personale IT per assistenza. Gli utenti possono sbloccare rapidamente se stessi e continuare a lavorare non importa dove sono o ora del giorno. Consentendo ai dipendenti di sbloccarsi, l'organizzazione può ridurre il tempo non produttivo e i costi di supporto elevati per i problemi più comuni relativi alle password.

SSPR dispone delle seguenti funzionalità chiave:

* Il self-service consente agli utenti finali di reimpostare le password scadute o non scadute senza contattare un amministratore o un helpdesk per assistenza.
* [Il writeback delle](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) password consente la gestione delle password locali e la risoluzione del blocco degli account nel cloud.
* I report delle attività di gestione delle password forniscono agli amministratori informazioni dettagliate sull'attività di reimpostazione e registrazione delle password che si verificano nell'organizzazione.

In questa guida alla distribuzione viene illustrato come pianificare e quindi testare un'implementazione di SSPR.

Per vedere rapidamente SSPR in azione e quindi tornare a comprendere ulteriori considerazioni sulla distribuzione:To quickly see SSPR in action and then back to understand additional deployment considerations:

> [!div class="nextstepaction"]
> [Abilitare la reimpostazione della password self-service (SSPR)Enable self-service password reset (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Scopri di più su SSPR

Ulteriori informazioni su SSPR. Vedere [Come funziona: Reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)di Azure AD .

### <a name="key-benefits"></a>Vantaggi principali

I principali vantaggi dell'abilitazione di SSPR sono:

* **Gestire i costi**. SSPR riduce i costi di supporto IT consentendo agli utenti di reimpostare le password da soli. Riduce anche il costo del tempo perso a causa di password perse e blocchi. 

* **Esperienza utente intuitiva**. Fornisce un processo di registrazione utente una tantum intuitivo che consente agli utenti di reimpostare le password e sbloccare gli account su richiesta da qualsiasi dispositivo o posizione. SSPR consente agli utenti di tornare al lavoro più velocemente ed essere più produttivi.

* **Flessibilità e sicurezza**. SSPR consente alle aziende di accedere alla sicurezza e alla flessibilità offerte da una piattaforma cloud. Gli amministratori possono modificare le impostazioni per soddisfare i nuovi requisiti di sicurezza ed eseguire il rolling queste modifiche agli utenti senza interrompere l'accesso.

* **Controllo e monitoraggio dell'utilizzo affidabili**. Un'organizzazione può garantire che i sistemi aziendali rimangano protetti mentre gli utenti reimpostano le proprie password. I solidi log di controllo includono informazioni su ogni fase del processo di reimpostazione della password. Questi registri sono disponibili da un'API e consentono all'utente di importare i dati in un sistema SIEM (Security Incident and Event Monitoring).

### <a name="licensing"></a>Gestione delle licenze

Azure Active Directory è concesso in licenza per utente, il che significa che ogni utente richiede una licenza appropriata per le funzionalità che usano. Si consiglia di licenze basate su gruppo per SSPR. 

Per confrontare edizioni e funzionalità e abilitare le licenze basate su gruppi o utenti, vedere Requisiti di licenza per la [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)di Azure AD.

Per altre informazioni sui prezzi, vedere Prezzi di [Azure Active Directory.For more information](https://azure.microsoft.com/pricing/details/active-directory/)about pricing, see Azure Active Directory pricing .

### <a name="prerequisites"></a>Prerequisiti

* Un tenant di Azure AD funzionante, con almeno una licenza di valutazione abilitata. Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un account con privilegi di amministratore globale.


### <a name="training-resources"></a>Risorse per la formazione

| Risorse| Collegamento e descrizione |
| - | - |
| Video| [Offri agli utenti una migliore scalabilità IT](https://youtu.be/g9RpRnylxS8) 
| |[Che cos'è la reimpostazione della password self-service?](https://youtu.be/hc97Yx5PJiM)|
| |[Distribuzione della reimpostazione della password self-service](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Come configurare la reimpostazione della password self-service per gli utenti in Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Come [preparare gli utenti a] registrare [loro] informazioni sulla sicurezza per Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Corsi online|[Gestione delle identità in Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Usa SSPR per offrire agli utenti un'esperienza moderna e protetta. Vedere in particolare il modulo " Gestione di[Azure Active Directory Users and Groups](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Corsi pluralisti a pagamento |[Problemi relativi alla gestione delle identità e degli accessi](https://www.pluralsight.com/courses/identity-access-management-issues) Informazioni sui problemi di IAM e di sicurezza di cui tenere presenti i problemi nell'organizzazione. Vedere in particolare il modulo "Altri metodi di autenticazione".|
| |[Introduzione a Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Scopri le procedure consigliate per estendere le risorse locali al cloud in modo da consentire l'autenticazione, l'autorizzazione, la crittografia e un'esperienza mobile protetta. Vedere in particolare il modulo "Configurazione delle funzionalità avanzate di Microsoft Azure Active Directory Premium".
|Esercitazioni |[Completare un'implementazione pilota per la reimpostazione della password self-service di Azure ADComplete an Azure AD self-service password reset pilot roll out](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Abilitazione del writeback delle password](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Reimpostazione della password di Azure AD dalla schermata di accesso per Windows 10Azure AD password reset from the login screen for Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Domande frequenti|[Domande frequenti sulla gestione delle password](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Architettura della soluzione

Nell'esempio seguente viene descritta l'architettura della soluzione di reimpostazione della password per ambienti ibridi comuni.

![diagramma dell'architettura della soluzione](./media/howto-sspr-deployment//solutions-architecture.png)

Descrizione del flusso di lavoro

Per reimpostare la password, gli utenti accedono al portale di [reimpostazione](https://aka.ms/sspr)della password. Devono verificare il metodo o i metodi di autenticazione registrati in precedenza per dimostrare la propria identità. Se la password è stata reimpostata correttamente, inizia il processo di ripristino.

* Per gli utenti solo cloud, SSPR archivia la nuova password in Azure AD. 

* Per gli utenti ibridi, SSPR scrive nuovamente la password in Active Directory locale tramite il servizio Azure AD Connect. 

Nota: per gli utenti con la sincronizzazione dell'hash delle password [(PHS, Password hash synchronization)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) disabilitata, SSPR memorizza le password solo in Active Directory locale.

### <a name="best-practices"></a>Procedure consigliate

È possibile aiutare gli utenti a registrarsi rapidamente distribuendo SSPR insieme a un'altra applicazione o servizio comune nell'organizzazione. Questa azione genererà un grande volume di dischi e unitàrà la registrazione.

Prima di distribuire SSPR, è possibile scegliere di determinare il numero e il costo medio di ogni chiamata di reimpostazione della password. È possibile utilizzare questi dati dopo la distribuzione per mostrare il valore che SSPR sta apportando all'organizzazione.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Abilitare la registrazione combinata per SSPR e MFAEnable combined registration for SSPR and MFA

Microsoft consiglia alle organizzazioni di abilitare l'esperienza di registrazione combinata per SSPR e l'autenticazione a più fattori. Quando si abilita questa esperienza di registrazione combinata, gli utenti devono selezionare le informazioni di registrazione una sola volta per abilitare entrambe le funzionalità.

L'esperienza di registrazione combinata non richiede alle organizzazioni di abilitare SSPR e Azure Multi-Factor Authentication.The combined registration experience does not require organizations to enable both SSPR and Azure Multi-Factor Authentication. La registrazione combinata offre alle organizzazioni un'esperienza utente migliore. Per altre informazioni, vedere Registrazione combinata delle informazioni di [sicurezza (anteprima)For](concept-registration-mfa-sspr-combined.md) more information, see Combined security information registration (preview)

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Considerare le esigenze dell'organizzazione mentre si determina la strategia per questa distribuzione nell'ambiente.

### <a name="engage-the-right-stakeholders"></a>Coinvolgi le parti interessate giuste

Quando i progetti tecnologici falliscono, in genere lo fanno a causa di aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare queste insidie, [assicurati di coinvolgere le parti interessate giuste](https://aka.ms/deploymentplans) e che i ruoli degli stakeholder nel progetto siano ben compresi documentando le parti interessate e il loro input e responsabilità del progetto.

#### <a name="required-administrator-roles"></a>Ruoli di amministratore obbligatori


| Ruolo attività/persona| Ruolo di Azure AD (se necessario)Azure AD Role (if necessary) |
| - | - |
| Helpdesk di livello 1| Amministratore password |
| Helpdesk di livello 2| Amministratore utenti |
| Amministratore SSPR| Amministratore globale |


### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di ogni nuovo servizio. È consigliabile comunicare in modo proattivo agli utenti come cambierà la loro esperienza, quando cambierà e come ottenere supporto in caso di problemi. Esaminare il materiale di implementazione per [la reimpostazione della password self-service nell'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) per informazioni su come pianificare la strategia di comunicazione degli utenti finali.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

È consigliabile che la configurazione iniziale di SSPR si sofsia in un ambiente di test. Iniziare con un gruppo pilota abilitando SSPR per un sottoinsieme di utenti nell'organizzazione. Vedere [Procedure consigliate per un progetto pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Per creare un gruppo, vedere come [creare un gruppo e aggiungere membri in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) 

## <a name="plan-configuration"></a>Pianificare la configurazione

Le impostazioni seguenti sono necessarie per abilitare SSPR insieme ai valori consigliati.

| Area | Impostazione | valore |
| --- | --- | --- |
| **Proprietà SSPR** | Reimpostazione password self-service abilitata | **Gruppo selezionato** per pilota / **Tutto** per la produzione |
| **Metodi di autenticazione** | Metodi di autenticazione necessari per la registrazione | Sempre 1 in più del necessario per il ripristino |
|   | Metodi di autenticazione necessari per reimpostare | Uno o due |
| **Registrazione** | Richiedere agli utenti di registrarsi all'accesso | Sì |
|   | Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione | 90 – 180 giorni |
| **Notifiche** | Inviare notifiche agli utenti al momento della reimpostazione della password | Sì |
|   | Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password | Sì |
| **Personalizzazione** | Personalizzare il collegamento dell'helpdesk | Sì |
|   | Indirizzo di posta elettronica o URL dell'helpdesk personalizzato | Sito di supporto o indirizzo e-mail |
| **Integrazione locale** | Scrivere le password in Active Directory locale | Sì |
|   | Consenti agli utenti di sbloccare l'account senza reimpostare la password | Sì |

### <a name="sspr-properties"></a>Proprietà SSPR

Quando si abilita SSPR, scegliere un gruppo di sicurezza appropriato nell'ambiente pilota.

* Per applicare la registrazione SSPR a tutti, è consigliabile usare l'opzione **Tutti.**
* In caso contrario, selezionare il gruppo di sicurezza di Azure AD o AD appropriato.

### <a name="authentication-methods"></a>Metodi di autenticazione

Quando SSPR è abilitato, gli utenti possono reimpostare la password solo se dispongono di dati presenti nei metodi di autenticazione abilitati dall'amministratore. I metodi includono telefono, notifica dell'app Authenticator, domande di sicurezza, ecc. Per ulteriori informazioni, vedere Che cosa sono i metodi di [autenticazione?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

Si consigliano le seguenti impostazioni del metodo di autenticazione:

* Impostare i metodi di **autenticazione necessari per la registrazione** almeno uno superiore al numero necessario per la reimpostazione. Consentire più autenticazioni offre agli utenti flessibilità quando devono essere reimpostati.

* Impostare **Numero di metodi necessari per ripristinare** un livello appropriato per l'organizzazione. Uno richiede il minimo attrito, mentre due possono aumentare la postura di sicurezza. 

Nota: l'utente deve disporre dei metodi di autenticazione configurati nei [criteri e nelle restrizioni delle](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)password in Azure Active Directory .

### <a name="registration-settings"></a>Impostazioni di registrazione

Impostare **Richiedi agli utenti di registrarsi quando si accede** su **Sì**. Questa impostazione richiede agli utenti di registrarsi al momento dell'accesso, garantendo che tutti gli utenti siano protetti.

Impostare Numero di giorni prima che agli utenti venga chiesto di **riconfermare le informazioni** di autenticazione su un periodo compreso tra **90** e **180** giorni, a meno che l'organizzazione non abbia esigenze aziendali per un intervallo di tempo più breve.

### <a name="notifications-settings"></a>Impostazioni delle notifiche

Configurare sia **Notifica agli utenti in caso di reimpostazione** della password e Notifica a tutti gli amministratori quando gli altri amministratori **reimpostano la password su** **Sì**. La selezione di **Sì** su entrambi aumenta la sicurezza assicurando si assicura che gli utenti siano consapevoli quando la password viene reimpostata. Garantisce inoltre che tutti gli amministratori siano consapevoli quando un amministratore modifica una password. Se gli utenti o gli amministratori ricevono una notifica e non hanno avviato la modifica, possono segnalare immediatamente un potenziale problema di sicurezza.

### <a name="customization-settings"></a>Impostazioni di personalizzazione

È fondamentale personalizzare l'indirizzo di posta elettronica o l'URL dell'helpdesk per garantire che gli utenti che riscontrano problemi possano ottenere assistenza immediatamente. Impostare questa opzione su un indirizzo di posta elettronica o una pagina Web comune con cui gli utenti hanno familiarità. 

Per altre informazioni, vedere Personalizzare la funzionalità di Azure AD per la [reimpostazione della password self-service.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

### <a name="password-writeback"></a>Writeback password

**Il writeback** delle password è abilitato con [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) e scrive la reimpostazione della password nel cloud in una directory locale esistente in tempo reale. Per ulteriori informazioni, vedere [Che cos'è il writeback delle password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Si consigliano le seguenti impostazioni:

* Verificare che l'impostazione **Sì (Write Back Passwords to on-premises AD)** locale sia **impostata**su Yes . 
* Impostare **Consenti agli utenti di sbloccare l'account senza reimpostare** la password su **Sì**.

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password.

### <a name="administrator-password-setting"></a>Impostazione della password dell'amministratore

Gli account amministratore dispongono di autorizzazioni elevate. Gli amministratori di livello aziendale o di dominio locali non possono reimpostare le password tramite SSPR. Gli account amministratore locali hanno le restrizioni seguenti:On-premises admin accounts have the following restrictions:

* può cambiare la propria password solo nell'ambiente precedente.
* non può mai utilizzare le domande e le risposte segrete come metodo per reimpostare la password.

È consigliabile non sincronizzare gli account amministratore di Active Directory locale con Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambienti con più sistemi di gestione delle identità

Alcuni ambienti dispongono di più sistemi di gestione delle identità. I gestori di identità locali, ad esempio Oracle AM e SiteMinder, richiedono la sincronizzazione con AD per le password. È possibile farlo utilizzando uno strumento come il servizio di notifica di modifica della password (PCNS) con Microsoft Identity Manager (MIM). Per informazioni su questo scenario più complesso, vedere [l'articolo Distribuire il](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)servizio di notifica di modifica della password MIM in un controller di dominio .

## <a name="plan-testing-and-support"></a>Pianificare i test e il supporto

In ogni fase della distribuzione dai gruppi pilota iniziali a tutto l'organizzazione, verificare che i risultati siano i primi.

### <a name="plan-testing"></a>Pianificare i test

Per assicurarsi che la distribuzione funzioni come previsto, pianificare un set di test case per convalidare l'implementazione. Per valutare i test case, è necessario un utente di test non amministratore con una password. Se è necessario creare un utente, vedere [Aggiungere nuovi utenti ad Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)

Nella tabella seguente sono inclusi scenari di test utili che è possibile utilizzare per documentare i risultati previsti dell'organizzazione in base ai criteri.
<br>


| Business case| Risultati previsti |
| - | - |
| Il portale SSPR è accessibile dall'interno della rete aziendale| Determinato dall'organizzazione |
| Il portale SSPR è accessibile dall'esterno della rete aziendale| Determinato dall'organizzazione |
| Reimpostare la password utente dal browser quando l'utente non è abilitato per la reimpostazione della password| L'utente non è in grado di accedere al flusso di reimpostazione della password |
| Reimpostare la password utente dal browser quando l'utente non è registrato per la reimpostazione della password| L'utente non è in grado di accedere al flusso di reimpostazione della password |
| L'utente accede quando viene imposto per eseguire la registrazione della reimpostazione della password| Richiede all'utente di registrare le informazioni di sicurezza |
| L'utente accede al termine della registrazione della reimpostazione della password| Richiede all'utente di registrare le informazioni di sicurezza |
| Il portale SSPR è accessibile quando l'utente non dispone di una licenza| È accessibile |
| Reimpostare la password utente dalla schermata di blocco del dispositivo aggiunto ad Azure AD o Ad Azure AD ibrido o aggiunta ad Azure AD di Windows 10| L'utente può reimpostare la password |
| I dati di registrazione e utilizzo SSPR sono disponibili per gli amministratori quasi in tempo reale| È disponibile tramite i registri di controllo |

È anche possibile fare riferimento a Completare un rotolo pilota di [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)di Azure AD . In questa esercitazione verrà abilitata un'implementazione pilota di SSPR nell'organizzazione e verrà esportata l'utilizzo di un account non amministratore.

### <a name="plan-support"></a>Pianificare il supporto

Sebbene SSPR in genere non crei problemi con gli utenti, è importante preparare il personale di supporto per affrontare i problemi che possono sorgere. Mentre un amministratore può reimpostare la password per gli utenti finali tramite il portale di Azure AD, è preferibile risolvere il problema tramite un processo di supporto self-service.

Per consentire il successo del team di supporto, puoi creare una FAQ in base alle domande che ricevi dai tuoi utenti. Di seguito sono disponibili alcuni esempi:

| Scenari| Descrizione |
| - | - |
| L'utente non dispone di metodi di autenticazione registrati disponibiliUser doesn't have any registered authentication methods available| Un utente sta tentando di reimpostare la password ma non dispone di uno dei metodi di autenticazione registrati disponibili (Ad esempio: hanno lasciato il cellulare a casa e non possono accedere alla posta elettronica) |
| L'utente non riceve un SMS o una chiamata sul proprio ufficio o cellulare| Un utente sta tentando di verificare la propria identità tramite SMS o chiamata, ma non riceve un SMS o una chiamata. |
| L'utente non può accedere al portale di reimpostazione della password| Un utente desidera reimpostare la password ma non è abilitato per la reimpostazione della password e non può accedere alla pagina per aggiornare le password. |
| L'utente non può impostare una nuova password| Un utente completa la verifica durante il flusso di reimpostazione della password, ma non può impostare una nuova password. |
| L'utente non vede un collegamento Reimposta password in un dispositivo Windows 10| Un utente sta tentando di reimpostare la password dalla schermata di blocco di Windows 10, ma il dispositivo non è aggiunto ad Azure AD o il criterio del dispositivo Intune non è abilitato |

### <a name="plan-rollback"></a>Pianificare il rollback

Per eseguire il rollback della distribuzione:

* per un singolo utente, rimuovere l'utente dal gruppo di sicurezza 

* per un gruppo, rimuovere il gruppo dalla configurazione SSPR

* Per tutti gli utenti, disabilitare SSPR per il tenant di Azure ADFor everyone, disable SSPR for the Azure AD tenant

## <a name="deploy-sspr"></a>Distribuire SSPR

Prima di eseguire la distribuzione, assicurarsi di aver eseguito le operazioni seguenti:

1. Creato e iniziato l'esecuzione del piano di [comunicazione](#plan-communications).

1. Determinate [le impostazioni di configurazione](#plan-configuration)appropriate.

1. Identificati gli utenti e i gruppi per gli ambienti [pilota](#plan-a-pilot) e di produzione.

1. [Determinate impostazioni di configurazione](#plan-configuration) per la registrazione e la modalità self-service.

1. [Il writeback delle password configurato](#password-writeback) se si dispone di un ambiente ibrido.


**Ora sei pronto per distribuire SSPR!**

Vedere Abilitare la [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) per istruzioni dettagliate sulla configurazione delle aree seguenti.

1. [Metodi di autenticazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Impostazioni di registrazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Impostazioni delle notifiche](#notifications-settings)

1. [Impostazioni di personalizzazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Integrazione locale](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Abilitare SSPR in WindowsEnable SSPR in Windows
Per i computer che eseguono Windows 7, 8, 8.1 e 10 è possibile consentire agli utenti di [reimpostare la password nella schermata](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows) di accesso di Windows

## <a name="manage-sspr"></a>Gestire SSPR

Azure AD può fornire informazioni aggiuntive sulle prestazioni di SSPR tramite controlli e report.

### <a name="password-management-activity-reports"></a>Report attività di gestione delle password 

È possibile usare report predefiniti nel portale di Azure per misurare le prestazioni di SSPR. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate. Per altre informazioni, vedere Opzioni di report per la gestione delle password di [Azure ADFor](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) more information, see Reporting options for Azure AD password management

> [!NOTE]
>  È necessario essere [un amministratore globale](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)ed è necessario acconsentire esplicitamente per raccogliere questi dati per l'organizzazione. Per acconsentire esplicitamente, è necessario visitare la scheda Report o i log di controllo nel portale di Azure almeno una volta. Fino ad allora, i dati non vengono raccolti per l'organizzazione.

I registri di controllo per la registrazione e la reimpostazione della password sono disponibili per 30 giorni. Se il controllo della sicurezza all'interno dell'azienda richiede una conservazione più lunga, i log devono essere esportati e utilizzati in uno strumento SIEM, ad esempio Azure Sentinel, Splunk o ArcSight.If security auditing within your corporation requires longer retention, the logs need to be exported and consumed into a SIEM tool such as [Azure Sentinel,](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)Splunk, or ArcSight.

![Schermata di creazione di report SSPR](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Metodi di autenticazione - Utilizzo e informazioni dettagliate

[L'utilizzo e le informazioni dettagliate](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) consentono di comprendere il funzionamento dei metodi di autenticazione per funzionalità come Azure MFA e SSPR nell'organizzazione. Questa funzionalità di reporting fornisce all'organizzazione i mezzi per comprendere quali metodi registrarsi e come usarli.

### <a name="troubleshoot"></a>Risolvere problemi

* Fare riferimento a Risolvere i problemi di [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Seguire le [domande frequenti sulla gestione delle password](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Documentazione utile

* [Cosa si intende per metodi di autenticazione?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Come funziona: reimpostazione della password self-service di Azure AD?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Personalizzare la funzionalità di Azure AD per la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Restrizioni e criteri password in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Che cos'è il writeback delle password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a distribuire SSPR, vedere Abilitare la reimpostazione della password self-service di Azure ADTo get started deploying SSPR, see [Enable Azure AD self-service password reset](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-sspr.md)

* [Valutare la possibilità di implementare la protezione con password di Azure ADConsider implementing Azure AD password protection](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Valutare la possibilità di implementare il blocco intelligente di Azure ADConsider implementing Azure AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)