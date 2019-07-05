---
title: Distribuzione di Privileged Identity Management (PIM) - Azure Active Directory | Microsoft Docs
description: Descrive come pianificare la distribuzione di Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7413fcf7992195753cba86a50b7d53a144b36023
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476431"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Distribuire Azure AD Privileged Identity Management (PIM)

In questa Guida dettagliata descrive come pianificare la distribuzione di Azure Active Directory (Azure AD) Privileged Identity Management (PIM) nell'organizzazione.

> [!TIP]
> In questo documento alcuni elementi sono contrassegnati con:
> 
> :heavy_check_mark: **Microsoft consiglia**
> 
> Si tratta di indicazioni di carattere generale che è opportuno implementare solo se sono applicabili alle specifiche esigenze aziendali.

## <a name="step-1-learn-about-pim"></a>Passaggio 1. Conoscere PIM

Azure AD Privileged Identity Management (PIM) consente di gestire i ruoli amministrativi con privilegi in Azure AD, nelle risorse di Azure e in altri servizi online di Microsoft. Poiché le identità con privilegi vengono spesso assegnate e poi dimenticate, PIM offre diverse soluzioni, come l'accesso just-in-time, i flussi di lavoro di richiesta di approvazione e le verifiche di accesso completamente integrate, per consentire di identificare, scoprire e impedire in tempo reale eventuali attività dannose di ruoli con privilegi. Distribuendo PIM per gestire i ruoli con privilegi in tutta l'organizzazione si possono ridurre notevolmente i rischi e al tempo stesso ottenere informazioni dettagliate preziose sulle attività svolte dai ruoli con privilegi.

### <a name="business-value-of-pim"></a>Valore di PIM a livello aziendale

**Gestire i rischi**: è possibile proteggere la propria organizzazione applicando il principio di [accesso con privilegi minimi](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e just-in-time. Riducendo al minimo il numero di assegnazioni permanenti degli utenti ai ruoli con privilegi e richiedendo approvazioni e autenticazione a più fattori per l'elevazione dei privilegi, è possibile ridurre notevolmente i rischi per la sicurezza correlati all'accesso con privilegi all'interno dell'organizzazione. L'applicazione dell'accesso con privilegi minimi e just-in-time consente inoltre di visualizzare una cronologia dell'accesso ai ruoli con privilegi e di identificare eventuali problemi di sicurezza in tempo reale.

**Gestire la conformità e la governance**: con la distribuzione di PIM si crea un ambiente per la governance costante delle identità. L'elevazione just-in-time delle identità con privilegi consente a PIM di tenere traccia delle attività di accesso con privilegi nell'organizzazione. È inoltre possibile visualizzare e ricevere notifiche per tutte le assegnazioni di ruoli idonei e permanenti all'interno dell'organizzazione. Tramite la verifica di accesso, si possono regolarmente controllare e rimuovere le identità con privilegi non necessarie e assicurarsi che l'organizzazione sia conforme agli standard di identità, accesso e sicurezza più rigorosi.

**Ridurre i costi**: con la corretta distribuzione di PIM si possono ridurre i costi eliminando inefficienze, errori umani e problemi di sicurezza. Il risultato di tutto questo è una riduzione dei crimini informatici associati alle identità con privilegi, che comportano notevoli costi e difficoltà di ripristino. PIM consente anche alle organizzazioni di ridurre i costi associati al controllo delle informazioni di accesso per garantire la conformità a normative e standard.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

### <a name="licensing-requirements"></a>Requisiti di licenza

Per usare PIM, la directory deve avere una delle licenze di valutazione o a pagamento seguenti:

- Azure AD P2 Premium
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Per altre informazioni, vedere [Requisiti della licenza per usare PIM](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Terminologia essenziale di PIM

| Termine o concetto | DESCRIZIONE |
| --- | --- |
| idoneo | Un'assegnazione di ruolo che richiede a un utente di eseguire una o più azioni per usare il ruolo. Se un utente è stato reso idoneo per un ruolo, potrà attivare il ruolo quando avrà bisogno di svolgere le attività con privilegi. Non esiste alcuna differenza sostanziale tra l'accesso concesso a un utente con l'assegnazione permanente e quello con l'assegnazione di idoneità al ruolo. L'unica differenza è che alcuni utenti non necessitano dell'accesso continuo. |
| attiva | Il processo di esecuzione di una o più azioni per usare un ruolo per cui un utente è idoneo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati. |
| accesso just-in-time (JIT) | Un modello in base al quale gli utenti ricevono autorizzazioni temporanee per eseguire attività con privilegi, che impedisce a utenti non autorizzati o malintenzionati di ottenere l'accesso dopo la scadenza delle autorizzazioni. L'accesso viene concesso solo quando l'utente ne ha necessità. |
| principio di accesso con privilegi minimi | Una procedura consigliata per la sicurezza in base alla quale ogni utente viene dotato solo dei privilegi minimi necessari per eseguire le attività che è autorizzato a svolgere. Ciò consente di ridurre al minimo il numero di amministratori globali usando in alternativa ruoli di amministratore specifici per determinati scenari. |

Per altre informazioni, vedere [Terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Panoramica generale del funzionamento di PIM

1. PIM viene configurato in modo da consentire agli utenti di risultare idonei a ruoli con privilegi.
1. Quando un utente idoneo deve usare il proprio ruolo con privilegi, procede all'attivazione del ruolo in PIM.
1. A seconda delle impostazioni di PIM configurate per il ruolo, l'utente deve completare determinate operazioni, ad esempio eseguire l'autenticazione a più fattori, ottenere l'approvazione o specificare un motivo.
1. Dopo che l'utente ha eseguito l'attivazione del ruolo, può usarlo per un periodo di tempo preconfigurato.
1. Gli amministratori possono visualizzare una cronologia di tutte le attività di PIM nel log di controllo. Possono inoltre assicurare una maggiore protezione dei tenant e soddisfare gli standard di conformità usando altre funzionalità di PIM come le verifiche di accesso e gli avvisi.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

### <a name="roles-that-can-be-managed-by-pim"></a>Ruoli che possono essere gestiti da PIM

**Ruoli di Azure AD** : questi ruoli sono tutti in Azure Active Directory (ad esempio amministratore globale, amministratore di Exchange e amministratore della sicurezza). Altre informazioni sui ruoli e sulle relative funzionalità sono disponibili in [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Per informazioni su come determinare i ruoli da assegnare agli amministratori, vedere [Ruoli con privilegi minimi per attività](../users-groups-roles/roles-delegate-by-task.md).

**Ruoli delle risorse di Azure**: i ruoli collegati a una risorsa, a un gruppo di risorse, a una sottoscrizione o a un gruppo di gestione in Azure. PIM offre l'accesso just-in-time sia ai ruoli predefiniti, ad esempio Proprietario, Amministratore Accesso utenti e Collaboratore, sia ai [ruoli personalizzati](../../role-based-access-control/custom-roles.md). Per altre informazioni sui ruoli delle risorse di Azure, vedere [Controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md).

Per altre informazioni, vedere [Ruoli che non è possibile gestire in PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Passaggio 2. Pianificare la distribuzione

Questa sezione illustra in particolare le operazioni da eseguire prima di distribuire PIM all'interno dell'organizzazione. È essenziale seguire le istruzioni e comprendere i concetti presentati poiché saranno utili per creare un piano ottimale, personalizzato in base alle identità con privilegi dell'organizzazione.

### <a name="identify-your-stakeholders"></a>Identificare gli stakeholder

La sezione seguente consente di identificare tutti gli stakeholder coinvolti nel progetto che hanno compiti di approvazione o revisione o che comunque devono tenersi informati sul progetto. Sono incluse tabelle separate per la distribuzione di ruoli di PIM per Azure AD e di PIM per le risorse di Azure. Aggiungere i nomi degli stakeholder alla tabella seguente in base alla struttura della propria organizzazione.

- A = Approvare il progetto
- R = Rivedere il progetto e fornire pareri
- I = Tenersi informati sul progetto

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Stakeholder: ruoli di PIM per Azure AD

| Attività | Ruolo | Azione |
| --- | --- | --- |
| Nome e indirizzo di posta elettronica | **Architetto delle identità o Amministratore globale di Azure**<br/>Un rappresentante del team di gestione delle identità che è responsabile di definire come questa modifica è allineata all'infrastruttura di base per la gestione delle identità all'interno dell'organizzazione. | A/R/I |
| Nome e indirizzo di posta elettronica | **Proprietario del servizio o diretto superiore**<br/>Un rappresentante dei proprietari IT di un servizio o di un gruppo di servizi. È fondamentale per prendere decisioni e aiutare a implementare PIM per il proprio team. | A/R/I |
| Nome e indirizzo di posta elettronica | **Responsabile della sicurezza**<br/>Un rappresentante del team addetto alla sicurezza che può approvare la conformità del piano ai requisiti di sicurezza dell'organizzazione. | A/R |
| Nome e indirizzo di posta elettronica | **Responsabile del supporto tecnico**<br/>Un rappresentante dell'organizzazione di supporto IT che può fornire pareri riguardanti l'attuabilità di questa modifica dal punto di vista del supporto tecnico. | R/I |
| Nome e indirizzo di posta elettronica per gli utenti pilota | **Utenti dotati di ruolo con privilegi**<br/>Il gruppo di utenti per cui è stata implementata la gestione delle identità con privilegi. Devono sapere come attivare i propri ruoli dopo che PIM è stato implementato. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Stakeholder: ruoli di PIM per le risorse di Azure

| Attività | Ruolo | Azione |
| --- | --- | --- |
| Nome e indirizzo di posta elettronica | **Proprietario delle risorse o delle sottoscrizioni**<br/>Un rappresentante dei proprietari IT di ogni sottoscrizione o risorsa per cui si vuole distribuire PIM. | A/R/I |
| Nome e indirizzo di posta elettronica | **Responsabile della sicurezza**<br/>Un rappresentante del team addetto alla sicurezza che può approvare la conformità del piano ai requisiti di sicurezza dell'organizzazione. | A/R |
| Nome e indirizzo di posta elettronica | **Responsabile del supporto tecnico**<br/>Un rappresentante dell'organizzazione di supporto IT che può fornire pareri riguardanti l'attuabilità di questa modifica dal punto di vista del supporto tecnico. | R/I |
| Nome e indirizzo di posta elettronica per gli utenti pilota | **Utenti dotati di ruolo RBAC**<br/>Il gruppo di utenti per cui è stata implementata la gestione delle identità con privilegi. Devono sapere come attivare i propri ruoli dopo che PIM è stato implementato. | I |

### <a name="enable-pim"></a>Abilitare PIM

Come parte del processo di pianificazione, è necessario prima di tutto fornire il consenso all'uso di PIM e abilitarlo seguendo le indicazioni del [documento su come iniziare a usare PIM](pim-getting-started.md). Abilitando PIM si può accedere ad alcune funzionalità specificamente progettate per semplificare la distribuzione.

Se si intende distribuire PIM per le risorse di Azure, è consigliabile seguire le indicazioni del [documento su come individuare le risorse di Azure per la gestione in PIM](pim-resource-roles-discover-resources.md). Solo i proprietari di ogni risorsa, gruppo di risorse e sottoscrizione sono in grado di individuarle all'interno di PIM. Se sei un amministratore globale provando a distribuire PIM per risorse di Azure, è possibile [elevare l'accesso per gestire tutte le sottoscrizioni Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) per aumentare l'accesso a tutte le risorse di Azure nella directory per l'individuazione. È tuttavia consigliabile ottenere l'approvazione dai singoli proprietari delle sottoscrizioni prima di gestire le loro risorse con PIM.

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

1. Visualizzare l'elenco degli utenti che dispongono di ruoli con privilegi all'interno dell'organizzazione. Per visualizzare una pagina simile alla seguente è possibile usare la [procedura guidata di PIM](pim-security-wizard.md#run-the-wizard).

    ![Individuare i ruoli con privilegi nel riquadro ruoli è che con privilegi](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Per tutti gli amministratori globali all'interno dell'organizzazione, individuare il motivo per cui devono avere tale ruolo. In base alle indicazioni riportate nella documentazione di riferimento precedente, se il lavoro di una persona può essere eseguito da uno o più ruoli di amministratore specifici, è necessario rimuoverla dal ruolo Amministratore globale ed eseguire le opportune assegnazioni di ruolo all'interno di Azure Active Directory. A titolo informativo: Microsoft attualmente ha solo circa 10 amministratori con il ruolo Amministratore globale. Per altre informazioni, vedere [come Microsoft usa PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. Per tutti gli altri ruoli di Azure AD, esaminare l'elenco delle assegnazioni, identificare gli amministratori che non hanno più bisogno del ruolo assegnato e rimuoverli dalle rispettive assegnazioni.

Per automatizzare i passaggi 3 e 4, è possibile usare la funzione per la verifica di accesso disponibile in PIM. Seguendo la procedura descritta in [Avviare una verifica di accesso per i ruoli della directory di Azure AD in PIM](pim-how-to-start-security-review.md), è possibile configurare una verifica di accesso per ogni ruolo di Azure AD che include uno o più membri.

![Creare un riquadro di revisione di accesso per i ruoli di Azure AD](./media/pim-deployment-plan/create-access-review.png)

È necessario impostare i revisori su **Membri (autonomo)** . In questo modo verrà inviato un messaggio di posta elettronica a tutti i membri del ruolo per chiedere loro di confermare se hanno bisogno dell'accesso. È anche necessario attivare **Richiedi il motivo all'approvazione** nelle impostazioni avanzate in modo che gli utenti possano indicare il motivo per cui devono disporre del ruolo. In base alle informazioni raccolte, sarà possibile rimuovere gli utenti dai ruoli non necessari e, nel caso di amministratori globali, delegare più ruoli di amministratore specifici.

Le verifiche di accesso si basano sulla posta elettronica per comunicare agli utenti di controllare il rispettivo accesso ai ruoli. Se si hanno alcuni account con privilegi a cui non è associato un indirizzo di posta elettronica, assicurarsi di specificare l'indirizzo di posta elettronica secondario per tali account. Per altre informazioni, vedere [Attributo proxyAddresses in Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Ruoli delle risorse di Azure

Per le sottoscrizioni e le risorse di Azure, è possibile configurare un processo di verifica di accesso simile per esaminare i ruoli in ogni sottoscrizione o risorsa. L'obiettivo di questo processo è ridurre al minimo le assegnazioni di ruolo Proprietario e Amministratore Accesso utenti associate a ogni sottoscrizione o risorsa e rimuovere le assegnazioni non necessarie. Le organizzazioni, tuttavia, delegano spesso tali attività al proprietario di ogni sottoscrizione o risorsa perché ha una conoscenza più approfondita dei ruoli specifici (soprattutto per quanto riguarda i ruoli personalizzati).

Se sei un amministratore IT con il ruolo di amministratore globale provando a distribuire PIM per risorse di Azure all'interno dell'organizzazione, è possibile [elevare l'accesso per gestire tutte le sottoscrizioni Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) per ottenere l'accesso a ogni sottoscrizione. È quindi possibile contattare i singoli proprietari delle sottoscrizioni e collaborare con loro per rimuovere le assegnazioni non necessarie e ridurre al minimo le assegnazioni del ruolo Proprietario.

Gli utenti con il ruolo Proprietario per una sottoscrizione di Azure possono anche usare le [verifiche di accesso per le risorse di Azure](pim-resource-roles-start-access-review.md) per controllare e rimuovere le assegnazioni di ruolo non necessarie in modo simile a come descritto in precedenza per i ruoli di Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Determinare le assegnazioni di ruolo da proteggere con PIM

Dopo l'operazione di pulizia delle assegnazioni di ruolo con privilegi all'interno dell'organizzazione, è necessario decidere quali ruoli proteggere con PIM.

Se un ruolo è protetto da PIM, è necessario elevare i privilegi degli utenti idonei assegnati a tale ruolo in modo che possano usare i privilegi concessi dal ruolo. Per il processo di elevazione dei privilegi può anche essere necessario ottenere l'approvazione, eseguire l'autenticazione a più fattori e/o specificare un motivo per l'attivazione. PIM può inoltre tenere traccia delle elevazioni dei privilegi tramite le notifiche e i log eventi di controllo di PIM e Azure AD.

La scelta dei ruoli da proteggere con PIM può essere difficile e varia a seconda dell'organizzazione. Questa sezione presenta i consigli pratici di Microsoft per la scelta dei ruoli di Azure AD e di quelli relativi alle risorse di Azure.

#### <a name="azure-ad-roles"></a>Ruoli di Azure AD

È importante dare priorità alla protezione dei ruoli di Azure AD che hanno il maggior numero di autorizzazioni. Di seguito sono riportati i 10 ruoli principali di Azure AD gestiti da PIM, in base ai modelli di utilizzo adottati da tutti i clienti di PIM:

1. Amministratore globale
1. Amministratore della sicurezza
1. Amministratore utenti
1. Amministratore di Exchange
1. Amministratore di SharePoint
1. Amministratore di Intune
1. Ruolo con autorizzazioni di lettura per la sicurezza
1. Amministratore dei servizi
1. Amministratore fatturazione
1. Amministratore di Skype for Business

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di gestire prima di tutto i ruoli Amministratore globale e Amministratore della sicurezza usando PIM poiché sono quelli che possono provocare più danni se vengono compromessi.

È importante considerare quali dati e autorizzazioni sono più vulnerabili per l'organizzazione. Alcune organizzazioni, ad esempio, possono ritenere opportuno proteggere il ruolo Amministratore di Power BI o Amministratore Teams usando PIM perché questi ruoli hanno la possibilità di accedere ai dati e/o modificare i flussi di lavoro principali.

Se hanno utenti guest assegnati, i ruoli sono particolarmente vulnerabili agli attacchi.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di gestire tutti i ruoli con utenti guest usando PIM per ridurre il rischio associato agli account utente guest compromessi.

I ruoli di lettore, come quelli con autorizzazioni di lettura nella directory, per il Centro messaggi e per la sicurezza, sono talvolta considerati meno importanti rispetto ad altri poiché non dispongono dell'autorizzazione di scrittura. Alcuni clienti, tuttavia, proteggono anche questi ruoli perché gli utenti malintenzionati che hanno accesso a questi account possono riuscire a leggere dati sensibili, ad esempio informazioni personali. È opportuno prendere in considerazione questa possibilità quando si decide se i ruoli di lettore all'interno dell'organizzazione devono essere gestiti tramite PIM.

#### <a name="azure-resource-roles"></a>Ruoli delle risorse di Azure

Quando si decidono le assegnazioni di ruolo da gestire tramite PIM per le risorse di Azure, è necessario prima di tutto identificare le sottoscrizioni o le risorse di importanza fondamentale per l'organizzazione. Ecco alcuni esempi di sottoscrizioni o risorse di questo tipo:

- Risorse che ospitano i dati più sensibili
- Risorse da cui dipendono le applicazioni principali destinate direttamente ai clienti

Se come amministratore globale si ha difficoltà a decidere quali sottoscrizioni e risorse sono più importanti, è opportuno contattare i proprietari delle sottoscrizioni nell'organizzazione per raccogliere un elenco delle risorse gestite per ogni sottoscrizione. Collaborare quindi con i proprietari delle sottoscrizioni per raggruppare le risorse in base al livello di gravità nel caso in cui vengano compromesse (basso, medio, alto). La priorità nella gestione delle risorse con PIM deve essere basata sul livello di gravità assegnato.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di collaborare con i proprietari di sottoscrizioni o risorse di servizi critici per configurare un flusso di lavoro di PIM per tutti i ruoli correlati a sottoscrizioni o risorse sensibili.

In PIM per le risorse di Azure sono supportati account di servizio con vincoli di tempo. È consigliabile gestire questi account di servizio esattamente come un account utente normale.

Per le sottoscrizioni o le risorse che non hanno un'importanza critica, non è necessario configurare PIM per tutti i ruoli. È comunque consigliabile proteggere i ruoli Proprietario e Amministratore Accesso utenti con PIM.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di gestire i ruoli Proprietario e Amministratore Accesso utenti di tutte le sottoscrizioni o risorse usando PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidere quali assegnazioni di ruolo devono essere permanenti o idonee

Dopo aver definito l'elenco dei ruoli da gestire con PIM, è necessario stabilire quali utenti devono ottenere il ruolo idoneo rispetto al ruolo attivo in modo permanente. I ruoli attivi in modo permanente sono quelli normali assegnati tramite Azure Active Directory e le risorse di Azure, mentre i ruoli idonei possono essere assegnati solo in PIM.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di non definire alcuna assegnazione attiva in modo permanente per i ruoli di Azure AD e per quelli relativi alle risorse di Azure, fatta eccezione per i [due account di accesso di emergenza](../users-groups-roles/directory-emergency-access.md) consigliati, che devono avere il ruolo Amministratore globale permanente.

Anche se è consigliabile una configurazione senza amministratori permanenti, talvolta le organizzazioni possono avere difficoltà a implementare immediatamente una configurazione di questo tipo. Ecco alcuni aspetti da considerare nel prendere questa decisione:

- Frequenza di elevazione dei privilegi: se l'utente ha bisogno di un'assegnazione con privilegi una sola volta, non è necessario configurare l'assegnazione permanente. Se invece l'utente ha bisogno del ruolo per svolgere le attività di tutti i giorni e l'uso di PIM riduce notevolmente la sua produttività, può essere opportuno assegnargli il ruolo permanente.
- Casi specifici per un'organizzazione: se l'utente a cui viene assegnato il ruolo idoneo è membro di un team molto distante oppure è un dirigente di alto livello, a tal punto che le comunicazioni e l'applicazione del processo di elevazione dei privilegi risulterebbe difficile, è possibile prenderlo in considerazione per il ruolo permanente.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di configurare verifiche di accesso periodiche per gli utenti con assegnazioni di ruolo permanenti eventualmente presenti. Altre informazioni sulle verifiche di accesso periodiche sono riportate nella sezione finale di questo piano di distribuzione.

### <a name="draft-your-pim-settings"></a>Definire una bozza delle impostazioni di PIM

Prima di implementare la soluzione PIM, è consigliabile definire una bozza delle impostazioni di PIM per ogni ruolo con privilegi usato dall'organizzazione. Questa sezione presenta alcuni esempi di impostazioni di PIM per ruoli particolari. Gli esempi sono riportati solo a scopo di riferimento e possono variare a seconda dell'organizzazione. Ognuna di queste impostazioni è descritta in dettaglio, con consigli di Microsoft, dopo le tabelle.

#### <a name="pim-settings-for-azure-ad-roles"></a>Impostazioni di PIM per i ruoli di Azure AD

| Ruolo | Richiedere l'autenticazione MFA | Notifica | Ticket di evento imprevisto | Richiedi approvazione | Responsabile approvazione | Durata attivazione | Amministratore permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Amministratore globale | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Altri amministratori globali | 1 ora | Account di accesso di emergenza |
| Amministratore di Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | None | 2 ore | None |
| Amministratore supporto tecnico | :x: | :x: | :heavy_check_mark: | :x: | None | 8 ore | None |

#### <a name="pim-settings-for-azure-resource-roles"></a>Impostazioni di PIM per i ruoli delle risorse di Azure

| Ruolo | Richiedere l'autenticazione MFA | Notifica | Richiedi approvazione | Responsabile approvazione | Durata attivazione | Amministratore attivo | Scadenza assegnazioni attive | Scadenza assegnazioni idonee |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietario di sottoscrizioni critiche | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Altri proprietari della sottoscrizione | 1 ora | None | n/d | 3 mesi |
| Amministratore Accesso utenti di sottoscrizioni meno critiche | :heavy_check_mark: | :heavy_check_mark: | :x: | None | 1 ora | None | n/d | 3 mesi |
| Collaboratore Macchina virtuale | :x: | :heavy_check_mark: | :x: | None | 3 ore | None | n/d | 6 mesi |

La tabella seguente include le descrizioni per ciascuna delle impostazioni.

| Impostazione | DESCRIZIONE |
| --- | --- |
| Ruolo | Nome del ruolo per cui si definiscono le impostazioni. |
| Richiedere l'autenticazione MFA | Indica se l'utente idoneo deve eseguire l'autenticazione a più fattori (MFA) prima dell'attivazione del ruolo.<br/><br/> :heavy_check_mark: **Microsoft consiglia** di applicare l'autenticazione a più fattori per tutti i ruoli di amministratore, soprattutto se questi includono utenti guest. |
| Notifica | Se è impostato l'invio di notifiche, gli utenti dell'organizzazione con i ruoli Amministratore globale, Amministratore dei ruoli con privilegi e Amministratore della sicurezza ricevono una notifica di posta elettronica quando un utente idoneo attiva il ruolo.<br/><br/>**Nota:** Alcune organizzazioni non hanno un indirizzo di posta elettronica associato agli account di amministratore. Per ricevere queste notifiche, è quindi necessario impostare un indirizzo di posta elettronica alternativo per consentire agli amministratori di ricevere i messaggi. |
| Ticket di evento imprevisto | Indica se l'utente idoneo deve registrare un numero di ticket di evento imprevisto quando attiva il proprio ruolo. Questa impostazione consente a un'organizzazione di identificare ogni attivazione con un numero di evento imprevisto interno per limitare le attivazioni indesiderate.<br/><br/> :heavy_check_mark: **Microsoft consiglia** di sfruttare i numeri di ticket di evento imprevisto per associare PIM al sistema interno dell'organizzazione. Questo è particolarmente utile per i responsabili dell'approvazione che devono conoscere il contesto dell'attivazione. |
| Richiedi approvazione | Indica se l'utente idoneo deve ottenere l'approvazione per attivare il ruolo.<br/><br/> :heavy_check_mark: **Microsoft consiglia** di configurare l'approvazione per i ruoli con il livello di autorizzazione più elevato. In base ai modelli di utilizzo di tutti i clienti di PIM, i ruoli Amministratore globale, Amministratore utenti, Amministratore di Exchange, Amministratore della sicurezza e Amministratore password sono quelli per cui la richiesta di approvazione è configurata più comunemente. |
| Responsabile approvazione | Se per attivare il ruolo idoneo è richiesta l'approvazione, indicare le persone che dovrebbero approvare la richiesta. Per impostazione predefinita, PIM imposta come responsabile dell'approvazione tutti gli utenti amministratore cui è assegnato un ruolo con privilegi, sia permanenti che idonei.<br/><br/>**Nota:** Se un utente è idoneo per un ruolo di Azure AD ed è anche responsabile dell'approvazione del ruolo, non sarà in grado di approvare se stesso.<br/><br/> :heavy_check_mark: **Microsoft consiglia** di scegliere come responsabili dell'approvazione gli utenti che conoscono meglio il ruolo specifico e lo usano frequentemente piuttosto che un amministratore globale. |
| Durata attivazione | Periodo di tempo durante il quale il ruolo di un utente rimarrà attivo prima della scadenza. |
| Amministratore permanente | Elenco degli utenti che saranno amministratori permanenti per il ruolo (l'attivazione non è mai necessaria).<br/><br/> :heavy_check_mark: **Microsoft consiglia** di non configurare amministratori permanenti per nessun ruolo tranne Amministratore globale. Per altre informazioni, vedere la sezione di questo piano che contiene suggerimenti per decidere quali assegnazioni di ruolo devono essere idonee e quali devono essere attive in modo permanente. |
| Amministratore attivo | Per le risorse di Azure, un amministratore attivo è uno degli utenti che non devono eseguire l'attivazione per usare il ruolo. Per questo amministratore non viene usato l'aggettivo permanente, come nei ruoli di Azure AD, perché è possibile impostare un termine di scadenza oltre il quale l'utente perderà il ruolo. |
| Scadenza assegnazioni attive | Un'assegnazione attiva per i ruoli delle risorse di Azure scade dopo il periodo di tempo configurato. È possibile scegliere tra 15 giorni, 1 mese, 3 mesi, 6 mesi, 1 anno oppure impostare un'assegnazione attiva in modo permanente. |
| Scadenza assegnazioni idonee | Un'assegnazione idonea per i ruoli delle risorse di Azure scade dopo il periodo di tempo configurato. È possibile scegliere tra 15 giorni, 1 mese, 3 mesi, 6 mesi, 1 anno oppure impostare un'assegnazione idonea in modo permanente. |

## <a name="step-3-implement-your-solution"></a>Passaggio 3. Implementare la soluzione

Una pianificazione appropriata deve avere alla base la possibilità di distribuire correttamente un'applicazione con Azure Active Directory.  Deve inoltre offrire soluzioni intelligenti per la sicurezza e l'integrazione in modo da semplificare l'onboarding e ridurre così il tempo necessario per le distribuzioni.  In questo modo si ha la sicurezza che l'applicazione venga integrata con facilità riducendo i tempi di inattività per gli utenti finali.

### <a name="identify-test-users"></a>Identificare gli utenti di test

Usare questa sezione per identificare un set di utenti e/o gruppi di utenti per convalidare l'implementazione. In base alle impostazioni selezionate nella sezione dedicata alla pianificazione, identificare gli utenti desiderati per testare ogni ruolo.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di impostare come utenti di test i proprietari del servizio di ogni ruolo di Azure AD in modo da consentire loro di acquisire familiarità con il processo e diventare promotori interni dell'implementazione.

In questa tabella identificare gli utenti di test che verificheranno il corretto funzionamento delle impostazioni per ogni ruolo.

| Nome del ruolo | Utenti di test |
| --- | --- |
| &lt;Nome del ruolo&gt; | &lt;Utenti per testare il ruolo&gt; |
| &lt;Nome del ruolo&gt; | &lt;Utenti per testare il ruolo&gt; |

### <a name="test-implementation"></a>Testare l'implementazione

Ora che sono stati identificati gli utenti di test, seguire questo passaggio per configurare PIM per tali utenti. Se l'organizzazione vuole incorporare il flusso di lavoro di PIM all'interno di una specifica applicazione anziché tramite l'interfaccia utente di PIM nel portale di Azure, tutte le operazioni di PIM sono supportate anche tramite l'[API Graph](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Configurare i ruoli di PIM per Azure AD

1. [Configurare le impostazioni del ruolo di Azure AD](pim-how-to-change-default-settings.md) basato su ciò che è pianificato.

1. Passare a **Ruoli di Azure AD**, fare clic su **Ruoli** e quindi selezionare il ruolo appena configurato.

1. Se gli utenti del gruppo di test sono già configurati come amministratori permanenti, per renderli idonei è possibile cercarli e convertirli da permanenti a idonei facendo clic sui tre puntini sulla riga corrispondente. Se invece agli utenti non sono stati ancora assegnati ruoli, è possibile [definire una nuova assegnazione idonea](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Ripetere i passaggi da 1 a 3 per tutti i ruoli da testare.

1. Dopo aver configurato gli utenti di test, è necessario inviare loro il collegamento per [attivare il proprio ruolo di Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Configurare i ruoli di PIM per le risorse di Azure

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

### <a name="communicate-pim-to-affected-stakeholders"></a>Comunicare le informazioni su PIM agli stakeholder interessati

Con la distribuzione di PIM, gli utenti dei ruoli con privilegi dovranno eseguire alcuni passaggi in più. Anche se PIM consente di ridurre notevolmente i problemi di sicurezza associati alle identità con privilegi, l'introduzione di questa soluzione deve essere comunicata in modo efficace prima della distribuzione a livello di tenant. A seconda del numero di amministratori interessati, le organizzazioni scelgono spesso di creare un documento interno, un video o un messaggio di posta elettronica per comunicare la novità. In queste comunicazioni sono spesso incluse le informazioni seguenti:

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
> :heavy_check_mark: **Microsoft consiglia** di organizzare un incontro con il proprio team di supporto tecnico in cui illustrare il flusso di lavoro di PIM (se all'interno dell'organizzazione è presente un team di supporto IT). Fornire loro la documentazione appropriata e le informazioni di contatto.

### <a name="move-to-production"></a>Passare in produzione

Al termine dell'intera procedura di test, distribuire PIM nell'ambiente di produzione ripetendo tutti i passaggi delle fasi di test per tutti gli utenti di ogni ruolo definito nella configurazione di PIM. Per i ruoli di PIM per Azure AD, le organizzazioni spesso testano e implementano PIM prima per gli amministratori globali e dopo per gli altri ruoli. Per le risorse di Azure, le organizzazioni in genere testano e implementano PIM per una sottoscrizione di Azure alla volta.

### <a name="in-the-case-a-rollback-is-needed"></a>In caso di ripristino dello stato precedente

Se PIM non ha funzionato come desiderato nell'ambiente di produzione, i passaggi seguenti possono essere utili per ripristinare uno stato valido noto prima della configurazione di PIM:

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

## <a name="step-4-next-steps-after-deploying-pim"></a>Passaggio 4. Operazioni successive alla distribuzione di PIM

La distribuzione di PIM nell'ambiente di produzione rappresenta un importante passo in avanti per quanto riguarda la protezione delle identità con privilegi dell'organizzazione. Con la distribuzione di PIM sono disponibili altre funzionalità da usare per garantire la sicurezza e la conformità.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Usare gli avvisi di PIM per proteggere l'accesso con privilegi

Per una migliore protezione del tenant è consigliabile usare la funzionalità predefinita di PIM per la generazione degli avvisi. Per altre informazioni, vedere [Avvisi di sicurezza](pim-how-to-configure-security-alerts.md#security-alerts). Questi avvisi segnalano ad esempio che gli amministratori non usano i ruoli con privilegi, che sono state eseguite assegnazioni di ruolo all'esterno di PIM, che i ruoli vengono attivati con una frequenza eccessiva e altro ancora. Per proteggere completamente la propria organizzazione, è necessario scorrere regolarmente l'elenco degli avvisi e risolvere i problemi. Per visualizzare e risolvere gli avvisi, procedere nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Aprire **Azure AD Privileged Identity Management**.
1. Fare clic su **Ruoli di Azure AD** e quindi su **Avvisi**.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di gestire immediatamente tutti gli avvisi contrassegnati con livello di gravità alto. Per quelli con livello di gravità medio e basso, è necessario tenersi informati e intervenire se si ritiene che sia presente una minaccia per la sicurezza.

Se uno degli avvisi non è utile o non è applicabile alla propria organizzazione, è sempre possibile ignorarlo nella pagina degli avvisi ed eventualmente ripristinarlo in un secondo momento nella pagina delle impostazioni di Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurare verifiche di accesso periodiche per controllare regolarmente le identità con privilegi dell'organizzazione

Le verifiche di accesso sono il modo migliore per chiedere agli utenti cui sono assegnati ruoli con privilegi o a revisori specifici se hanno effettivamente bisogno dell'identità con privilegi. Le verifiche di accesso sono molto utili se si vuole ridurre la superficie di attacco assicurando al tempo stesso la conformità. Per altre informazioni sull'avvio di una verifica di accesso, vedere [Verifiche di accesso per i ruoli di Azure AD](pim-how-to-start-security-review.md) e [Verifiche di accesso per i ruoli delle risorse di Azure](pim-resource-roles-start-access-review.md). Per alcune organizzazioni, l'esecuzione di una verifica di accesso periodica è necessaria per garantire la conformità alle leggi e alle normative, mentre per altre una verifica di accesso è il modo migliore per applicare il principio di accesso con privilegi minimi in tutta l'organizzazione.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di configurare l'esecuzione di verifiche di accesso trimestrali per tutti i ruoli di Azure AD e per quelli relativi alle risorse di Azure.

Nella maggior parte dei casi, il revisore per i ruoli di Azure AD è l'utente stesso, mentre il revisore per i ruoli delle risorse di Azure è il proprietario della sottoscrizione in cui si trova il ruolo. Tuttavia, capita spesso che le aziende abbiano account con privilegi che non sono collegati all'indirizzo di posta elettronica di una persona particolare. In questi casi, nessuno legge i messaggi e verifica i privilegi di accesso.

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di aggiungere un indirizzo di posta elettronica secondario per tutti gli account cui sono assegnati ruoli con privilegi che non sono associati a un indirizzo di posta elettronica controllato regolarmente.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Sfruttare al meglio il log di controllo per migliorare la sicurezza e la conformità

Il log di controllo è lo strumento che consente di rimanere sempre aggiornati e assicurare la conformità alle normative. PIM attualmente archivia nel log di controllo l'intera cronologia dell'organizzazione per un periodo di 30 giorni, inclusi i dati seguenti:

- Attivazione o disattivazione dei ruoli idonei
- Attività di assegnazione di ruoli all'interno e all'esterno di PIM
- Modifiche nelle impostazioni dei ruoli
- Attività di richiesta, approvazione o rifiuto per l'attivazione dei ruoli con configurazione dell'approvazione
- Aggiornamenti degli avvisi

I log di controllo sono accessibili agli amministratori globali o agli amministratori cui sono assegnati ruoli con privilegi. Per altre informazioni, vedere gli articoli sulla [cronologia dei controlli per i ruoli di Azure AD](pim-how-to-use-audit-log.md) e sulla [cronologia dei controlli per i ruoli delle risorse di Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di assegnare ad almeno un amministratore il compito di leggere tutti gli eventi di controllo una volta alla settimana ed esportare tali eventi ogni mese.

Se si vogliono archiviare automaticamente gli eventi di controllo per un periodo di tempo più lungo, il log di controllo di PIM viene sincronizzato automaticamente nei [log di controllo di Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Microsoft consiglia** di configurare il [monitoraggio dei log di Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) per archiviare gli eventi di controllo in un account di archiviazione di Azure in modo da soddisfare i requisiti di sicurezza e conformità.
