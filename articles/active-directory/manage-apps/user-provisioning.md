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
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144512"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizzare il provisioning e il deprovisioning utenti in applicazioni con Azure Active Directory

In Azure Active Directory (Azure AD), il termine **provisioning app** si riferisce alla creazione automatica di identità e ruoli utente nelle applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente durante la modifica dello stato o dei ruoli. Gli scenari comuni includono il provisioning di un utente Azure AD in applicazioni come [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)e altro ancora.

![Diagramma della panoramica del provisioning](media/user-provisioning/provisioning-overview.png)

Questa funzionalità consente di:

- **Automatizzare il provisioning**: creare automaticamente nuovi account nei sistemi appropriati per le nuove persone quando partecipano al team o all'organizzazione.
- **Automatizzare il deprovisioning:** Disattivare automaticamente gli account nei sistemi corretti quando gli utenti lasciano il team o l'organizzazione.
- **Sincronizzare i dati tra i sistemi:** Assicurarsi che le identità nelle app e nei sistemi siano sempre aggiornate in base alle modifiche apportate alla directory o al sistema di risorse umane.
- **Gruppi di provisioning:** Effettuare il provisioning di gruppi alle applicazioni che le supportano.
- **Governare l'accesso:** Monitorare e controllare gli utenti di cui è stato effettuato il provisioning nelle applicazioni.
- **Distribuisci con semplicità negli scenari di campo marrone:** Abbinare le identità esistenti tra i sistemi e consentire una facile integrazione, anche quando gli utenti sono già presenti nel sistema di destinazione.
- **USA personalizzazione avanzata:** Sfruttare i mapping degli attributi personalizzabili che definiscono quali dati utente devono essere propagati dal sistema di origine al sistema di destinazione.
- **Ottenere avvisi per gli eventi critici:** Il servizio di provisioning fornisce avvisi per gli eventi critici e consente l'integrazione Log Analytics in cui è possibile definire avvisi personalizzati per soddisfare le esigenze aziendali.

## <a name="benefits-of-automatic-provisioning"></a>Vantaggi del provisioning automatico

Poiché il numero di applicazioni utilizzate nelle organizzazioni moderne continua a crescere, gli amministratori IT hanno la funzione di gestione degli accessi su larga scala. Gli standard, ad esempio Security Assertion Markup Language (SAML) o Open ID Connect (OIDC) consentono agli amministratori di configurare rapidamente Single Sign-On (SSO), ma l'accesso richiede anche che gli utenti vengano sottoposti a provisioning nell'app. Per molti amministratori, il provisioning comporta la creazione manuale di ogni account utente o il caricamento di file CSV ogni settimana, ma questi processi sono dispendiosi in termini di tempo, costosi e soggetti a errori. Sono state adottate soluzioni come SAML just-in-time (JIT) per automatizzare il provisioning, ma le aziende necessitano anche di una soluzione per effettuare il deprovisioning degli utenti quando lasciano l'organizzazione o non richiedono più l'accesso a determinate app in base alla modifica del ruolo.

Di seguito sono riportate alcune motivazioni comuni per l'uso del provisioning automatico:

- Ottimizzazione dell'efficienza e dell'accuratezza dei processi di provisioning.
- Risparmio sui costi associati all'hosting e alla gestione di soluzioni e script di provisioning sviluppati in personalizzato.
- Proteggere l'organizzazione rimuovendo immediatamente le identità degli utenti dalle principali app SaaS quando lasciano l'organizzazione.
- Importazione semplice di un numero elevato di utenti in un'applicazione o un sistema SaaS specifico.
- Disporre di un singolo set di criteri per determinare chi ne è stato effettuato il provisioning e chi può accedere a un'app.

Azure AD provisioning utenti può aiutare a risolvere questi problemi. Per ulteriori informazioni sul modo in cui i clienti utilizzano Azure AD provisioning degli utenti, è possibile leggere il [Case Study ASOS](https://aka.ms/asoscasestudy). Il video seguente offre una panoramica del provisioning degli utenti in Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quali applicazioni e sistemi è possibile usare con il provisioning utenti automatico di Azure AD?

Azure AD offre un supporto pre-integrato per molte app SaaS e sistemi di risorse umane comuni e il supporto generico per le app che implementano parti specifiche dello [standard SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Applicazioni preintegrate (app SaaS della raccolta)** . È possibile trovare tutte le applicazioni per le quali Azure AD supporta un connettore di provisioning pre-integrato nell' [elenco delle esercitazioni sulle applicazioni per il provisioning degli utenti](../saas-apps/tutorial-list.md). Le applicazioni preintegrate elencate nella raccolta usano in genere le API di gestione utenti basate su SCIM 2,0 per il provisioning. 

   ![Logo Salesforce](media/user-provisioning/gallery-app-logos.png)

   Se si vuole richiedere una nuova applicazione per il provisioning, è possibile [richiedere che l'applicazione sia integrata con la raccolta di app](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing). Per una richiesta di provisioning dell'utente, è necessario che l'applicazione disponga di un endpoint conforme a SCIM. Chiedere al fornitore dell'applicazione di seguire lo standard SCIM per poter caricare rapidamente l'app sulla piattaforma.

* **Applicazioni che supportano SCIM 2,0**. Per informazioni su come connettere in modo generico le applicazioni che implementano le API di gestione utenti basate su SCIM 2,0, vedere [uso di SCIM per effettuare automaticamente il provisioning di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>Che cos'è SCIM?

Per automatizzare il provisioning e il deprovisioning, le app espongono API di utenti e gruppi proprietarie. Tuttavia, chiunque abbia provato a gestire gli utenti in più app indica che ogni app tenta di eseguire le stesse azioni semplici, ad esempio la creazione o l'aggiornamento degli utenti, l'aggiunta di utenti a gruppi o il deprovisioning degli utenti. Tuttavia, tutte queste semplici azioni vengono implementate in modo leggermente diverso, usando percorsi di endpoint diversi, metodi diversi per specificare le informazioni utente e uno schema diverso per rappresentare ogni elemento di informazioni.

Per risolvere questi problemi, la specifica SCIM fornisce uno schema utente comune che consente agli utenti di spostarsi in, da e verso le app. SCIM sta diventando lo standard de facto per il provisioning e, se usato in combinazione con gli standard di federazione come SAML o OpenID Connect, fornisce agli amministratori una soluzione basata su standard end-to-end per la gestione degli accessi.

Per istruzioni dettagliate sull'uso di SCIM per automatizzare il provisioning e il deprovisioning di utenti e gruppi in un'applicazione, vedere [provisioning utenti scim con Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="how-does-automatic-provisioning-work"></a>Come funziona il provisioning automatico?

Il **servizio di provisioning Azure ad esegue il provisioning** degli utenti nelle app SaaS e in altri sistemi connettendosi agli endpoint dell'API di gestione degli utenti forniti da ogni fornitore dell'applicazione. Gli endpoint dell'API di gestione utenti consentono ad Azure AD di creare, aggiornare e rimuovere utenti a livello di codice. Per le applicazioni selezionate, il servizio di provisioning può anche creare, aggiornare e rimuovere oggetti correlati all'identità aggiuntivi, ad esempio gruppi e ruoli.

![Azure AD servizio di provisioning](./media/user-provisioning/provisioning0.PNG)
*Figura 1: il servizio di provisioning Azure ad*

![flusso di lavoro di provisioning utenti *in uscita](./media/user-provisioning/provisioning1.PNG)
figura 2: flusso di lavoro di provisioning utenti "in uscita" da Azure ad a applicazioni SaaS più diffuse*

![flusso di lavoro di provisioning utenti *in ingresso](./media/user-provisioning/provisioning2.PNG)
figura 3: flusso di lavoro di provisioning utenti "in ingresso" da applicazioni di gestione di capitale umana più diffuse per Azure Active Directory e Windows Server Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

Per le applicazioni preintegrate elencate nella raccolta, sono disponibili istruzioni dettagliate per la configurazione del provisioning automatico. Vedere l' [elenco delle esercitazioni per le app integrate della raccolta](https://docs.microsoft.com/azure/active-directory/saas-apps/). Il video seguente illustra come configurare il provisioning utenti automatico per SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Per altre applicazioni che supportano SCIM 2,0, seguire la procedura descritta nell'articolo [provisioning utenti scim con Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="what-happens-during-provisioning"></a>Cosa accade durante il provisioning?

Quando Azure AD è il sistema di origine, il servizio di provisioning usa la [funzionalità di query differenziale dell'API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) per monitorare gli utenti e i gruppi. Il servizio di provisioning esegue un ciclo iniziale sul sistema di origine e di destinazione, seguito da cicli incrementali periodici.

### <a name="initial-cycle"></a>Ciclo iniziale

Quando viene avviato il servizio di provisioning, la prima sincronizzazione viene eseguita:

1. Il servizio esegue una query su tutti gli utenti e i gruppi presenti nel sistema di origine e recupera tutti gli attributi definiti nei [mapping degli attributi](customize-application-attributes.md).
1. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).
1. Quando un utente viene assegnato o nell'ambito del provisioning, il servizio esegue una query sul sistema di destinazione per un utente corrispondente usando gli [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties)specificati. Se ad esempio il nome userPrincipal nel sistema di origine è un attributo di corrispondenza ed è mappato a userName nel sistema di destinazione, il servizio di provisioning esegue una query sul sistema di destinazione per trovare i valori di userName che corrispondono ai valori del nome userPrincipal nel sistema di origine.
1. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, usato per eseguire tutte le operazioni future su tale utente.
1. Se viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Dopo che l'account utente è stato associato, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, usato per eseguire tutte le operazioni future su tale utente.
1. Se i mapping degli attributi contengono attributi di riferimento, il servizio esegue ulteriori aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui si fa riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
1. Salva in modo permanente una filigrana alla fine del ciclo iniziale, che fornisce il punto di partenza per i cicli incrementali successivi.

Alcune applicazioni come ServiceNow, G Suite e box supportano non solo il provisioning di utenti, ma anche il provisioning dei gruppi e dei relativi membri. In questi casi, se il provisioning del gruppo è abilitato nei [mapping](customize-application-attributes.md), il servizio di provisioning Sincronizza gli utenti e i gruppi e successivamente sincronizza le appartenenze ai gruppi.

### <a name="incremental-cycles"></a>Cicli incrementali

Dopo il ciclo iniziale, tutti gli altri cicli:

1. Il servizio esegue una query sul sistema di origine per trovare gli utenti e i gruppi che sono stati aggiornati dopo il salvataggio dell'ultima filigrana.
1. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](assign-user-or-group-access-portal.md) configurate o [filtri di ambito basati su attributi](define-conditional-rules-for-provisioning-user-accounts.md).
1. Quando un utente viene assegnato o nell'ambito del provisioning, il servizio esegue una query sul sistema di destinazione per un utente corrispondente usando gli [attributi corrispondenti](customize-application-attributes.md#understanding-attribute-mapping-properties)specificati.
1. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine. Dopo aver creato l'account utente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, usato per eseguire tutte le operazioni future su tale utente.
1. Se viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine. Se è un account appena assegnato corrispondente, il servizio di provisioning rileva e memorizza nella cache l'ID del sistema di destinazione per il nuovo utente, usato per eseguire tutte le operazioni future su tale utente.
1. Se i mapping degli attributi contengono attributi di riferimento, il servizio esegue ulteriori aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui si fa riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
1. Se un utente precedentemente incluso nell'ambito per il provisioning viene rimosso dall'ambito e la relativa assegnazione viene annullata, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
1. Se un utente precedentemente incluso nell'ambito per il provisioning viene disabilitato o eliminato temporaneamente nel sistema di origine, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
1. Se un utente precedentemente incluso nell'ambito per il provisioning viene eliminato definitivamente nel sistema di origine, il servizio elimina l'utente nel sistema di destinazione. In Azure AD, gli utenti vengono eliminati definitivamente 30 giorni dopo essere stati eliminati temporaneamente.
1. Salva in modo permanente una nuova filigrana alla fine del ciclo incrementale, che fornisce il punto di partenza per i cicli incrementali successivi.

> [!NOTE]
> Facoltativamente, è possibile disabilitare le operazioni di **creazione**, **aggiornamento**o **eliminazione** usando le caselle di controllo **azioni oggetto di destinazione** nella sezione [mapping](customize-application-attributes.md) . La logica per disabilitare un utente durante un aggiornamento viene controllata anche tramite un mapping di attributi da un campo come "accountEnabled".

Il servizio di provisioning continua a eseguire cicli incrementali back-to-back a tempo indeterminato, a intervalli definiti nell' [esercitazione specifica per ogni applicazione](../saas-apps/tutorial-list.md), fino a quando non si verifica uno degli eventi seguenti:

- Il servizio viene arrestato manualmente tramite il portale di Azure o con il comando appropriato dell'API Graph. 
- Un nuovo ciclo iniziale viene attivato utilizzando l'opzione **Cancella stato e riavvia** nell'portale di Azure o utilizzando il comando API Graph appropriato. Questa azione Cancella qualsiasi filigrana archiviata e fa in modo che tutti gli oggetti di origine vengano nuovamente valutati.
- Viene attivato un nuovo ciclo iniziale a causa di una modifica nei mapping degli attributi o nei filtri di ambito. Questa azione Cancella anche eventuali filigrane archiviate e fa in modo che tutti gli oggetti di origine vengano nuovamente valutati.
- Il processo di provisioning entra in quarantena (vedere di seguito) a causa di una frequenza di errori elevata e rimane in quarantena per più di quattro settimane. In questo caso, il servizio viene disabilitato automaticamente.

### <a name="errors-and-retries"></a>Errori e ripetizione di tentativi

Se non è possibile aggiungere, aggiornare o eliminare un singolo utente nel sistema di destinazione a causa di un errore nel sistema di destinazione, l'operazione viene ritentata nel ciclo di sincronizzazione successivo. Se l'errore si verifica nuovamente, la frequenza dei tentativi viene gradualmente ridotta fino a un solo tentativo al giorno. Per risolvere il problema, gli amministratori devono controllare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) per determinare la causa principale e intraprendere l'azione appropriata. Tra gli errori comuni possono essere inclusi i seguenti:

- Per gli utenti nel sistema di origine non è definito un valore di attributo che è necessario nel sistema di destinazione.
- Gli utenti hanno un valore di attributo nel sistema di origine per il quale esiste un vincolo UNIQUE nel sistema di destinazione e lo stesso valore è presente in un altro record utente

Questi errori possono essere risolti modificando i valori di attributo per l'utente interessato nel sistema di origine o modificando i mapping degli attributi in modo da non causare conflitti.

### <a name="quarantine"></a>Quarantena

Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo a causa di un errore (ad esempio per credenziali di amministratore non valide), il processo di provisioning passa allo stato "quarantena". Questo stato è indicato nel [report di riepilogo del provisioning](check-status-user-account-provisioning.md) e tramite posta elettronica se le notifiche di posta elettronica sono state configurate nel portale di Azure.

In quarantena, la frequenza dei cicli incrementali viene gradualmente ridotta a una volta al giorno.

Il processo di provisioning verrà rimosso dalla quarantena dopo che tutti gli errori sono stati risolti e viene avviato il ciclo di sincronizzazione successivo. Se lo stato di quarantena dura per più di quattro settimane, il processo di provisioning viene disabilitato. Per altre informazioni, vedere lo stato di quarantena [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo sarà necessario per eseguire il provisioning degli utenti?

Le prestazioni variano a seconda che il processo di provisioning esegua un ciclo di provisioning iniziale o un ciclo incrementale. Per informazioni dettagliate sul tempo necessario per il provisioning e su come monitorare lo stato del servizio di provisioning, vedere [controllare lo stato del](application-provisioning-when-will-provisioning-finish-specific-user.md)provisioning dell'utente.

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Come è possibile stabilire se il provisioning utenti viene eseguito correttamente?

Tutte le operazioni eseguite dal servizio di provisioning utenti vengono registrate nei [log di provisioning Azure ad (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Sono incluse tutte le operazioni di lettura e scrittura effettuate nei sistemi di origine e di destinazione e i dati utente letti o scritti durante ogni operazione.

Per informazioni su come leggere i log di provisioning nel portale di Azure, vedere la [Guida alla creazione di report sul provisioning](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Come si risolvono i problemi relativi al provisioning utenti?

Per informazioni aggiuntive su come risolvere i problemi di provisioning automatico dell'utente in base agli scenari, vedere [Problemi di configurazione e provisioning degli utenti in un'applicazione](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quali sono le procedure consigliate per implementare il provisioning utenti automatico?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Per un esempio di piano di distribuzione dettagliato per il provisioning utenti in uscita verso un'applicazione, vedere [Identity Deployment Guide for User Provisioning](https://aka.ms/deploymentplans/userprovisioning) (Guida alla distribuzione delle identità per il provisioning utenti).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con gli utenti B2B in Azure AD?

Sì, è possibile usare il servizio di provisioning utenti Azure AD per effettuare il provisioning di utenti B2B (o Guest) in Azure AD alle applicazioni SaaS.

Tuttavia, per consentire agli utenti B2B di accedere all'applicazione SaaS usando Azure AD, l'applicazione SaaS deve avere la funzionalità Single Sign-On basata su SAML configurata in modo specifico. Per altre informazioni su come configurare le applicazioni SaaS per supportare gli accessi dagli utenti B2B, vedere [Configurare app SaaS per Collaborazione B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con i gruppi dinamici in Azure AD?

Sì. Quando è configurato per "sincronizzare solo utenti e gruppi assegnati", il Azure AD servizio di provisioning utente può effettuare il provisioning o il deprovisioning degli utenti in un'applicazione SaaS a seconda che siano membri di un [gruppo dinamico](../users-groups-roles/groups-create-rule.md). I gruppi dinamici funzionano anche con l'opzione "Sincronizza tutti gli utenti e i gruppi".

Tuttavia, l'utilizzo di gruppi dinamici può compromettere le prestazioni complessive del provisioning utenti end-to-end da Azure AD alle applicazioni SaaS. Quando si usano i gruppi dinamici, tenere presenti queste avvertenze e consigli:

- La velocità di provisioning o deprovisioning di un utente di un gruppo dinamico in un'applicazione SaaS dipende dalla velocità con cui il gruppo dinamico riesce a valutare le modifiche all'appartenenza. Per informazioni su come controllare lo stato di elaborazione di un gruppo dinamico, vedere [Controllare lo stato di elaborazione per una regola di appartenenza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Quando si usano i gruppi dinamici, è necessario considerare attentamente le regole con il provisioning e il deprovisioning degli utenti, in quanto una perdita di appartenenza comporta un evento di deprovisioning.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Il provisioning utenti automatico nelle app SaaS funziona con i gruppi annidati in Azure AD?

No. Se configurato per "Sincronizza solo utenti e gruppi assegnati", il servizio di provisioning utenti Azure AD non è in grado di leggere o eseguire il provisioning degli utenti presenti in gruppi annidati. È solo in grado di leggere ed effettuare il provisioning degli utenti che sono membri immediati del gruppo assegnato in modo esplicito.

Si tratta di una limitazione delle "assegnazioni basate su gruppi alle applicazioni", che ha effetto anche su Single Sign-On e viene descritta in [Uso di un gruppo per gestire l'accesso ad applicazioni SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Per risolvere il problema, è necessario assegnare in modo esplicito (o in altro modo [ambito](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) i gruppi che contengono gli utenti di cui è necessario eseguire il provisioning.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Viene eseguito il provisioning tra Azure AD e un'applicazione di destinazione usando un canale crittografato?

Sì. Viene utilizzata la crittografia SSL HTTPS per la destinazione del server.

## <a name="related-articles"></a>Articoli correlati

- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
- [Personalizzazione dei mapping degli attributi per il provisioning degli utenti](customize-application-attributes.md)
- [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
- [Filtri di ambito per il provisioning degli utenti](define-conditional-rules-for-provisioning-user-accounts.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure AD alle applicazioni](use-scim-to-provision-users-and-groups.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
