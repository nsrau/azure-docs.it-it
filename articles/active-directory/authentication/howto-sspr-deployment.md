---
title: Considerazioni sulla distribuzione della reimpostazione della password self-service di Azure Active Directory
description: Informazioni sulle considerazioni e sulla strategia di distribuzione per una corretta implementazione della reimpostazione della password self-service di Azure AD
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
ms.openlocfilehash: a56f7248d5782b63befc55c4215360e0f5cb52b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84338567"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Pianificare una distribuzione della reimpostazione della password self-service di Azure Active Directory

> [!IMPORTANT]
> Questo piano di distribuzione illustra le linee guida e le procedure consigliate per la distribuzione della reimpostazione della password self-service di Azure AD.
>
> **Se si è un utente finale ed è necessario tornare al proprio account, passare a [https://aka.ms/sspr](https://aka.ms/sspr) **.

La [reimpostazione della password self-service](https://www.youtube.com/watch?v=tnb2Qf4hTP8) è una funzionalità di Azure Active Directory (AD) che consente agli utenti di reimpostare le proprie password senza l'assistenza del personale IT. Gli utenti possono sbloccare l'account rapidamente e continuare a lavorare indipendentemente da dove si trovano o dall'ora del giorno. Consentendo ai dipendenti di sbloccare autonomamente l'account, l'organizzazione può ridurre il tempo non produttivo e i costi di supporto elevati legati ai problemi più comuni correlati alle password.

La reimpostazione della password self-service offre le funzionalità chiave seguenti:

* L'operazione in modalità self-service consente agli utenti finali di reimpostare le password scadute o non scadute senza rivolgersi a un amministratore o al supporto tecnico per assistenza.
* Il [writeback delle password](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) consente la gestione delle password locali e la risoluzione del blocco dell'account tramite il cloud.
* I report sulle attività di gestione delle password forniscono agli amministratori informazioni dettagliate sulle attività di reimpostazione e registrazione delle password all'interno dell'organizzazione.

Questa guida alla distribuzione illustra come pianificare e quindi testare un'implementazione della reimpostazione della password self-service.

Per vedere rapidamente come funziona la reimpostazione della password self-service e quindi tornare a esaminare ulteriori considerazioni sulla distribuzione:

> [!div class="nextstepaction"]
> [Abilitare la reimpostazione della password self-service](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Informazioni sulla reimpostazione della password self-service

Informazioni sulla reimpostazione della password self-service. Vedere [Funzionamento: Reimpostazione della password self-service di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Vantaggi principali

I principali vantaggi dell'abilitazione della reimpostazione della password self-service sono i seguenti:

* **Gestione dei costi**. La reimpostazione della password self-service riduce i costi di assistenza IT consentendo agli utenti di reimpostare le password in modo autonomo. Consente inoltre di ridurre i costi legati alle perdite di tempo dovute a password perse e blocchi degli account. 

* **Esperienza utente intuitiva**. Viene fornito un processo di registrazione utente occasionale intuitivo che consente agli utenti di reimpostare le password e sbloccare gli account su richiesta da qualsiasi dispositivo o posizione. La reimpostazione della password self-service consente agli utenti di tornare al lavoro più rapidamente ed essere più produttivi.

* **Flessibilità e sicurezza**. La reimpostazione della password self-service offre alle aziende la sicurezza e la flessibilità tipiche di una piattaforma cloud. Gli amministratori possono modificare le impostazioni per soddisfare i nuovi requisiti di sicurezza e implementare queste modifiche senza compromettere l'accesso degli utenti.

* **Controllo e monitoraggio dell'utilizzo avanzati**. Un'organizzazione può garantire che i sistemi aziendali rimangano sicuri anche se gli utenti reimpostano le proprie password. I log di controllo avanzati includono informazioni su ogni passaggio del processo di reimpostazione della password. Questi log sono disponibili tramite un'API e consentono all'utente di importare i dati in un sistema SIEM (Security Incident and Event Monitoring, informazioni di sicurezza e gestione degli eventi) di sua scelta.

### <a name="licensing"></a>Gestione delle licenze

Azure Active Directory prevede un modello di licenza per utente, ovvero per ogni utente è necessaria una licenza appropriata in base alle funzionalità usate. Per la reimpostazione della password self-service è consigliabile usare licenze basate sui gruppi. 

Per confrontare le edizioni e le funzionalità e abilitare le licenze basate sui gruppi o sugli utenti, vedere [Requisiti di licenza per la reimpostazione della password self-service di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Per altre informazioni sui prezzi, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Prerequisiti

* Un tenant di Azure AD funzionante, con almeno una licenza di valutazione abilitata. Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un account con privilegi di amministratore globale.


### <a name="training-resources"></a>Risorse di formazione

| Risorse| Collegamento e descrizione |
| - | - |
| Video| [Supportare gli utenti con una migliore scalabilità IT](https://youtu.be/g9RpRnylxS8) 
| |[Che cos'è la reimpostazione della password self-service?](https://youtu.be/hc97Yx5PJiM)|
| |[Distribuzione della reimpostazione della password self-service](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Come configurare la reimpostazione della password self-service per gli utenti in Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Come preparare gli utenti a registrare le loro informazioni di sicurezza per Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Corsi online|[Managing Identities in Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) (Gestione delle identità in Microsoft Azure Active Directory). Usare la reimpostazione della password self-service per offrire agli utenti un'esperienza moderna e protetta. Vedere in particolare il modulo "[Managing Azure Active Directory Users and Groups](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)" (Gestione di utenti e gruppi di Azure Active Directory). |
|Corsi Pluralsight a pagamento |[The Issues of Identity and Access Management](https://www.pluralsight.com/courses/identity-access-management-issues) (I problemi della gestione delle identità e degli accessi). Informazioni su IAM e sui problemi di sicurezza a cui fare attenzione nell'organizzazione. Vedere in particolare il modulo "Other Authentication Methods" (Altri metodi di autenticazione).|
| |[Getting Started with the Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) (Introduzione a Microsoft Enterprise Mobility Suite). Informazioni sulle procedure consigliate per estendere gli asset locali nel cloud così da consentire autenticazione, autorizzazione, crittografia e sicurezza nell'esperienza con i dispositivi mobili. Vedere in particolare il modulo "Configuring Advanced Features of Microsoft Azure Active Directory Premium" (Configurazione delle funzionalità avanzate di Microsoft Azure Active Directory Premium).
|Esercitazioni |[Completare l'implementazione di un gruppo pilota per la reimpostazione della password self-service di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Abilitazione del writeback delle password](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Reimpostazione della password di Azure AD dalla schermata di accesso per Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Domande frequenti|[Domande frequenti sulla gestione delle password](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Architettura della soluzione

L'esempio seguente descrive l'architettura della soluzione di reimpostazione della password per ambienti ibridi comuni.

![Diagramma dell'architettura della soluzione](./media/howto-sspr-deployment//solutions-architecture.png)

Descrizione del flusso di lavoro

Per reimpostare la password, gli utenti devono usare il [portale di reimpostazione della password](https://aka.ms/sspr). Devono verificare il metodo o i metodi di autenticazione registrati in precedenza per dimostrare la propria identità. Se la password viene reimpostata correttamente, inizia il processo di reimpostazione.

* Per gli utenti solo cloud, la reimpostazione della password self-service archivia la nuova password in Azure AD. 

* Per gli utenti ibridi, la reimpostazione della password self-service esegue il writeback della password nell'istanza di Active Directory locale tramite il servizio Azure AD Connect. 

Nota: per gli utenti che hanno la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) disabilitata, la reimpostazione della password self-service archivia le password solo nell'istanza di Active Directory locale.

### <a name="best-practices"></a>Procedure consigliate

È possibile aiutare gli utenti a eseguire rapidamente la registrazione distribuendo la reimpostazione della password self-service insieme a un altro servizio o applicazione popolare nell'organizzazione. Ciò genererà un volume elevato di accessi e incentiverà la registrazione.

Prima di distribuire la reimpostazione della password self-service, è possibile scegliere di determinare il numero e il costo medio delle chiamate per la reimpostazione della password. È possibile usare questi dati successivamente alla distribuzione per mostrare il valore che la reimpostazione della password self-service offre all'organizzazione.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Abilitare la registrazione combinata per la reimpostazione della password self-service e MFA

Microsoft consiglia alle organizzazioni di abilitare l'esperienza di registrazione combinata per la reimpostazione della password self-service e l'autenticazione a più fattori. Quando si abilita questa esperienza di registrazione combinata, gli utenti devono selezionare le informazioni di registrazione una sola volta per abilitare entrambe le funzionalità.

L'esperienza di registrazione combinata non richiede alle organizzazioni di abilitare sia la reimpostazione della password self-service che Azure Multi-Factor Authentication. La registrazione combinata consente alle organizzazioni di migliorare l'esperienza utente. Per altre informazioni, vedere [Panoramica della registrazione delle informazioni di sicurezza combinata](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Considerare le esigenze organizzative quando si determina la strategia per la distribuzione nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici non hanno successo, in genere la causa è legata alle diverse aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](https://aka.ms/deploymentplans) e che i ruoli degli stakeholder nel progetto vengano compresi in modo esatto, documentando le informazioni sugli stakeholder e sui rispettivi input e responsabilità in merito al progetto.

#### <a name="required-administrator-roles"></a>Ruoli di amministratore obbligatori


| Ruolo aziendale/Utente tipo| Ruolo di Azure AD (se necessario) |
| - | - |
| Supporto tecnico di livello 1| Amministratore password |
| Supporto tecnico di livello 2| Amministratore utenti |
| Amministratore della reimpostazione della password self-service| Amministratore globale |


### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunicare in modo proattivo con gli utenti spiegando come cambierà l'esperienza, quando verrà modificata e come ottenere supporto in caso di problemi. Vedere i [materiali di implementazione della reimpostazione della password self-service nell'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) per idee su come pianificare la strategia di comunicazione con gli utenti finali.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

È consigliabile eseguire la configurazione iniziale della reimpostazione della password self-service in un ambiente di test. Iniziare con un gruppo pilota abilitando la reimpostazione della password self-service per un subset di utenti nell'organizzazione. Vedere [Procedure consigliate per un progetto pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Per creare un gruppo, vedere le informazioni in [Creare un gruppo e aggiungere membri in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Pianificare la configurazione

Le impostazioni seguenti sono necessarie per abilitare la reimpostazione della password self-service con i valori consigliati.

| Area | Impostazione | Valore |
| --- | --- | --- |
| **Proprietà della reimpostazione della password self-service** | Reimpostazione password self-service abilitata | **Gruppo selezionato** per il progetto pilota/**Tutti** per l'ambiente di produzione |
| **Metodi di autenticazione** | Authentication methods required to register (Metodi di autenticazione necessari per la registrazione) | Sempre uno in più di quanto necessario per la reimpostazione |
|   | Authentication methods required to reset (Metodi di autenticazione necessari per la reimpostazione) | Uno o due |
| **Registrazione** | Richiedere agli utenti di registrarsi all'accesso | Sì |
|   | Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione | 90 - 180 giorni |
| **Notifiche** | Inviare notifiche agli utenti al momento della reimpostazione della password | Sì |
|   | Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password | Sì |
| **Personalizzazione** | Personalizza collegamento al supporto tecnico | Sì |
|   | Indirizzo di posta elettronica o URL del supporto tecnico | Sito o indirizzo di posta elettronica del supporto tecnico |
| **Integrazione locale** | Writeback delle password in Active Directory locale | Sì |
|   | Consenti agli utenti di sbloccare gli account senza reimpostare la password | Sì |

### <a name="sspr-properties"></a>Proprietà della reimpostazione della password self-service

Quando si abilita la reimpostazione della password self-service, scegliere un gruppo di sicurezza appropriato nell'ambiente pilota.

* Per applicare la registrazione della reimpostazione della password self-service per tutti gli utenti, usare l'opzione **Tutti**.
* In caso contrario, selezionare il gruppo di sicurezza di AD o Azure AD appropriato.

### <a name="authentication-methods"></a>Metodi di autenticazione

Quando la reimpostazione della password self-service è abilitata, gli utenti possono reimpostare la password solo se dispongono di dati presenti nei metodi di autenticazione abilitati dall'amministratore. I metodi includono telefono, notifica dell'app Authenticator, domande di sicurezza e così via. Per altre informazioni, vedere [Cosa sono i metodi di autenticazione?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

È consigliabile usare le impostazioni dei metodi di autenticazione seguenti:

* Impostare l'opzione **Authentication methods required to register** (Metodi di autenticazione necessari per la registrazione) su un valore di almeno un'unità superiore rispetto al numero di metodi per la reimpostazione. Consentendo più metodi di autenticazione si offre maggiore flessibilità agli utenti quando devono eseguire la reimpostazione.

* Impostare **Numero di metodi da reimpostare** su un livello appropriato per l'organizzazione. Un valore di uno comporta il minor attrito, mentre un valore pari a due può migliorare la postura di sicurezza. 

Nota: l'utente deve avere i metodi di autenticazione configurati come descritto in [Restrizioni e criteri password in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Impostazioni di registrazione

Impostare **Richiedere agli utenti di registrarsi all'accesso** su **Sì**. Questa impostazione richiede agli utenti di registrarsi all'accesso, per assicurare che tutti gli utenti siano protetti.

Impostare **Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** su un valore compreso tra **90** e **180** giorni, a meno che l'organizzazione richieda un intervallo più breve.

### <a name="notifications-settings"></a>Impostazioni delle notifiche

Impostare entrambe le opzioni **Notificare agli utenti le reimpostazioni delle password** e **Notificare agli amministratori quando altri amministratori reimpostano le proprie password** su **Sì**. Selezionando **Sì** per entrambe le opzioni è possibile aumentare la sicurezza, facendo in modo che gli utenti sappiano quando la loro password viene reimpostata. Si assicura inoltre che tutti gli amministratori sappiano quando un amministratore modifica una password. Se gli utenti o gli amministratori ricevono una notifica e non hanno avviato la modifica, possono segnalare immediatamente un potenziale problema di sicurezza.

### <a name="customization-settings"></a>Impostazioni di personalizzazione

È fondamentale personalizzare l'URL o l'indirizzo di posta elettronica del supporto tecnico per assicurarsi che gli utenti che riscontrano problemi possano ottenere assistenza immediata. Impostare questa opzione su un indirizzo di posta elettronica o una pagina Web del supporto tecnico comune con cui gli utenti hanno familiarità. 

Per altre informazioni, vedere [Personalizzare la funzionalità di Azure AD per la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Writeback password

Il **writeback delle password** è una funzionalità abilitata con [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) che consente di riscrivere le reimpostazioni delle password nel cloud in una directory locale esistente in tempo reale. Per altre informazioni, vedere [Che cos'è il writeback delle password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

È consigliabile usare le impostazioni seguenti:

* Assicurarsi che l'opzione **Writeback delle password in Active Directory locale** sia impostata su **Sì**. 
* Impostare l'opzione **Consenti agli utenti di sbloccare gli account senza reimpostare la password** su **Sì**.

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password.

### <a name="administrator-password-setting"></a>Impostazione della password di amministratore

Gli account amministratore hanno autorizzazioni elevate. Gli amministratori di dominio o aziendali locali non possono reimpostare le password tramite la reimpostazione della password self-service. Gli account amministratore locali prevedono le restrizioni seguenti:

* È possibile modificare la password solo nell'ambiente locale.
* Non è mai possibile usare le domande e le risposte segrete come metodo per reimpostare la password.

È consigliabile non sincronizzare gli account amministratore di Active Directory locali con Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambienti con più sistemi di gestione delle identità

Alcuni ambienti presentano più sistemi di gestione delle identità. Gli strumenti di gestione delle identità locali come Oracle AM e SiteMinder richiedono la sincronizzazione con Active Directory per le password. A tale scopo, è possibile usare uno strumento come il servizio di notifica di modifica della password (PCNS, Password Change Notification Service) con Microsoft Identity Manager (MIM). Per trovare informazioni su questo scenario più complesso, vedere l'articolo [Distribuire il servizio di notifica di modifica della password di MIM in un controller di dominio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Pianificare i test e il supporto

In ogni fase della distribuzione, dai gruppi pilota iniziali fino al coinvolgimento dell'intera organizzazione, assicurarsi che i risultati siano quelli previsti.

### <a name="plan-testing"></a>Panificare i test

Per assicurarsi che la distribuzione funzioni come previsto, pianificare un set di test case per convalidare l'implementazione. Per valutare i test case, è necessario un utente di test non amministratore con una password. Se è necessario creare un utente, vedere [Aggiungere nuovi utenti in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

La tabella seguente include utili scenari di test che è possibile usare per documentare i risultati previsti per l'organizzazione in base ai criteri.
<br>


| Caso aziendale| Risultati previsti |
| - | - |
| Il portale di reimpostazione della password self-service è accessibile dall'interno della rete aziendale| Determinati dall'organizzazione |
| Il portale di reimpostazione della password self-service è accessibile dall'esterno della rete aziendale| Determinati dall'organizzazione |
| Reimpostazione della password utente dal browser quando l'utente non è abilitato per la reimpostazione della password| L'utente non può accedere al flusso di reimpostazione della password |
| Reimpostazione della password utente dal browser quando l'utente non ha eseguito la registrazione per la reimpostazione della password| L'utente non può accedere al flusso di reimpostazione della password |
| L'utente accede quando viene richiesta la registrazione per la reimpostazione della password| Viene richiesto all'utente di registrare le informazioni di sicurezza |
| L'utente accede quando la registrazione per la reimpostazione della password è completata| Viene richiesto all'utente di registrare le informazioni di sicurezza |
| Il portale di reimpostazione della password self-service è accessibile quando l'utente non dispone di una licenza| Accessibile |
| Reimpostazione della password utente dalla schermata di blocco di un dispositivo Windows 10 aggiunto ad Azure AD o aggiunto ad Azure AD ibrido| L'utente può reimpostare la password |
| I dati sull'utilizzo e sulla registrazione della reimpostazione della password self-service sono disponibili per gli amministratori quasi in tempo reale| Disponibilità tramite log di controllo |

Vedere anche [Completare l'implementazione di un gruppo pilota per la reimpostazione della password self-service di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). In questa esercitazione viene abilitata un'implementazione pilota della reimpostazione della password self-service nell'organizzazione e la soluzione viene testata con un account non amministratore.

### <a name="plan-support"></a>Pianificare il supporto

Sebbene la reimpostazione della password self-service non crei in genere problemi per gli utenti, è importante che il personale di supporto sia preparato ad affrontare i problemi che possono verificarsi. Anche se un amministratore può reimpostare la password per gli utenti finali tramite il portale di Azure AD, è preferibile risolvere il problema tramite un processo di supporto self-service.

Per consentire il successo del team di supporto, è possibile preparare un documento di domande frequenti in base alle domande ricevute dagli utenti. Di seguito sono disponibili alcuni esempi:

| Scenari| Descrizione |
| - | - |
| L'utente non ha metodi di autenticazione registrati disponibili| Un utente sta cercando di reimpostare la password, ma non dispone di alcun metodo di autenticazione registrato disponibile (ad esempio, ha lasciato il telefono cellulare a casa e non può accedere alla posta elettronica) |
| L'utente non riceve un SMS o una chiamata sul proprio telefono dell'ufficio o cellulare| Un utente sta cercando di verificare la propria identità tramite SMS o telefonata, ma non riceve il messaggio o la chiamata |
| Un utente non può accedere al portale di reimpostazione della password| Un utente vuole reimpostare la password, ma non è abilitato per la reimpostazione e non può accedere alla pagina per l'aggiornamento delle password |
| Un utente non può impostare una nuova password| Un utente completa la verifica durante il flusso di reimpostazione della password, ma non può impostare una nuova password |
| Un utente non visualizza un collegamento per la reimpostazione della password in un dispositivo Windows 10| Un utente sta cercando di reimpostare la password dalla schermata di blocco di Windows 10, ma il dispositivo non è stato aggiunto ad Azure AD oppure i criteri dei dispositivi di Intune non sono abilitati |

### <a name="plan-rollback"></a>Pianificare il rollback

Per eseguire il rollback della distribuzione:

* Per un singolo utente, rimuovere l'utente dal gruppo di sicurezza 

* Per un gruppo, rimuovere il gruppo dalla configurazione della reimpostazione della password self-service

* Per tutti, disabilitare la reimpostazione della password self-service per il tenant Azure AD

## <a name="deploy-sspr"></a>Distribuire la reimpostazione della password self-service

Prima della distribuzione, assicurarsi di aver eseguito queste operazioni:

1. Creazione e avvio dell'esecuzione del [piano di comunicazione](#plan-communications).

1. Determinazione delle [impostazioni di configurazione](#plan-configuration) appropriate.

1. Identificazione degli utenti e dei gruppi per gli ambienti [pilota](#plan-a-pilot) e di produzione.

1. [Determinazione delle impostazioni di configurazione](#plan-configuration) per la registrazione e la gestione self-service.

1. [Configurazione del writeback delle password](#password-writeback) se si usa un ambiente ibrido.


**È ora possibile distribuire la reimpostazione della password self-service**

Vedere [Abilitare la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) per istruzioni dettagliate complete sulla configurazione delle aree seguenti.

1. [Metodi di autenticazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Impostazioni di registrazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Impostazioni delle notifiche](#notifications-settings)

1. [Impostazioni di personalizzazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Integrazione locale](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Abilitare la reimpostazione della password self-service in Windows
Per i computer che eseguono Windows 7, 8, 8.1 e 10 è possibile [consentire agli utenti di reimpostare la password nella schermata di accesso di Windows](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Gestire la reimpostazione della password self-service

Azure AD può fornire informazioni aggiuntive sulle prestazioni della reimpostazione della password self-service tramite controlli e report.

### <a name="password-management-activity-reports"></a>Report sulle attività di gestione delle password 

È possibile usare i report predefiniti disponibili nel portale di Azure per misurare le prestazioni della reimpostazione della password self-service. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate. Per altre informazioni, vedere [Opzioni di creazione di report per la gestione delle password di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  È necessario essere [un amministratore globale](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) e acconsentire esplicitamente alla raccolta di questi dati per l'organizzazione. A tale scopo, è necessario visualizzare almeno una volta la scheda Report o i log di controllo nel portale di Azure. Fino a che non si eseguono queste operazioni, i dati per l'organizzazione non vengono raccolti.

I log di controllo per la registrazione e la reimpostazione della password sono disponibili per 30 giorni. Se il controllo di sicurezza all'interno dell'azienda richiede un periodo di conservazione più lungo, i log devono essere esportati e utilizzati in uno strumento SIEM, ad esempio [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), Splunk o ArcSight.

![Screenshot dei report di reimpostazione della password self-service](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Metodi di autenticazione - Utilizzo e informazioni dettagliate

La pagina [Utilizzo e informazioni dettagliate](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) consente di comprendere come funzionano i metodi di autenticazione per funzionalità come Azure MFA e la reimpostazione della password self-service nell'organizzazione. Questa funzionalità di creazione di report consente all'organizzazione di capire quali metodi registrare e come usarli.

### <a name="troubleshoot"></a>Risolvere problemi

* Vedere [Risolvere i problemi di reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Vedere [Domande frequenti sulla gestione delle password](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Documentazione utile

* [Cosa si intende per metodi di autenticazione?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Funzionamento: Reimpostazione della password self-service di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Personalizzare la funzionalità di Azure AD per la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Restrizioni e criteri password in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Che cos'è il writeback delle password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla distribuzione della reimpostazione della password self-service, vedere [Abilitare la reimpostazione della password self-service di Azure AD](tutorial-enable-sspr.md)

* [Considerare l'implementazione delle password di protezione di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Considerare l'implementazione del blocco intelligente di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)
