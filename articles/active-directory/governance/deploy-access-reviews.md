---
title: Pianificare una distribuzione di verifiche di accesso Azure Active Directory
description: Guida alla pianificazione per una distribuzione corretta delle verifiche di accesso
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/14/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51983e52643d587fc497a1a67fcb0cd29dbb7f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306566"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Pianificazione della distribuzione delle verifiche di accesso Azure Active Directory

Le verifiche di [accesso Azure Active Directory (Azure ad)](access-reviews-overview.md) consentono all'organizzazione di proteggere la rete con maggiore sicurezza gestendo il ciclo di vita dell' [accesso alle risorse](identity-governance-overview.md). Con le verifiche di accesso è possibile:

* Pianificare verifiche regolari o eseguire revisioni ad hoc per vedere chi ha accesso a risorse specifiche, ad esempio applicazioni e gruppi

* Rilevare le verifiche per informazioni dettagliate, conformità o motivi dei criteri

* Delegare le revisioni a amministratori specifici, proprietari aziendali o utenti finali che possono autocertificare la necessità di accesso continuo

* Usare le informazioni dettagliate per determinare in modo efficiente se gli utenti devono continuare ad accedere

* Automatizzare i risultati della revisione, ad esempio la rimozione dell'accesso degli utenti alle risorse

  ![Diagramma che mostra il flusso delle verifiche di accesso.](./media/deploy-access-review/1-planning-review.png)

Le verifiche di accesso sono una funzionalità [Azure ad Identity governance](identity-governance-overview.md) . Le altre funzionalità sono [gestione dei diritti](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) e condizioni per l' [utilizzo](../conditional-access/terms-of-use.md). Insieme, consentono alle organizzazioni di risolvere queste quattro domande:

* Quali utenti hanno accesso a quali risorse?

* In che modo questi utenti usano l'accesso?

* È disponibile un controllo aziendale efficace per la gestione dell'accesso?

* I revisori possono verificare l'operatività dei controlli?

La pianificazione della distribuzione delle verifiche di accesso è essenziale per assicurarsi di ottenere la strategia di governance desiderata per gli utenti dell'organizzazione.

### <a name="key-benefits"></a>Vantaggi principali

I vantaggi principali dell'abilitazione delle verifiche di accesso sono:

* **Controllare la collaborazione**. Le verifiche di accesso consentono alle organizzazioni di gestire l'accesso a tutte le risorse necessarie agli utenti. Quando gli utenti condividono e collaborano, le organizzazioni possono avere la certezza che le informazioni siano solo tra gli utenti autorizzati.

* **Gestire i rischi**. Le verifiche di accesso consentono alle organizzazioni di esaminare l'accesso ai dati e alle applicazioni, abbassando il rischio di perdita di dati e fuoriuscita di dati. Sono incluse le funzionalità per esaminare periodicamente l'accesso del partner esterno alle risorse aziendali. 

* **Conformità e governance degli indirizzi**. Con le verifiche di accesso, è possibile gestire e ricertificare il ciclo di vita dell'accesso a gruppi, app e siti. È possibile controllare le revisioni per la conformità o le applicazioni sensibili al rischio specifiche per la propria organizzazione. 

* **Ridurre i costi**. Le verifiche di accesso sono compilate nel cloud e funzionano in modo nativo con risorse cloud come gruppi, applicazioni e pacchetti di accesso. L'uso delle verifiche di accesso è meno costoso della creazione di strumenti personalizzati o dell'aggiornamento del set di strumenti locale.

### <a name="training-resources"></a>Risorse di formazione

I video seguenti possono essere utili quando si apprenderanno le verifiche di accesso:

* [Informazioni sulle verifiche di accesso in Azure AD](https://youtu.be/kDRjQQ22Wkk)

* [Come creare verifiche di accesso in Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Come abilitare le verifiche di accesso in Azure AD](https://youtu.be/X1SL2uubx9M)

* [Come verificare l'accesso tramite My Access](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenses

È necessaria una licenza di Azure AD Premium (P2) valida per ogni persona, ad eccezione degli amministratori globali o degli amministratori utenti, che creeranno o eseguiranno verifiche di accesso. Per altre informazioni, vedere [Access reviews License requirements](access-reviews-overview.md).

Potrebbero essere necessarie anche altre funzionalità di governance delle identità, ad esempio la gestione del ciclo di vita [dei diritti](entitlement-management-overview.md) o la gestione delle identità con privilegi. In tal caso, potrebbero essere necessarie anche le licenze correlate. Per altre informazioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Pianificare il progetto di distribuzione delle verifiche di accesso

Prendere in considerazione le esigenze dell'organizzazione per determinare la strategia di distribuzione delle verifiche di accesso nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere la causa sono le diverse aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi inconvenienti, [assicurarsi di coinvolgere gli stakeholder appropriati](https://aka.ms/deploymentplans) e che i ruoli del progetto siano chiari.

Per le verifiche di accesso, è probabile che si includano i rappresentanti dei seguenti team all'interno dell'organizzazione:

* L' **amministrazione it** gestisce l'infrastruttura IT e amministra gli investimenti per il cloud e le app SaaS (software as a Service). Questo team:

   * Esaminare l'accesso con privilegi all'infrastruttura e alle app, inclusi Microsoft 365 e Azure AD.

   * Pianificare ed eseguire verifiche di accesso sui gruppi usati per gestire elenchi di eccezioni o progetti pilota IT, per mantenere aggiornati gli elenchi di accesso.

   * Assicurarsi che l'accesso a livello di codice (con script) alle risorse tramite entità servizio sia regolato e controllato.

* I **team di sviluppo** creano e gestiscono le applicazioni per l'organizzazione. Questo team:

   * Controllare chi può accedere e gestire i componenti nelle risorse SaaS, PaaS e IaaS che comprendono le soluzioni sviluppate.

   * Gestire i gruppi che possono accedere alle applicazioni e agli strumenti per lo sviluppo di applicazioni interne.

   * Richiedi identità con privilegi che possono accedere al software di produzione o alle soluzioni ospitate per i clienti

* Le **Business Unit** gestiscono progetti e applicazioni personali. Questo team: 

   * Esaminare e approvare o negare l'accesso a gruppi e applicazioni per utenti interni ed esterni.

   * Pianificare ed eseguire revisioni per attestare l'accesso continuo per i dipendenti e le identità esterne, ad esempio i partner commerciali.

* La **governance aziendale** garantisce che l'organizzazione stia seguendo i criteri interni e conforme alle normative. Questo team:

   * Richiedere o pianificare nuove verifiche di accesso.

   * Valutare i processi e le procedure per la verifica dell'accesso, inclusa la documentazione e la conservazione dei record per la conformità.

   * Esaminare i risultati di precedenti revisioni per le risorse più importanti.

> [!NOTE]
> Per le revisioni che richiedono valutazioni manuali, assicurarsi di pianificare i revisori adeguati e i cicli di revisione che soddisfano i criteri e le esigenze di conformità. Se i cicli di revisione sono troppo frequenti o se sono presenti troppi revisori, la qualità potrebbe andare persa e un numero eccessivo o troppo basso di persone potrebbe avere accesso. 

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo processo di business. Comunica in modo proattivo agli utenti come e quando la loro esperienza cambierà e come ottenere supporto se si verificano problemi. 

#### <a name="communicate-changes-in-accountability"></a>Comunicare le modifiche nella responsabilità

Le verifiche di accesso supportano lo spostamento della responsabilità di rivedere e agire sull'accesso continuo ai proprietari aziendali. La separazione delle decisioni di accesso da esso determina un numero maggiore di decisioni di accesso più accurate. Si tratta di una modifica culturale alla responsabilità e alla responsabilità del proprietario delle risorse. Comunica in modo proattivo questa modifica e assicurati che i proprietari delle risorse vengano sottoposti a training e possano usare le informazioni dettagliate per prendere decisioni ottimali.

Ovviamente, sarà necessario mantenere il controllo per tutte le decisioni di accesso relative all'infrastruttura e le assegnazioni di ruolo con privilegi. 

#### <a name="customize-email-communication"></a>Personalizzare la comunicazione tramite posta elettronica

Quando si pianifica una revisione, si nominano gli utenti che eseguiranno la revisione. Questi revisori ricevono quindi una notifica tramite posta elettronica delle nuove verifiche assegnate, oltre ai promemoria prima della scadenza di una verifica assegnata.

Gli amministratori possono scegliere di inviare questa notifica a metà strada prima della scadenza della verifica o di un giorno prima della scadenza. 

Il messaggio di posta elettronica inviato ai revisori può essere personalizzato in modo da includere un breve messaggio personalizzato che incoraggi ad agire sulla revisione. È consigliabile usare il testo aggiuntivo per:

* Includere un messaggio personale ai revisori, in modo da comprendere che viene inviato dalla conformità o dal reparto IT.

* Includere un collegamento ipertestuale o un riferimento alle informazioni interne su quali sono le aspettative della revisione e materiale di riferimento o di formazione aggiuntivo.

* Includere un collegamento alle istruzioni su [come eseguire una verifica automatica dell'accesso.](review-your-access.md) 

  ![Revisore-posta elettronica](./media/deploy-access-review/2-plan-reviewer-email.png)

Quando si seleziona Avvia revisione, i revisori vengono indirizzati al [portale di accesso](https://myapplications.microsoft.com/) per le verifiche di accesso alle applicazioni e ai gruppi. Il portale offre una panoramica di tutti gli utenti che hanno accesso alla risorsa di cui si sta eseguendo la revisione e consigli di sistema in base all'ultimo accesso e alle informazioni di accesso.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

Invitiamo i clienti a pilotare inizialmente le verifiche di accesso con un piccolo gruppo e come destinazione risorse non critiche. Il progetto pilota può aiutarti a modificare i processi e le comunicazioni in base alle esigenze e a migliorare la capacità degli utenti e dei revisori di soddisfare i requisiti di sicurezza e conformità.

Nel progetto pilota è consigliabile:

* Iniziare con le revisioni in cui i risultati non vengono applicati automaticamente ed è possibile controllare le implicazioni.

* Assicurarsi che tutti gli utenti dispongano di indirizzi di posta elettronica validi elencati in Azure AD e ricevano la comunicazione tramite posta elettronica per intraprendere l'azione appropriata. 

* Documentare tutti gli accessi rimossi come parte del progetto pilota nel caso in cui sia necessario ripristinarli rapidamente.

* Monitorare i log di controllo per assicurarsi che tutti gli eventi siano controllati correttamente.

Per ulteriori informazioni, vedere [procedure consigliate per un progetto pilota](../fundamentals/active-directory-deployment-plans.md).

## <a name="introduction-to-access-reviews"></a>Introduzione alle verifiche di accesso

In questa sezione vengono presentati i concetti di verifica dell'accesso da tenere presente prima di pianificare le revisioni.

### <a name="what-resource-types-can-be-reviewed"></a>Quali tipi di risorse è possibile esaminare?

Una volta integrate le risorse dell'organizzazione con Azure AD, ad esempio utenti, applicazioni e gruppi, è possibile gestirle e esaminarle. 

Le destinazioni tipiche per la revisione includono:

* [Applicazioni integrate con Azure ad per l'accesso Single Sign-on](../manage-apps/what-is-application-management.md) (ad esempio Saas, line-of-business).

* [Appartenenza](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) a un gruppo (sincronizzato con Azure ad o creato in Azure AD o Microsoft 365, incluso Microsoft Teams).

* Consente di [accedere al pacchetto](/azure/active-directory/governance/entitlement-management-overview) che raggruppa le risorse (gruppi, app e siti) in un unico pacchetto per gestire l'accesso.

* [Azure ad ruoli e i ruoli delle risorse di Azure](../privileged-identity-management/pim-resource-roles-assign-roles.md) come definito in Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Quali utenti creeranno e gestiranno le verifiche di accesso?

Il ruolo amministrativo necessario per creare, gestire o leggere una verifica di accesso dipende dal tipo di risorsa da rivedere. La tabella seguente indica i ruoli necessari per ogni tipo di risorsa:

| Tipo di risorsa| Creare e gestire le verifiche di accesso (autori)| Leggere i risultati della verifica di accesso |
| - | - | -|
| Gruppo o applicazione| Amministratore globale <p>Amministratore utenti| Autori e amministratori della sicurezza |
| Ruoli con privilegi in Azure AD| Amministratore globale <p>Amministratore dei ruoli con privilegi| Autori <p>Ruolo con autorizzazioni di lettura per la sicurezza<p>Amministratore della protezione |
| Ruoli con privilegi in Azure (risorse)| Amministratore globale<p>Amministratore utenti<p>Proprietario risorsa| Autori |
| Pacchetto di accesso| Amministratore globale<p>Creatore del pacchetto di accesso| Solo amministratore globale |


Per altre informazioni, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Chi rivedrà l'accesso alla risorsa?

Il creatore della verifica di accesso decide al momento della creazione che eseguirà la revisione. Questa impostazione non può essere modificata dopo l'avvio della verifica. I revisori sono rappresentati da tre persone:

* Proprietari delle risorse, ovvero i proprietari aziendali della risorsa.

* Set di delegati selezionati singolarmente, selezionati dall'amministratore delle verifiche di accesso.

* Utenti finali che garantiranno l'accesso continuo a ogni autocertificazione.

Quando si crea una verifica di accesso, gli amministratori possono scegliere uno o più revisori. Tutti i revisori possono avviare ed eseguire una revisione, scegliendo gli utenti per l'accesso continuo a una risorsa o la loro rimozione. 

### <a name="components-of-an-access-review"></a>Componenti di una verifica di accesso

Prima di implementare le verifiche di accesso, è necessario pianificare i tipi di revisione rilevanti per l'organizzazione. A tale scopo, è necessario prendere decisioni aziendali su ciò che si vuole rivedere e sulle azioni da intraprendere in base a tali verifiche.

Per creare un criterio di verifica di accesso, è necessario disporre delle informazioni seguenti.

* Quali sono le risorse da esaminare?

* Di cui è in corso la revisione dell'accesso.

* Con quale frequenza si verifica la revisione?

* Chi eseguirà la revisione?

   * In che modo verranno inviate notifiche per la revisione?

   * Quali sono le sequenze temporali da applicare per la revisione?

* Quali azioni automatiche applicare in base alla revisione?

   * Cosa accade se il revisore non risponde nel tempo?

* Quali azioni manuali verranno eseguite come risultato in base alla revisione?

* Quali comunicazioni devono essere inviate in base alle azioni eseguite?


**Piano di verifica di accesso di esempio**

| Componente| Valore |
| - | - |
| **Risorse da rivedere**| Accesso a Microsoft Dynamics |
| **Frequenza di Revisione**| Mensilmente |
| **Chi esegue la revisione**| Responsabili del programma di Dynamics Business Group |
| **Notifica**| Invia un messaggio di posta elettronica 24 ore prima della revisione all'alias Dynamics-Pms<p>Includere un messaggio personalizzato incoraggiante ai revisori per proteggere l'acquisto |
| **Sequenza temporale**| 48 ore dalla notifica |
|**Azioni automatiche**| Rimuovere l'accesso da qualsiasi account senza accesso interattivo entro 90 giorni, rimuovendo l'utente dal gruppo di sicurezza Dynamics-Access. <p>*Eseguire le azioni se non vengono esaminate nella sequenza temporale.* |
| **Azioni manuali**| Se lo si desidera, i revisori possono eseguire l'approvazione delle rimozioni prima dell'azione automatica. |
| **Comunicazioni**| Inviare utenti interni (membri) che sono stati rimossi da un messaggio di posta elettronica che spiegano che vengono rimossi e come riottenere l'accesso. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatizzare le azioni in base alle verifiche di accesso

È possibile scegliere di automatizzare la rimozione dell'accesso impostando l'opzione applica automaticamente i risultati alla risorsa per abilitare.

  ![Pianificazione delle verifiche di accesso](./media/deploy-access-review/3-automate-actions-settings.png)

Al termine della verifica, gli utenti che non sono stati approvati dal revisore verranno rimossi automaticamente dalla risorsa, o mantenuti con accesso continuo. Questo potrebbe significare rimuovere l'appartenenza al gruppo, la relativa assegnazione dell'applicazione o revocare il diritto di elevare a un ruolo con privilegi.

Accetta consigli

Le raccomandazioni vengono visualizzate ai revisori come parte dell'esperienza del revisore e indicano l'ultimo accesso di un utente al tenant o l'ultimo accesso a un'applicazione. Queste informazioni consentono ai revisori di prendere la decisione di accesso corretta. Se si seleziona accettano raccomandazioni, verranno seguite le raccomandazioni della verifica di accesso. Al termine di una verifica di accesso, il sistema applicherà automaticamente queste raccomandazioni per gli utenti che i revisori non hanno risposto.

Le raccomandazioni si basano sui criteri della verifica di accesso. Se ad esempio si configura la revisione per rimuovere l'accesso senza accesso interattivo per 90 giorni, si consiglia di rimuovere tutti gli utenti che soddisfano tali criteri. Microsoft sta lavorando continuamente per migliorare le raccomandazioni. 

### <a name="review-guest-user-access"></a>Verificare l'accesso utente Guest

Usare le verifiche di accesso per rivedere e pulire le identità dei partner di collaborazione da organizzazioni esterne. La configurazione di una revisione per partner può soddisfare i requisiti di conformità.

Alle identità esterne è possibile concedere l'accesso alle risorse aziendali tramite una delle azioni seguenti:

* Aggiunta a un gruppo 

* Invitato ai team 

* Assegnato a un'applicazione aziendale o a un pacchetto di accesso

* Assegnazione di un ruolo con privilegi in Azure AD o in una sottoscrizione di Azure

Vedere [script di esempio](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Lo script indicherà dove vengono usate le identità esterne invitate nel tenant. È possibile visualizzare l'appartenenza a gruppi, le assegnazioni di ruolo e le assegnazioni di applicazioni dell'utente esterno in Azure AD. Nello script non verranno visualizzate le assegnazioni al di fuori di Azure AD, ad esempio l'assegnazione diretta dei diritti alle risorse di SharePoint, senza l'utilizzo di gruppi.

Quando si crea una verifica di accesso per i gruppi o le applicazioni, è possibile scegliere di consentire al revisore di concentrarsi su tutti gli utenti con accesso o solo su utenti guest. Selezionando solo utenti guest, ai revisori viene assegnato un elenco mirato di identità esterne da Azure AD B2B che hanno accesso alla risorsa.

 ![Esaminare gli utenti Guest](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> NON sono inclusi i membri esterni che hanno un userType del membro. Non verranno inoltre inclusi gli utenti invitati al di fuori della collaborazione B2B di Azure AD, ad esempio quelli che hanno accesso al contenuto condiviso direttamente tramite SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Pianificare le verifiche di accesso per i pacchetti Access

I [pacchetti di Access](entitlement-management-overview.md) possono semplificare notevolmente la strategia di controllo della governance e della verifica di accesso. Un pacchetto di accesso è costituito da un bundle di tutte le risorse con l'accesso che un utente deve usare per un progetto o per eseguire le attività. È ad esempio possibile creare un pacchetto di accesso che includa tutte le applicazioni necessarie per gli sviluppatori dell'organizzazione o per tutte le applicazioni a cui gli utenti esterni devono disporre di accesso. Un amministratore o un gestore di pacchetti di accesso delegato raggruppa quindi le risorse (gruppi o app) e i ruoli necessari agli utenti per tali risorse.

Quando si [Crea un pacchetto di accesso](entitlement-management-access-package-create.md), è possibile creare uno o più criteri di accesso che impostano le condizioni per cui gli utenti possono richiedere un pacchetto di accesso, l'aspetto del processo di approvazione e la frequenza con cui una persona deve richiedere nuovamente l'accesso. Le verifiche di accesso vengono configurate durante la creazione o la modifica di un criterio di accesso ai pacchetti

Aprire la scheda ciclo di vita per scorrere verso il basso fino a verifiche di accesso.

 ![Screenshot che mostra il "modifica criterio" nella scheda "ciclo di vita".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Pianificare le verifiche di accesso per i gruppi

Oltre ad accedere ai pacchetti, la revisione dell'appartenenza al gruppo è il modo più efficace per gestire l'accesso. È consigliabile che l'accesso alle risorse venga assegnato tramite [gruppi di sicurezza o gruppi di Microsoft 365](../fundamentals/active-directory-manage-groups.md)e che gli utenti vengano aggiunti a tali gruppi per ottenere l'accesso.

A un singolo gruppo è possibile concedere l'accesso a tutte le risorse appropriate. È possibile assegnare al gruppo l'accesso a singole risorse o a un pacchetto di accesso che raggruppa le applicazioni e altre risorse. Con questo metodo è possibile esaminare l'accesso al gruppo anziché l'accesso di un utente a ogni applicazione. 

L'appartenenza al gruppo può essere esaminata da: 

* Administrators

* Proprietari del gruppo

* Utenti selezionati, funzionalità di revisione delegata al momento della creazione della verifica

* Membri del gruppo, attestazione per se stessi

### <a name="group-ownership"></a>Proprietà di gruppo

È consigliabile che i proprietari del gruppo rivedano l'appartenenza, perché sono più adatti per conoscere chi necessita di accesso. La proprietà dei gruppi è diversa dal tipo di gruppo:

I gruppi creati in Microsoft 365 e Azure AD hanno uno o più proprietari ben definiti. Nella maggior parte dei casi, questi proprietari eseguono revisori perfetti per i propri gruppi, in quanto sanno chi deve avere accesso. 

Microsoft teams, ad esempio, USA i gruppi di Microsoft 365 come modello di autorizzazione sottostante per concedere agli utenti l'accesso alle risorse che si trovano in SharePoint, Exchange, OneNote o altri servizi di Microsoft 365. L'autore del team diventa automaticamente un proprietario e deve essere responsabile dell'attestazione dell'appartenenza a tale gruppo. 

I gruppi creati manualmente nel portale di Azure AD o tramite scripting tramite Microsoft Graph potrebbero non avere necessariamente i proprietari definiti. È consigliabile definirli tramite il portale di Azure AD nella sezione "owners" del gruppo o tramite Graph.

I gruppi sincronizzati da Active Directory locali non possono avere un proprietario nel Azure AD. Quando si crea una verifica di accesso per tali utenti, è necessario selezionare i singoli utenti che sono più adatti per stabilire l'appartenenza al gruppo.

> [!NOTE]
> Si consiglia di definire i criteri di business che definiscono il modo in cui vengono creati i gruppi per garantire una chiara proprietà del gruppo e la responsabilità per una verifica regolare dell'appartenenza. 

### <a name="review-membership-of-exclusion-groups-in-ca-policies"></a>Verificare l'appartenenza dei gruppi di esclusioni nei criteri della CA 

In alcuni casi, i criteri di accesso condizionale progettati per proteggere la rete non devono essere applicati a tutti gli utenti. Ad esempio, un criterio dell'autorità di certificazione che consente agli utenti di accedere solo mentre nella rete aziendale potrebbe non essere applicabile al team di vendita, che viaggia in modo esteso. In tal caso, i membri del team di vendita vengono inseriti in un gruppo e tale gruppo verrebbe escluso dai criteri di autorità di certificazione. 

Verificare regolarmente l'appartenenza a un gruppo, in quanto l'esclusione rappresenta un potenziale rischio se i membri non corretti sono esclusi dal requisito.

È possibile [usare le verifiche di accesso di Azure ad per gestire gli utenti esclusi dai criteri di accesso condizionale](conditional-access-exclusion.md).

### <a name="review-external-users-group-memberships"></a>Verificare l'appartenenza a gruppi dell'utente esterno

Per ridurre al minimo il lavoro manuale e gli eventuali errori associati, provare a usare i [gruppi dinamici](../users-groups-roles/groups-create-rule.md) per assegnare l'appartenenza ai gruppi in base agli attributi di un utente. Potrebbe essere necessario creare uno o più gruppi dinamici per utenti esterni. Lo sponsor interno può fungere da revisore per l'appartenenza al gruppo. 

Nota: gli utenti esterni rimossi da un gruppo in seguito a una verifica di accesso non vengono eliminati dal tenant. 

Possono essere eliminati da un tenant o rimossi manualmente o tramite uno script.

### <a name="review-access-to-on-premises-groups"></a>Verificare l'accesso ai gruppi locali

Le verifiche di accesso non possono modificare l'appartenenza al gruppo dei gruppi sincronizzati da locale con [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md). Questo perché l'origine dell'autorità è locale.

È comunque possibile usare le verifiche di accesso per pianificare e gestire verifiche regolari dei gruppi locali. I revisori eseguiranno quindi un'azione nel gruppo locale. Questa strategia mantiene le verifiche di accesso come strumento per tutte le revisioni.

È possibile usare i risultati di una verifica di accesso nei gruppi locali ed elaborarli ulteriormente, in base a quanto segue:

* Scaricare il report CSV dalla verifica di accesso e intraprendere manualmente l'azione.

* Utilizzo di Microsoft Graph per accedere a livello di codice ai risultati e alle decisioni nelle verifiche di accesso completate.

Per accedere ai risultati per un gruppo gestito da Windows AD, ad esempio, usare questo [script di esempio di PowerShell](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Lo script illustra le chiamate al grafo richieste ed Esporta i comandi di Windows AD-PowerShell per eseguire le modifiche.

## <a name="plan-access-reviews-for-applications"></a>Pianificare le verifiche di accesso per le applicazioni 

Quando si verifica l'accesso a un'applicazione, si sta esaminando l'accesso per i dipendenti e le identità esterne alle informazioni e ai dati all'interno dell'applicazione. Scegliere di esaminare un'applicazione quando è necessario conoscere chi ha accesso a un'applicazione specifica, anziché un pacchetto di accesso o un gruppo. 

Si consiglia di pianificare le revisioni per le applicazioni negli scenari seguenti:

* Agli utenti viene concesso l'accesso diretto all'applicazione (all'esterno di un gruppo o di un pacchetto di accesso).

* L'applicazione espone informazioni critiche o riservate.

* L'applicazione dispone di requisiti di conformità specifici per i quali è necessario attestare.

* Si sospetta l'accesso non appropriato.

Per creare verifiche di accesso per un'applicazione, impostare assegnazione utente obbligatoria? Proprietà su Sì. Se è impostato su No, tutti gli utenti della directory, incluse le identità esterne, possono accedere all'applicazione e non è possibile verificare l'accesso all'applicazione. 

 ![pianificare le assegnazioni delle app](./media/deploy-access-review/6-plan-applications-assignment-required.png)

È quindi necessario [assegnare gli utenti e i gruppi](../manage-apps/assign-user-or-group-access-portal.md) a cui si vuole accedere. 

### <a name="reviewers-for-an-application"></a>Revisori per un'applicazione

Le verifiche di accesso possono essere relative ai membri di un gruppo o agli utenti assegnati a un'applicazione. Le applicazioni in Azure AD non hanno necessariamente un proprietario, motivo per cui l'opzione per la selezione del proprietario dell'applicazione come revisore non è possibile. È possibile definire ulteriormente l'ambito di una revisione per esaminare solo gli utenti guest assegnati all'applicazione, anziché rivedere tutti gli accessi.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Pianificare la revisione dei ruoli di Azure AD e delle risorse di Azure

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) semplifica il modo in cui le aziende gestiscono l'accesso con privilegi alle risorse Azure ad. In questo modo, l'elenco dei ruoli con privilegi, sia in [Azure ad](../users-groups-roles/directory-assign-admin-roles.md) che nelle [risorse di Azure](../../role-based-access-control/built-in-roles.md) è molto più piccolo e aumenta la sicurezza complessiva della directory.

Le verifiche di accesso consentono ai revisori di attestare se gli utenti devono ancora avere un ruolo. Proprio come le verifiche di accesso per i pacchetti Access, le recensioni per i ruoli di Azure AD e le risorse di Azure sono integrate nell'esperienza utente di amministrazione di PIM. Si consiglia di esaminare regolarmente le seguenti assegnazioni di ruolo:

* Amministratore globale

* Amministratore utenti

* Amministratore autenticazione con privilegi

* Amministratore di accesso condizionale

* Amministratore della protezione

* Tutti i ruoli di amministrazione del servizio Microsoft 365 e Dynamics

I ruoli selezionati includono ruolo permanente e idoneo. 

Nella sezione Revisori selezionare una o più persone per la verifica di tutti gli utenti. In alternativa è possibile fare in modo che i membri verifichino il proprio accesso.

 ![Modificare i criteri](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Distribuire le verifiche di accesso

Dopo aver preparato una strategia e un piano per esaminare l'accesso alle risorse integrate con Azure AD, distribuire e gestire le revisioni usando le risorse seguenti.

### <a name="review-access-packages"></a>Verifica i pacchetti di accesso

Per ridurre il rischio di accesso non aggiornato, gli amministratori possono abilitare le verifiche periodiche degli utenti che hanno assegnazioni attive a un pacchetto di accesso. Seguire le istruzioni riportate nel collegamento seguente:

| Procedure| Description |
| - | - |
| [Creazione di verifiche di accesso](entitlement-management-access-reviews-create.md)| Abilita le verifiche del pacchetto di accesso. |
| [Eseguire verifiche di accesso](entitlement-management-access-reviews-review-access.md)| Eseguire verifiche di accesso per altri utenti assegnati a un pacchetto di accesso. |
| [Self-Review assegnato/i pacchetti di accesso](entitlement-management-access-reviews-self-review.md)| Revisione automatica dei pacchetti di accesso assegnati |


> [!NOTE]
> Gli utenti finali che riesaminano e dicono che non necessitano più dell'accesso non vengono rimossi immediatamente dal pacchetto di accesso. Vengono rimossi dal pacchetto di accesso al termine della verifica o se un amministratore interrompe la revisione.

### <a name="review-groups-and-apps"></a>Esaminare gruppi e app

Le esigenze di accesso ai gruppi e alle applicazioni per i dipendenti e ai Guest cambiano probabilmente nel tempo. Per ridurre i rischi associati alle assegnazioni di accesso obsolete, gli amministratori possono creare verifiche di accesso per i membri del gruppo o l'accesso alle applicazioni. Seguire le istruzioni riportate nel collegamento seguente:

| Procedure| Description |
| - | - |
| [Creazione di verifiche di accesso](create-access-review.md)| Creare una o più verifiche di accesso per i membri del gruppo o l'accesso alle applicazioni. |
| [Eseguire verifiche di accesso](perform-access-review.md)| Eseguire una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione. |
| [Verifica autonoma dell'accesso](review-your-access.md)| I membri verificano l'accesso a un gruppo o a un'applicazione |
| [Completa la verifica di accesso](complete-access-review.md)| Visualizzare una verifica di accesso e applicare i risultati |
| [Intervenire per i gruppi locali](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Script di PowerShell di esempio per agire sulle verifiche di accesso per i gruppi locali. |


### <a name="review-azure-ad-roles"></a>Esaminare i ruoli di Azure AD

Per ridurre i rischi associati alle assegnazioni di ruolo obsolete, è necessario esaminare periodicamente l'accesso ai ruoli di Azure AD con privilegi.

![Screenshot che mostra l'elenco "verifica appartenenza" dei ruoli di Azure A D.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Seguire le istruzioni riportate nei collegamenti seguenti:

| Procedure | Description |
| - | - |
 [Creazione di verifiche di accesso](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Creare verifiche di accesso per i ruoli di Azure AD con privilegi in PIM |
| [Verifica autonoma dell'accesso](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se si è assegnati a un ruolo amministrativo, approvare o negare l'accesso al ruolo |
| [Completare una verifica di accesso](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visualizzare una verifica di accesso e applicare i risultati |


### <a name="review-azure-resource-roles"></a>Esaminare i ruoli delle risorse di Azure

Per ridurre i rischi associati alle assegnazioni di ruolo obsolete, è necessario verificare regolarmente l'accesso ai ruoli delle risorse di Azure con privilegi. 

![esaminare i ruoli di Azure ad](./media/deploy-access-review/9-review-azure-roles-picker.png)

Seguire le istruzioni riportate nei collegamenti seguenti:

| Procedure| Description |
| - | -|
| [Creazione di verifiche di accesso](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Creare verifiche di accesso per i ruoli delle risorse di Azure con privilegi in PIM |
| [Verifica autonoma dell'accesso](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se si è assegnati a un ruolo amministrativo, approvare o negare l'accesso al ruolo |
| [Completare una verifica di accesso](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visualizzare una verifica di accesso e applicare i risultati |


## <a name="use-the-access-reviews-api"></a>Usare l'API verifiche di accesso

Vedere [metodi dell'API Graph](/graph/api/resources/accessreviews-root?view=graph-rest-beta) e [controlli di autorizzazione per ruoli e applicazioni](/graph/api/resources/accessreviews-root?view=graph-rest-beta) per interagire con le risorse e gestirle. I metodi delle verifiche di accesso nell'API Microsoft Graph sono disponibili sia per i contesti dell'applicazione che dell'utente. Quando si eseguono script nel contesto dell'applicazione, all'account usato per eseguire l'API (l'entità servizio) deve essere concessa l'autorizzazione "AccessReview. Read. All" per eseguire query sulle informazioni sulle verifiche di accesso.

Le attività di verifica di accesso più diffuse per automatizzare l'utilizzo del API Graph per le verifiche di accesso sono:

* Creare e avviare una verifica di accesso

* Terminare manualmente una verifica di accesso prima della fine pianificata

* Elencare tutte le verifiche di accesso in esecuzione e il relativo stato

* Vedere la cronologia di una serie di revisione e le decisioni e le azioni intraprese in ogni revisione.

* Raccogliere le decisioni da una verifica di accesso

* Raccogliere le decisioni dalle revisioni completate in cui il revisore ha preso una decisione diversa rispetto a quella consigliata dal sistema.

Quando si creano nuove query di API Graph per l'automazione, è consigliabile usare [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer). È possibile compilare ed esplorare le query Graph prima di inserirle in script e codice. Questo consente di eseguire rapidamente l'iterazione della query in modo da ottenere esattamente i risultati desiderati, senza modificare il codice dello script.

## <a name="monitor-access-reviews"></a>Monitorare le verifiche di accesso

Le attività delle verifiche di accesso vengono registrate e disponibili dai [log di controllo Azure ad](../reports-monitoring/concept-audit-logs.md). È possibile filtrare i dati di controllo relativi alla categoria, al tipo di attività e all'intervallo di date. Ecco una query di esempio:

| Category| Policy |
| - | - |
| Tipo di attività| Creare una verifica di accesso |
| | Aggiornamento della verifica di accesso |
| | Verifica di accesso terminata |
| | Eliminare la verifica di accesso |
| | Approva decisione |
| | Nega decisione |
| | Reimposta decisione |
| | Applica decisione |
| Intervallo di date| 7 giorni |


Per eseguire query e analisi più avanzate delle verifiche di accesso e per tenere traccia delle modifiche e del completamento delle verifiche, è consigliabile esportare i log di controllo Azure AD in [azure log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) o nell'hub eventi di Azure. Quando viene archiviato in Azure Log Analytics, è possibile usare il [linguaggio di analisi avanzato](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) e creare dashboard personalizzati.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle tecnologie correlate seguenti.

* [Che cos'è Azure AD gestione dei diritti?](entitlement-management-overview.md)

* [Che cos'è Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

