---
title: Provisioning utenti automatizzato per app SaaS in Azure AD | Microsoft Docs
description: "Introduzione all'uso di Azure AD per eseguire automaticamente il provisioning, il deprovisioning e l'aggiornamento continuo degli account utente in più applicazioni SaaS di terze parti."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: e14ba62ce2d6c48e47a6b75387bcede68bb1a5b0
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Informazioni sul provisioning utenti automatizzato per app SaaS
Azure Active Directory (Azure AD) consente di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), ad esempio Dropbox, Salesforce, ServiceNow e così via.

**Di seguito sono riportati alcuni esempi di operazioni che questa funzionalità consente di eseguire:**

* Creare automaticamente nuovi account nei sistemi appropriati per i membri che si uniscono al team o all'organizzazione.
* Disattivare automaticamente nuovi account nei sistemi appropriati per i membri che lasciano il team o l'organizzazione.
* Assicurarsi che le identità nelle app e nei sistemi siano sempre aggiornate in base alle modifiche nella directory o nel sistema di risorse umane.
* Effettuare il provisioning di oggetti non utente, ad esempio di gruppi, in applicazioni che li supportano.

**Il provisioning utenti automatico include anche le funzionalità seguenti:**

* Possibilità di ottenere una corrispondenza tra identità esistenti in sistemi di origine e di destinazione.
* Mapping personalizzabili degli attributi che definiscono quali dati degli utenti devono essere trasferiti dal sistema di origine a quello di destinazione.
* Avvisi di posta elettronica facoltativi per segnalare gli errori di provisioning.
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

![Provisioning](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figura 1: Provisioning del servizio di Azure AD*

![Provisioning in uscita](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figura 2: Flusso di lavoro di provisioning degli utenti "in uscita" da Azure AD verso applicazioni SaaS comuni*

![Provisioning in ingresso](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figura 3: Flusso di lavoro di provisioning degli utenti "in ingresso" da applicazioni di gestione delle risorse umane (HCM) comuni verso Azure Active Directory e Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quali applicazioni e sistemi è possibile usare con il provisioning utenti automatico di Azure AD?

Azure AD offre il supporto preintegrato per un'ampia gamma di sistemi di risorse umane e app SaaS comuni, nonché il supporto generico per le app che implementano parti specifiche dello standard SCIM 2.0.

Per un elenco di tutte le applicazioni per cui Azure AD supporta un connettore di provisioning preintegrato, vedere l'[elenco delle esercitazioni sulle applicazioni per il provisioning utenti](active-directory-saas-tutorial-list.md).

Per informazioni su come aggiungere il supporto per il provisioning utenti di Azure AD a un'applicazione, vedere [Uso di System for Cross-Domain Identity Management per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni](active-directory-scim-provisioning.md).

Per contattare il team di progettazione di Azure AD e richiedere supporto per il provisioning di applicazioni aggiuntive, inviare un messaggio tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> Per supportare il provisioning utenti automatico, un'applicazione deve prima di tutto includere le API di gestione utenti necessarie che consentono ai programmi esterni di automatizzare la creazione, la manutenzione e la rimozione degli utenti. Pertanto, non tutte le app SaaS sono compatibili con questa funzionalità. Per le app che supportano le API di gestione utenti, il team di progettazione di Azure AD potrà creare un connettore di provisioning e l'ordine di priorità di questa operazione è stabilito in base alle esigenze dei clienti attuali e potenziali. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

La configurazione del servizio di provisioning di Azure AD per un'applicazione selezionata viene avviata nel **[portale di Azure](https://portal.azure.com)**. Nella sezione **Azure Active Directory > Applicazioni aziendali** selezionare **Aggiungi**, quindi scegliere **Tutti** e aggiungere quanto segue a seconda dello scenario:

* Tutte le applicazioni nella sezione **Applicazioni in primo piano** supportano il provisioning automatico. Per eventuali altre applicazioni, vedere l'[elenco delle esercitazioni sulle applicazioni per il provisioning utenti]active-directory-saas-tutorial-list.md).

* Usare l'opzione "Applicazione non nella raccolta" per le integrazioni SCIM sviluppate in modo personalizzato

![Gallery](./media/active-directory-saas-app-provisioning/gallery.png)

Il provisioning è configurato nella scheda **Provisioning** della schermata di gestione delle applicazioni.

![Impostazioni](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* È necessario specificare le **credenziali amministratore** al servizio di provisioning di Azure AD per permettergli di connettersi all'API di gestione utenti indicata dall'applicazione. Questa sezione consente anche di abilitare l'invio di notifiche di posta elettronica se le credenziali non risultano corrette o il processo di provisioning entra in [quarantena](#quarantine).

* È possibile configurare **mapping degli attributi** che consentono di specificare per quali campi del sistema di origine, ad esempio Azure AD, verrà eseguita la sincronizzazioni del contenuto con i campi corrispondenti del sistema di destinazione, ad esempio ServiceNow. Se l'applicazione di destinazione supporta tale possibilità, questa sezione consente anche di configurare il provisioning dei gruppi, oltre che degli account utente. Le proprietà corrispondenti permettono di selezionare i campi usati per associare gli account tra i sistemi. Le [espressioni](active-directory-saas-writing-expressions-for-attribute-mappings.md) permettono di modificare e trasformare i valori recuperati dal sistema di origine prima che vengano scritti nel sistema di destinazione. Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md).

![Impostazioni](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* I **filtri di ambito** indicano al servizio di provisioning gli utenti e il gruppo nel sistema di origine di cui effettuare il provisioning e/o il deprovisioning nel sistema di destinazione. I filtri di ambito presentano due aspetti che vengono valutati insieme e determinano l'inclusione nell'ambito del provisioning:

    * **Filtro in base ai valori di attributo**: il menu "Ambito dell'oggetto di origine" nei mapping degli attributi consente di filtrare valori di attributo specifici. Ad esempio, è possibile specificare che solo gli utenti con un attributo "Department" di "Sales" devono essere inclusi nell'ambito del provisioning. Per altre informazioni, vedere [Uso di filtri per la definizione dell'ambito](active-directory-saas-scoping-filters.md).

    * **Filtro in base alle assegnazioni**: il menu "Ambito" nella sezione Provisioning &gt; Impostazioni del portale consente di specificare se nell'ambito del provisioning devono essere inclusi solo gli utenti e gruppi "assegnati" o se va effettuato il provisioning di tutti gli utenti nella directory di Azure AD. Per informazioni sull'assegnazione di utenti e gruppi, vedere [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* Le **impostazioni** permettono di controllare il funzionamento del servizio di provisioning per un'applicazione, incluso lo stato di esecuzione o meno.

* I **log di controllo** restituiscono i record di ogni operazione eseguita dal servizio di provisioning di Azure AD. Per altre informazioni, vedere la [guida alla creazione di report sul provisioning](active-directory-saas-provisioning-reporting.md).

![Impostazioni](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> Il servizio di provisioning utenti di Azure AD può essere configurato e gestito anche con l'[API di Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Cosa accade durante il provisioning?

Quando Azure AD è il sistema di origine, il servizio di provisioning usa la [funzionalità di query differenziale dell'API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) per monitorare gli utenti e i gruppi. Il servizio di provisioning esegue una sincronizzazione iniziale rispetto al sistema di origine e a quello di destinazione, seguita da sincronizzazioni incrementali periodiche. 

### <a name="initial-sync"></a>Sincronizzazione iniziale
Quando viene avviato il servizio di provisioning, la prima sincronizzazione consiste nelle operazioni seguenti:

1. Il servizio esegue una query su tutti gli utenti e i gruppi presenti nel sistema di origine e recupera tutti gli attributi definiti nei [mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md).
2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](active-directory-coreapps-assign-user-azure-portal.md) configurate o [filtri di ambito basati su attributi](active-directory-saas-scoping-filters.md).
3. Quando viene trovato un utente da assegnare o incluso nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per individuare un utente corrispondente usando gli [attributi di corrispondenza](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) designati. Se ad esempio il nome userPrincipal nel sistema di origine è un attributo di corrispondenza ed è mappato a userName nel sistema di destinazione, il servizio di provisioning esegue una query sul sistema di destinazione per trovare i valori di userName che corrispondono ai valori del nome userPrincipal nel sistema di origine.
4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine.
5. Se invece viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine.
6. Se i mapping degli attributi contengono attributi "di riferimento", il servizio esegue altri aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui viene fatto riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
7. Al termine della sincronizzazione iniziale, il servizio salva in modo permanente una filigrana che fornisce il punto di partenza per le successive sincronizzazioni incrementali.

Alcune applicazioni come ServiceNow, Google Apps e Box supportano non solo il provisioning degli utenti ma anche quello dei gruppi e dei relativi membri. In questi casi, se nei [mapping](active-directory-saas-customizing-attribute-mappings.md) è abilitato il provisioning di gruppi, il servizio di provisioning sincronizza sia gli utenti che i gruppi e successivamente anche le appartenenze ai gruppi. 

### <a name="incremental-syncs"></a>Sincronizzazioni incrementali
Dopo la sincronizzazione iniziale, tutte le sincronizzazioni successive consistono nelle operazioni seguenti:

1. Il servizio esegue una query sul sistema di origine per trovare gli utenti e i gruppi che sono stati aggiornati dopo il salvataggio dell'ultima filigrana.
2. Il servizio filtra gli utenti e i gruppi restituiti, usando [assegnazioni](active-directory-coreapps-assign-user-azure-portal.md) configurate o [filtri di ambito basati su attributi](active-directory-saas-scoping-filters.md).
3. Quando viene trovato un utente da assegnare o incluso nell'ambito per il provisioning, il servizio esegue una query sul sistema di destinazione per individuare un utente corrispondente usando gli [attributi di corrispondenza](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) designati.
4. Se nel sistema di destinazione non viene trovato un utente corrispondente, questo viene creato usando gli attributi restituiti dal sistema di origine.
5. Se invece viene trovato un utente corrispondente, questo viene aggiornato usando gli attributi forniti dal sistema di origine.
6. Se i mapping degli attributi contengono attributi "di riferimento", il servizio esegue altri aggiornamenti nel sistema di destinazione per creare e collegare gli oggetti a cui viene fatto riferimento. È ad esempio possibile che nel sistema di destinazione un utente abbia un attributo "Manager" collegato a un altro utente creato nel sistema di destinazione.
7. Se un utente precedentemente incluso nell'ambito per il provisioning viene rimosso dall'ambito e la relativa assegnazione viene annullata, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
8. Se un utente precedentemente incluso nell'ambito per il provisioning viene disabilitato o eliminato temporaneamente nel sistema di origine, il servizio disabilita l'utente nel sistema di destinazione eseguendo un aggiornamento.
9. Se un utente precedentemente incluso nell'ambito per il provisioning viene eliminato definitivamente nel sistema di origine, il servizio elimina l'utente nel sistema di destinazione. In Azure AD gli utenti vengono eliminati definitivamente dopo che sono trascorsi 30 giorni dall'eliminazione temporanea.
10. Alla fine di una sincronizzazione incrementale, il servizio salva in modo permanente una nuova filigrana che fornisce il punto di partenza per le successive sincronizzazioni incrementali.

>[!NOTE]
> È possibile disabilitare le operazioni di creazione, aggiornamento o eliminazione usando le caselle di controllo **Azioni oggetto di destinazione** nella sezione [Mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md). La logica per disabilitare un utente durante un aggiornamento viene controllata anche tramite un mapping di attributi da un campo come "accountEnabled".

Il servizio di provisioning continua a eseguire sincronizzazioni incrementali back-to-back all'infinito, in base agli intervalli definiti nell'[esercitazione specifica di ogni applicazione](active-directory-saas-tutorial-list.md), finché non si verifica uno degli eventi seguenti:

* Il servizio viene arrestato manualmente tramite il portale di Azure o con il comando appropriato dell'API Graph. 
* Viene attivata una nuova sincronizzazione iniziale tramite l'opzione **Cancella lo stato corrente e riavvia la sincronizzazione** nel portale di Azure o con il comando appropriato dell'API Graph. Per effetto di questa operazione, le eventuali filigrane salvate vengono cancellate e tutti gli oggetti di origine vengono nuovamente valutati.
* Viene attivata una nuova sincronizzazione iniziale a causa di una modifica nel mapping degli attributi o nei filtri di ambito. Anche per effetto di questa operazione, le eventuali filigrane salvate vengono cancellate e tutti gli oggetti di origine vengono nuovamente valutati.
* Il processo di provisioning entra in quarantena (vedere sotto) a causa di errori molto frequenti e resta in quarantena per più di quattro settimane. In questo caso, il servizio viene disabilitato automaticamente.

### <a name="errors-and-retries"></a>Errori e ripetizione di tentativi 
Se non è possibile aggiungere, aggiornare o eliminare un singolo utente nel sistema di destinazione a causa di un errore in tale sistema, il tentativo di eseguire questa operazione viene ripetuto nel ciclo di sincronizzazione successivo. Se l'errore si verifica nuovamente, la frequenza dei tentativi viene gradualmente ridotta fino a un solo tentativo al giorno. Per risolvere l'errore, gli amministratori devono verificare gli eventi di "deposito dei processi" nei [log di controllo](active-directory-saas-provisioning-reporting.md) per determinare la causa all'origine del problema e intraprendere l'azione appropriata. Tra gli errori comuni possono essere inclusi i seguenti:

* Per gli utenti nel sistema di origine non è definito un valore di attributo che è necessario nel sistema di destinazione.
* Per gli utenti nel sistema di origine è definito un valore di attributo per cui è impostato un vincolo univoco nel sistema di destinazione e lo stesso valore è presente in un altro record utente.

Questi errori possono essere risolti modificando i valori di attributo per l'utente interessato nel sistema di origine o modificando i mapping degli attributi in modo da non causare conflitti.   

### <a name="quarantine"></a>Quarantena
Se la maggior parte o tutte le chiamate al sistema di destinazione non riuscono a causa di un errore (ad esempio nel caso di credenziali di amministratore non valide), il processo di provisioning passa allo stato di "quarantena". Questa condizione viene indicata nel [report di riepilogo sul provisioning](active-directory-saas-provisioning-reporting.md) e tramite posta elettronica, se nel portale di Azure sono state configurate le notifiche di posta elettronica. 

In stato di quarantena, la frequenza delle sincronizzazioni incrementali viene gradualmente ridotta fino a diventare giornaliera. 

Dopo che tutti gli errori sono stati risolti, il processo di provisioning viene rimosso dalla quarantena e viene avviato il ciclo di sincronizzazione successivo. Se lo stato di quarantena dura per più di quattro settimane, il processo di provisioning viene disabilitato.


## <a name="frequently-asked-questions"></a>Domande frequenti

**Quanto tempo sarà necessario eseguire il provisioning degli utenti?**

Il livello delle prestazioni varia a seconda che la sincronizzazione eseguita dal processo di provisioning sia iniziale o incrementale.

Per le sincronizzazioni iniziali, il tempo di completamento dipende direttamente dal numero di utenti, gruppi e membri di gruppo presenti nel sistema di origine. I sistemi di origine di dimensioni molto ridotte con un numero di oggetti nell'ordine delle centinaia possono completare la sincronizzazione iniziale in pochi minuti. Al contrario, i sistemi di origine con centinaia di migliaia o milioni di oggetti combinati posso richiedere un tempo molto lungo.

Per le sincronizzazioni incrementali, il tempo necessario dipende dal numero di modifiche rilevate in un ciclo di sincronizzazione specifico. Se vengono rilevate meno di 5000 modifiche relative a utenti o membri di gruppi, la sincronizzazione può essere in genere completata entro 40 minuti. 

Si noti che le prestazioni complessive dipendono da entrambi i sistemi di origine e di destinazione. Alcuni sistemi di destinazione implementano limitazioni relative al numero e alla frequenza delle richieste, che possono influire negativamente sulle prestazioni durante la sincronizzazione di grandi quantità di dati, e i connettori di provisioning predefiniti di Azure AD per tali sistemi tengono conto di queste limitazioni.

Le prestazioni risultano ridotte anche se sono presenti troppi errori, registrati nei [log di controllo](active-directory-saas-provisioning-reporting.md), e il servizio di provisioning è in stato di quarantena.

**Come si possono migliorare le prestazioni di sincronizzazione?**

La maggior parte dei problemi di prestazioni si verifica durante la sincronizzazione iniziale di sistemi che hanno un numero elevato di gruppi e membri di gruppo.

Se non è necessario eseguire la sincronizzazione di gruppi o appartenenze a gruppi, è possibile migliorare le prestazioni di sincronizzazione in maniera significativa eseguendo queste operazioni:

1. Impostare il menu **Provisioning > Impostazioni > Ambito** su **Sync all** (Sincronizza tutti), anziché limitare la sincronizzazione agli utenti e ai gruppi assegnati.
2. Usare i [filtri di ambito](active-directory-saas-scoping-filters.md) anziché le assegnazioni per filtrare l'elenco di utenti di cui viene eseguito il provisioning.

> [!NOTE]
> Per le applicazioni che supportano il provisioning dei nomi e delle proprietà dei gruppi, come ServiceNow e Google Apps, la disabilitazione del provisioning di questi dati consente di ridurre il tempo necessario per completare una sincronizzazione iniziale. Se non si vuole effettuare il provisioning di membri e nomi di gruppo nell'applicazione, è possibile disabilitare questa opzione nei [mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md) della configurazione di provisioning.

**Come è possibile monitorare lo stato del processo di provisioning corrente?**

Vedere la [guida alla creazione di report sul provisioning](active-directory-saas-provisioning-reporting.md).

**Come si può stabilire se il provisioning utenti non è stato eseguito correttamente?**

Tutti gli errori vengono registrati nei log di controllo di Azure AD. Per altre informazioni, vedere la [guida alla creazione di report sul provisioning](active-directory-saas-provisioning-reporting.md).

**Come si può creare un'applicazione che funziona con il servizio di provisioning?**

Vedere [Uso di System for Cross-Domain Identity Management per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**È possibile inviare commenti e suggerimenti al team di progettazione?**

È possibile contattare Microsoft tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Articoli correlati
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](active-directory-saas-customizing-attribute-mappings.md)
* [Scrittura di espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Ambito dei filtri per il Provisioning utente](active-directory-saas-scoping-filters.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
* [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)

