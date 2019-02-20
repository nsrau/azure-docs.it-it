---
title: Provisioning di utenti automatizzato per app SaaS in Azure AD | Microsoft Docs
description: Introduzione all'uso di Azure AD per eseguire automaticamente il provisioning, il deprovisioning e l'aggiornamento continuo degli account utente in più applicazioni SaaS di terze parti.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8b099f845df66dfe8c43bc6f968fd63b30d09d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186353"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Informazioni sul provisioning utenti automatizzato per app SaaS
Azure Active Directory (Azure AD) consente di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), ad esempio Dropbox, Salesforce, ServiceNow e così via.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Di seguito sono riportati alcuni esempi di operazioni che questa funzionalità consente di eseguire:**

* Creare automaticamente nuovi account nei sistemi appropriati per i membri che si uniscono al team o all'organizzazione.
* Disattivare automaticamente nuovi account nei sistemi appropriati per i membri che lasciano il team o l'organizzazione.
* Assicurarsi che le identità nelle app e nei sistemi siano sempre aggiornate in base alle modifiche nella directory o nel sistema di risorse umane.
* Effettuare il provisioning di oggetti non utente, ad esempio di gruppi, in applicazioni che li supportano.

**Il provisioning utenti automatico include anche le funzionalità seguenti:**

* Possibilità di ottenere una corrispondenza tra identità esistenti in sistemi di origine e di destinazione.
* Mapping personalizzabili degli attributi che definiscono quali dati degli utenti devono essere trasferiti dal sistema di origine a quello di destinazione.
* Avvisi di posta elettronica facoltativi per errori di provisioning.
* Log di report e attività per facilitare il monitoraggio e la risoluzione dei problemi.

## <a name="why-use-automated-provisioning"></a>Perché usare il provisioning automatico?

Di seguito sono riportate alcune motivazioni comuni per l'uso di questa funzionalità:

* Per evitare i costi, le inefficienze e gli errori umani associati ai processi di provisioning manuali.
* Per evitare i costi associati all'hosting e alla gestione di soluzioni e script di provisioning sviluppati in modo personalizzato.
* Per proteggere l'organizzazione, rimuovendo immediatamente dalle app SaaS principali le identità degli utenti che lasciano l'organizzazione.
* Per importare facilmente un numero elevato di utenti in un'applicazione o un sistema SaaS specifico.
* Per usare un unico set di criteri per determinare gli utenti di cui viene eseguito il provisioning e che possono accedere a un'app.

## <a name="how-does-automatic-provisioning-work"></a>Come funziona il provisioning automatico?
    
Il **servizio di provisioning di Azure AD** effettua il provisioning degli utenti nelle app SaaS e in altri sistemi connettendosi agli endpoint dell'API di gestione utenti specificati dai fornitori di ogni applicazione. Gli endpoint dell'API di gestione utenti consentono ad Azure AD di creare, aggiornare e rimuovere utenti a livello di codice. Per alcune applicazioni, il servizio di provisioning può anche creare, aggiornare e rimuovere oggetti aggiuntivi relative alle identità, ad esempio i gruppi e i ruoli. 

![Provisioning](./media/user-provisioning/provisioning0.PNG)
*Figura 1: Provisioning del servizio di Azure AD*

![Provisioning in uscita](./media/user-provisioning/provisioning1.PNG)
*Figura 2: Flusso di lavoro di provisioning degli utenti "in uscita" da Azure AD verso applicazioni SaaS comuni*

![Provisioning in ingresso](./media/user-provisioning/provisioning2.PNG)
*Figura 3: Flusso di lavoro di provisioning degli utenti "in ingresso" da applicazioni di gestione delle risorse umane (HCM) comuni verso Azure Active Directory e Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quali applicazioni e sistemi è possibile usare con il provisioning utenti automatico di Azure AD?

Azure AD offre il supporto preintegrato per un'ampia gamma di sistemi di risorse umane e app SaaS comuni, nonché il supporto generico per le app che implementano parti specifiche dello standard SCIM 2.0.

### <a name="pre-integrated-applications"></a>Applicazioni preintegrate

Per un elenco di tutte le applicazioni per cui Azure AD supporta un connettore di provisioning preintegrato, vedere l'[elenco delle esercitazioni sulle applicazioni per il provisioning utenti](../saas-apps/tutorial-list.md).

Per contattare il team di progettazione di Azure AD e richiedere supporto per il provisioning di applicazioni aggiuntive, inviare un messaggio tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Per supportare il provisioning utenti automatico, un'applicazione deve prima di tutto includere le API di gestione utenti necessarie che consentono ai programmi esterni di automatizzare la creazione, la manutenzione e la rimozione degli utenti. Pertanto, non tutte le app SaaS sono compatibili con questa funzionalità. Per le app che supportano le API di gestione utenti, il team di progettazione di Azure AD potrà creare un connettore di provisioning e l'ordine di priorità di questa operazione è stabilito in base alle esigenze dei clienti attuali e potenziali. 

### <a name="connecting-applications-that-support-scim-20"></a>Connessione di applicazioni che supportano SCIM 2.0

Per informazioni su come connettere genericamente applicazioni che implementano API di gestione utenti basate su SCIM 2.0, vedere [Uso di System for Cross-Domain Identity Management (SCIM) per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

La configurazione del servizio di provisioning di Azure AD per un'applicazione selezionata viene avviata nel **[portale di Azure](https://portal.azure.com)**. Nella sezione **Azure Active Directory > Applicazioni aziendali** selezionare **Aggiungi**, quindi scegliere **Tutti** e aggiungere quanto segue a seconda dello scenario:

* Tutte le applicazioni nella sezione **Applicazioni in primo piano** supportano il provisioning automatico. Per eventuali altre applicazioni, vedere l'[elenco delle esercitazioni sulle applicazioni per il provisioning utenti](../saas-apps/tutorial-list.md).

* Usare l'opzione "Applicazione non nella raccolta" per le integrazioni SCIM sviluppate in modo personalizzato

![Gallery](./media/user-provisioning/gallery.png)

Il provisioning è configurato nella scheda **Provisioning** della schermata di gestione delle applicazioni.

![Impostazioni](./media/user-provisioning/provisioning_settings0.PNG)


* È necessario specificare le **credenziali amministratore** al servizio di provisioning di Azure AD per permettergli di connettersi all'API di gestione utenti indicata dall'applicazione. Questa sezione consente anche di abilitare l'invio di notifiche di posta elettronica se le credenziali non risultano corrette o il processo di provisioning entra in [quarantena](#quarantine).

* È possibile configurare **mapping degli attributi** che consentono di specificare per quali campi del sistema di origine, ad esempio Azure AD, verrà eseguita la sincronizzazione del contenuto con i campi corrispondenti del sistema di destinazione, ad esempio ServiceNow. Se l'applicazione di destinazione supporta tale possibilità, questa sezione consente anche di configurare il provisioning dei gruppi, oltre che degli account utente. Le proprietà corrispondenti permettono di selezionare i campi usati per associare gli account tra i sistemi. Le [espressioni](functions-for-customizing-application-data.md) permettono di modificare e trasformare i valori recuperati dal sistema di origine prima che vengano scritti nel sistema di destinazione. Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi](customize-application-attributes.md).

![Impostazioni](./media/user-provisioning/provisioning_settings1.PNG)

* I **filtri di ambito** indicano al servizio di provisioning gli utenti e il gruppo nel sistema di origine di cui effettuare il provisioning e/o il deprovisioning nel sistema di destinazione. I filtri di ambito presentano due aspetti che vengono valutati insieme e determinano l'inclusione nell'ambito del provisioning:

    * **Filtro in base ai valori di attributo**: il menu "Ambito dell'oggetto di origine" nei mapping degli attributi consente di filtrare valori di attributo specifici. Ad esempio, è possibile specificare che solo gli utenti con un attributo "Department" di "Sales" devono essere inclusi nell'ambito del provisioning. Per altre informazioni, vedere [Uso di filtri per la definizione dell'ambito](define-conditional-rules-for-provisioning-user-accounts.md).

    * **Filtro in base alle assegnazioni**: il menu "Ambito" nella sezione Provisioning &gt; Impostazioni del portale consente di specificare se nell'ambito del provisioning devono essere inclusi solo gli utenti e gruppi "assegnati" o se va effettuato il provisioning di tutti gli utenti nella directory di Azure AD. Per informazioni sull'assegnazione di utenti e gruppi, vedere [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](assign-user-or-group-access-portal.md).
    
* Le **impostazioni** permettono di controllare il funzionamento del servizio di provisioning per un'applicazione, incluso lo stato di esecuzione o meno.

* I **log di controllo** restituiscono i record di ogni operazione eseguita dal servizio di provisioning di Azure AD. Per altre informazioni, vedere la [guida alla creazione di report sul provisioning](check-status-user-account-provisioning.md).

![Impostazioni](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Il servizio di provisioning utenti di Azure AD può essere configurato e gestito anche con l'[API di Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Cosa accade durante il provisioning?

Quando Azure AD è il sistema di origine, il servizio di provisioning usa la [funzionalità di query differenziale dell'API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) per monitorare gli utenti e i gruppi. Il servizio di provisioning esegue una sincronizzazione iniziale rispetto al sistema di origine e a quello di destinazione, seguita da sincronizzazioni incrementali periodiche. 

### <a name="initial-sync"></a>Sincronizzazione iniziale

Quando viene avviato il servizio di provisioning, la prima sincronizzazione consiste nelle operazioni seguenti:

1. Il servizio esegue una query su tutti gli utenti e i gruppi presenti nel sistema di origine e recupera tutti gli attributi definiti nei [mapping degli attributi](customize-application-attributes.md).
2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando viene trovato un utente da assegnare o incluso nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per individuare un utente corrispondente usando gli [attributi di corrispondenza](customize-application-attributes.md#understanding-attribute-mapping-properties) designati. Esempio: Se il nome userPrincipal nel sistema di origine è un attributo di corrispondenza ed è mappato a userName nel sistema di destinazione, il servizio di provisioning esegue una query sul sistema di destinazione per trovare i valori di userName che corrispondono ai valori del nome userPrincipal nel sistema di origine.
4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, che viene usato per eseguire tutte le operazioni future di tale utente.
5. Se invece viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Dopo aver abbinato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, che viene usato per eseguire tutte le operazioni future di tale utente.
6. Se i mapping degli attributi contengono attributi "di riferimento", il servizio esegue altri aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui viene fatto riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
7. Al termine della sincronizzazione iniziale, il servizio salva in modo permanente una filigrana che fornisce il punto di partenza per le successive sincronizzazioni incrementali.

Alcune applicazioni come ServiceNow, Google Apps e Box supportano non solo il provisioning degli utenti ma anche quello dei gruppi e dei relativi membri. In questi casi, se nei [mapping](customize-application-attributes.md) è abilitato il provisioning di gruppi, il servizio di provisioning sincronizza sia gli utenti che i gruppi e successivamente anche le appartenenze ai gruppi. 

### <a name="incremental-syncs"></a>Sincronizzazioni incrementali

Dopo la sincronizzazione iniziale, tutte le sincronizzazioni successive consistono nelle operazioni seguenti:

1. Il servizio esegue una query sul sistema di origine per trovare gli utenti e i gruppi che sono stati aggiornati dopo il salvataggio dell'ultima filigrana.
2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando viene trovato un utente da assegnare o incluso nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per individuare un utente corrispondente usando gli [attributi di corrispondenza](customize-application-attributes.md#understanding-attribute-mapping-properties) designati.
4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, che viene usato per eseguire tutte le operazioni future di tale utente.
5. Se invece viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Se viene abbinato un account utente appena assegnato, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, che viene usato per eseguire tutte le operazioni future di tale utente.
6. Se i mapping degli attributi contengono attributi "di riferimento", il servizio esegue altri aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui viene fatto riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
7. Se un utente precedentemente incluso nell'ambito per il provisioning viene rimosso dall'ambito e la relativa assegnazione viene annullata, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
8. Se un utente precedentemente incluso nell'ambito per il provisioning viene disabilitato o eliminato temporaneamente nel sistema di origine, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
9. Se un utente precedentemente incluso nell'ambito per il provisioning viene eliminato definitivamente nel sistema di origine, il servizio elimina l'utente nel sistema di destinazione. In Azure AD gli utenti vengono eliminati definitivamente dopo che sono trascorsi 30 giorni dall'eliminazione temporanea.
10. Alla fine di una sincronizzazione incrementale, il servizio salva in modo permanente una nuova filigrana che fornisce il punto di partenza per le successive sincronizzazioni incrementali.

>[!NOTE]
> È possibile disabilitare le operazioni di creazione, aggiornamento o eliminazione usando le caselle di controllo **Azioni oggetto di destinazione** nella sezione [Mapping degli attributi](customize-application-attributes.md). La logica per disabilitare un utente durante un aggiornamento viene controllata anche tramite un mapping di attributi da un campo come "accountEnabled".

Il servizio di provisioning continua a eseguire sincronizzazioni incrementali back-to-back all'infinito, in base agli intervalli definiti nell'[esercitazione specifica di ogni applicazione](../saas-apps/tutorial-list.md), finché non si verifica uno degli eventi seguenti:

* Il servizio viene arrestato manualmente tramite il portale di Azure o con il comando appropriato dell'API Graph. 
* Viene attivata una nuova sincronizzazione iniziale tramite l'opzione **Cancella lo stato corrente e riavvia la sincronizzazione** nel portale di Azure o con il comando appropriato dell'API Graph. Per effetto di questa operazione, le eventuali filigrane salvate vengono cancellate e tutti gli oggetti di origine vengono nuovamente valutati.
* Viene attivata una nuova sincronizzazione iniziale a causa di una modifica nel mapping degli attributi o nei filtri di ambito. Anche per effetto di questa operazione, le eventuali filigrane salvate vengono cancellate e tutti gli oggetti di origine vengono nuovamente valutati.
* Il processo di provisioning entra in quarantena (vedere sotto) a causa di errori molto frequenti e resta in quarantena per più di quattro settimane. In questo caso, il servizio viene disabilitato automaticamente.

### <a name="errors-and-retries"></a>Errori e ripetizione di tentativi

Se non è possibile aggiungere, aggiornare o eliminare un singolo utente nel sistema di destinazione a causa di un errore in tale sistema, il tentativo di eseguire questa operazione viene ripetuto nel ciclo di sincronizzazione successivo. Se l'errore si verifica nuovamente, la frequenza dei tentativi viene gradualmente ridotta fino a un solo tentativo al giorno. Per risolvere l'errore, gli amministratori devono verificare gli eventi di "deposito dei processi" nei [log di controllo](check-status-user-account-provisioning.md) per determinare la causa all'origine del problema e intraprendere l'azione appropriata. Tra gli errori comuni possono essere inclusi i seguenti:

* Per gli utenti nel sistema di origine non è definito un valore di attributo che è necessario nel sistema di destinazione.
* Per gli utenti nel sistema di origine è definito un valore di attributo per cui è impostato un vincolo univoco nel sistema di destinazione e lo stesso valore è presente in un altro record utente.

Questi errori possono essere risolti modificando i valori di attributo per l'utente interessato nel sistema di origine o modificando i mapping degli attributi in modo da non causare conflitti.   

### <a name="quarantine"></a>Quarantena

Se la maggior parte o tutte le chiamate al sistema di destinazione non riuscono a causa di un errore (ad esempio nel caso di credenziali di amministratore non valide), il processo di provisioning passa allo stato di "quarantena". Questa condizione viene indicata nel [report di riepilogo sul provisioning](check-status-user-account-provisioning.md) e tramite posta elettronica, se nel portale di Azure sono state configurate le notifiche di posta elettronica. 

In stato di quarantena, la frequenza delle sincronizzazioni incrementali viene gradualmente ridotta fino a diventare giornaliera. 

Dopo che tutti gli errori sono stati risolti, il processo di provisioning viene rimosso dalla quarantena e viene avviato il ciclo di sincronizzazione successivo. Se lo stato di quarantena dura per più di quattro settimane, il processo di provisioning viene disabilitato.


## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo sarà necessario per eseguire il provisioning degli utenti?

Il livello delle prestazioni varia a seconda che la sincronizzazione eseguita dal processo di provisioning sia iniziale o incrementale, come descritto nella sezione precedente.

Per le **sincronizzazioni iniziali**, la durata del processo dipende da diversi fattori, tra cui il numero di utenti e gruppi nell'ambito del provisioning e il numero totale di utenti e gruppi nel sistema di origine. Più avanti in questa sezione è presente un elenco completo dei fattori che influiscono sulle prestazioni della sincronizzazione iniziale.

Per le **sincronizzazioni incrementali**, il tempo necessario dipende dal numero di modifiche rilevate in un ciclo di sincronizzazione specifico. Se sono presenti meno di 5.000 modifiche agli utenti o alle appartenenze a gruppi, è possibile completare il processo in un unico ciclo di sincronizzazione incrementale. 

La tabella seguente riepiloga i tempi di sincronizzazione per gli scenari di provisioning più comuni. In questi scenari, il sistema di origine è Azure AD e il sistema di destinazione è un'applicazione SaaS. I tempi di sincronizzazione sono elaborati tramite un'analisi statistica dei processi di sincronizzazione per le applicazioni SaaS ServiceNow, Workplace, Salesforce e Google Apps.


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

* Numero totale di utenti e gruppi nell'ambito del provisioning.

* Numero totale di utenti, gruppi e membri del gruppo presenti nel sistema di origine (Azure AD).

* Se gli utenti nell'ambito del provisioning corrispondono o meno agli utenti esistenti nell'applicazione di destinazione o se devono essere creati per la prima volta. I processi di sincronizzazione nei quali tutti gli utenti vengono creati per la prima volta hanno una durata approssimativa *doppia* rispetto ai processi di sincronizzazione nei quali tutti gli utenti corrispondono a utenti esistenti.

* Numero di errori nei [log di controllo](check-status-user-account-provisioning.md). Le prestazioni risultano ridotte se sono presenti troppi errori e se il servizio di provisioning è in stato di quarantena.    

* Limitazioni relative al numero e alla frequenza delle richieste implementate dal sistema di destinazione. Alcuni sistemi di destinazione implementano limitazioni relative al numero e alla frequenza delle richieste, che possono influire negativamente sulle prestazioni durante la sincronizzazione di grandi quantità di dati. In queste condizioni, un'applicazione che riceve un numero eccessivo di richieste potrebbe ridurre la propria velocità di risposta o interrompere la connessione. Per migliorare le prestazioni, il connettore deve essere regolato in modo da non inviare le richieste di app più velocemente di quanto l'app possa elaborarle. I connettori di provisioning creati da Microsoft sono in grado di eseguire questa regolazione. 

* Numero e dimensione dei gruppi assegnati. La sincronizzazione dei gruppi assegnati richiede più tempo rispetto alla sincronizzazione degli utenti. Il numero e la dimensione dei gruppi assegnati incidono sulle prestazioni. Se in un'applicazione è [abilitato il mapping per la sincronizzazione dell'oggetto del gruppo](customize-application-attributes.md#editing-group-attribute-mappings), in aggiunta agli utenti vengono sincronizzate le proprietà del gruppo, come i nomi e le appartenenze del gruppo. Queste sincronizzazioni aggiuntive richiederanno più tempo rispetto alla sincronizzazione dei soli oggetti utente.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Come è possibile stabilire se il provisioning utenti viene eseguito correttamente?

Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei log di controllo di Azure AD. Nella registrazione sono incluse tutte le operazioni di lettura e scrittura eseguite nei sistemi di origine e di destinazione, oltre ai dati degli utenti che sono stati letti o scritti durante ogni operazione.

Per informazioni su come leggere i log di controllo nel portale di Azure, vedere la [guida alla creazione di report sul provisioning](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Come si risolvono i problemi relativi al provisioning utenti?

Per informazioni aggiuntive su come risolvere i problemi di provisioning automatico dell'utente in base agli scenari, vedere [Problemi di configurazione e provisioning degli utenti in un'applicazione](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quali sono le procedure consigliate per implementare il provisioning utenti automatico?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Per un esempio di piano di distribuzione dettagliato per il provisioning utenti in uscita verso un'applicazione, vedere [Identity Deployment Guide for User Provisioning](https://aka.ms/userprovisioningdeploymentplan) (Guida alla distribuzione delle identità per il provisioning utenti).

## <a name="more-frequently-asked-questions"></a>Domande frequenti

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con gli utenti B2B in Azure AD?

Sì, è possibile usare il servizio di provisioning utenti di Azure AD per effettuare il provisioning degli utenti B2B (o guest) di Azure AD in applicazioni SaaS.

Tuttavia, perché gli utenti B2B possano accedere all'applicazione SaaS con Azure AD, la funzionalità Single Sign-On basata su SAML dell'applicazione SaaS deve essere configurata in modo specifico. Per altre informazioni su come configurare le applicazioni SaaS per supportare gli accessi dagli utenti B2B, vedere [Configurare app SaaS per Collaborazione B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con i gruppi dinamici in Azure AD?

Sì. Il servizio di provisioning utenti di Azure AD, se configurato per "sincronizzare solo utenti e gruppi assegnati", può effettuare il provisioning o il deprovisioning degli utenti in un'applicazione SaaS a seconda che siano o meno membri di un [gruppo dinamico](../users-groups-roles/groups-create-rule.md). I gruppi dinamici funzionano anche con l'opzione "Sincronizza tutti gli utenti e i gruppi".

Tuttavia, l'utilizzo di gruppi dinamici può compromettere le prestazioni complessive del provisioning utenti end-to-end da Azure AD alle applicazioni SaaS. Quando si usano i gruppi dinamici, tenere presenti queste avvertenze e raccomandazioni:

* La velocità di provisioning o deprovisioning di un utente di un gruppo dinamico in un'applicazione SaaS dipende dalla velocità con cui il gruppo dinamico riesce a valutare le modifiche all'appartenenza. Per informazioni su come controllare lo stato di elaborazione di un gruppo dinamico, vedere [Controllare lo stato di elaborazione per una regola di appartenenza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule#check-processing-status-for-a-membership-rule).

* Quando si usano i gruppi dinamici, è necessario prestare particolare attenzione alle regole in relazione al provisioning e al deprovisioning, perché una perdita dell'appartenenza genererà un evento di deprovisioning.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con i gruppi annidati in Azure AD?

 No. Il servizio di provisioning utenti di Azure AD, se configurato per "sincronizzare solo utenti e gruppi assegnati", non riesce a effettuare la lettura o il provisioning di utenti inclusi in gruppi annidati. Può effettuare la lettura e il provisioning solo degli utenti che sono membri immediati del gruppo assegnato in modo esplicito.

Si tratta di una limitazione delle "assegnazioni basate su gruppi alle applicazioni", che ha effetto anche su Single Sign-On e viene descritta in [Uso di un gruppo per gestire l'accesso ad applicazioni SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Come soluzione alternativa è consigliabile assegnare in modo esplicito (o [definire l'ambito](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) i gruppi contenenti gli utenti di cui è necessario effettuare il provisioning.

## <a name="related-articles"></a>Articoli correlati

* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](customize-application-attributes.md)
* [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
* [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md)
* [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
