---
title: Distribuire Privileged Identity Management (PIM)-Azure AD | Microsoft Docs
description: Descrive come pianificare la distribuzione di Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9ba7217dfc167a06a1fea389cfc40a5e1251ca
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367806"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Distribuire Azure AD Privileged Identity Management (PIM)

Questo articolo è una guida dettagliata che descrive come pianificare la distribuzione di Privileged Identity Management (PIM) nell'organizzazione di Azure Active Directory (Azure AD). Gli utenti con privilegi elevati verranno riassegnati a ruoli predefiniti o personalizzati meno potenti laddove possibile e verranno pianificate le assegnazioni di ruolo JIT per i ruoli con privilegi più elevati. In questo articolo vengono forniti consigli per la pianificazione e l'implementazione della distribuzione.

> [!TIP]
> In questo articolo vengono visualizzati gli elementi contrassegnati come:
>
> : heavy_check_mark: **Microsoft consiglia**
>
> Si tratta di raccomandazioni generali ed è necessario implementarle solo quando si applicano alle specifiche esigenze aziendali.

## <a name="licensing-requirements"></a>Requisiti di licenza

Per usare Privileged Identity Management, la directory deve avere una delle seguenti licenze a pagamento o di valutazione. Per ulteriori informazioni, vedere [requisiti di licenza per l'utilizzo di Privileged Identity Management](subscription-requirements.md).

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>Funzionamento di PIM

In questa sezione viene fornita una verifica a scopo di pianificazione delle parti pertinenti del processo di Privileged Identity Management. Per ulteriori informazioni, vedere [che cos'è Azure ad Privileged Identity Management?](pim-configure.md)

1. Iniziare a usare Privileged Identity Management in modo che gli utenti siano idonei per i ruoli con privilegi.
1. Quando un utente idoneo deve usare il proprio ruolo con privilegi, attiva il ruolo usando Privileged Identity Management.
1. L'utente può essere richiesto in impostazioni per:

    - Utilizzare Multi-Factor Authentication
    - Richiedi l'approvazione per l'attivazione
    - Fornire un motivo aziendale per l'attivazione

1. Dopo che l'utente ha correttamente attivato il proprio ruolo, avrà le autorizzazioni del ruolo per una durata impostata.
1. Gli amministratori possono visualizzare una cronologia di tutte le attività Privileged Identity Management nel log di controllo. Sono inoltre in grado di proteggere ulteriormente le organizzazioni Azure AD e di rispettare la conformità utilizzando Privileged Identity Management funzionalità come le verifiche di accesso e gli avvisi.

## <a name="roles-that-can-be-managed-by-pim"></a>Ruoli che possono essere gestiti da PIM

**Azure ad ruoli** sono tutti in Azure Active Directory, ad esempio amministratore globale, amministratore di Exchange e amministratore della sicurezza. Altre informazioni sui ruoli e sulle relative funzionalità sono disponibili in [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../roles/permissions-reference.md). Per informazioni su come determinare i ruoli da assegnare agli amministratori, vedere [Ruoli con privilegi minimi per attività](../roles/delegate-by-task.md).

I **ruoli di Azure** sono ruoli collegati a una risorsa di Azure, un gruppo di risorse, una sottoscrizione o un gruppo di gestione. È possibile usare PIM per fornire l'accesso JIT ai ruoli predefiniti di Azure come proprietario, amministratore accesso utenti e collaboratore e anche ai [ruoli personalizzati](../../role-based-access-control/custom-roles.md). Per altre informazioni sui ruoli di Azure, vedere [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).

Per altre informazioni, vedere [ruoli che non è possibile gestire in Privileged Identity Management](pim-roles.md).

## <a name="deployment-plan"></a>Piano di distribuzione

Prima di distribuire Privileged Identity Management nell'organizzazione, seguire le istruzioni e comprendere i concetti in questa sezione per creare un piano personalizzato per i requisiti di identità con privilegi dell'organizzazione.

### <a name="identify-your-stakeholders"></a>Identificare gli stakeholder

La sezione seguente consente di identificare tutte le parti interessate coinvolte nel progetto ed è necessario disconnettersi, rivedere o rimanere informati. Include tabelle separate per la distribuzione di PIM per i ruoli di Azure AD e PIM per i ruoli di Azure. Aggiungere i nomi degli stakeholder alla tabella seguente in base alla struttura della propria organizzazione.

- A = Approvare il progetto
- R = Rivedere il progetto e fornire pareri
- I = Tenersi informati sul progetto

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Parti interessate: Privileged Identity Management per i ruoli di Azure AD

| Name | Ruolo | Action |
| --- | --- | --- |
| Nome e indirizzo di posta elettronica | **Architetto delle identità o Amministratore globale di Azure**<br/>Un rappresentante del team di gestione delle identità responsabile della definizione di come allineare questa modifica all'infrastruttura di gestione delle identità di base dell'organizzazione. | A/R/I |
| Nome e indirizzo di posta elettronica | **Proprietario del servizio o diretto superiore**<br/>Un rappresentante dei proprietari IT di un servizio o di un gruppo di servizi. Sono essenziali per prendere decisioni e contribuire a implementare Privileged Identity Management per il team. | A/R/I |
| Nome e indirizzo di posta elettronica | **Responsabile della sicurezza**<br/>Rappresentante del team addetto alla sicurezza che può disconnettere il piano soddisfa i requisiti di sicurezza dell'organizzazione. | A/R |
| Nome e indirizzo di posta elettronica | **Responsabile del supporto tecnico**<br/>Un rappresentante dell'organizzazione del supporto IT che può fornire commenti e suggerimenti sul supporto di questa modifica dal punto di vista del supporto tecnico. | R/I |
| Nome e indirizzo di posta elettronica per gli utenti pilota | **Utenti dotati di ruolo con privilegi**<br/>Il gruppo di utenti per cui è stata implementata la gestione delle identità con privilegi. È necessario essere in grado di attivare i ruoli una volta implementato Privileged Identity Management. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Stakeholder: Privileged Identity Management per i ruoli di Azure

| Name | Ruolo | Action |
| --- | --- | --- |
| Nome e indirizzo di posta elettronica | **Proprietario delle risorse o delle sottoscrizioni**<br/>Un rappresentante dei proprietari IT di ogni sottoscrizione o risorsa che si desidera distribuire Privileged Identity Management per | A/R/I |
| Nome e indirizzo di posta elettronica | **Responsabile della sicurezza**<br/>Un rappresentante del team addetto alla sicurezza che può approvare la conformità del piano ai requisiti di sicurezza dell'organizzazione. | A/R |
| Nome e indirizzo di posta elettronica | **Responsabile del supporto tecnico**<br/>Un rappresentante dell'organizzazione del supporto IT che può fornire commenti e suggerimenti sul supporto di questa modifica dal punto di vista del supporto tecnico. | R/I |
| Nome e indirizzo di posta elettronica per gli utenti pilota | **Utenti del ruolo di Azure**<br/>Il gruppo di utenti per cui è stata implementata la gestione delle identità con privilegi. È necessario essere in grado di attivare i ruoli una volta implementato Privileged Identity Management. | I |

### <a name="start-using-privileged-identity-management"></a>Iniziare a usare Privileged Identity Management

Come parte del processo di pianificazione, è necessario preparare Privileged Identity Management seguendo l'articolo [iniziare a usare Privileged Identity Management](pim-getting-started.md) . Privileged Identity Management consente di accedere ad alcune funzionalità progettate per facilitare la distribuzione.

Se l'obiettivo è la distribuzione di Privileged Identity Management per le risorse di Azure, è necessario seguire l'articolo [individuare le risorse di Azure da gestire in Privileged Identity Management](pim-resource-roles-discover-resources.md) . Solo i proprietari di sottoscrizioni e gruppi di gestione possono portare queste risorse sotto la gestione di Privileged Identity Management. Al termine della gestione, la funzionalità PIM è disponibile per i proprietari a tutti i livelli, inclusi gruppo di gestione, sottoscrizione, gruppo di risorse e risorsa. Per gli amministratori globali che tentano di distribuire Privileged Identity Management per le risorse di Azure, è possibile [elevare l'accesso per gestire tutte le sottoscrizioni di Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) per consentire l'accesso a tutte le risorse di Azure nella directory per l'individuazione. Tuttavia, si consiglia di ottenere l'approvazione da ogni proprietario della sottoscrizione prima di gestire le risorse con Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Applicare il principio di accesso con privilegi minimi

È importante assicurarsi di avere applicato il principio di privilegio minimo nell'organizzazione sia per il Azure AD che per i ruoli di Azure.

#### <a name="plan-least-privilege-delegation"></a>Delega dei privilegi minimi del piano

Per Azure AD ruoli, è comune per le organizzazioni assegnare il ruolo di amministratore globale a un numero di amministratori quando la maggior parte degli amministratori necessita solo di uno o due ruoli di amministratore specifici e meno avanzati. Con un numero elevato di amministratori globali o altri ruoli con privilegi elevati, è difficile tenere traccia delle assegnazioni di ruolo con privilegi sufficienti.

Attenersi alla procedura seguente per implementare il principio dei privilegi minimi per i ruoli del Azure AD.

1. Comprendere le caratteristiche specifiche dei ruoli leggendo le informazioni sui [ruoli di amministratore disponibili in Azure AD](../roles/permissions-reference.md#available-roles). È inoltre utile fare riferimento al documento sui [ruoli di amministratore per attività di identità in Azure AD](../roles/delegate-by-task.md), che presenta il ruolo con privilegi minimi da usare per ogni attività specifica.

1. Visualizzare l'elenco degli utenti che dispongono di ruoli con privilegi all'interno dell'organizzazione. È possibile usare l' [individuazione Privileged Identity Management e le informazioni dettagliate (anteprima)](pim-security-wizard.md) per ridurre l'esposizione.

    ![Pagina Discovery and Insights (anteprima) per ridurre l'esposizione tramite ruoli con privilegi](./media/pim-deployment-plan/new-preview-page.png)

1. Per tutti gli amministratori globali all'interno dell'organizzazione, individuare il motivo per cui devono avere tale ruolo. Quindi rimuoverli dal ruolo amministratore globale e assegnare ruoli predefiniti o ruoli personalizzati con privilegi inferiori all'interno Azure Active Directory. FYI, attualmente Microsoft dispone solo di circa 10 amministratori con il ruolo di amministratore globale. Altre informazioni sul [modo in cui Microsoft usa Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. Per tutti gli altri ruoli di Azure AD, esaminare l'elenco delle assegnazioni, identificare gli amministratori che non hanno più bisogno del ruolo assegnato e rimuoverli dalle rispettive assegnazioni.

Per automatizzare gli ultimi due passaggi, è possibile usare le verifiche di accesso in Privileged Identity Management. Seguendo i passaggi descritti in [avviare una verifica di accesso per i ruoli Azure ad in Privileged Identity Management](pim-how-to-start-security-review.md), è possibile configurare una verifica di accesso per ogni ruolo Azure ad che dispone di uno o più membri.

![Creare un riquadro di verifica di accesso per i ruoli Azure AD](./media/pim-deployment-plan/create-access-review.png)

Impostare i revisori sui **membri (auto)**. Tutti gli utenti del ruolo riceveranno un messaggio di posta elettronica in cui viene chiesto di confermare che necessitano dell'accesso. Inoltre, attivare **Richiedi motivo all'approvazione** nelle impostazioni avanzate, in modo che gli utenti debbano indicare il motivo per cui necessitano del ruolo. In base a queste informazioni, è possibile rimuovere gli utenti dai ruoli non necessari o delegarli a ruoli di amministratore più granulari.

Le verifiche di accesso si basano sulla posta elettronica per comunicare agli utenti di controllare il rispettivo accesso ai ruoli. Se si hanno alcuni account con privilegi a cui non è associato un indirizzo di posta elettronica, assicurarsi di specificare l'indirizzo di posta elettronica secondario per tali account. Per altre informazioni, vedere [Attributo proxyAddresses in Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Pianificare la delega del ruolo delle risorse di Azure

Per le sottoscrizioni e le risorse di Azure, è possibile configurare un processo di verifica di accesso simile per esaminare i ruoli in ogni sottoscrizione o risorsa. L'obiettivo di questo processo è ridurre al minimo le assegnazioni di proprietario e amministratore accesso utenti associate a ogni sottoscrizione o risorsa e rimuovere le assegnazioni non necessarie. Le organizzazioni, tuttavia, delegano spesso tali attività al proprietario di ogni sottoscrizione o risorsa perché ha una conoscenza più approfondita dei ruoli specifici (soprattutto per quanto riguarda i ruoli personalizzati).

Se si ha il ruolo di amministratore globale che tenta di distribuire PIM per i ruoli di Azure nell'organizzazione, è possibile [elevare l'accesso per gestire tutte le sottoscrizioni di Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) per ottenere l'accesso a ogni sottoscrizione. È quindi possibile contattare i singoli proprietari delle sottoscrizioni e collaborare con loro per rimuovere le assegnazioni non necessarie e ridurre al minimo le assegnazioni del ruolo Proprietario.

Gli utenti con il ruolo proprietario per una sottoscrizione di Azure possono anche usare le verifiche di [accesso per le risorse di Azure](pim-resource-roles-start-access-review.md) per controllare e rimuovere le assegnazioni di ruolo non necessarie in modo analogo al processo descritto in precedenza per Azure ad ruoli.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Decidere quali assegnazioni di ruolo devono essere protette da Privileged Identity Management

Dopo aver pulito le assegnazioni di ruolo con privilegi all'interno dell'organizzazione, è necessario decidere quali ruoli proteggere con Privileged Identity Management.

Se un ruolo è protetto da Privileged Identity Management, gli utenti idonei ad essi assegnati dovranno elevare per utilizzare i privilegi concessi dal ruolo. Il processo di elevazione può includere anche l'approvazione, l'uso dell'autenticazione a più fattori e il motivo per cui si stanno attivando. Privileged Identity Management inoltre possibile rilevare le elevazioni tramite le notifiche e il Privileged Identity Management e Azure AD i registri eventi di controllo.

La scelta dei ruoli da proteggere con Privileged Identity Management può essere difficile e sarà diversa per ogni organizzazione. Questa sezione illustra le procedure consigliate per Azure AD e i ruoli di Azure.

#### <a name="azure-ad-roles"></a>Ruoli di Azure AD

È importante assegnare la priorità alla protezione dei ruoli di Azure AD con la maggior parte delle autorizzazioni. In base ai modelli di utilizzo di tutti i Privileged Identity Management clienti, i primi 10 Azure AD ruoli gestiti da Privileged Identity Management sono:

1. Amministratore globale
1. Amministratore della sicurezza
1. Amministratore utenti
1. Amministratori di Exchange
1. Amministratore di SharePoint
1. Amministratore di Intune
1. Ruolo con autorizzazioni di lettura per la sicurezza
1. Amministratore del servizio
1. Amministratore fatturazione
1. Amministratore di Skype for Business

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire tutti gli amministratori globali e gli amministratori della sicurezza usando Privileged Identity Management come primo passaggio, perché sono gli utenti che possono eseguire la maggior parte del danno quando vengono compromessi.

È importante considerare i dati e le autorizzazioni più sensibili per l'organizzazione. È ad esempio possibile che alcune organizzazioni vogliano proteggere il proprio ruolo di amministratore di Power BI o il ruolo di amministratore dei team usando Privileged Identity Management, poiché possono accedere ai dati e modificare i flussi di lavoro principali.

Se sono presenti ruoli con utenti guest assegnati, questi sono vulnerabili agli attacchi.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire tutti i ruoli con gli utenti guest utilizzando Privileged Identity Management per ridurre i rischi associati agli account utente Guest compromessi.

I ruoli di lettore come il lettore di directory, il lettore del centro messaggi e il lettore di sicurezza vengono talvolta considerati meno importanti rispetto ad altri ruoli perché non hanno l'autorizzazione di scrittura. Tuttavia, abbiamo alcuni clienti che proteggono anche questi ruoli perché gli utenti malintenzionati con accesso a questi account potrebbero essere in grado di leggere dati sensibili, ad esempio i dati personali. Prendere in considerazione questo rischio quando si decide se si desidera che i ruoli di lettore nell'organizzazione vengano gestiti mediante Privileged Identity Management.

#### <a name="azure-roles"></a>Ruoli di Azure

Quando si decide quali assegnazioni di ruolo devono essere gestite usando Privileged Identity Management per la risorsa di Azure, è necessario innanzitutto identificare le sottoscrizioni e le risorse più importanti per l'organizzazione. Ecco alcuni esempi di sottoscrizioni o risorse di questo tipo:

- Risorse che ospitano i dati più sensibili
- Risorse da cui dipendono le applicazioni principali destinate direttamente ai clienti

Se si è un amministratore globale che ha difficoltà a decidere quali sottoscrizioni e risorse sono più importanti, è necessario contattare i proprietari della sottoscrizione dell'organizzazione per raccogliere un elenco di risorse gestite da ogni sottoscrizione. Quindi, collaborare con i proprietari della sottoscrizione per raggruppare le risorse in base al livello di gravità nel caso in cui siano compromesse (bassa, media, alta). Assegnare priorità alla gestione delle risorse con Privileged Identity Management in base a questo livello di gravità.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di collaborare con i proprietari di sottoscrizioni o risorse di servizi critici per configurare il flusso di lavoro Privileged Identity Management per tutti i ruoli all'interno di sottoscrizioni o risorse sensibili.

Privileged Identity Management per le risorse di Azure supporta gli account del servizio con associazione temporale. È consigliabile gestire questi account di servizio esattamente come un account utente normale.

Per sottoscrizioni o risorse non cruciali, non è necessario configurare Privileged Identity Management per tutti i ruoli. Tuttavia, è comunque necessario proteggere i ruoli proprietario e amministratore accesso utenti con Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire i ruoli proprietario e amministratore accesso utenti di tutte le sottoscrizioni o risorse usando Privileged Identity Management.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Decidere se usare un gruppo per assegnare i ruoli

Se assegnare un ruolo a un gruppo anziché a singoli utenti è una decisione strategica. Quando si pianifica, è consigliabile assegnare un ruolo a un gruppo per gestire le assegnazioni di ruolo nei casi seguenti:

- Molti utenti sono assegnati a un ruolo
- Si desidera delegare l'assegnazione del ruolo

#### <a name="many-users-are-assigned-to-a-role"></a>Molti utenti sono assegnati a un ruolo

Tenere traccia degli utenti assegnati a un ruolo e gestire le relative assegnazioni in base al momento in cui sono necessarie, quando vengono eseguite manualmente. Per assegnare un gruppo a un ruolo, [creare innanzitutto un gruppo assegnabile di ruolo](../roles/groups-create-eligible.md) e quindi assegnare il gruppo come idoneo per un ruolo. Questa azione sottopone a tutti gli utenti del gruppo lo stesso processo di attivazione dei singoli utenti idonei a elevare il ruolo. I membri del gruppo attivano le assegnazioni al gruppo singolarmente utilizzando la richiesta di attivazione Privileged Identity Management e il processo di approvazione. Il gruppo non è attivato, bensì solo l'appartenenza al gruppo dell'utente.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Si desidera delegare l'assegnazione del ruolo

Un proprietario del gruppo può gestire l'appartenenza a un gruppo. Per Azure AD gruppi assegnabili al ruolo, solo l'amministratore del ruolo con privilegi, l'amministratore globale e i proprietari del gruppo possono gestire l'appartenenza al gruppo. Aggiungendo nuovi membri al gruppo, il membro ottiene l'accesso ai ruoli a cui il gruppo viene assegnato se l'assegnazione è idonea o attiva. Usare i proprietari del gruppo per delegare la gestione dell'appartenenza al gruppo per un ruolo assegnato in modo da ridurre la gamma di privilegi necessari. Per ulteriori informazioni sull'assegnazione di un proprietario a un gruppo durante la creazione del gruppo, vedere [creare un gruppo assegnabile al ruolo in Azure ad](../roles/groups-create-eligible.md).

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di importare Azure ad gruppi assegnabili al ruolo gestiti da Privileged Identity Management. Quando un gruppo assegnabile al ruolo viene portato sotto la gestione da PIM, viene chiamato gruppo di accesso con privilegi. Usare PIM per richiedere ai proprietari del gruppo di attivare l'assegnazione del ruolo di proprietario prima di poter gestire l'appartenenza al gruppo. Per ulteriori informazioni sull'attivazione di gruppi in gestione PIM, vedere la pagina relativa all'introduzione [ai gruppi di accesso con privilegi (anteprima) in Privileged Identity Management](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidere quali assegnazioni di ruolo devono essere permanenti o idonee

Una volta deciso l'elenco dei ruoli da gestire con Privileged Identity Management, è necessario decidere quali utenti devono ottenere il ruolo idoneo rispetto al ruolo attivo permanente. I ruoli attivi in modo permanente sono i normali ruoli assegnati tramite Azure Active Directory e le risorse di Azure, mentre i ruoli idonei possono essere assegnati solo in Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di disporre di zero assegnazioni attive in modo permanente sia per i ruoli di Azure ad che per i ruoli di Azure, ad eccezione dei [due account di accesso di emergenza break-Glass](../roles/security-emergency-access.md)consigliati, che devono avere il ruolo di amministratore globale permanente.

Anche se è consigliabile una configurazione senza amministratori permanenti, talvolta le organizzazioni possono avere difficoltà a implementare immediatamente una configurazione di questo tipo. Ecco alcuni aspetti da considerare nel prendere questa decisione:

- Frequenza di elevazione dei privilegi: se l'utente ha bisogno di un'assegnazione con privilegi una sola volta, non è necessario configurare l'assegnazione permanente. D'altra parte, se l'utente ha bisogno del ruolo per il proprio lavoro giornaliero e l'uso di Privileged Identity Management ridurrebbe notevolmente la produttività, è possibile considerarlo per il ruolo permanente.
- Casi specifici dell'organizzazione: se la persona a cui viene assegnato il ruolo idoneo è da un team distante o da un dirigente di rango elevato fino al punto in cui la comunicazione e l'applicazione del processo di elevazione è difficile, è possibile prendere in considerazione il ruolo permanente.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di configurare le verifiche di accesso ricorrenti per gli utenti con assegnazioni di ruolo permanenti (se presenti). Altre informazioni sulle verifiche di accesso periodiche sono riportate nella sezione finale di questo piano di distribuzione.

### <a name="draft-your-privileged-identity-management-settings"></a>Bozza delle impostazioni di Privileged Identity Management

Prima di implementare la soluzione di Privileged Identity Management, è consigliabile elaborare le impostazioni del Privileged Identity Management per ogni ruolo con privilegi utilizzati dall'organizzazione. Questa sezione contiene alcuni esempi di impostazioni di Privileged Identity Management per determinati ruoli (sono solo per riferimento e potrebbero essere diversi per l'organizzazione). Ognuna di queste impostazioni è descritta in dettaglio, con consigli di Microsoft, dopo le tabelle.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Impostazioni Privileged Identity Management per i ruoli di Azure AD

| Ruolo | Richiedere l'autenticazione MFA | Notifica | Ticket di evento imprevisto | Richiedi approvazione | Responsabile approvazione | Durata attivazione | Amministratore permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Amministratore globale | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Altri amministratori globali | 1 ora | Account di accesso di emergenza |
| Amministratore di Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | nessuno | 2 ore | nessuno |
| Amministratore del supporto tecnico | :x: | :x: | :heavy_check_mark: | :x: | nessuno | 8 ore | nessuno |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Impostazioni Privileged Identity Management per i ruoli di Azure

| Ruolo | Richiedere l'autenticazione MFA | Notifica | Richiedi approvazione | Responsabile approvazione | Durata attivazione | Amministratore attivo | Scadenza assegnazioni attive | Scadenza assegnazioni idonee |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietario di sottoscrizioni critiche | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Altri proprietari della sottoscrizione | 1 ora | nessuno | n/d | 3 mesi |
| Amministratore Accesso utenti di sottoscrizioni meno critiche | :heavy_check_mark: | :heavy_check_mark: | :x: | nessuno | 1 ora | nessuno | n/d | 3 mesi |
| Collaboratore macchine virtuali | :x: | :heavy_check_mark: | :x: | nessuno | 3 ore | nessuno | n/d | 6 mesi |

La tabella seguente include le descrizioni per ciascuna delle impostazioni.

| Impostazione | Descrizione |
| --- | --- |
| Ruolo | Nome del ruolo per cui si definiscono le impostazioni. |
| Richiedere l'autenticazione MFA | Indica se l'utente idoneo deve eseguire l'autenticazione a più fattori (MFA) prima dell'attivazione del ruolo.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di applicare l'autenticazione a più fattori per tutti i ruoli di amministratore, soprattutto se i ruoli hanno utenti guest. |
| Notifica | Se è impostato l'invio di notifiche, gli utenti dell'organizzazione con i ruoli Amministratore globale, Amministratore dei ruoli con privilegi e Amministratore della sicurezza ricevono una notifica di posta elettronica quando un utente idoneo attiva il ruolo.<br/><br/>**Nota:** Alcune organizzazioni non dispongono di un indirizzo di posta elettronica associato agli account di amministratore. per ricevere queste notifiche di posta elettronica, è necessario impostare un indirizzo di posta elettronica alternativo in modo che gli amministratori ricevano questi messaggi. |
| Ticket di evento imprevisto | Indica se l'utente idoneo deve registrare un numero di ticket di evento imprevisto quando attiva il proprio ruolo. Questa impostazione consente a un'organizzazione di identificare ogni attivazione con un numero di evento imprevisto interno per limitare le attivazioni indesiderate.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di sfruttare i numeri dei biglietti per gli eventi imprevisti per associare Privileged Identity Management al sistema interno. Questo metodo può essere utile per i responsabili approvazione che necessitano di contesto per l'attivazione. |
| Richiedi approvazione | Indica se l'utente idoneo deve ottenere l'approvazione per attivare il ruolo.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di impostare l'approvazione per i ruoli con la massima autorizzazione. In base ai modelli di utilizzo di tutti i Privileged Identity Management clienti, amministratore globale, amministratore utente, amministratore di Exchange, amministratore della sicurezza e amministratore password sono i ruoli più comuni con la configurazione dell'approvazione. |
| Responsabile approvazione | Se per attivare il ruolo idoneo è richiesta l'approvazione, indicare le persone che dovrebbero approvare la richiesta. Per impostazione predefinita, Privileged Identity Management imposta il responsabile approvazione come tutti gli utenti che sono amministratori dei ruoli con privilegi, che siano permanenti o idonei.<br/><br/>**Nota:** Se un utente è idoneo per un ruolo Azure AD e un responsabile approvazione del ruolo, non sarà in grado di approvare se stesso.<br/><br/> : heavy_check_mark: **Microsoft consiglia** di scegliere gli approvatori come utenti più esperti del ruolo e dei relativi utenti frequenti anziché come amministratore globale. |
| Durata attivazione | Periodo di tempo durante il quale il ruolo di un utente rimarrà attivo prima della scadenza. |
| Amministratore permanente | Elenco degli utenti che saranno amministratori permanenti per il ruolo (l'attivazione non è mai necessaria).<br/><br/> : heavy_check_mark: **Microsoft consiglia** di disporre di un amministratore con zero diritti per tutti i ruoli, ad eccezione degli amministratori globali. Per altre informazioni, vedere la sezione di questo piano che contiene suggerimenti per decidere quali assegnazioni di ruolo devono essere idonee e quali devono essere attive in modo permanente. |
| Amministratore attivo | Per le risorse di Azure, un amministratore attivo è uno degli utenti che non devono eseguire l'attivazione per usare il ruolo. Questo elenco non è definito amministratore permanente come nei ruoli Azure AD perché è possibile impostare una data di scadenza per la perdita di questo ruolo da parte dell'utente. |
| Scadenza assegnazioni attive | Le assegnazioni di ruolo attive per i ruoli di Azure scadono dopo la durata configurata. È possibile scegliere tra 15 giorni, 1 mese, 3 mesi, 6 mesi, 1 anno oppure impostare un'assegnazione attiva in modo permanente. |
| Scadenza assegnazioni idonee | Le assegnazioni di ruolo idonee per i ruoli di Azure scadono dopo tale periodo di validità. È possibile scegliere tra 15 giorni, 1 mese, 3 mesi, 6 mesi, 1 anno oppure impostare un'assegnazione idonea in modo permanente. |

## <a name="implementation-plan"></a>Piano di implementazione

Una pianificazione appropriata deve avere alla base la possibilità di distribuire correttamente un'applicazione con Azure Active Directory.  Deve inoltre offrire soluzioni intelligenti per la sicurezza e l'integrazione in modo da semplificare l'onboarding e ridurre così il tempo necessario per le distribuzioni.  In questo modo si ha la sicurezza che l'applicazione venga integrata con facilità riducendo i tempi di inattività per gli utenti finali.

### <a name="identify-test-users"></a>Identificare gli utenti di test

Usare questa sezione per identificare un set di utenti e/o gruppi di utenti per convalidare l'implementazione. In base alle impostazioni selezionate nella sezione dedicata alla pianificazione, identificare gli utenti desiderati per testare ogni ruolo.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di fare in modo che i proprietari del servizio di ogni ruolo di Azure ad siano gli utenti di test in modo che possano acquisire familiarità con il processo e diventare un sostenitore interno per la distribuzione.

In questa tabella identificare gli utenti di test che verificheranno che le impostazioni per i ruoli funzionino.

| Nome del ruolo | Utenti di test |
| --- | --- |
| &lt;Nome del ruolo&gt; | &lt;Utenti per testare il ruolo&gt; |
| &lt;Nome del ruolo&gt; | &lt;Utenti per testare il ruolo&gt; |

### <a name="test-implementation"></a>Testare l'implementazione

Ora che sono stati identificati gli utenti di test, usare questo passaggio per configurare Privileged Identity Management per gli utenti di test. Se l'organizzazione vuole incorporare Privileged Identity Management flusso di lavoro nella propria applicazione interna invece di usare Privileged Identity Management nel portale di Azure, tutte le operazioni in Privileged Identity Management sono supportate anche tramite l' [API Graph](/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configurare Privileged Identity Management per i ruoli di Azure AD

1. [Configurare le impostazioni del ruolo Azure ad](pim-how-to-change-default-settings.md) in base a ciò che è stato pianificato.

1. Passare a **Azure ad ruoli**, selezionare **ruoli**, quindi selezionare il ruolo configurato.

1. Per il gruppo di utenti di test, se sono già amministratori permanenti, è possibile renderli idonei cercandoli e convertirli da Permanent a idonei selezionando i tre puntini sulla riga. Se invece agli utenti non sono stati ancora assegnati ruoli, è possibile [definire una nuova assegnazione idonea](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Ripetere i passaggi da 1 a 3 per tutti i ruoli da testare.

1. Dopo aver configurato gli utenti di test, è necessario inviare loro il collegamento per [attivare il proprio ruolo di Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Configurare Privileged Identity Management per i ruoli di Azure

1. [Configurare le impostazioni dei ruoli delle risorse di Azure](pim-resource-roles-configure-role-settings.md) per un ruolo all'interno di una sottoscrizione o una risorsa che si vuole testare.

1. Passare a **risorse di Azure** per la sottoscrizione e selezionare **ruoli**e selezionare il ruolo configurato.

1. Se gli utenti del gruppo di test sono già configurati come amministratori attivi, per renderli idonei è possibile cercarli e [aggiornare la rispettiva assegnazione di ruolo](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se invece gli utenti non hanno ancora un ruolo, è possibile [assegnarne uno nuovo](pim-resource-roles-assign-roles.md#assign-a-role).

1. Ripetere i passaggi da 1 a 3 per tutti i ruoli da testare.

1. Dopo aver configurato gli utenti di test, è necessario inviare loro il collegamento per [attivare il rispettivo ruolo delle risorse di Azure](pim-resource-roles-activate-your-roles.md).

È consigliabile usare questa fase per verificare se l'intera configurazione definita per i ruoli funziona correttamente. Usare la tabella seguente per documentare i test. Questa fase è utile anche per ottimizzare le comunicazioni con gli utenti interessati.

| Ruolo | Comportamento previsto durante l'attivazione | Risultati effettivi |
| --- | --- | --- |
| Amministratore globale | (1) Richiedere l'autenticazione MFA<br/>(2) Richiedere l'approvazione<br/>(3) Il responsabile dell'approvazione riceve una notifica e può approvare l'assegnazione<br/>(4) Il ruolo scade dopo il tempo prestabilito |  |
| Proprietario della sottoscrizione *X* | (1) Richiedere l'autenticazione MFA<br/>(2) L'assegnazione idonea scade dopo il periodo di tempo configurato |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Comunicare Privileged Identity Management alle parti interessate interessate

La distribuzione di Privileged Identity Management introdurrà passaggi aggiuntivi per gli utenti con ruoli con privilegi. Sebbene Privileged Identity Management riduca notevolmente i problemi di sicurezza associati alle identità con privilegi, la modifica deve essere effettivamente comunicata prima della distribuzione a livello dell'organizzazione. A seconda del numero di amministratori interessati, le organizzazioni scelgono spesso di creare un documento interno, un video o un messaggio di posta elettronica per comunicare la novità. In queste comunicazioni sono spesso incluse le informazioni seguenti:

- Che cos'è PIM
- Quale vantaggio offre all'organizzazione
- Quali saranno gli utenti interessati
- Quando verrà implementato PIM
- Quali passaggi in più dovranno eseguire gli utenti per attivare il proprio ruolo
    - È consigliabile inviare collegamenti alla documentazione di Microsoft:
    - [Attivare i ruoli di Azure AD](pim-how-to-activate-role.md)
    - [Attivare i ruoli di Azure](pim-resource-roles-activate-your-roles.md)
- Informazioni di contatto o collegamento al supporto tecnico per eventuali problemi relativi a PIM

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di impostare il tempo con il team di supporto/supporto tecnico per esaminare il flusso di lavoro Privileged Identity Management (se l'organizzazione dispone di un team di supporto IT interno). Fornire loro la documentazione appropriata e le informazioni di contatto.

### <a name="move-to-production"></a>Passare in produzione

Al termine del test, spostare Privileged Identity Management in produzione ripetendo tutti i passaggi nelle fasi di testing per tutti gli utenti di ogni ruolo definito nella configurazione del Privileged Identity Management. Per Privileged Identity Management per i ruoli di Azure AD, le organizzazioni spesso testano e implementano Privileged Identity Management per gli amministratori globali prima di testare e distribuire Privileged Identity Management per altri ruoli. Nel frattempo, per le risorse di Azure, le organizzazioni normalmente testano e implementano Privileged Identity Management una sottoscrizione di Azure alla volta.

### <a name="if-a-rollback-is-needed"></a>Se è necessario un rollback

Se Privileged Identity Management non è riuscito a funzionare nel modo desiderato nell'ambiente di produzione, i passaggi di rollback seguenti possono risultare utili per ripristinare uno stato valido noto prima di configurare Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Ruoli di Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Aprire **Azure ad Privileged Identity Management**.
1. Selezionare **Azure ad ruoli** , quindi selezionare **ruoli**.
1. Per ogni ruolo configurato, selezionare i puntini di sospensione (**...**) per tutti gli utenti che dispongono di un'assegnazione idonea.
1. Selezionare l'opzione **Rendi permanente** per rendere permanente l'assegnazione di ruolo.

#### <a name="azure-roles"></a>Ruoli di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Aprire **Azure ad Privileged Identity Management**.
1. Selezionare **risorse di Azure** , quindi selezionare una sottoscrizione o una risorsa di cui si vuole eseguire il rollback.
1. Selezionare **Ruoli**.
1. Per ogni ruolo configurato, selezionare i puntini di sospensione (**...**) per tutti gli utenti che dispongono di un'assegnazione idonea.
1. Selezionare l'opzione **Rendi permanente** per rendere permanente l'assegnazione di ruolo.

## <a name="next-steps-after-deploying"></a>Passaggi successivi dopo la distribuzione

La distribuzione di Privileged Identity Management nell'ambiente di produzione è un importante passo avanti in termini di protezione delle identità con privilegi dell'organizzazione. Con la distribuzione di Privileged Identity Management vengono fornite funzionalità Privileged Identity Management aggiuntive da utilizzare per la sicurezza e la conformità.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Usare Privileged Identity Management avvisi per salvaguardare l'accesso con privilegi

Per ulteriori informazioni sull'utilizzo della funzionalità di avviso incorporata Privileged Identity Management per proteggere l'organizzazione, vedere [avvisi di sicurezza](pim-how-to-configure-security-alerts.md#security-alerts). Questi avvisi includono: gli amministratori non usano i ruoli con privilegi, i ruoli vengono assegnati all'esterno del Privileged Identity Management, i ruoli vengono attivati troppo spesso e molto altro. Per proteggere completamente la propria organizzazione, è necessario scorrere regolarmente l'elenco degli avvisi e risolvere i problemi. Per visualizzare e risolvere gli avvisi, procedere nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Aprire **Azure ad Privileged Identity Management**.
1. Selezionare **Azure ad ruoli** , quindi selezionare **avvisi**.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di gestire immediatamente tutti gli avvisi contrassegnati con una gravità elevata. Per quelli con livello di gravità medio e basso, è necessario tenersi informati e intervenire se si ritiene che sia presente una minaccia per la sicurezza.

Se uno degli avvisi non è utile o non è applicabile alla propria organizzazione, è sempre possibile ignorarlo nella pagina degli avvisi ed eventualmente ripristinarlo in un secondo momento nella pagina delle impostazioni di Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurare verifiche di accesso periodiche per controllare regolarmente le identità con privilegi dell'organizzazione

Le verifiche di accesso sono il modo migliore per chiedere agli utenti cui sono assegnati ruoli con privilegi o a revisori specifici se hanno effettivamente bisogno dell'identità con privilegi. Le verifiche di accesso sono molto utili se si vuole ridurre la superficie di attacco assicurando al tempo stesso la conformità. Per altre informazioni sull'avvio di una verifica di accesso, vedere verifiche di [accesso dei ruoli di Azure ad](pim-how-to-start-security-review.md) e verifiche di [accesso ai ruoli di Azure](pim-resource-roles-start-access-review.md). Per alcune organizzazioni, l'esecuzione di una verifica di accesso periodica è necessaria per garantire la conformità alle leggi e alle normative, mentre per altre una verifica di accesso è il modo migliore per applicare il principio di accesso con privilegi minimi in tutta l'organizzazione.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di configurare le verifiche di accesso trimestrali per tutti i ruoli di Azure ad e Azure.

Nella maggior parte dei casi, il revisore per Azure AD ruoli è costituito dagli utenti stessi, mentre il revisore per i ruoli di Azure è il proprietario della sottoscrizione, in cui si trova il ruolo. Tuttavia, capita spesso che le aziende abbiano account con privilegi che non sono collegati all'indirizzo di posta elettronica di una persona particolare. In questi casi, nessuno legge i messaggi e verifica i privilegi di accesso.

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di aggiungere un indirizzo di posta elettronica secondario per tutti gli account con assegnazioni di ruolo con privilegi che non sono collegati a un indirizzo di posta elettronica controllato regolarmente

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Sfruttare al meglio il log di controllo per migliorare la sicurezza e la conformità

Il log di controllo è lo strumento che consente di rimanere sempre aggiornati e assicurare la conformità alle normative. Privileged Identity Management archivia attualmente una cronologia di 30 giorni di tutta la cronologia dell'organizzazione nel log di controllo, tra cui:

- Attivazione o disattivazione dei ruoli idonei
- Attività di assegnazione di ruolo all'interno e all'esterno di Privileged Identity Management
- Modifiche nelle impostazioni dei ruoli
- Attività di richiesta, approvazione o rifiuto per l'attivazione dei ruoli con configurazione dell'approvazione
- Aggiornamenti degli avvisi

È possibile accedere ai log di controllo se si è un amministratore globale o un amministratore del ruolo con privilegi. Per altre informazioni, vedere [cronologia di controllo per i ruoli Azure ad](pim-how-to-use-audit-log.md) e [cronologia di controllo per i ruoli di Azure](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di fare in modo che almeno un amministratore Legga tutti gli eventi di controllo su base settimanale ed esporti gli eventi di controllo su base mensile.

Se si desidera archiviare automaticamente gli eventi di controllo per un periodo di tempo più lungo, il registro di controllo di Privileged Identity Management viene sincronizzato automaticamente nei [log di controllo Azure ad](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **Microsoft consiglia** di configurare il monitoraggio dei [log di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) per archiviare gli eventi di controllo in un account di archiviazione di Azure per una maggiore sicurezza e conformità.
