---
title: Considerazioni sulla distribuzione per Azure Active Directory la reimpostazione della password self-service
description: Informazioni sulle considerazioni e sulla strategia di distribuzione per una corretta implementazione di Azure AD la reimpostazione della password self-service
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
ms.openlocfilehash: 8bec28b98a8d2640b5a8034569d49077ce6b4177
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450990"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Pianificare un Azure Active Directory la distribuzione della reimpostazione della password self-service

> [!IMPORTANT]
> Questo piano di distribuzione offre linee guida e procedure consigliate per la distribuzione di Azure AD la reimpostazione della password self-service (SSPR).
>
> **Se sei e l'utente finale devono tornare al tuo account, vai a [https://aka.ms/sspr](https://aka.ms/sspr) **.

La [reimpostazione della password self-service (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) è una funzionalità di Azure Active Directory (ad) che consente agli utenti di reimpostare le password senza contattare il personale IT per assistenza. Gli utenti possono sbloccarsi rapidamente e continuare a lavorare indipendentemente dalla loro posizione o ora del giorno. Consentendo ai dipendenti di sbloccare autonomamente, l'organizzazione può ridurre il tempo non produttivo e i costi di supporto elevati per i problemi più comuni relativi alla password.

SSPR offre le funzionalità principali seguenti:

* Self-Service consente agli utenti finali di reimpostare le password scadute o non scadute senza rivolgersi a un amministratore o al supporto tecnico.
* Il [writeback delle password](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) consente la gestione delle password locali e la risoluzione del blocco degli account tramite il cloud.
* I report delle attività di gestione delle password forniscono agli amministratori informazioni dettagliate sulle attività di registrazione e reimpostazione della password che si verificano nell'organizzazione

Questa guida alla distribuzione Mostra come pianificare e quindi testare un'implementazione di SSPR.

Per visualizzare rapidamente SSPR in azione e quindi tornare a comprendere ulteriori considerazioni sulla distribuzione:

> [!div class="nextstepaction"]
> [Abilitare la reimpostazione della password self-service (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Informazioni su SSPR

Altre informazioni su SSPR. Scopri [come funziona: Azure ad la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Vantaggi principali

I vantaggi principali dell'abilitazione di SSPR sono i seguenti:

* **Gestisci i costi**. SSPR riduce i costi di supporto IT consentendo agli utenti di reimpostare le password in modo autonomo. Consente inoltre di ridurre il costo del tempo perso a causa di password e blocchi persi. 

* **Esperienza utente intuitiva**. Fornisce un processo intuitivo di registrazione utente monouso che consente agli utenti di reimpostare le password e sbloccare gli account su richiesta da qualsiasi dispositivo o posizione. SSPR consente agli utenti di tornare al lavoro più rapidamente ed essere più produttivi.

* **Flessibilità e sicurezza**. SSPR consente alle aziende di accedere alla sicurezza e alla flessibilità fornite da una piattaforma cloud. Gli amministratori possono modificare le impostazioni per soddisfare i nuovi requisiti di sicurezza e distribuire queste modifiche agli utenti senza compromettere l'accesso.

* **Controllo affidabile e rilevamento dell'utilizzo**. Un'organizzazione può garantire che i sistemi aziendali rimangano protetti mentre gli utenti reimpostano le proprie password. I log di controllo affidabili includono informazioni di ogni passaggio del processo di reimpostazione della password. Questi log sono disponibili da un'API e consentono all'utente di importare i dati in un sistema di monitoraggio degli eventi e degli eventi imprevisti della sicurezza (SIEM).

### <a name="licensing"></a>Gestione licenze

Azure Active Directory viene concessa in licenza per ogni utente, il che significa che ogni utente richiede una licenza appropriata per le funzionalità che usano. Si consiglia di usare licenze basate sui gruppi per SSPR. 

Per confrontare le edizioni e le funzionalità e abilitare le licenze basate su utenti e gruppi, vedere [requisiti di licenza per la reimpostazione della password self-service Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Per ulteriori informazioni sui prezzi, vedere [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Prerequisiti

* Un tenant di Azure AD funzionante, con almeno una licenza di valutazione abilitata. Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un account con privilegi di amministratore globale.


### <a name="training-resources"></a>Risorse di formazione

| Risorse| Collegamento e descrizione |
| - | - |
| Video| [Consentire agli utenti di ottenere una migliore scalabilità IT](https://youtu.be/g9RpRnylxS8) 
| |[Che cos'è la reimpostazione della password self-service?](https://youtu.be/hc97Yx5PJiM)|
| |[Distribuzione della reimpostazione della password self-service](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Come configurare la reimpostazione self-service delle password per gli utenti in Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Procedura [preparare gli utenti a] registrare le informazioni di sicurezza per Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Corsi online|[Gestione delle identità in Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) USA SSPR per offrire agli utenti un'esperienza moderna e protetta. Vedere in particolare il modulo "[managing Azure Active Directory Users and groups](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Corsi a pagamento Pluralsight |[Problemi di gestione delle identità e degli accessi](https://www.pluralsight.com/courses/identity-access-management-issues) Informazioni su IAM e sui problemi di sicurezza da tenere presenti nell'organizzazione. Vedere in particolare il modulo "altri metodi di autenticazione".|
| |[Introduzione con Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Informazioni sulle procedure consigliate per estendere le risorse locali al cloud in modo da consentire l'autenticazione, l'autorizzazione, la crittografia e un'esperienza mobile protetta. Vedere in particolare il modulo "configurazione delle funzionalità avanzate del Microsoft Azure Active Directory Premium".
|Esercitazioni |[Completa un'Azure AD implementazione del progetto pilota per la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Abilitazione del writeback delle password](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Azure AD la reimpostazione della password dalla schermata di accesso per Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Domande frequenti|[Domande frequenti sulla gestione delle password](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Architettura della soluzione

Nell'esempio seguente viene descritta l'architettura della soluzione di reimpostazione della password per gli ambienti ibridi comuni.

![diagramma dell'architettura della soluzione](./media/howto-sspr-deployment//solutions-architecture.png)

Descrizione del flusso di lavoro

Per reimpostare la password, gli utenti passano al [portale di reimpostazione della password](https://aka.ms/sspr). Devono verificare il metodo o i metodi di autenticazione registrati in precedenza per dimostrare la propria identità. Se la password viene reimpostata correttamente, inizia il processo di reimpostazione.

* Per gli utenti solo cloud, SSPR archivia la nuova password in Azure AD. 

* Per gli utenti ibridi, SSPR esegue la scrittura della password nel Active Directory locale tramite il servizio Azure AD Connect. 

Nota: per gli utenti con [sincronizzazione dell'hash delle password (pH)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) disabilitato, SSPR archivia le password solo nel Active Directory locale.

### <a name="best-practices"></a>Procedure consigliate

È possibile consentire agli utenti di effettuare rapidamente la registrazione distribuendo SSPR insieme a un'altra applicazione o servizio comune nell'organizzazione. Questa azione genererà un volume elevato di accessi e guiderà la registrazione.

Prima di distribuire SSPR, è possibile scegliere di determinare il numero e il costo medio di ogni chiamata di reimpostazione della password. È possibile usare questa distribuzione post di dati per mostrare il valore che SSPR sta portando all'organizzazione.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Abilita la registrazione combinata per SSPR e multi-factor authentication

Microsoft consiglia alle organizzazioni di abilitare l'esperienza di registrazione combinata per SSPR e la funzionalità di autenticazione a più fattori. Quando si abilita questa esperienza di registrazione combinata, gli utenti devono solo selezionare le informazioni di registrazione una sola volta per abilitare entrambe le funzionalità.

L'esperienza di registrazione combinata non richiede alle organizzazioni di abilitare sia SSPR che Azure Multi-Factor Authentication. La registrazione combinata consente alle organizzazioni di migliorare l'esperienza utente. Per altre informazioni, vedere [registrazione di informazioni di sicurezza combinate](concept-registration-mfa-sspr-combined.md) .

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Prendere in considerazione le esigenze organizzative quando si determina la strategia per la distribuzione nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere lo fanno a causa di una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](https://aka.ms/deploymentplans) e che i ruoli della parte interessata nel progetto siano ben comprensibili documentando gli stakeholder e i rispettivi input e responsabilità del progetto.

#### <a name="required-administrator-roles"></a>Ruoli di amministratore obbligatori


| Ruolo aziendale/persona| Ruolo Azure AD (se necessario) |
| - | - |
| Supporto tecnico di livello 1| Amministratore password |
| Supporto tecnico di livello 2| Amministratore degli utenti |
| Amministratore di SSPR| Amministratore globale |


### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. È necessario comunicare in modo proattivo con gli utenti in che modo cambiano l'esperienza, quando verrà modificata e come ottenere supporto se si verificano problemi. Esaminare i [materiali di implementazione della reimpostazione della password self-service nell'area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56768) per idee su come pianificare la strategia di comunicazione dell'utente finale.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

È consigliabile che la configurazione iniziale di SSPR sia in un ambiente di test. Iniziare con un gruppo pilota abilitando SSPR per un subset di utenti dell'organizzazione. Vedere le [procedure consigliate per un progetto pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Per creare un gruppo, vedere come [creare un gruppo e aggiungere membri in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Configurazione del piano

Le impostazioni seguenti sono necessarie per abilitare SSPR insieme ai valori consigliati.

| Area | Impostazione | valore |
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

### <a name="sspr-properties"></a>Proprietà di SSPR

Quando si Abilita SSPR, scegliere un gruppo di sicurezza appropriato nell'ambiente pilota.

* Per applicare la registrazione SSPR per tutti gli utenti, è consigliabile usare l'opzione **All** .
* In caso contrario, selezionare il Azure AD o il gruppo di sicurezza di Active Directory appropriato.

### <a name="authentication-methods"></a>Metodi di autenticazione

Quando SSPR è abilitato, gli utenti possono reimpostare la password solo se hanno dati presenti nei metodi di autenticazione abilitati dall'amministratore. I metodi includono telefono, notifica dell'app Authenticator, domande di sicurezza e così via. Per altre informazioni, vedere [che cosa sono i metodi di autenticazione?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

È consigliabile usare le impostazioni del metodo di autenticazione seguenti:

* Impostare i **metodi di autenticazione necessari per la registrazione** ad almeno un numero maggiore del numero necessario per la reimpostazione. Consentire a più autenticazioni offre agli utenti la flessibilità necessaria per la reimpostazione.

* Impostare il **numero di metodi necessari per la reimpostazione** a un livello appropriato per l'organizzazione. Uno richiede il minor attrito, mentre due può aumentare il comportamento di sicurezza. 

Nota: è necessario che l'utente disponga dei metodi di autenticazione configurati nei [criteri e nelle restrizioni per le password in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Impostazioni di registrazione

Impostare **Richiedi agli utenti di registrarsi all'accesso** a **Sì**. Questa impostazione richiede agli utenti di registrarsi all'accesso, assicurandosi che tutti gli utenti siano protetti.

Impostare il **numero di giorni prima che agli utenti venga richiesto di riconfermare le informazioni di autenticazione** a un intervallo compreso tra **90** e **180** giorni, a meno che l'organizzazione non abbia bisogno di un intervallo di tempo più breve.

### <a name="notifications-settings"></a>Impostazioni notifiche

Configurare la reimpostazione della **password invia notifiche agli utenti** e **Invia notifiche a tutti gli amministratori quando altri amministratori reimpostano la propria password** su **Sì**. Se **si seleziona Sì, si** aumenta la sicurezza garantendo che gli utenti siano consapevoli della reimpostazione della password. Assicura inoltre che tutti gli amministratori siano consapevoli quando un amministratore modifica una password. Se gli utenti o gli amministratori ricevono una notifica e non hanno avviato la modifica, possono segnalare immediatamente un potenziale problema di sicurezza.

### <a name="customization-settings"></a>Impostazioni di personalizzazione

È fondamentale personalizzare l'URL o l'indirizzo di posta elettronica del supporto tecnico per assicurarsi che gli utenti che riscontrano problemi possano ottenere assistenza immediata. Impostare questa opzione su un indirizzo di posta elettronica o una pagina Web del supporto tecnico comune con cui gli utenti hanno familiarità. 

Per altre informazioni, vedere [personalizzare la funzionalità di Azure ad per la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Writeback password

Il **writeback delle password** è abilitato con [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) e scrive le reimpostazioni della password nel cloud di nuovo in una directory locale esistente in tempo reale. Per ulteriori informazioni, vedere [che cos'è il writeback delle password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Sono consigliate le impostazioni seguenti:

* Verificare che **il writeback delle password in Active Directory locale** sia impostato su **Sì**. 
* Impostare l' **opzione Consenti agli utenti di sbloccare l'account senza reimpostare la password** su **Sì**.

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password.

### <a name="administrator-password-setting"></a>Impostazione password amministratore

Gli account amministratore dispongono di autorizzazioni elevate. Gli amministratori di dominio o dell'organizzazione locale non possono reimpostare le password tramite SSPR. Gli account amministratore locale presentano le restrizioni seguenti:

* può solo modificare la password nell'ambiente locale.
* non può mai usare le domande e le risposte segrete come metodo per reimpostare la password.

Si consiglia di non sincronizzare gli account amministratori Active Directory locali con Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Ambienti con più sistemi di gestione delle identità

Alcuni ambienti hanno più sistemi di gestione delle identità. I gestori di identità locali come Oracle AM e SiteMinder richiedono la sincronizzazione con Active Directory per le password. A tale scopo, è possibile usare uno strumento come il servizio di notifica di modifica della password (PCNS) con Microsoft Identity Manager (MIM). Per trovare informazioni su questo scenario più complesso, vedere l'articolo [distribuire il servizio di notifica di modifica della password di MIM in un controller di dominio](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Pianificare i test e il supporto

In ogni fase della distribuzione da gruppi pilota iniziali a livello di organizzazione, assicurarsi che i risultati siano quelli previsti.

### <a name="plan-testing"></a>Test del piano

Per assicurarsi che la distribuzione funzioni come previsto, pianificare un set di test case per convalidare l'implementazione. Per valutare i test case, è necessario un utente di test non amministratore con una password. Se è necessario creare un utente, vedere [aggiungere nuovi utenti a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

La tabella seguente include utili scenari di test che è possibile usare per documentare i risultati previsti dalle organizzazioni in base ai criteri.
<br>


| Business case| Risultati previsti |
| - | - |
| Il portale di SSPR è accessibile dall'interno della rete aziendale| Determinato dall'organizzazione |
| Il portale di SSPR è accessibile dall'esterno della rete aziendale| Determinato dall'organizzazione |
| Reimposta la password utente dal browser quando l'utente non è abilitato per la reimpostazione della password| L'utente non è in grado di accedere al flusso di reimpostazione della password |
| Reimposta la password utente dal browser quando l'utente non ha effettuato la registrazione per la reimpostazione della password| L'utente non è in grado di accedere al flusso di reimpostazione della password |
| L'utente accede quando viene applicato per la registrazione della reimpostazione della password| Richiede all'utente di registrare le informazioni di sicurezza |
| L'utente accede al termine della registrazione per la reimpostazione della password| Richiede all'utente di registrare le informazioni di sicurezza |
| Il portale di SSPR è accessibile quando l'utente non dispone di una licenza| Accessibile |
| Reimposta la password dell'utente da Windows 10 Azure AD schermata di blocco del dispositivo aggiunta o ibrida Azure AD| L'utente può reimpostare la password |
| I dati di registrazione e di utilizzo di SSPR sono disponibili per gli amministratori quasi in tempo reale| È disponibile tramite i log di controllo |

È anche possibile fare riferimento a [completare un Azure ad roll di reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). In questa esercitazione verrà abilitata un'implementazione pilota di SSPR nell'organizzazione e verrà eseguito il test con un account non amministratore.

### <a name="plan-support"></a>Piano di supporto

Anche se SSPR non crea in genere problemi con gli utenti, è importante preparare il personale di supporto per risolvere i problemi che possono verificarsi. Anche se un amministratore può reimpostare la password per gli utenti finali tramite il portale di Azure AD, è preferibile risolvere il problema tramite un processo di supporto self-service.

Per consentire il successo del team di supporto, è possibile creare domande frequenti in base alle domande ricevute dagli utenti. Di seguito sono disponibili alcuni esempi:

| Scenari| Descrizione |
| - | - |
| Non sono disponibili metodi di autenticazione registrati per l'utente| Un utente sta tentando di reimpostare la password, ma non dispone di alcun metodo di autenticazione registrato disponibile (ad esempio, ha lasciato il telefono cellulare a casa e non può accedere alla posta elettronica) |
| L'utente non riceve un SMS o una chiamata sul proprio ufficio o telefono cellulare| Un utente sta provando a verificare la propria identità tramite il testo o la chiamata ma non riceve un testo/chiamata. |
| L'utente non può accedere al portale di reimpostazione della password| Un utente desidera reimpostare la password, ma non è abilitata per la reimpostazione della password e non può accedere alla pagina per aggiornare le password. |
| L'utente non può impostare una nuova password| Un utente completa la verifica durante il flusso di reimpostazione della password, ma non può impostare una nuova password. |
| L'utente non visualizza un collegamento per la reimpostazione della password in un dispositivo Windows 10| Un utente sta tentando di reimpostare la password dalla schermata di blocco di Windows 10, ma il dispositivo non è stato aggiunto a Azure AD oppure i criteri dei dispositivi di Intune non sono abilitati |

### <a name="plan-rollback"></a>Rollback del piano

Per eseguire il rollback della distribuzione:

* per un singolo utente, rimuovere l'utente dal gruppo di sicurezza 

* per un gruppo, rimuovere il gruppo dalla configurazione SSPR

* Per tutti gli utenti, disabilitare SSPR per il tenant Azure AD

## <a name="deploy-sspr"></a>Distribuire SSPR

Prima della distribuzione, assicurarsi di aver eseguito le operazioni seguenti:

1. Creazione e inizio dell'esecuzione del [piano di comunicazione](#plan-communications).

1. Sono state determinate le [impostazioni di configurazione](#plan-configuration)appropriate.

1. Identificazione degli utenti e dei gruppi per gli ambienti [pilota](#plan-a-pilot) e di produzione.

1. [Impostazioni di configurazione determinate](#plan-configuration) per la registrazione e la gestione self-service.

1. [Configurare il writeback delle password](#password-writeback) se si dispone di un ambiente ibrido.


**A questo punto si è pronti per la distribuzione di SSPR.**

Vedere [abilitare la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) per istruzioni dettagliate sulla configurazione delle aree seguenti.

1. [Metodi di autenticazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Impostazioni di registrazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Impostazioni notifiche](#notifications-settings)

1. [Impostazioni di personalizzazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Integrazione locale](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Abilitare SSPR in Windows
Per i computer che eseguono Windows 7, 8, 8,1 e 10 è possibile [consentire agli utenti di reimpostare la password nella schermata di accesso di Windows](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Gestisci SSPR

Azure AD possibile fornire informazioni aggiuntive sulle prestazioni di SSPR tramite controlli e report.

### <a name="password-management-activity-reports"></a>Report attività di gestione delle password 

È possibile usare i report predefiniti su portale di Azure per misurare le prestazioni di SSPR. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate. Per ulteriori informazioni, vedere [la pagina relativa alle opzioni di Reporting per Azure ad gestione delle password](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  È necessario essere [un amministratore globale](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)ed è necessario acconsentire esplicitamente alla raccolta dei dati per l'organizzazione. Per acconsentire esplicitamente, è necessario visitare la scheda report o i log di controllo nel portale di Azure almeno una volta. Fino a quel momento, i dati non vengono raccolti per l'organizzazione.

I log di controllo per la registrazione e la reimpostazione della password sono disponibili per 30 giorni. Se il controllo della sicurezza all'interno dell'azienda richiede un periodo di conservazione più lungo, i log devono essere esportati e utilizzati in uno strumento SIEM, ad esempio [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), Splunk o ArcSight.

![Schermata per la creazione di report SSPR](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Metodi di autenticazione-uso e informazioni dettagliate

L' [utilizzo e le informazioni dettagliate](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) consentono di comprendere in che modo i metodi di autenticazione per funzionalità come Azure multi-factor authentication e SSPR sono in esecuzione nell'organizzazione. Questa funzionalità di creazione di report consente all'organizzazione di individuare i metodi di registrazione e il modo in cui utilizzarli.

### <a name="troubleshoot"></a>Risolvere problemi

* Vedere [risolvere i problemi di reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Seguire le [domande frequenti sulla gestione delle password](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Documentazione utile

* [Cosa si intende per metodi di autenticazione?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Come funziona: Azure AD la reimpostazione della password self-service?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Personalizzare la funzionalità di Azure AD per la reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Restrizioni e criteri password in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Che cos'è il writeback delle password?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a distribuire SSPR, vedere [abilitare Azure ad la reimpostazione della password self-service](tutorial-enable-sspr.md)

* [Prendere in considerazione l'implementazione della protezione Azure AD password](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Prendere in considerazione l'implementazione di Azure AD blocco intelligente](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)