---
title: Pianificare la distribuzione dell'accesso condizionale in Microsoft Azure Active Directory
description: Informazioni su come progettare i criteri di accesso condizionale e distribuirli in modo efficace nell'organizzazione.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 06/09/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ef10f51f058739b0e545d341b77af4313dd9e97
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992867"
---
# <a name="plan-a-conditional-access-deployment"></a>Pianificare una distribuzione dell'accesso condizionale

Pianificare la distribuzione dell'accesso condizionale è fondamentale affinché la strategia di accesso per le applicazioni e le risorse dell'organizzazione sia quella desiderata.

In un mondo dominato dai dispositivi mobili e basato sul cloud, gli utenti accedono alle risorse dell'organizzazione da qualsiasi luogo con una serie di dispositivi e applicazioni. Pertanto, non è più sufficiente concentrarsi solo su chi può accedere a una risorsa. È anche necessario considerare la posizione in cui si trova l'utente, il dispositivo usato, la risorsa a cui accede e altro ancora. 

L'accesso condizionale (CA) di Azure Active Directory (Azure AD) analizza segnali quali l'utente, il dispositivo e la posizione per automatizzare le decisioni e applicare i criteri di accesso dell'organizzazione per le risorse. È possibile usare i criteri di accesso condizionale per applicare controlli di accesso quali l'autenticazione a più fattori (MFA). I criteri di accesso condizionale consentono di richiedere agli utenti di usare l'autenticazione a più fattori quando necessario per la sicurezza e di evitare di ostacolare gli utenti quando non necessario.

![Panoramica dell'accesso condizionale](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Microsoft fornisce criteri condizionali standard denominati [impostazioni predefinite per la sicurezza](../fundamentals/concept-fundamentals-security-defaults.md) che assicurano un livello di sicurezza di base. Tuttavia, l'organizzazione potrebbe richiedere una maggiore flessibilità rispetto a quella offerta dalle impostazioni predefinite per la sicurezza. È possibile utilizzare l'accesso condizionale per personalizzate le impostazioni predefinite per la sicurezza con maggiore granularità e per configurare nuovi criteri per la sicurezza in base alle esigenze.

## <a name="learn"></a>Informazioni

Prima di iniziare, assicurarsi di comprendere il funzionamento dell'[accesso condizionale](overview.md) e quando usarlo.

### <a name="benefits"></a>Vantaggi

I vantaggi della distribuzione dell'accesso condizionale sono:

* Aumento della produttività. Interrupt degli utenti solo con una condizione di accesso, ad esempio l'autenticazione a più fattori quando richiesta da uno o più segnali. I criteri di accesso condizionale consentono di controllare quando agli utenti viene richiesta l'autenticazione a più fattori, quando l'accesso è bloccato e quando è necessario usare un dispositivo attendibile.

* per gestire i rischi. L'automazione della valutazione dei rischi con le condizioni dei criteri significa che gli accessi a rischio sono identificati immediatamente e vengono risolti o bloccati. L'accoppiamento dell'accesso condizionale con [Identity Protection](../identity-protection/overview-identity-protection.md), che rileva anomalie ed eventi sospetti, consente di definire le destinazioni quando l'accesso alle risorse è bloccato o controllato. 

* Gestione della conformità e della governance. L'accesso condizionale consente di controllare l'accesso alle applicazioni, di presentare le condizioni per l'utilizzo per il consenso e di limitare l'accesso in base ai criteri di conformità.

* Gestione dei costi. Il trasferimento dei criteri di accesso a Azure AD riduce la dipendenza da soluzioni personalizzate o locali per l'accesso condizionale e i relativi costi di infrastruttura.

### <a name="license-requirements"></a>Requisiti relativi alle licenze

Vedere [Requisiti di licenza per l'uso dell'accesso condizionale](overview.md).

Se sono richieste funzionalità aggiuntive, potrebbero anche essere necessarie le licenze correlate. Per altre informazioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Prerequisiti

* Un tenant di Azure AD funzionante con una licenza di Azure AD Premium o di valutazione. Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un account con privilegi di amministratore dell'accesso condizionale.

* Un utente non amministratore con una password conosciuta, ad esempio testuser. Se è necessario creare un utente, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

* Un gruppo di cui l'utente non amministratore è membro. Se è necessario creare un gruppo, vedere [Creare un gruppo di base e aggiungere membri in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

### <a name="training-resources"></a>Training delle risorse

Le seguenti risorse possono essere utili per acquisire familiarità con l'accesso condizionale:


#### <a name="videos"></a>Video

* [Informazioni sull'accesso condizionale](https://youtu.be/ffMAw2IVO7A)
* [Distribuzione dell'accesso condizionale](https://youtu.be/c_izIRNJNuk)
* [Distribuzione dei criteri dell'accesso condizionale agli utenti finali](https://youtu.be/0_Fze7Zpyvc)
* [Accesso condizionale con controlli dispositivo](https://youtu.be/NcONUf-jeS4)
* [Accesso condizionale con Microsoft Azure Multi-Factor Authentication](https://youtu.be/Tbc-SU97G-w)
* [Accesso condizionale in Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>Corsi online su Pluralsight

* [Progettazione di Identity Management in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Progettazione dell'autenticazione per Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Progettazione dell'autorizzazione per Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Considerare le esigenze organizzative quando si determina la strategia per la distribuzione nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere la causa sono le diverse aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi inconvenienti, [assicurarsi di coinvolgere gli stakeholder appropriati](../fundamentals/active-directory-deployment-plans.md) e che i ruoli del progetto siano chiari.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunica in modo proattivo con gli utenti su come cambierà l'esperienza, quando verrà modificata, e su come ottenere supporto in caso di problemi.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

Quando i nuovi criteri sono pronti per l'ambiente, distribuirli in più fasi nell'ambiente di produzione. Applicare innanzitutto un criterio a un piccolo set di utenti in un ambiente di test e verificare se il criterio si comporta come previsto. Consultare [Procedure consigliate per un progetto pilota](../fundamentals/active-directory-deployment-plans.md).

> [!NOTE]
> Per implementare nuovi criteri non specifici per gli amministratori, escludere tutti gli amministratori. In questo modo gli amministratori possono comunque accedere ai criteri e apportare modifiche o revocarli in caso di impatto significativo. Convalidare sempre i criteri con gruppi di utenti più piccoli prima di applicare i criteri a tutti gli utenti.

## <a name="understand-ca-policy-components"></a>Informazioni sui componenti dei criteri di accesso condizionale
I criteri CA sono istruzioni if-then: se viene soddisfatta un'assegnazione, applicare tali controlli di accesso.

Quando si configurano i criteri dell'accesso condizionale, le condizioni sono denominate *assegnazioni*. I criteri di accesso condizionale consentono di applicare i controlli di accesso alle applicazioni dell'organizzazione in base a determinate assegnazioni.


Per altre informazioni, vedere [Creazione di un criterio di accesso condizionale](concept-conditional-access-policies.md).

![Schermata Crea criterio](media/plan-conditional-access/create-policy.png)

Le [assegnazioni](concept-conditional-access-policies.md#assignments) definiscono

* [Utenti e gruppi](concept-conditional-access-users-groups.md) interessati dai criteri

* [App Cloud o azioni](concept-conditional-access-cloud-apps.md) a cui verranno applicati i criteri 

* [Condizioni](concept-conditional-access-conditions.md) in base alle quali verranno applicati i criteri.

Le impostazioni dei [controlli di accesso](concept-conditional-access-policies.md) stabiliscono come applicare un criterio:

* [Concedere o bloccare l'accesso](concept-conditional-access-grant.md) alle applicazioni cloud.

* I [controlli della sessione](concept-conditional-access-session.md) consentono di limitare l'esperienza in applicazioni cloud specifiche.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Porre le domande corrette per creare i criteri

I criteri rispondono alle domande sugli utenti che devono accedere alle risorse, sulle risorse a cui devono accedere e sulle condizioni. I criteri possono essere progettati per concedere o per bloccare l'accesso. Assicurarsi di porre le domande corrette sulle operazioni che i criteri tentano di eseguire. 

Documentare le risposte alle domande relative a ogni criterio prima della creazione. 

#### <a name="common-questions-about-assignments"></a>Domande frequenti sulle assegnazioni

[Utenti e gruppi](concept-conditional-access-users-groups.md)

* Quali utenti e gruppi verranno inclusi o esclusi dal criterio?

* Questo criterio include tutti gli utenti, un gruppo specifico di utenti, ruoli della directory o utenti esterni?

[Applicazioni o azioni cloud](concept-conditional-access-cloud-apps.md)

* A quali applicazioni verrà applicato il criterio?

* Quali azioni utente saranno soggette a questo criterio?

[Condizioni](concept-conditional-access-conditions.md)

* Quali piattaforme di dispositivo verranno incluse o escluse dal criterio?

* Quali sono i percorsi attendibili dell'organizzazione?

* Quali percorsi verranno inclusi o esclusi dal criterio?

* Quali tipi di applicazioni client (browser, dispositivi mobili, client desktop, applicazioni con metodi di autenticazione legacy) verranno inclusi o esclusi dal criterio?

* Si dispone di criteri che potrebbero escludere i dispositivi aggiunti ad Azure AD o i dispositivi aggiunti ad Azure AD ibrido dai criteri? 

* Se si usa [Identity Protection](../identity-protection/concept-identity-protection-risks.md), si vuole incorporare la protezione per il rischio di accesso?

#### <a name="common-questions-about-access-controls"></a>Domande frequenti sui controlli di accesso

[Concedere o bloccare](concept-conditional-access-grant.md) 

Si desidera concedere l'accesso alle risorse richiedendo uno o più degli elementi seguenti?

* Richiedere l'autenticazione MFA

* Richiedere che i dispositivi siano contrassegnati come conformi

* Richiedere un dispositivo aggiunto ad Azure AD ibrido

* Richiedere app client approvata

* Richiedere criteri di protezione dell'app

[Controllo della sessione](concept-conditional-access-session.md)

Si desidera applicare uno dei controlli di accesso seguenti nelle applicazioni cloud?

* Usare autorizzazioni imposte dalle applicazioni

* Usare il controllo app per l'accesso condizionale

* Applicare la frequenza di accesso

* Usare la sessione del browser persistente

### <a name="access-token-issuance"></a>Rilascio dei token di accesso

È importante comprendere la modalità di rilascio dei token di accesso. 

![Diagramma di rilascio dei token di accesso](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> Se non è richiesta alcuna assegnazione e nessun criterio CA è attivo, il comportamento predefinito prevede l'emissione di un token di accesso. 

Si consideri, ad esempio, un criterio in cui:

"IF" l'utente è nel Gruppo 1, "THEN" forzare l'autenticazione a più fattori per l'accesso all'app 1.

Se un utente non presente nel Gruppo 1 tenta di accedere all'applicazione, non viene soddisfatta la condizione "if" e viene rilasciato un token. Per escludere utenti esterni al Gruppo 1, è necessario un criterio separato per bloccare tutti gli altri utenti.

## <a name="follow-best-practices"></a>Seguire le procedura consigliate

Il framework di accesso condizionale offre ottima flessibilità di configurazione. Con un'elevata flessibilità, tuttavia, è consigliabile esaminare attentamente ogni criterio di configurazione prima del rilascio per evitare risultati indesiderati.

### <a name="apply-ca-policies-to-every-app"></a>Applicare i criteri di accesso condizionale a tutte le applicazioni

Per impostazione predefinita, i token di accesso vengono rilasciati se una condizione del criterio di accesso condizionale non attiva un controllo di accesso. Assicurarsi che tutte le applicazioni dispongano di almeno un criterio di accesso condizionale applicato

> [!IMPORTANT]
> Prestare molta attenzione nell'uso del blocco e di tutte le applicazioni in un singolo criterio. Questo potrebbe bloccare l'accesso degli amministratori al Portale di amministrazione di Azure e sarebbe impossibile configurare le esclusioni per endpoint importanti, ad esempio Microsoft Graph.

### <a name="minimize-the-number-of-ca-policies"></a>Ridurre al minimo il numero di criteri di accesso condizionale

La creazione di un criterio per ciascuna applicazione non è un'operazione efficiente e rende complicata l'amministrazione. L'accesso condizionale applicherà solo i primi 195 criteri per utente. Si consiglia di analizzare le applicazioni e raggrupparle in applicazioni che presentano gli stessi requisiti di risorse per gli stessi utenti. Se, ad esempio, tutte le app Microsoft 365 o tutte le app HR hanno gli stessi requisiti per gli stessi utenti, creare un singolo criterio e includere tutte le app a cui si applica. 

### <a name="set-up-emergency-access-accounts"></a>Configurare account di accesso di emergenza

Se si configura un criterio in modo errato, potrebbe bloccare l'accesso delle organizzazioni al portale di Azure. È possibile ridurre l'impatto di un blocco accidentale degli accessi degli amministratori creando due o più [account di accesso di emergenza](../users-groups-roles/directory-emergency-access.md) nell'organizzazione.

* Creare un account utente dedicato all'amministrazione dei criteri ed escluso da tutti i criteri.

* Scenario critico per ambienti ibridi:

  * Creare un gruppo di sicurezza locale e sincronizzarlo con Azure AD. Il gruppo di sicurezza deve contenere l'account di amministrazione dei criteri dedicato. 

   * ESENTARE questo gruppo di sicurezza da tutti i criteri di accesso condizionale.

   * Quando si verifica un'interruzione del servizio, aggiungere gli altri amministratori al gruppo locale nel modo appropriato e forzare una sincronizzazione. In questo modo viene attivata l’esenzione dai criteri di accesso condizionale.

### <a name="set-up-report-only-mode"></a>Configurare la modalità solo report

Può essere difficile prevedere il numero e i nomi degli utenti interessati da iniziative comuni di distribuzione, ad esempio:

* blocco dell'autenticazione legacy
* richiesta di autenticazione a più fattori
* implementazione di criteri di rischio di accesso

La [modalità solo report ](concept-conditional-access-report-only.md) consente agli amministratori di valutare l'impatto dei criteri di accesso condizionale prima di abilitarli nell'ambiente in uso.

Informazioni su come [configurare la modalità solo report in un criterio di accesso condizionale](howto-conditional-access-insights-reporting.md).

### <a name="plan-for-disruption"></a>Pianificare l'interruzione

Se per proteggere i sistemi IT ci si basa sul controllo di accesso singolo, ad esempio l'autenticazione a più fattori (MFA) o un unico percorso di rete, si è soggetti a errori di accesso se tale controllo di accesso singolo non è disponibile o non è configurato correttamente. Per ridurre il rischio di blocco durante le interruzioni impreviste, [pianificare strategie](../authentication/concept-resilient-controls.md) da adottare per la propria organizzazione.

### <a name="set-naming-standards-for-your-policies"></a>Impostare gli standard di denominazione per i criteri

Lo standard di denominazione aiuta a individuare i criteri e comprenderne le finalità senza aprirli nel portale di amministrazione di Azure. Si consiglia di denominare il criterio per visualizzare:

* Un numero di sequenza

* Le applicazioni cloud a cui si applica

* Risposta.

* A chi si applica

* Quando si applica (se applicabile)

![Screenshot che Mostra gli standard di denominazione per i criteri.](media/plan-conditional-access/11.png)

**Esempio**: un criterio per richiedere l'autenticazione a più fattori per gli utenti di marketing che accedono all'applicazione Dynamics CRP dalle reti esterne potrebbe essere:

![Standard di denominazione](media/plan-conditional-access/naming-example.png)

Un nome descrittivo consente di ottenere una panoramica dell'implementazione dell'accesso condizionale. Il numero di sequenza è utile se è necessario fare riferimento a un criterio in una conversazione. Se ad esempio si parla al telefono con un altro amministratore, è possibile chiedergli di aprire il criterio CA01 per risolvere un problema.

#### <a name="naming-standards-for-emergency-access-controls"></a>Standard di denominazione per i controlli di accesso di emergenza

Oltre ai criteri attivi, è opportuno implementare anche criteri disabilitati che agiscano come [controlli di accesso resilienti secondari in scenari di emergenza o di interruzione dei servizi](../authentication/concept-resilient-controls.md). Lo standard di denominazione per i criteri di emergenza deve includere:
* ENABLE IN EMERGENCY all'inizio in modo che il nome si distingua dagli altri criteri.

* Il nome dell'interruzione a cui deve essere applicato.

* Un numero di sequenza di ordinamento per consentire all'amministratore di sapere in che ordine devono essere abilitati i criteri.

**Esempio**

Il nome seguente indica che questo è il primo di quattro criteri da abilitare se si verifica un'interruzione dell'autenticazione a più fattori:

EM01 - ENABLE IN EMERGENCY: Interruzione dell'autenticazione a più fattori [1/4]-Exchange SharePoint: Richiesta di aggiunta ad Azure AD ibrido per utenti VIP.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Esclusione dei paesi da cui non si prevede mai un accesso.

Azure Active Directory consente di creare [località denominate](location-condition.md). Creare una località denominata che includa tutti i paesi da cui non si prevede mai che si verifichi un accesso. Creare quindi un criterio per tutte le applicazioni che blocchi l'accesso da tale località denominata. **Assicurarsi di escludere gli amministratori da questi criteri**.

### <a name="plan-your-policy-deployment"></a>Pianificare la distribuzione dei criteri

Quando per l'ambiente sono pronti nuovi criteri, assicurarsi di esaminare ogni criterio prima di rilasciarlo per evitare risultati indesiderati. Per informazioni importanti sul modo in cui vengono applicati i criteri e su come evitare problemi, consultare la documentazione seguente

* [Informazioni utili](best-practices.md)

* [Azioni da evitare](best-practices.md)

## <a name="common-policies"></a>Criteri comuni

Quando si pianifica una soluzione di criterio di accesso condizionale, valutare se è necessario creare criteri per ottenere i seguenti risultati.

* [Richiedere l'autenticazione MFA](#require-mfa)
* [Rispondere ad account potenzialmente compromessi](#respond-to-potentially-compromised-accounts)
* [Richiedere dispositivi gestiti](#require-managed-devices)
* [Richiedi applicazioni client approvate](#require-approved-client-apps)
* [Blocca accesso](#block-access)

### <a name="require-mfa"></a>Richiedere l'autenticazione MFA

I casi d'uso comuni per richiedere l'autenticazione a più fattori:

* [Da parte degli amministratori](howto-conditional-access-policy-admin-mfa.md)

* [A specifiche app](../authentication/tutorial-enable-azure-mfa.md)

* [Per tutti gli utenti](howto-conditional-access-policy-all-users-mfa.md)

* [Da percorsi di rete non considerati attendibili](untrusted-networks.md)

* [Per la gestione di Azure](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Rispondere ad account potenzialmente compromessi

Con i criteri di accesso condizionale, è possibile implementare risposte automatiche per gli accessi da identità potenzialmente compromesse. La probabilità che un account sia stato compromesso viene espressa sotto forma di livelli di rischio. Identity Protection calcola due livelli di rischio: rischio di accesso e rischio utente. Di seguito sono riportati i tre criteri predefiniti che è possibile abilitare.

* [Richiedere agli utenti di registrarsi per l'autenticazione a più fattori](howto-conditional-access-policy-risk.md)

* [Richiedere una modifica della password per gli utenti ad alto rischio](howto-conditional-access-policy-risk.md)

* [Richiedere l'autenticazione a più fattori per gli utenti con rischi di accesso medio o elevato](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>Richiedere dispositivi gestiti

L'ampia diffusione dei dispositivi supportati per l'accesso alle risorse cloud offre notevoli vantaggi in termini di produttività degli utenti. Può tuttavia essere necessario evitare che alcune risorse dell'ambiente risultino accessibili a dispositivi con un livello di protezione sconosciuto. Per le risorse di questo tipo, [richiedere che gli utenti possano accedervi solo tramite un dispositivo gestito](require-managed-devices.md).

### <a name="require-approved-client-apps"></a>Richiedere app client approvate

I dipendenti usano dispositivi mobili sia per le attività personali che per quelle aziendali. Per gli scenari BYOD è necessario decidere se gestire l'intero dispositivo o solo i dati in esso contenuti. Se si gestiscono solo i dati e l'accesso, è possibile [richiedere applicazioni cloud approvate](app-based-conditional-access.md) in grado di proteggere i dati aziendali. È possibile, ad esempio, richiedere l'accesso alla posta elettronica solo tramite Outlook Mobile e non tramite un programma di posta elettronica generico.

### <a name="block-access"></a>Blocca accesso

L'opzione di [blocco di tutti gli accessi](howto-conditional-access-policy-block-access.md) è notevolmente efficace. Può essere usata, ad esempio, durante la migrazione di un'applicazione ad Azure AD quando non è possibile ancora consentire a nessuno di accedervi. Blocca accesso: 

* Esegue l'override di tutte le altre assegnazioni per un utente

* Consente di bloccare l'accesso al tenant da parte dell'intera organizzazione

> [!IMPORTANT]
> Se si crea un criterio per bloccare l'accesso a tutti gli utenti, assicurarsi di escludere gli account di accesso di emergenza e prendere in considerazione l'esclusione di tutti gli amministratori dal criterio.

Altri scenari comuni in cui è possibile bloccare l'accesso agli utenti sono:

* [Blocco di alcuni percorsi di rete](howto-conditional-access-policy-location.md) dall'accesso alle applicazioni cloud. È possibile usare questo criterio per bloccare determinati paesi da cui si sa che non deve provenire traffico.

* Azure AD supporta l'autenticazione legacy. Tuttavia, l'autenticazione legacy non supporta l'autenticazione a più fattori richiesta da molti ambienti per gestire la sicurezza delle identità. In questo caso è possibile [impedire alle applicazioni che usano l'autenticazione legacy](block-legacy-authentication.md) di accedere alle risorse dei tenant.

## <a name="build-and-test-policies"></a>Compilare e testare i criteri

In ogni fase della distribuzione assicurarsi che i risultati siano quelli previsti. 

Quando i nuovi criteri sono pronti, distribuirli in più fasi nell'ambiente di produzione:

* Dare comunicazione delle modifiche agli utenti finali.

* Iniziare con un set ridotto di utenti e verificare che il criterio abbia il comportamento previsto.

* Quando si espande un criterio per includere un maggior numero di utenti, continuare a escludere tutti gli amministratori. Escludere gli amministratori assicura che qualcuno possa comunque accedere a un criterio, se è necessaria una modifica.

* Applicare un criterio a tutti gli utenti solo dopo averlo testato accuratamente. Assicurarsi di disporre di almeno un account amministratore a cui non si applichi il criterio.

### <a name="create-test-users"></a>Creare utenti di test

Creare un set di utenti di test che riflettano gli utenti nell'ambiente di produzione. La creazione di utenti di test consente di verificare che i criteri funzionino come previsto prima di applicarli agli utenti reali e potenzialmente compromettere il loro accesso alle applicazioni e alle risorse.

Alcune organizzazioni hanno tenant di test a questo scopo. Tuttavia, può essere difficile ricreare tutte le condizioni e le app in un tenant di test per verificare completamente i risultati di un criterio.

### <a name="create-a-test-plan"></a>Creare un piano di test

Il piano di test è importante per disporre di un confronto tra i risultati previsti e i risultati effettivi. È sempre necessario definire il risultato atteso prima di eseguire un test. Nella tabella seguente vengono descritti alcuni test case di esempio. Modificare gli scenari e i risultati previsti in base al modo in cui sono configurati i criteri di accesso condizionale.

| Policy| Scenario| Risultato previsto |
| - | - | - |
| [Richiedi autenticazione a più fattori quando non al lavoro](untrusted-networks.md)| Un utente autorizzato accede all'applicazione da un percorso attendibile/dal posto di lavoro| All'utente non viene richiesta l'autenticazione a più fattori |
| [Richiedi autenticazione a più fattori quando non al lavoro](untrusted-networks.md)| Un utente autorizzato accede all'applicazione da un percorso non attendibile/non dal posto di lavoro| All'utente viene richiesta l'autenticazione a più fattori e può eseguire correttamente l'accesso |
| [Richiedi autenticazione a più fattori (per l'amministratore)](../fundamentals/concept-fundamentals-security-defaults.md)| L'amministratore globale accede all'applicazione| All'amministratore viene richiesta l'autenticazione a più fattori |
| [Accessi a rischio](../identity-protection/howto-identity-protection-configure-risk-policies.md)| L'utente accede all'app usando un browser non approvato| All'amministratore viene richiesta l'autenticazione a più fattori |
| [Gestione del dispositivo](require-managed-devices.md)| Un utente autorizzato cerca di accedere da un dispositivo autorizzato| Accesso concesso |
| [Gestione del dispositivo](require-managed-devices.md)| Un utente autorizzato cerca di accedere da un dispositivo non autorizzato| Accesso bloccato |
| [Modifica password per gli utenti a rischio](../identity-protection/howto-identity-protection-configure-risk-policies.md)| Un utente autorizzato cerca di accedere con credenziali compromesse (accesso ad alto rischio)| All'utente viene richiesto di cambiare la password o l'accesso viene bloccato in base al criterio |


### <a name="configure-the-test-policy"></a>Configurare il criterio di test

Nel [portale di Azure](https://portal.azure.com/) configurare i criteri di accesso condizionale in Azure Active Directory > Sicurezza > Accesso condizionale.

Per ulteriori informazioni su come creare i criteri di accesso condizionale, vedere l'esempio seguente: [Criterio di accesso condizionale per richiedere l'autenticazione a più fattori quando un utente accede al portale di Azure](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json). Questo argomento di avvio rapido consente di:

* Acquisire familiarità con l'interfaccia utente

* Ottenere una panoramica del funzionamento dell'accesso condizionale

### <a name="enable-the-policy-in-report-only-mode"></a>Abilitare il criterio in modalità solo report

Per valutare l'effetto del criterio, iniziare abilitandolo in [modalità solo report](concept-conditional-access-report-only.md). I criteri solo report vengono valutati durante l'accesso, ma non vengono applicati i controlli di concessione e di sessione. Dopo aver salvato il criterio in modalità solo report, è possibile osservarne l'effetto sugli accessi in tempo reale nei log di accesso. Nei log di accesso selezionare un evento e passare alla scheda Solo report per visualizzare il risultato di ogni criterio solo report.


![Modalità solo report ](media/plan-conditional-access/report-only-mode.png)

Selezionando il criterio, è anche possibile vedere come le assegnazioni e i controlli di accesso del criterio sono stati valutati usando la schermata Dettagli criteri. Per applicare un criterio a un accesso, è necessario che vengano soddisfatte tutte le assegnazioni configurate. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Comprendere l'effetto dei criteri usando la cartella di lavoro Informazioni dettagliate e report.

È possibile visualizzare l'effetto aggregato dei criteri di accesso condizionale nella cartella di lavoro Informazioni dettagliate e report. Per accedere alla cartella di lavoro, è necessaria una sottoscrizione di Monitoraggio di Azure e sarà necessario [trasmettere i log di accesso a un'area di lavoro Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Simulare gli accessi mediante lo strumento What If

Un altro modo per convalidare i criteri di accesso condizionale consiste nell'usare lo [strumento What If](troubleshoot-conditional-access-what-if.md), che simula i criteri applicabili in circostanze ipotetiche a un utente che effettua l'accesso. Selezionare gli attributi di accesso che si desidera testare, ad esempio, utente, applicazione, piattaforma del dispositivo e posizione, e vedere quali criteri si applicano.

> [!NOTE] 
> Anche se un'esecuzione simulata offre un'impressione valida dell'impatto dei criteri di accesso condizionale, non sostituisce un'effettiva esecuzione dei test.

### <a name="test-your-policy"></a>Verificare i criteri

Eseguire tutti i test nel piano di test con gli utenti di test.

**Assicurarsi di testare i criteri di esclusione di un criterio**. È ad esempio possibile escludere un utente o gruppo da un criterio che richiede l'autenticazione MFA. Verificare se agli utenti esclusi viene richiesta l'autenticazione MFA, perché la combinazione di altri criteri potrebbe richiedere l'autenticazione MFA per questi stessi utenti.

### <a name="roll-back-policies"></a>Eseguire il rollback dei criteri

Nel caso in cui sia necessario eseguire il rollback dei criteri appena implementati, usare una o più delle opzioni seguenti:

* **Disabilitare il criterio.** La disabilitazione di un criterio garantisce che non venga applicato quando un utente tenta di accedere. È sempre possibile tornare indietro e abilitare il criterio quando si vorrà utilizzarlo.

![Abilitare l'immagine del criterio](media/plan-conditional-access/enable-policy.png)

* **Escludere un utente o un gruppo da un criterio.** Se un utente non è in grado di accedere all'applicazione, è possibile scegliere di escluderlo dal criterio.

![Escludere utenti e gruppi](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Questa opzione deve essere usata solo se necessario, solo in situazioni in cui l'utente è attendibile. L'utente deve essere nuovamente aggiunto al criterio o al gruppo appena possibile.

* **Eliminare il criterio.** Se il criterio non è più necessario, [eliminarlo](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

## <a name="manage-access-to-cloud-apps"></a>Gestire l'accesso alle applicazioni cloud

Usare le seguenti opzioni di gestione per controllare e gestire i criteri di accesso condizionale:

![Screenshot mostra le opzioni di gestione per i criteri di C, inclusi i percorsi denominati, i controlli personalizzati, Condizioni per l'utilizzo, la connettività V P N e i criteri classici selezionati.](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Posizioni specifiche

La condizione della posizione dei criteri di accesso condizionale consente di associare le impostazioni dei controlli dell'accesso ai percorsi di rete degli utenti. Con le [località denominate](location-condition.md) è possibile creare raggruppamenti logici di intervalli di indirizzi IP o di paesi e regioni.

### <a name="custom-controls"></a>Controlli personalizzati

I [controlli personalizzati](controls.md) reindirizzano gli utenti a un servizio compatibile per soddisfare i requisiti di autenticazione esterni ad Azure AD. Per soddisfare questo controllo, il browser dell'utente viene reindirizzato al servizio esterno, esegue le eventuali attività di autenticazione richieste e viene quindi reindirizzato ad Azure AD. Azure AD verifica la risposta e, se l'utente è stato correttamente autenticato o convalidato, può proseguire nel flusso di accesso condizionale.

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Prima che gli utenti accedano ad applicazioni cloud specifiche all'interno dell'ambiente in uso, è possibile ottenere il consenso di questi sotto forma di accettazione delle Condizioni per l'utilizzo (Terms of Use, ToU). Seguire questo [Avvio rapido per creare le Condizioni per l'utilizzo](require-tou.md).

### <a name="classic-policies"></a>Criteri classici

Nel [portale di Azure](https://portal.azure.com/) è possibile trovare i criteri di accesso condizionale in Azure Active Directory > Sicurezza > Accesso condizionale. È possibile che l'organizzazione disponga anche di criteri di accesso condizionale meno recenti, non creati utilizzando questa pagina. Questi criteri sono conosciuti come criteri classici. Si consiglia di [valutare di eseguire la migrazione dei criteri classici nel portale di Azure](best-practices.md).

## <a name="troubleshoot-conditional-access"></a>Risolvere i problemi di accesso condizionale

Quando un utente ha un problema con un criterio di accesso condizionale, raccogliere le informazioni seguenti per semplificare la risoluzione dei problemi.

* Nome dell'entità utente

* Nome visualizzato dell'utente

* Nome del sistema operativo

* Timestamp (è sufficiente un'approssimazione)

* Applicazione di destinazione

* Tipo di applicazione client (browser rispetto a client)

* ID di correlazione (univoco per l'accesso)

Se l'utente ha ricevuto un messaggio con un collegamento a ulteriori dettagli, è possibile raccogliere la maggior parte di queste informazioni dal collegamento.

![Messaggio di errore impossibile accedere all'applicazione](media/plan-conditional-access/cant-get-to-app.png)

Dopo aver raccolto le informazioni, vedere le seguenti risorse:

* [Problemi di accesso con l'accesso condizionale](troubleshoot-conditional-access.md): comprendere i risultati di accesso imprevisti correlati all'accesso condizionale usando i messaggi di errore e il log degli accessi di Azure AD.

* [Uso dello strumento What-If](troubleshoot-conditional-access-what-if.md): comprendere il motivo per cui un criterio è stato o non è stato applicato a un utente in una circostanza specifica o se un criterio verrebbe applicato in uno stato noto.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'autenticazione a più fattori](../authentication/concept-mfa-howitworks.md)

[Altre informazioni su Identity Protection](../identity-protection/overview-identity-protection.md)

[Gestire i criteri dell'accesso condizionale con l'API Microsoft Graph](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md)