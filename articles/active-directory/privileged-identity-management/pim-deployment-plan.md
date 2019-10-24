---
title: Distribuire Privileged Identity Management (PIM)-Azure Active Directory | Microsoft Docs
description: Descrive come pianificare la distribuzione di Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a1263d494ff26ff6ab87d39bc864271f14457b3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756272"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Distribuire Azure AD Privileged Identity Management (PIM)

Questa guida dettagliata descrive come pianificare la distribuzione di Privileged Identity Management (PIM) nell'organizzazione di Azure Active Directory (Azure AD).

> [!TIP]
> In questo articolo vengono visualizzati gli elementi contrassegnati come:
> 
> : heavy_check_mark: **Microsoft consiglia**
> 
> Si tratta di indicazioni di carattere generale che è opportuno implementare solo se sono applicabili alle specifiche esigenze aziendali.

## <a name="learn-about-privileged-identity-management"></a>Informazioni sulle Privileged Identity Management

Azure AD Privileged Identity Management consente di gestire i ruoli amministrativi con privilegi in Azure AD, risorse di Azure e altri Microsoft Online Services. In un mondo in cui le identità con privilegi vengono assegnate e dimenticate, Privileged Identity Management offre soluzioni come l'accesso JIT, i flussi di lavoro di approvazione delle richieste e le verifiche di accesso completamente integrate, in modo da poter identificare, scoprire ed evitare malware attività dei ruoli con privilegi in tempo reale. La distribuzione di Privileged Identity Management per gestire i ruoli con privilegi all'interno dell'organizzazione ridurrà notevolmente i rischi, mostrando al tempo stesso informazioni preziose sulle attività dei ruoli con privilegi.

### <a name="business-value-of-privileged-identity-management"></a>Valore di business di Privileged Identity Management

**Gestire i rischi**: è possibile proteggere la propria organizzazione applicando il principio di [accesso con privilegi minimi](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e just-in-time. Riducendo al minimo il numero di assegnazioni permanenti degli utenti ai ruoli con privilegi e richiedendo approvazioni e autenticazione a più fattori per l'elevazione dei privilegi, è possibile ridurre notevolmente i rischi per la sicurezza correlati all'accesso con privilegi all'interno dell'organizzazione. L'applicazione dell'accesso con privilegi minimi e just-in-time consente inoltre di visualizzare una cronologia dell'accesso ai ruoli con privilegi e di identificare eventuali problemi di sicurezza in tempo reale.

**Conformità e governance** : la distribuzione di Privileged Identity Management crea un ambiente per la governance delle identità in corso. L'elevazione JIT delle identità con privilegi fornisce un modo per Privileged Identity Management tenere traccia delle attività di accesso con privilegi all'interno dell'organizzazione. È inoltre possibile visualizzare e ricevere notifiche per tutte le assegnazioni di ruoli idonei e permanenti all'interno dell'organizzazione. Tramite la verifica di accesso, si possono regolarmente controllare e rimuovere le identità con privilegi non necessarie e assicurarsi che l'organizzazione sia conforme agli standard di identità, accesso e sicurezza più rigorosi.

**Riduci i costi** : Riduci i costi eliminando inefficienze, errori umani e problemi di sicurezza distribuendo Privileged Identity Management correttamente. Il risultato di tutto questo è una riduzione dei crimini informatici associati alle identità con privilegi, che comportano notevoli costi e difficoltà di ripristino. Privileged Identity Management consentirà anche all'organizzazione di ridurre i costi associati al controllo delle informazioni di accesso per quanto riguarda la conformità a normative e standard.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

### <a name="licensing-requirements"></a>Requisiti di licenza

Per usare Privileged Identity Management, la directory deve avere una delle licenze a pagamento o di valutazione seguenti:

- Azure AD P2 Premium
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Per ulteriori informazioni, vedere [requisiti di licenza per l'utilizzo di Privileged Identity Management](subscription-requirements.md).

### <a name="key-terminology"></a>Terminologia chiave

| Termine o concetto | Description |
| --- | --- |
| idoneo | Un'assegnazione di ruolo che richiede a un utente di eseguire una o più azioni per usare il ruolo. Se un utente è stato reso idoneo per un ruolo, potrà attivare il ruolo quando avrà bisogno di svolgere le attività con privilegi. Non esiste alcuna differenza sostanziale tra l'accesso concesso a un utente con l'assegnazione permanente e quello con l'assegnazione di idoneità al ruolo. L'unica differenza è che alcuni utenti non necessitano dell'accesso continuo. |
| activate | Il processo di esecuzione di una o più azioni per usare un ruolo per cui un utente è idoneo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati. |
| accesso JIT (Just-In-Time) | Un modello in base al quale gli utenti ricevono autorizzazioni temporanee per eseguire attività con privilegi, che impedisce a utenti non autorizzati o malintenzionati di ottenere l'accesso dopo la scadenza delle autorizzazioni. L'accesso viene concesso solo quando l'utente ne ha necessità. |
| principio di accesso con privilegi minimi | Una procedura consigliata per la sicurezza in base alla quale ogni utente viene dotato solo dei privilegi minimi necessari per eseguire le attività che è autorizzato a svolgere. Ciò consente di ridurre al minimo il numero di amministratori globali usando in alternativa ruoli di amministratore specifici per determinati scenari. |

Per altre informazioni, vedere [Terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Panoramica di alto livello del funzionamento di Privileged Identity Management

1. Privileged Identity Management è configurato in modo che gli utenti siano idonei per i ruoli con privilegi.
1. Quando un utente idoneo deve usare il proprio ruolo con privilegi, attiva il ruolo in Privileged Identity Management.
1. A seconda delle impostazioni di Privileged Identity Management configurate per il ruolo, l'utente deve completare alcuni passaggi, ad esempio l'esecuzione dell'autenticazione a più fattori, la ricezione dell'approvazione o la specifica di un motivo.
1. Dopo che l'utente ha eseguito l'attivazione del ruolo, può usarlo per un periodo di tempo preconfigurato.
1. Gli amministratori possono visualizzare una cronologia di tutte le attività Privileged Identity Management nel log di controllo. Sono inoltre in grado di proteggere ulteriormente le organizzazioni Azure AD e di rispettare la conformità usando Privileged Identity Management funzionalità come le verifiche di accesso e gli avvisi.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Ruoli che possono essere gestiti da Privileged Identity Management

**Ruoli di Azure ad** : questi ruoli sono tutti in Azure Active Directory (ad esempio amministratore globale, amministratore di Exchange e amministratore della sicurezza). Altre informazioni sui ruoli e sulle relative funzionalità sono disponibili in [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Per informazioni su come determinare i ruoli da assegnare agli amministratori, vedere [Ruoli con privilegi minimi per attività](../users-groups-roles/roles-delegate-by-task.md).

**Ruoli delle risorse di Azure**: i ruoli collegati a una risorsa, a un gruppo di risorse, a una sottoscrizione o a un gruppo di gestione in Azure. Privileged Identity Management fornisce l'accesso JIT a entrambi i ruoli predefiniti, ad esempio proprietario, amministratore accesso utenti e collaboratore, oltre ai [ruoli personalizzati](../../role-based-access-control/custom-roles.md). Per altre informazioni sui ruoli delle risorse di Azure, vedere [Controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md).

Per ulteriori informazioni, vedere [ruoli che non è possibile gestire in Privileged Identity Management](pim-roles.md).

## <a name="plan-your-deployment"></a>Pianificare la distribuzione

Questa sezione è incentrata sulle operazioni che è necessario eseguire prima di distribuire Privileged Identity Management nell'organizzazione. È essenziale seguire le istruzioni e comprendere i concetti presentati poiché saranno utili per creare un piano ottimale, personalizzato in base alle identità con privilegi dell'organizzazione.

### <a name="identify-your-stakeholders"></a>Identificare gli stakeholder

La sezione seguente consente di identificare tutti gli stakeholder coinvolti nel progetto che hanno compiti di approvazione o revisione o che comunque devono tenersi informati sul progetto. Include tabelle separate per la distribuzione di Privileged Identity Management per i ruoli Azure AD e Privileged Identity Management per i ruoli delle risorse di Azure. Aggiungere i nomi degli stakeholder alla tabella seguente in base alla struttura della propria organizzazione.

- A = Approvare il progetto
- R = Rivedere il progetto e fornire pareri
- I = Tenersi informati sul progetto

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Parti interessate: Privileged Identity Management per i ruoli di Azure AD

| name | Ruolo | Azione |
| --- | --- | --- |
| Nome e indirizzo di posta elettronica | **Architetto delle identità o Amministratore globale di Azure**<br/>Un rappresentante del team di gestione delle identità che è responsabile di definire come questa modifica è allineata all'infrastruttura di base per la gestione delle identità all'interno dell'organizzazione. | A/R/I |
| Nome e indirizzo di posta elettronica | **Proprietario del servizio o diretto superiore**<br/>Un rappresentante dei proprietari IT di un servizio o di un gruppo di servizi. Sono essenziali per prendere decisioni e contribuire a implementare Privileged Identity Management per il team. | A/R/I |
| Nome e indirizzo di posta elettronica | **Responsabile della sicurezza**<br/>Un rappresentante del team addetto alla sicurezza che può approvare la conformità del piano ai requisiti di sicurezza dell'organizzazione. | A/R |
| Nome e indirizzo di posta elettronica | **Responsabile del supporto tecnico**<br/>Un rappresentante dell'organizzazione di supporto IT che può fornire pareri riguardanti l'attuabilità di questa modifica dal punto di vista del supporto tecnico. | R/I |
| Nome e indirizzo di posta elettronica per gli utenti pilota | **Utenti dotati di ruolo con privilegi**<br/>Il gruppo di utenti per cui è stata implementata la gestione delle identità con privilegi. Dovranno essere in grado di attivare i ruoli una volta implementato Privileged Identity Management. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Stakeholder: Privileged Identity Management per i ruoli delle risorse di Azure

| name | Ruolo | Azione |
| --- | --- | --- |
| Nome e indirizzo di posta elettronica | **Proprietario delle risorse o delle sottoscrizioni**<br/>Un rappresentante dei proprietari IT di ogni sottoscrizione o risorsa che si desidera distribuire Privileged Identity Management per | A/R/I |
| Nome e indirizzo di posta elettronica | **Responsabile della sicurezza**<br/>Un rappresentante del team addetto alla sicurezza che può approvare la conformità del piano ai requisiti di sicurezza dell'organizzazione. | A/R |
| Nome e indirizzo di posta elettronica | **Responsabile del supporto tecnico**<br/>Un rappresentante dell'organizzazione di supporto IT che può fornire pareri riguardanti l'attuabilità di questa modifica dal punto di vista del supporto tecnico. | R/I |
| Nome e indirizzo di posta elettronica per gli utenti pilota | **Utenti dotati di ruolo RBAC**<br/>Il gruppo di utenti per cui è stata implementata la gestione delle identità con privilegi. Dovranno essere in grado di attivare i ruoli una volta implementato Privileged Identity Management. | I |

### <a name="enable-privileged-identity-management"></a>Abilita Privileged Identity Management

Come parte del processo di pianificazione, è necessario prima di tutto concedere il consenso e abilitare Privileged Identity Management seguendo l'articolo [iniziare a usare Privileged Identity Management](pim-getting-started.md) . L'abilitazione di Privileged Identity Management consente di accedere ad alcune funzionalità progettate in modo specifico per facilitare la distribuzione.

Se l'obiettivo consiste nel distribuire Privileged Identity Management per le risorse di Azure, è necessario seguire l'articolo [individuare le risorse di Azure da gestire in Privileged Identity Management](pim-resource-roles-discover-resources.md) . Solo i proprietari di ogni risorsa, gruppo di risorse e sottoscrizione saranno in grado di individuarli all'interno Privileged Identity Management. Gli amministratori globali che tentano di distribuire Privileged Identity Management per le risorse di Azure possono [elevare l'accesso per gestire tutte le sottoscrizioni di Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) per consentire l'accesso a tutte le risorse di Azure nella directory per l'individuazione. Tuttavia, si consiglia di ottenere l'approvazione da ogni proprietario della sottoscrizione prima di gestire le risorse con Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Applicare il principio di accesso con privilegi minimi

È importante verificare di aver applicato il principio di accesso con privilegi minimi all'interno dell'organizzazione sia per i ruoli di Azure AD sia per quelli relativi alle risorse di Azure.

#### <a name="azure-ad-roles"></a>Ruoli di Azure AD

Per quanto riguarda i ruoli di Azure AD, le organizzazioni assegnano in genere il ruolo Amministratore globale a un numero notevole di amministratori quando invece la maggior parte degli amministratori deve usare solo uno o due ruoli specifici. Anche le assegnazioni di ruoli con privilegi non vengono in genere gestite.

> [!NOTE]
> Errori comuni nella delega dei ruoli:
>
> - All'amministratore responsabile di Exchange viene assegnato il ruolo Amministratore globale anche se per lui è sufficiente il ruolo Amministratore di Exchange per svolgere le attività di tutti i giorni.
> - Il ruolo Amministratore globale viene assegnato a un amministratore di Office perché tale amministratore deve avere entrambi i ruoli Amministratore di SharePoint e Amministratore della sicurezza ed è più facile delegare un solo ruolo.
> - A un amministratore è stato assegnato un ruolo Amministratore della sicurezza per eseguire un'attività molto tempo fa, ma tale ruolo non è stato mai rimosso.

Per applicare il principio di accesso con privilegi minimi per i ruoli di Azure AD, seguire questi passaggi.

1. Comprendere le caratteristiche specifiche dei ruoli leggendo le informazioni sui [ruoli di amministratore disponibili in Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). È inoltre utile fare riferimento al documento sui [ruoli di amministratore per attività di identità in Azure AD](../users-groups-roles/roles-delegate-by-task.md), che presenta il ruolo con privilegi minimi da usare per ogni attività specifica.

1. Visualizzare l'elenco degli utenti che dispongono di ruoli con privilegi all'interno dell'organizzazione. È possibile utilizzare la [procedura guidata Privileged Identity Management](pim-security-wizard.md#run-the-wizard) per ottenere una pagina simile alla seguente.

    ![Riquadro individua ruoli con privilegi che mostra i ruoli con privilegi](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Per tutti gli amministratori globali all'interno dell'organizzazione, individuare il motivo per cui devono avere tale ruolo. In base alla lettura della documentazione precedente, se il processo della persona può essere eseguito da uno o più ruoli di amministratore granulari, è necessario rimuoverli dal ruolo di amministratore globale ed effettuare le assegnazioni di conseguenza all'interno Azure Active Directory (come riferimento: Microsoft dispone attualmente di circa 10 amministratori con il ruolo di amministratore globale. Altre informazioni [sull'uso di Privileged Identity Management da Microsoft](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. Per tutti gli altri ruoli di Azure AD, esaminare l'elenco delle assegnazioni, identificare gli amministratori che non hanno più bisogno del ruolo assegnato e rimuoverli dalle rispettive assegnazioni.

Per automatizzare gli ultimi due passaggi, è possibile usare le verifiche di accesso in Privileged Identity Management. Seguendo i passaggi descritti in [avviare una verifica di accesso per i ruoli Azure ad in Privileged Identity Management](pim-how-to-start-security-review.md), è possibile configurare una verifica di accesso per ogni ruolo Azure ad che dispone di uno o più membri.

![Creare un riquadro di verifica di accesso per i ruoli Azure AD](./media/pim-deployment-plan/create-access-review.png)

È necessario impostare i revisori su **Membri (autonomo)** . In questo modo verrà inviato un messaggio di posta elettronica a tutti i membri del ruolo per chiedere loro di confermare se hanno bisogno dell'accesso. È anche necessario attivare **Richiedi il motivo all'approvazione** nelle impostazioni avanzate in modo che gli utenti possano indicare il motivo per cui devono disporre del ruolo. In base alle informazioni raccolte, sarà possibile rimuovere gli utenti dai ruoli non necessari e, nel caso di amministratori globali, delegare più ruoli di amministratore specifici.

Le verifiche di accesso si basano sulla posta elettronica per comunicare agli utenti di controllare il rispettivo accesso ai ruoli. Se si hanno alcuni account con privilegi a cui non è associato un indirizzo di posta elettronica, assicurarsi di specificare l'indirizzo di posta elettronica secondario per tali account. Per altre informazioni, vedere [Attributo proxyAddresses in Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Ruoli delle risorse di Azure

Per le sottoscrizioni e le risorse di Azure, è possibile configurare un processo di verifica di accesso simile per esaminare i ruoli in ogni sottoscrizione o risorsa. L'obiettivo di questo processo è ridurre al minimo le assegnazioni di ruolo Proprietario e Amministratore Accesso utenti associate a ogni sottoscrizione o risorsa e rimuovere le assegnazioni non necessarie. Le organizzazioni, tuttavia, delegano spesso tali attività al proprietario di ogni sottoscrizione o risorsa perché ha una conoscenza più approfondita dei ruoli specifici (soprattutto per quanto riguarda i ruoli personalizzati).

Se si è un amministratore IT con il ruolo di amministratore globale che tenta di distribuire Privileged Identity Management per le risorse di Azure nell'organizzazione, è possibile [elevare l'accesso per gestire tutte le sottoscrizioni di Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) per ottenere l'accesso a ogni sottoscrizione. È quindi possibile contattare i singoli proprietari delle sottoscrizioni e collaborare con loro per rimuovere le assegnazioni non necessarie e ridurre al minimo le assegnazioni del ruolo Proprietario.

Gli utenti con il ruolo Proprietario per una sottoscrizione di Azure possono anche usare le [verifiche di accesso per le risorse di Azure](pim-resource-roles-start-access-review.md) per controllare e rimuovere le assegnazioni di ruolo non necessarie in modo simile a come descritto in precedenza per i ruoli di Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Decidere quali assegnazioni di ruolo devono essere protette da Privileged Identity Management

Dopo aver pulito le assegnazioni di ruolo con privilegi all'interno dell'organizzazione, sarà necessario decidere quali ruoli proteggere con Privileged Identity Management.

Se un ruolo è protetto da Privileged Identity Management, gli utenti idonei ad essi assegnati dovranno elevare per utilizzare i privilegi concessi dal ruolo. Per il processo di elevazione dei privilegi può anche essere necessario ottenere l'approvazione, eseguire l'autenticazione a più fattori e/o specificare un motivo per l'attivazione. Privileged Identity Management inoltre possibile rilevare le elevazioni tramite le notifiche e il Privileged Identity Management e Azure AD i registri eventi di controllo.

La scelta dei ruoli da proteggere con Privileged Identity Management può essere difficile e sarà diversa per ogni organizzazione. Questa sezione presenta i consigli pratici di Microsoft per la scelta dei ruoli di Azure AD e di quelli relativi alle risorse di Azure.

#### <a name="azure-ad-roles"></a>Ruoli di Azure AD

È importante dare priorità alla protezione dei ruoli di Azure AD che hanno il maggior numero di autorizzazioni. In base ai modelli di utilizzo di tutti i Privileged Identity Management clienti, i primi 10 Azure AD ruoli gestiti da Privileged Identity Management sono:

1. Amministratore globale
1. Amministratore della sicurezza
1. Amministratore utenti
1. Amministratore di Exchange
1. Amministratore di SharePoint
1. Amministratore di Intune
1. Ruolo con autorizzazioni di lettura per la sicurezza
1. Amministratore del servizio
1. Amministratore fatturazione
1. Amministratore di Skype for Business

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire tutti gli amministratori globali e gli amministratori della sicurezza usando Privileged Identity Management come primo passaggio, in quanto sono quelli che possono eseguire la maggior parte del danno quando vengono compromessi.

È importante considerare quali dati e autorizzazioni sono più vulnerabili per l'organizzazione. È ad esempio possibile che alcune organizzazioni vogliano proteggere il proprio ruolo di amministratore di Power BI o il ruolo di amministratore dei team utilizzando Privileged Identity Management poiché hanno la possibilità di accedere ai dati e/o di modificare i flussi di lavoro principali.

Se hanno utenti guest assegnati, i ruoli sono particolarmente vulnerabili agli attacchi.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire tutti i ruoli con gli utenti guest usando Privileged Identity Management per ridurre i rischi associati agli account utente Guest compromessi.

I ruoli di lettore, come quelli con autorizzazioni di lettura nella directory, per il Centro messaggi e per la sicurezza, sono talvolta considerati meno importanti rispetto ad altri poiché non dispongono dell'autorizzazione di scrittura. Alcuni clienti, tuttavia, proteggono anche questi ruoli perché gli utenti malintenzionati che hanno accesso a questi account possono riuscire a leggere dati sensibili, ad esempio informazioni personali. È necessario tenere in considerazione questo aspetto quando si decide se i ruoli di Reader nell'organizzazione devono essere gestiti con Privileged Identity Management.

#### <a name="azure-resource-roles"></a>Ruoli delle risorse di Azure

Quando si decide quali assegnazioni di ruolo devono essere gestite usando Privileged Identity Management per la risorsa di Azure, è necessario innanzitutto identificare le sottoscrizioni e le risorse più importanti per l'organizzazione. Ecco alcuni esempi di sottoscrizioni o risorse di questo tipo:

- Risorse che ospitano i dati più sensibili
- Risorse da cui dipendono le applicazioni principali destinate direttamente ai clienti

Se come amministratore globale si ha difficoltà a decidere quali sottoscrizioni e risorse sono più importanti, è opportuno contattare i proprietari delle sottoscrizioni nell'organizzazione per raccogliere un elenco delle risorse gestite per ogni sottoscrizione. Collaborare quindi con i proprietari delle sottoscrizioni per raggruppare le risorse in base al livello di gravità nel caso in cui vengano compromesse (basso, medio, alto). È consigliabile assegnare priorità alla gestione delle risorse con Privileged Identity Management in base a questo livello di gravità.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di collaborare con i proprietari di sottoscrizioni o risorse di servizi critici per configurare Privileged Identity Management flusso di lavoro per tutti i ruoli all'interno di sottoscrizioni o risorse sensibili.

Privileged Identity Management per le risorse di Azure supporta gli account del servizio con associazione temporale. È consigliabile gestire questi account di servizio esattamente come un account utente normale.

Per sottoscrizioni o risorse non cruciali, non è necessario configurare Privileged Identity Management per tutti i ruoli. Tuttavia, è comunque necessario proteggere i ruoli proprietario e amministratore accesso utenti con Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire i ruoli proprietario e amministratore accesso utenti di tutte le sottoscrizioni o risorse usando Privileged Identity Management.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidere quali assegnazioni di ruolo devono essere permanenti o idonee

Una volta deciso l'elenco dei ruoli da gestire con Privileged Identity Management, è necessario decidere quali utenti devono ottenere il ruolo idoneo rispetto al ruolo attivo permanente. I ruoli attivi in modo permanente sono i normali ruoli assegnati tramite Azure Active Directory e le risorse di Azure, mentre i ruoli idonei possono essere assegnati solo in Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di avere zero assegnazioni attive in modo permanente sia per i ruoli Azure ad che per i ruoli delle risorse di Azure, ad eccezione dei [due account di accesso di emergenza break-Glass](../users-groups-roles/directory-emergency-access.md)consigliati, che devono avere il Ruolo di amministratore globale.

Anche se è consigliabile una configurazione senza amministratori permanenti, talvolta le organizzazioni possono avere difficoltà a implementare immediatamente una configurazione di questo tipo. Ecco alcuni aspetti da considerare nel prendere questa decisione:

- Frequenza di elevazione dei privilegi: se l'utente ha bisogno di un'assegnazione con privilegi una sola volta, non è necessario configurare l'assegnazione permanente. D'altra parte, se l'utente ha bisogno del ruolo per il proprio lavoro giornaliero e l'uso di Privileged Identity Management ridurrebbe notevolmente la produttività, è possibile considerarlo per il ruolo permanente.
- Casi specifici per un'organizzazione: se l'utente a cui viene assegnato il ruolo idoneo è membro di un team molto distante oppure è un dirigente di alto livello, a tal punto che le comunicazioni e l'applicazione del processo di elevazione dei privilegi risulterebbe difficile, è possibile prenderlo in considerazione per il ruolo permanente.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di configurare le verifiche di accesso ricorrenti per gli utenti con assegnazioni di ruolo permanenti (se presenti). Altre informazioni sulle verifiche di accesso periodiche sono riportate nella sezione finale di questo piano di distribuzione.

### <a name="draft-your-privileged-identity-management-settings"></a>Bozza delle impostazioni di Privileged Identity Management

Prima di implementare la soluzione di Privileged Identity Management, è consigliabile elaborare le impostazioni del Privileged Identity Management per ogni ruolo con privilegi utilizzati dall'organizzazione. Questa sezione contiene alcuni esempi di impostazioni di Privileged Identity Management per determinati ruoli (sono solo per riferimento e potrebbero essere diversi per l'organizzazione). Ognuna di queste impostazioni è descritta in dettaglio, con consigli di Microsoft, dopo le tabelle.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Impostazioni Privileged Identity Management per i ruoli di Azure AD

| Ruolo | Richiedere l'autenticazione MFA | Notifica | Ticket di evento imprevisto | Richiedi approvazione | Responsabile approvazione | Durata attivazione | Amministratore permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Amministratore globale | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Altri amministratori globali | 1 ora | Account di accesso di emergenza |
| Amministratore di Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Nessuno | 2 ore | Nessuno |
| Amministratore del supporto tecnico | :x: | :x: | :heavy_check_mark: | :x: | Nessuno | 8 ore | Nessuno |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Impostazioni Privileged Identity Management per i ruoli delle risorse di Azure

| Ruolo | Richiedere l'autenticazione MFA | Notifica | Richiedi approvazione | Responsabile approvazione | Durata attivazione | Amministratore attivo | Scadenza assegnazioni attive | Scadenza assegnazioni idonee |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietario di sottoscrizioni critiche | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Altri proprietari della sottoscrizione | 1 ora | Nessuno | N/D | 3 mesi |
| Amministratore Accesso utenti di sottoscrizioni meno critiche | :heavy_check_mark: | :heavy_check_mark: | :x: | Nessuno | 1 ora | Nessuno | N/D | 3 mesi |
| Collaboratore macchine virtuali | :x: | :heavy_check_mark: | :x: | Nessuno | 3 ore | Nessuno | N/D | 6 mesi |

La tabella seguente include le descrizioni per ciascuna delle impostazioni.

| Impostazione | Description |
| --- | --- |
| Ruolo | Nome del ruolo per cui si definiscono le impostazioni. |
| Richiedere l'autenticazione MFA | Indica se l'utente idoneo deve eseguire l'autenticazione a più fattori (MFA) prima dell'attivazione del ruolo.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di applicare l'autenticazione a più fattori per tutti i ruoli di amministratore, soprattutto se i ruoli hanno utenti guest. |
| Notifica | Se è impostato l'invio di notifiche, gli utenti dell'organizzazione con i ruoli Amministratore globale, Amministratore dei ruoli con privilegi e Amministratore della sicurezza ricevono una notifica di posta elettronica quando un utente idoneo attiva il ruolo.<br/><br/>**Nota:** Alcune organizzazioni non dispongono di un indirizzo di posta elettronica associato agli account di amministratore. per ricevere queste notifiche di posta elettronica, è necessario impostare un indirizzo di posta elettronica alternativo in modo che gli amministratori ricevano questi messaggi. |
| Ticket di evento imprevisto | Indica se l'utente idoneo deve registrare un numero di ticket di evento imprevisto quando attiva il proprio ruolo. Questa impostazione consente a un'organizzazione di identificare ogni attivazione con un numero di evento imprevisto interno per limitare le attivazioni indesiderate.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di sfruttare i numeri di ticket per gli eventi imprevisti per associare Privileged Identity Management al sistema interno. Questo è particolarmente utile per i responsabili dell'approvazione che devono conoscere il contesto dell'attivazione. |
| Richiedi approvazione | Indica se l'utente idoneo deve ottenere l'approvazione per attivare il ruolo.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di impostare l'approvazione per i ruoli con la massima autorizzazione. In base ai modelli di utilizzo di tutti i Privileged Identity Management clienti, amministratore globale, amministratore utente, amministratore di Exchange, amministratore della sicurezza e amministratore password sono i ruoli più comuni con la configurazione dell'approvazione. |
| Responsabile approvazione | Se per attivare il ruolo idoneo è richiesta l'approvazione, indicare le persone che dovrebbero approvare la richiesta. Per impostazione predefinita, Privileged Identity Management imposta il responsabile approvazione come tutti gli utenti che sono amministratori dei ruoli con privilegi, che siano permanenti o idonei.<br/><br/>**Nota:** Se un utente è idoneo per un ruolo Azure AD e un responsabile approvazione del ruolo, non sarà in grado di approvare se stesso.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di scegliere responsabili approvazione come quelli più esperti del ruolo specifico e dei relativi utenti frequenti anziché un amministratore globale. |
| Durata attivazione | Periodo di tempo durante il quale il ruolo di un utente rimarrà attivo prima della scadenza. |
| Amministratore permanente | Elenco degli utenti che saranno amministratori permanenti per il ruolo (l'attivazione non è mai necessaria).<br/><br/> : heavy_check_mark: **Microsoft consiglia** di avere un amministratore con zero diritti per tutti i ruoli, ad eccezione degli amministratori globali. Per altre informazioni, vedere la sezione di questo piano che contiene suggerimenti per decidere quali assegnazioni di ruolo devono essere idonee e quali devono essere attive in modo permanente. |
| Amministratore attivo | Per le risorse di Azure, un amministratore attivo è uno degli utenti che non devono eseguire l'attivazione per usare il ruolo. Per questo amministratore non viene usato l'aggettivo permanente, come nei ruoli di Azure AD, perché è possibile impostare un termine di scadenza oltre il quale l'utente perderà il ruolo. |
| Scadenza assegnazioni attive | Un'assegnazione attiva per i ruoli delle risorse di Azure scade dopo il periodo di tempo configurato. È possibile scegliere tra 15 giorni, 1 mese, 3 mesi, 6 mesi, 1 anno oppure impostare un'assegnazione attiva in modo permanente. |
| Scadenza assegnazioni idonee | Un'assegnazione idonea per i ruoli delle risorse di Azure scade dopo il periodo di tempo configurato. È possibile scegliere tra 15 giorni, 1 mese, 3 mesi, 6 mesi, 1 anno oppure impostare un'assegnazione idonea in modo permanente. |

## <a name="implement-your-solution"></a>Implementare la soluzione

Una pianificazione appropriata deve avere alla base la possibilità di distribuire correttamente un'applicazione con Azure Active Directory.  Deve inoltre offrire soluzioni intelligenti per la sicurezza e l'integrazione in modo da semplificare l'onboarding e ridurre così il tempo necessario per le distribuzioni.  In questo modo si ha la sicurezza che l'applicazione venga integrata con facilità riducendo i tempi di inattività per gli utenti finali.

### <a name="identify-test-users"></a>Identificare gli utenti di test

Usare questa sezione per identificare un set di utenti e/o gruppi di utenti per convalidare l'implementazione. In base alle impostazioni selezionate nella sezione dedicata alla pianificazione, identificare gli utenti desiderati per testare ogni ruolo.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di fare in modo che i proprietari del servizio di ogni ruolo Azure ad siano gli utenti di test in modo che possano acquisire familiarità con il processo e diventare un sostenitore interno per la distribuzione.

In questa tabella identificare gli utenti di test che verificheranno il corretto funzionamento delle impostazioni per ogni ruolo.

| Nome del ruolo | Utenti di test |
| --- | --- |
| &lt;Nome del ruolo&gt; | &lt;Utenti per testare il ruolo&gt; |
| &lt;Nome del ruolo&gt; | &lt;Utenti per testare il ruolo&gt; |

### <a name="test-implementation"></a>Testare l'implementazione

Ora che sono stati identificati gli utenti di test, usare questo passaggio per configurare Privileged Identity Management per gli utenti di test. Se l'organizzazione vuole incorporare Privileged Identity Management flusso di lavoro nella propria applicazione interna invece di usare Privileged Identity Management nel portale di Azure, sono supportate anche tutte le operazioni in Privileged Identity Management tramite l' [API Graph](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configurare Privileged Identity Management per i ruoli di Azure AD

1. [Configurare le impostazioni del ruolo Azure ad](pim-how-to-change-default-settings.md) in base a ciò che è stato pianificato.

1. Passare a **Ruoli di Azure AD**, fare clic su **Ruoli** e quindi selezionare il ruolo appena configurato.

1. Se gli utenti del gruppo di test sono già configurati come amministratori permanenti, per renderli idonei è possibile cercarli e convertirli da permanenti a idonei facendo clic sui tre puntini sulla riga corrispondente. Se invece agli utenti non sono stati ancora assegnati ruoli, è possibile [definire una nuova assegnazione idonea](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Ripetere i passaggi da 1 a 3 per tutti i ruoli da testare.

1. Dopo aver configurato gli utenti di test, è necessario inviare loro il collegamento per [attivare il proprio ruolo di Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Configurare Privileged Identity Management per i ruoli delle risorse di Azure

1. [Configurare le impostazioni dei ruoli delle risorse di Azure](pim-resource-roles-configure-role-settings.md) per un ruolo all'interno di una sottoscrizione o una risorsa che si vuole testare.

1. Passare a **Risorse di Azure** per la sottoscrizione specifica, fare clic su **Ruoli** e selezionare il ruolo appena configurato.

1. Se gli utenti del gruppo di test sono già configurati come amministratori attivi, per renderli idonei è possibile cercarli e [aggiornare la rispettiva assegnazione di ruolo](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se invece gli utenti non hanno ancora un ruolo, è possibile [assegnarne uno nuovo](pim-resource-roles-assign-roles.md#assign-a-role).

1. Ripetere i passaggi da 1 a 3 per tutti i ruoli da testare.

1. Dopo aver configurato gli utenti di test, è necessario inviare loro il collegamento per [attivare il rispettivo ruolo delle risorse di Azure](pim-resource-roles-activate-your-roles.md).

È consigliabile usare questa fase per verificare se l'intera configurazione definita per i ruoli funziona correttamente. Usare la tabella seguente per documentare i test. Questa fase è utile anche per ottimizzare le comunicazioni con gli utenti interessati.

| Ruolo | Comportamento previsto durante l'attivazione | Risultati effettivi |
| --- | --- | --- |
| Amministratore globale | (1) Richiedere l'autenticazione MFA<br/>(2) Richiedere l'approvazione<br/>(3) Il responsabile dell'approvazione riceve una notifica e può approvare l'assegnazione<br/>(4) Il ruolo scade dopo il tempo prestabilito |  |
| Proprietario della sottoscrizione *X* | (1) Richiedere l'autenticazione MFA<br/>(2) L'assegnazione idonea scade dopo il periodo di tempo configurato |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Comunicare Privileged Identity Management alle parti interessate interessate

La distribuzione di Privileged Identity Management introdurrà passaggi aggiuntivi per gli utenti con ruoli con privilegi. Sebbene Privileged Identity Management riduca notevolmente i problemi di sicurezza associati alle identità con privilegi, la modifica deve essere effettivamente comunicata prima della distribuzione a livello di tenant. A seconda del numero di amministratori interessati, le organizzazioni scelgono spesso di creare un documento interno, un video o un messaggio di posta elettronica per comunicare la novità. In queste comunicazioni sono spesso incluse le informazioni seguenti:

- Che cos'è PIM
- Quale vantaggio offre all'organizzazione
- Quali saranno gli utenti interessati
- Quando verrà implementato PIM
- Quali passaggi in più dovranno eseguire gli utenti per attivare il proprio ruolo
    - È consigliabile inviare collegamenti alla documentazione di Microsoft:
    - [Attivare i ruoli di Azure AD](pim-how-to-activate-role.md)
    - [Attivare i ruoli delle risorse di Azure](pim-resource-roles-activate-your-roles.md)
- Informazioni di contatto o collegamento al supporto tecnico per eventuali problemi relativi a PIM

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di impostare il tempo con il team di supporto/supporto tecnico per esaminare il flusso di lavoro Privileged Identity Management (se l'organizzazione dispone di un team di supporto IT interno). Fornire loro la documentazione appropriata e le informazioni di contatto.

### <a name="move-to-production"></a>Passare in produzione

Al termine del test, spostare Privileged Identity Management in produzione ripetendo tutti i passaggi nelle fasi di testing per tutti gli utenti di ogni ruolo definito nella configurazione del Privileged Identity Management. Per Privileged Identity Management per i ruoli di Azure AD, le organizzazioni spesso testano e implementano Privileged Identity Management per gli amministratori globali prima di testare e distribuire Privileged Identity Management per altri ruoli. Nel frattempo, per le risorse di Azure, le organizzazioni normalmente testano e implementano Privileged Identity Management una sottoscrizione di Azure alla volta.

### <a name="in-the-case-a-rollback-is-needed"></a>In caso di ripristino dello stato precedente

Se Privileged Identity Management non è riuscito a funzionare nel modo desiderato nell'ambiente di produzione, i passaggi di rollback seguenti possono risultare utili per ripristinare uno stato valido noto prima di configurare Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Ruoli di Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Aprire **Azure AD Privileged Identity Management**.
1. Fare clic su **Ruoli di Azure AD** e quindi su **Ruoli**.
1. Per ogni ruolo configurato, fare clic sui puntini di sospensione ( **...** ) per tutti gli utenti con un'assegnazione idonea.
1. Fare clic sull'opzione **Rendi permanente** per rendere permanente l'assegnazione di ruolo.

#### <a name="azure-resource-roles"></a>Ruoli delle risorse di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Aprire **Azure AD Privileged Identity Management**.
1. Fare clic su **Risorse di Azure** e quindi su una sottoscrizione o una risorsa per cui si vuole ripristinare lo stato precedente.
1. Fare clic su **Ruoli**.
1. Per ogni ruolo configurato, fare clic sui puntini di sospensione ( **...** ) per tutti gli utenti con un'assegnazione idonea.
1. Fare clic sull'opzione **Rendi permanente** per rendere permanente l'assegnazione di ruolo.

## <a name="next-steps-after-deploying"></a>Passaggi successivi dopo la distribuzione

La distribuzione di Privileged Identity Management nell'ambiente di produzione è un importante passo avanti in termini di protezione delle identità con privilegi dell'organizzazione. Con la distribuzione di Privileged Identity Management vengono fornite funzionalità Privileged Identity Management aggiuntive da utilizzare per la sicurezza e la conformità.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Usare Privileged Identity Management avvisi per salvaguardare l'accesso con privilegi

È consigliabile usare la funzionalità di avviso incorporata di Privileged Identity Management per salvaguardare meglio il tenant. Per altre informazioni, vedere [Avvisi di sicurezza](pim-how-to-configure-security-alerts.md#security-alerts). Questi avvisi includono: gli amministratori non usano i ruoli con privilegi, i ruoli vengono assegnati all'esterno del Privileged Identity Management, i ruoli vengono attivati troppo spesso e molto altro. Per proteggere completamente la propria organizzazione, è necessario scorrere regolarmente l'elenco degli avvisi e risolvere i problemi. Per visualizzare e risolvere gli avvisi, procedere nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Aprire **Azure AD Privileged Identity Management**.
1. Fare clic su **Ruoli di Azure AD** e quindi su **Avvisi**.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire immediatamente tutti gli avvisi contrassegnati con una gravità elevata. Per quelli con livello di gravità medio e basso, è necessario tenersi informati e intervenire se si ritiene che sia presente una minaccia per la sicurezza.

Se uno degli avvisi non è utile o non è applicabile alla propria organizzazione, è sempre possibile ignorarlo nella pagina degli avvisi ed eventualmente ripristinarlo in un secondo momento nella pagina delle impostazioni di Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurare verifiche di accesso periodiche per controllare regolarmente le identità con privilegi dell'organizzazione

Le verifiche di accesso sono il modo migliore per chiedere agli utenti cui sono assegnati ruoli con privilegi o a revisori specifici se hanno effettivamente bisogno dell'identità con privilegi. Le verifiche di accesso sono molto utili se si vuole ridurre la superficie di attacco assicurando al tempo stesso la conformità. Per altre informazioni sull'avvio di una verifica di accesso, vedere [Verifiche di accesso per i ruoli di Azure AD](pim-how-to-start-security-review.md) e [Verifiche di accesso per i ruoli delle risorse di Azure](pim-resource-roles-start-access-review.md). Per alcune organizzazioni, l'esecuzione di una verifica di accesso periodica è necessaria per garantire la conformità alle leggi e alle normative, mentre per altre una verifica di accesso è il modo migliore per applicare il principio di accesso con privilegi minimi in tutta l'organizzazione.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di configurare le verifiche di accesso trimestrali per tutti i Azure ad e i ruoli delle risorse di Azure.

Nella maggior parte dei casi, il revisore per i ruoli di Azure AD è l'utente stesso, mentre il revisore per i ruoli delle risorse di Azure è il proprietario della sottoscrizione in cui si trova il ruolo. Tuttavia, capita spesso che le aziende abbiano account con privilegi che non sono collegati all'indirizzo di posta elettronica di una persona particolare. In questi casi, nessuno legge i messaggi e verifica i privilegi di accesso.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di aggiungere un indirizzo di posta elettronica secondario per tutti gli account con assegnazioni di ruolo con privilegi che non sono collegati a un indirizzo di posta elettronica controllato regolarmente

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Sfruttare al meglio il log di controllo per migliorare la sicurezza e la conformità

Il log di controllo è lo strumento che consente di rimanere sempre aggiornati e assicurare la conformità alle normative. Privileged Identity Management archivia attualmente una cronologia di 30 giorni di tutta la cronologia dell'organizzazione nel log di controllo, tra cui:

- Attivazione o disattivazione dei ruoli idonei
- Attività di assegnazione di ruolo all'interno e all'esterno di Privileged Identity Management
- Modifiche nelle impostazioni dei ruoli
- Attività di richiesta, approvazione o rifiuto per l'attivazione dei ruoli con configurazione dell'approvazione
- Aggiornamenti degli avvisi

I log di controllo sono accessibili agli amministratori globali o agli amministratori cui sono assegnati ruoli con privilegi. Per altre informazioni, vedere gli articoli sulla [cronologia dei controlli per i ruoli di Azure AD](pim-how-to-use-audit-log.md) e sulla [cronologia dei controlli per i ruoli delle risorse di Azure](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di fare in modo che almeno un amministratore Legga tutti gli eventi di controllo su base settimanale ed esporti gli eventi di controllo su base mensile.

Se si desidera archiviare automaticamente gli eventi di controllo per un periodo di tempo più lungo, il registro di controllo di Privileged Identity Management viene sincronizzato automaticamente nei [log di controllo Azure ad](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di configurare il monitoraggio dei [log di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) per archiviare gli eventi di controllo in un account di archiviazione di Azure per la necessità di sicurezza e conformità.
