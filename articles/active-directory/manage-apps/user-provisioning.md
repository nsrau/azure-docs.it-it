---
title: Provisioning di utenti automatizzato per app SaaS in Azure AD | Microsoft Docs
description: Introduzione all'uso di Azure AD per eseguire automaticamente il provisioning, il deprovisioning e l'aggiornamento continuo degli account utente in più applicazioni SaaS di terze parti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2019
ms.author: mimart
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bdae98f19be11cb960fe53c444c34a0b521f90e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824232"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Informazioni sul provisioning utenti automatizzato per app SaaS
Azure Active Directory (Azure AD) consente di automatizzare la creazione, manutenzione e rimozione delle identità utente nel cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) le applicazioni, ad esempio Dropbox, Salesforce, ServiceNow e altro ancora.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Questa funzionalità consente di:**

- Creare automaticamente nuovi account nei sistemi appropriati per i membri che si uniscono al team o all'organizzazione.
- Disattivare automaticamente nuovi account nei sistemi appropriati per i membri che lasciano il team o l'organizzazione.
- Assicurarsi che le identità nelle app e nei sistemi siano sempre aggiornate in base alle modifiche nella directory o nel sistema di risorse umane.
- Effettuare il provisioning di oggetti non utente, ad esempio di gruppi, in applicazioni che li supportano.

**Il provisioning utenti automatico include anche questa funzionalità:**

- Possibilità di ottenere una corrispondenza tra identità esistenti in sistemi di origine e di destinazione.
- Mapping personalizzabili degli attributi che definiscono quali dati degli utenti devono essere trasferiti dal sistema di origine a quello di destinazione.
- Avvisi di posta elettronica facoltativi per errori di provisioning.
- Log di report e attività per facilitare il monitoraggio e la risoluzione dei problemi.

## <a name="why-use-automated-provisioning"></a>Perché usare il provisioning automatico?

Di seguito sono riportate alcune motivazioni comuni per l'uso di questa funzionalità:

- Per evitare i costi, le inefficienze e gli errori umani associati ai processi di provisioning manuali.
- Per evitare i costi associati all'hosting e alla gestione di soluzioni e script di provisioning sviluppati in modo personalizzato.
- Proteggere l'organizzazione, rimuovendo immediatamente le identità degli utenti dalle App SaaS principali quando gli utenti lasciano l'organizzazione.
- Facilmente importando un numero elevato di utenti in un'applicazione SaaS specifica o un sistema.
- Presenza di un singolo set di criteri per determinare chi viene eseguito il provisioning e chi può accedere a un'app.

## <a name="how-does-automatic-provisioning-work"></a>Come funziona il provisioning automatico?
    
Il **servizio di Provisioning di Azure AD** effettua il provisioning agli utenti di App SaaS e altri sistemi connettendosi agli endpoint di gestione API di utente forniti dal produttore dell'applicazione. Gli endpoint dell'API di gestione utenti consentono ad Azure AD di creare, aggiornare e rimuovere utenti a livello di codice. Per le applicazioni selezionate, il servizio di provisioning può inoltre creare, aggiornare e rimuovere oggetti aggiuntivi relative alle identità, ad esempio gruppi e ruoli. 

![Provisioning](./media/user-provisioning/provisioning0.PNG)
*Figura 1: Provisioning del servizio di Azure AD*

![Provisioning in uscita](./media/user-provisioning/provisioning1.PNG)
*Figura 2: Flusso di lavoro di provisioning degli utenti "in uscita" da Azure AD verso applicazioni SaaS comuni*

![Provisioning in ingresso](./media/user-provisioning/provisioning2.PNG)
*Figura 3: Flusso di lavoro di provisioning degli utenti "in ingresso" da applicazioni di gestione delle risorse umane (HCM) comuni verso Azure Active Directory e Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quali applicazioni e sistemi è possibile usare con il provisioning utenti automatico di Azure AD?

Azure AD offre pre-integrato il supporto per molti sistemi più diffusi SaaS App e delle risorse umane e il supporto generico per le app che implementano parti specifiche dello standard SCIM 2.0.

### <a name="pre-integrated-applications"></a>Applicazioni preintegrate

Per un elenco di tutte le applicazioni per cui Azure AD supporta un connettore di provisioning preintegrato, vedere l'[elenco delle esercitazioni sulle applicazioni per il provisioning utenti](../saas-apps/tutorial-list.md).

Per contattare il team di progettazione di Azure AD e richiedere supporto per il provisioning di applicazioni aggiuntive, inviare un messaggio tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Per supportare il provisioning utenti automatico, un'applicazione deve prima di tutto includere le API di gestione utenti necessarie che consentono ai programmi esterni di automatizzare la creazione, la manutenzione e la rimozione degli utenti. Pertanto, non tutte le app SaaS sono compatibili con questa funzionalità. Per le app che supportano l'API di gestione utenti, il team di progettazione di Azure AD può quindi compilare un connettore di provisioning alle App e questa operazione viene definita la priorità per le esigenze dei clienti attuali e potenziali. 

### <a name="connecting-applications-that-support-scim-20"></a>Connessione di applicazioni che supportano SCIM 2.0

Per informazioni su come connettere genericamente applicazioni che implementano API di gestione utenti basate su SCIM 2.0, vedere [Uso di System for Cross-Domain Identity Management (SCIM) per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Usare il portale di Azure Active Directory per configurare il provisioning del servizio per un'applicazione selezionata AD Azure.

1. Aprire il  **[portale di Azure Active Directory](https://aad.portal.azure.com)**.

1. Selezionare **applicazioni aziendali** nel riquadro sinistro. Un elenco di tutte le app configurate verrà mostrati.

1. Scegli **+ nuova applicazione** per aggiungere un'applicazione. Aggiungere uno dei modi seguenti a seconda dello scenario:

   - Il **Aggiungi app personalizzata** opzione supporta le integrazioni SCIM sviluppate in modo personalizzato.

   - Tutte le applicazioni di **Aggiungi dalla raccolta** > **applicazioni in primo piano** sezione supportano il provisioning automatico. Per eventuali altre applicazioni, vedere l'[elenco delle esercitazioni sulle applicazioni per il provisioning utenti](../saas-apps/tutorial-list.md).

1. Specificare i dettagli e selezionare **Add**. La nuova app viene aggiunta all'elenco di applicazioni aziendali e viene visualizzata la schermata di gestione dell'applicazione.

1. Selezionare **Provisioning** per gestire le impostazioni per le app di provisioning dell'account utente.

   ![Impostazioni](./media/user-provisioning/provisioning_settings0.PNG)

1. Selezionare l'opzione automatica per il **modalità di Provisioning** per specificare le impostazioni per la sincronizzazione, i mapping, avvio e arresto e le credenziali di amministratore.

   - Espandere **credenziali amministratore** per immettere le credenziali necessarie per Azure AD per la connessione all'API di gestione utente dell'applicazione. In questa sezione consente anche di abilitare le notifiche di posta elettronica se le credenziali non risultano corrette o il processo di provisioning passa allo [quarantena](#quarantine).

   - Espandere **mapping** per visualizzare e modificare gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione quando gli account utente vengono effettuato il provisioning o aggiornati. Se l'applicazione di destinazione lo supporta, in questa sezione consente, facoltativamente, configurare il provisioning di gruppi e account utente. Selezionare un mapping nella tabella per aprire l'editor di mapping a destra, in cui è possibile visualizzare e personalizzare gli attributi dell'utente.
   
     **I filtri di ambito** informa il servizio di provisioning che gli utenti e gruppi nel sistema di origine devono essere eseguito il provisioning o deprovisioning nel sistema di destinazione. Nel **mapping degli attributi** riquadro, selezionare **ambito dell'oggetto di origine** per filtrare i valori di attributo specifico. Ad esempio, è possibile specificare che solo gli utenti con un attributo "Department" di "Sales" devono essere inclusi nell'ambito del provisioning. Per altre informazioni, vedere [Uso di filtri per la definizione dell'ambito](define-conditional-rules-for-provisioning-user-accounts.md).
    
     Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi](customize-application-attributes.md).

   - **Impostazioni** controllano il funzionamento del servizio di provisioning per un'applicazione, ad esempio se è in esecuzione. Il **ambito** menu consente di specificare se devono essere solo utenti e gruppi assegnati nell'ambito del provisioning, o se devono essere effettuato il provisioning di tutti gli utenti nella directory di Azure AD. Per informazioni sull'assegnazione di utenti e gruppi, vedere [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](assign-user-or-group-access-portal.md).

Nella schermata di gestione di app, selezionare **log di controllo** per visualizzare i record di ogni operazione di eseguono da Azure Active Directory del servizio di provisioning. Per altre informazioni, vedere la [guida alla creazione di report sul provisioning](check-status-user-account-provisioning.md).

![Impostazioni](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Il servizio di provisioning utenti di Azure AD può essere configurato e gestito anche con l'[API di Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Cosa accade durante il provisioning?

Quando Azure AD è il sistema di origine, il servizio di provisioning usa la [funzionalità di query differenziale dell'API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) per monitorare gli utenti e i gruppi. Il servizio di provisioning esegue una sincronizzazione iniziale rispetto al sistema di origine e a quello di destinazione, seguita da sincronizzazioni incrementali periodiche. 

### <a name="initial-sync"></a>Sincronizzazione iniziale

Quando viene avviato il servizio di provisioning, la prima sincronizzazione mai eseguire sarà:

1. Il servizio esegue una query su tutti gli utenti e i gruppi presenti nel sistema di origine e recupera tutti gli attributi definiti nei [mapping degli attributi](customize-application-attributes.md).
2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando un utente è assegnato o nell'ambito del provisioning, il servizio esegue una query di sistema di destinazione per un utente corrispondente usando l'oggetto specificato [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties). Esempio: Se il nome userPrincipal nel sistema di origine è un attributo di corrispondenza ed è mappato a userName nel sistema di destinazione, il servizio di provisioning esegue una query sul sistema di destinazione per trovare i valori di userName che corrispondono ai valori del nome userPrincipal nel sistema di origine.
4. Se un utente corrispondente non viene trovato nel sistema di destinazione, viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache ID del sistema di destinazione per il nuovo utente, che consente di eseguire tutte le operazioni future di quell'utente.
5. Se viene trovato un utente corrispondente, viene aggiornato usando gli attributi forniti dal sistema di origine. Dopo che l'account utente viene confrontato, il servizio di provisioning rileva e memorizza nella cache ID del sistema di destinazione per il nuovo utente, che consente di eseguire tutte le operazioni future di quell'utente.
6. Se i mapping degli attributi contengono attributi di "riferimento", il servizio esegue altri aggiornamenti del sistema di destinazione per creare e collegare gli oggetti di riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
7. Salvare in modo permanente una filigrana alla fine della sincronizzazione iniziale, che fornisce il punto di partenza per le sincronizzazioni incrementali in un secondo momento.

Alcune applicazioni, ad esempio ServiceNow, G Suite e Box supportano non solo il provisioning degli utenti, ma anche il provisioning di gruppi e i relativi membri. In questi casi, se il provisioning di gruppi è abilitato nel [mapping](customize-application-attributes.md), il servizio di provisioning Sincronizza gli utenti e i gruppi e quindi la sincronizzazione in un secondo momento le appartenenze a gruppi. 

### <a name="incremental-syncs"></a>Sincronizzazioni incrementali

Dopo la sincronizzazione iniziale, tutte le altre sincronizzazioni saranno:

1. Il servizio esegue una query sul sistema di origine per trovare gli utenti e i gruppi che sono stati aggiornati dopo il salvataggio dell'ultima filigrana.
2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando un utente è assegnato o nell'ambito del provisioning, il servizio esegue una query di sistema di destinazione per un utente corrispondente usando l'oggetto specificato [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties).
4. Se un utente corrispondente non viene trovato nel sistema di destinazione, viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache ID del sistema di destinazione per il nuovo utente, che consente di eseguire tutte le operazioni future di quell'utente.
5. Se viene trovato un utente corrispondente, viene aggiornato usando gli attributi forniti dal sistema di origine. Se si tratta di un account appena assegnato corrispondente, il servizio di provisioning rileva e memorizza nella cache ID del sistema di destinazione per il nuovo utente, che consente di eseguire tutte le operazioni future di quell'utente.
6. Se i mapping degli attributi contengono attributi di "riferimento", il servizio esegue altri aggiornamenti del sistema di destinazione per creare e collegare gli oggetti di riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
7. Se un utente precedentemente incluso nell'ambito per il provisioning viene rimosso dall'ambito e la relativa assegnazione viene annullata, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
8. Se un utente precedentemente incluso nell'ambito per il provisioning viene disabilitato o eliminato temporaneamente nel sistema di origine, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
9. Se un utente precedentemente incluso nell'ambito per il provisioning viene eliminato definitivamente nel sistema di origine, il servizio elimina l'utente nel sistema di destinazione. In Azure AD, gli utenti vengono eliminati definitivamente 30 giorni dopo averli collocati eliminati temporaneamente.
10. Salvare in modo permanente una filigrana di nuovo alla fine della sincronizzazione incrementale, che fornisce il punto di partenza per le sincronizzazioni incrementali in un secondo momento.

>[!NOTE]
> È possibile disabilitare il **Create**, **Update**, o **eliminare** operazioni usando il **azioni oggetto di destinazione** caselle di controllo nel [Mapping](customize-application-attributes.md) sezione. La logica per disabilitare un utente durante un aggiornamento viene controllata anche tramite un mapping di attributi da un campo come "accountEnabled".

Il servizio di provisioning continua l'esecuzione di sincronizzazioni incrementali back to back all'infinito, a intervalli definiti nel [esercitazione specifica di ogni applicazione](../saas-apps/tutorial-list.md), fino a quando non si verifica uno degli eventi seguenti:

- Il servizio viene arrestato manualmente tramite il portale di Azure o con il comando appropriato dell'API Graph. 
- Viene attivata una nuova sincronizzazione iniziale tramite l'opzione **Cancella lo stato corrente e riavvia la sincronizzazione** nel portale di Azure o con il comando appropriato dell'API Graph. Questa azione Cancella eventuali filigrane e fa in modo che tutti gli oggetti di origine deve essere valutata nuovamente.
- A causa di una modifica nel mapping degli attributi o i filtri di ambito viene attivata una nuova sincronizzazione iniziale. Inoltre, questa azione Cancella eventuali filigrane e fa in modo che tutti gli oggetti di origine deve essere valutata nuovamente.
- Il processo di provisioning entra in quarantena (vedere sotto) a causa di errori molto frequenti e resta in quarantena per più di quattro settimane. In questo caso, il servizio viene disabilitato automaticamente.

### <a name="errors-and-retries"></a>Errori e ripetizione di tentativi

Se un singolo utente non possa essere aggiunti, aggiornato o eliminato nel sistema di destinazione a causa di un errore nel sistema di destinazione, l'operazione viene ritentata nel successivo ciclo di sincronizzazione. Se l'errore si verifica nuovamente, la frequenza dei tentativi viene gradualmente ridotta fino a un solo tentativo al giorno. Per risolvere l'errore, gli amministratori devono verificare i [log di controllo](check-status-user-account-provisioning.md) per "deposito dei processi" causa degli eventi per stabilire la causa e intraprendere l'azione appropriata. Tra gli errori comuni possono essere inclusi i seguenti:

- Per gli utenti nel sistema di origine non è definito un valore di attributo che è necessario nel sistema di destinazione.
- Gli utenti che dispongono di un valore di attributo nel sistema di origine per cui è disponibile un vincolo univoco nel sistema di destinazione e lo stesso valore sono presenti in un altro record utente

Questi errori possono essere risolti modificando i valori di attributo per l'utente interessato nel sistema di origine o modificando i mapping degli attributi in modo da non causare conflitti.   

### <a name="quarantine"></a>Quarantena

Se la maggior parte o tutte le chiamate effettuate in modo coerente il sistema di destinazione hanno esito negativo a causa di un errore (ad esempio per le credenziali di amministratore non valide), quindi il processo di provisioning entra in uno stato di "quarantena". Questo stato è indicato nel [report di riepilogo del provisioning](check-status-user-account-provisioning.md) e tramite posta elettronica se le notifiche di posta elettronica sono state configurate nel portale di Azure. 

In stato di quarantena, la frequenza delle sincronizzazioni incrementali viene gradualmente ridotta fino a diventare giornaliera. 

Il processo di provisioning verrà rimossi dalla quarantena dopo tutti gli errori vengono corretti e avvia il ciclo di sincronizzazione successivo. Se lo stato di quarantena dura per più di quattro settimane, il processo di provisioning viene disabilitato.


## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo sarà necessario per eseguire il provisioning degli utenti?

Prestazioni varia a seconda che il processo di provisioning sia in esecuzione una sincronizzazione iniziale o incrementale.

Per la **iniziale viene sincronizzata**, ora del processo dipende da molti fattori, inclusi il numero di utenti e gruppi nell'ambito del provisioning e il numero totale di utenti e i gruppi nel sistema di origine. Più avanti in questa sezione è presente un elenco completo dei fattori che influiscono sulle prestazioni della sincronizzazione iniziale.

Per le **sincronizzazioni incrementali**, il tempo necessario dipende dal numero di modifiche rilevate in un ciclo di sincronizzazione specifico. Se sono presenti meno di 5.000 modifiche agli utenti o alle appartenenze a gruppi, è possibile completare il processo in un unico ciclo di sincronizzazione incrementale. 

La tabella seguente riepiloga i tempi di sincronizzazione per gli scenari di provisioning più comuni. In questi scenari, il sistema di origine è Azure AD e il sistema di destinazione è un'applicazione SaaS. I tempi di sincronizzazione sono derivati da un'analisi statistica dei processi di sincronizzazione per le applicazioni SaaS ServiceNow, all'area di lavoro, Salesforce e G Suite.


| Configurazione dell'ambito | Utenti, gruppi e membri nell'ambito | Ora della sincronizzazione iniziale | Ora della sincronizzazione incrementale |
| -------- | -------- | -------- | -------- |
| Sincronizza solo utenti e gruppi assegnati |  < 1.000 |  < 30 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |  1.000 - 10.000 | 142 - 708 minuti | < 30 minuti |
| Sincronizza solo utenti e gruppi assegnati |   10.000 - 100.000 | 1.170 - 2.340 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  < 1.000 | < 30 minuti  | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  1.000 - 10.000 | < 30 - 120 minuti | < 30 minuti |
| Sincronizza tutti gli utenti e i gruppi in Azure AD |  10.000 - 100.000  | 713 - 1.425 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD|  < 1.000  | < 30 minuti | < 30 minuti |
| Sincronizza tutti gli utenti in Azure AD | 1.000 - 10.000  | 43 - 86 minuti | < 30 minuti |


Nella configurazione **Sincronizza solo utenti e gruppi assegnati**, è possibile applicare le formule seguenti per determinare la durata approssimativa prevista, minima e massima, per la **sincronizzazione iniziale**:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Riepilogo dei fattori che influenzano il tempo necessario per completare una **sincronizzazione iniziale**:

- Numero totale di utenti e gruppi nell'ambito del provisioning.

- Numero totale di utenti, gruppi e membri del gruppo presenti nel sistema di origine (Azure AD).

- Indica se gli utenti nell'ambito del provisioning corrispondono agli utenti esistenti nell'applicazione di destinazione o devono essere creati per la prima volta. I processi di sincronizzazione per il quale vengono creati tutti gli utenti per la prima volta richiedere circa *due volte più a lungo* come sincronizzare i processi per il quale vengono confrontati tutti gli utenti per gli utenti esistenti.

- Numero di errori nei [log di controllo](check-status-user-account-provisioning.md). Le prestazioni risultano ridotte se sono presenti troppi errori e se il servizio di provisioning è in stato di quarantena.    

- Limitazioni relative al numero e alla frequenza delle richieste implementate dal sistema di destinazione. Alcuni sistemi di destinazione implementano limiti di velocità di richiesta e la limitazione delle richieste, che può influire sulle prestazioni durante le operazioni di sincronizzazione di grandi dimensioni. In queste condizioni, un'applicazione che riceve un numero eccessivo di richieste potrebbe ridurre la propria velocità di risposta o interrompere la connessione. Per migliorare le prestazioni, il connettore deve essere regolato in modo da non inviare le richieste di app più velocemente di quanto l'app possa elaborarle. I connettori di provisioning creati da Microsoft sono in grado di eseguire questa regolazione. 

- Numero e dimensione dei gruppi assegnati. La sincronizzazione dei gruppi assegnati richiede più tempo rispetto alla sincronizzazione degli utenti. Il numero e la dimensione dei gruppi assegnati incidono sulle prestazioni. Se in un'applicazione è [abilitato il mapping per la sincronizzazione dell'oggetto del gruppo](customize-application-attributes.md#editing-group-attribute-mappings), in aggiunta agli utenti vengono sincronizzate le proprietà del gruppo, come i nomi e le appartenenze del gruppo. Queste sincronizzazioni aggiuntive richiederanno più tempo rispetto alla sincronizzazione dei soli oggetti utente.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Come è possibile stabilire se il provisioning utenti viene eseguito correttamente?

Tutte le operazioni eseguite dall'utente del servizio di provisioning vengono registrate in Azure Active Directory log di controllo. Questo include tutte le operazioni effettuate da sistemi di origine e destinazione e dati utente che è stato letto o scritti durante ogni operazione di lettura e scrittura.

Per informazioni su come leggere i log di controllo nel portale di Azure, vedere la [Guida alla creazione di report sul provisioning](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Come si risolvono i problemi relativi al provisioning utenti?

Per informazioni aggiuntive su come risolvere i problemi di provisioning automatico dell'utente in base agli scenari, vedere [Problemi di configurazione e provisioning degli utenti in un'applicazione](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quali sono le procedure consigliate per implementare il provisioning utenti automatico?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Per un esempio di piano di distribuzione dettagliato per il provisioning utenti in uscita verso un'applicazione, vedere [Identity Deployment Guide for User Provisioning](https://aka.ms/userprovisioningdeploymentplan) (Guida alla distribuzione delle identità per il provisioning utenti).

## <a name="more-frequently-asked-questions"></a>Domande frequenti

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con gli utenti B2B in Azure AD?

Sì, è possibile usare l'utente di Azure AD provisioning degli utenti del servizio di provisioning B2B (o guest) di Azure AD verso applicazioni SaaS.

Tuttavia, per gli utenti B2B accedere all'applicazione SaaS con Azure AD, l'applicazione SaaS deve avere relativa basato su SAML single sign-on di funzionalità configurate in modo specifico. Per altre informazioni su come configurare le applicazioni SaaS per supportare gli accessi dagli utenti B2B, vedere [App configurare SaaS per collaborazione B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con i gruppi dinamici in Azure AD?

Sì. Quando configurato per "sincronizzare assegnati solo gli utenti e gruppi", il servizio di provisioning utenti di Azure AD può eseguire il provisioning o deprovisioning utenti in un'applicazione SaaS in base alle se sono membri di un [gruppo dinamico](../users-groups-roles/groups-create-rule.md). I gruppi dinamici funzionano anche con l'opzione "Sincronizza tutti gli utenti e i gruppi".

Tuttavia, l'utilizzo di gruppi dinamici può compromettere le prestazioni complessive del provisioning utenti end-to-end da Azure AD alle applicazioni SaaS. Quando si usano i gruppi dinamici, tenere queste avvertenze e le raccomandazioni presenti:

- La velocità di provisioning o deprovisioning di un utente di un gruppo dinamico in un'applicazione SaaS dipende dalla velocità con cui il gruppo dinamico riesce a valutare le modifiche all'appartenenza. Per informazioni su come controllare lo stato di elaborazione di un gruppo dinamico, vedere [Controllare lo stato di elaborazione per una regola di appartenenza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Quando si usano i gruppi dinamici, è necessario considerare con attenzione le regole con il provisioning e deprovisioning presente come una perdita dei risultati di appartenenza in un evento di deprovisioning.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con i gruppi annidati in Azure AD?

 No. Quando configurato per "sincronizzare assegnati solo gli utenti e gruppi", il servizio di provisioning utenti di Azure AD non è in grado di leggere o eseguire il provisioning degli utenti che si trovano in gruppi annidati. È solo in grado di leggere e il provisioning degli utenti che sono membri immediati di gruppo assegnato in modo esplicito.

Si tratta di una limitazione delle "assegnazioni basate su gruppi alle applicazioni", che ha effetto anche su Single Sign-On e viene descritta in [Uso di un gruppo per gestire l'accesso ad applicazioni SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

In alternativa, è necessario assegnare in modo esplicito (o in caso contrario [definire l'ambito](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) i gruppi che contengono gli utenti che devono eseguire il provisioning.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Esegue il provisioning tra Azure AD e un'applicazione di destinazione utilizzando un canale crittografato?

Sì. Si userà la crittografia SSL HTTPS per il server di destinazione. 

## <a name="related-articles"></a>Articoli correlati

- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
- [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](customize-application-attributes.md)
- [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
- [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
