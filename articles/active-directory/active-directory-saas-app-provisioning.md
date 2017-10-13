---
title: Provisioning di utenti automatizzato per app SaaS in Azure AD | Microsoft Docs
description: "Introduzione all'uso di Azure AD per eseguire automaticamente il provisioning, il deprovisioning e l'aggiornamento continuo degli account utente in più applicazioni SaaS di terze parti."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 3fe57e9c22d04a3557978093ce3fe86613c5c1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
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
* Opzioni di personalizzazione per adattare Azure AD alle configurazioni correnti delle app e dei sistemi attualmente usati dall'organizzazione.
* Avvisi di posta elettronica facoltativi per errori di provisioning.
* Log di report e attività per facilitare il monitoraggio e la risoluzione dei problemi.

## <a name="why-use-automated-provisioning"></a>Perché usare il provisioning automatico?
Di seguito sono riportate alcune motivazioni comuni per l'uso di questa funzionalità:

* Per evitare i costi, le inefficienze e gli errori umani associati ai processi di provisioning manuale.
* Per evitare i costi associati all'hosting e alla gestione di script e soluzioni di provisioning sviluppate in modo personalizzato.
* Per proteggere l'organizzazione, rimuovendo immediatamente dalle app SaaS principali le identità degli utenti che lasciano l'organizzazione.
* Per importare facilmente in blocco un certo numero di utenti in un'applicazione o un sistema specifico.
* Per sfruttare la praticità dell'uso da parte della soluzione di provisioning degli stessi criteri di accesso alle app definiti per Single Sign-On di Azure AD.


## <a name="how-does-automatic-provisioning-work"></a>Come funziona il provisioning automatico?
    
Il **servizio di provisioning di Azure AD** effettua il provisioning degli utenti nelle app SaaS e in altri sistemi connettendosi agli endpoint dell'API di gestione utenti specificati dai fornitori di ogni applicazione. Gli endpoint dell'API di gestione utenti consentono ad Azure AD di creare, aggiornare e rimuovere utenti a livello di codice. Per alcune applicazioni, il servizio di provisioning può anche creare, aggiornare e rimuovere oggetti aggiuntivi relative alle identità, ad esempio i gruppi e i ruoli. 

![Provisioning](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figura 1: Provisioning del servizio di Azure AD*

![Provisioning in uscita](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figura 2: Flusso di lavoro di provisioning degli utenti "in uscita" da Azure AD verso applicazioni SaaS comuni*

![Provisioning in ingresso](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figura 3: Flusso di lavoro di provisioning degli utenti "in ingresso" da applicazioni di gestione delle risorse umane (HCM) comuni verso Azure Active Directory e Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quali applicazioni e sistemi è possibile usare con il provisioning utenti automatico di Azure AD?

Azure AD offre il supporto pre-integrato per un'ampia gamma di app SaaS e sistemi di risorse umane comuni, nonché il supporto generico per le app che implementano parti specifiche dello [standard SCIM 2.0](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-scim-provisioning).

Tutte le app "In primo piano" nella raccolta di applicazioni di Azure AD supportano il provisioning utenti automatizzato. L'elenco delle app in primo piano è disponibile [qui](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Perché un'applicazione supporti il provisioning utenti automatico, deve prima di tutto includere gli endpoint di gestione utenti necessari che consentono ai programmi esterni di automatizzare la creazione, la manutenzione e la rimozione degli utenti. Pertanto, non tutte le app SaaS sono compatibili con questa funzionalità. Per le app che supportano le API di gestione utenti, il team di progettazione di Azure AD potrà creare un connettore di provisioning e l'ordine di priorità di questa operazione è stabilito in base alle esigenze dei clienti attuali e potenziali. 

Per contattare il team di progettazione di Azure AD e richiedere supporto per il provisioning di applicazioni aggiuntive, inviare un messaggio tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning). 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

La configurazione del servizio di provisioning di Azure AD per un'applicazione selezionata viene avviata nel **[portale di Azure](https://potal.azure.com)**. Nella sezione **Azure Active Directory > Applicazioni aziendali** selezionare **Aggiungi**, quindi scegliere **Tutti** e aggiungere quanto segue a seconda dello scenario:

* Tutte le applicazioni nella sezione **Applicazioni in primo piano** supportano il provisioning automatico

* Usare l'opzione "Applicazione non nella raccolta" per le integrazioni SCIM sviluppate in modo personalizzato

![Gallery](./media/active-directory-saas-app-provisioning/gallery.png)

Il provisioning è configurato nella scheda **Provisioning** della schermata di gestione delle applicazioni.

![Impostazioni](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* È necessario specificare le **credenziali amministratore** al servizio di provisioning di Azure AD per permettergli di connettersi all'API di gestione utenti indicata dall'applicazione.

* È possibile configurare **mapping degli attributi** che consentono di specificare per quali campi del sistema di origine, ad esempio Azure AD, verrà eseguita la sincronizzazioni del contenuto con i campi corrispondenti del sistema di destinazione, ad esempio ServiceNow. Se l'applicazione di destinazione supporta tale possibilità, questa sezione consente anche di configurare il provisioning dei gruppi, oltre che degli account utente. Le proprietà corrispondenti permettono di selezionare i campi usati per associare gli account tra i sistemi. Le [espressioni](active-directory-saas-writing-expressions-for-attribute-mappings.md) permettono di modificare e trasformare i valori recuperati dal sistema di origine prima che vengano scritti nel sistema di destinazione. Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md).

![Impostazioni](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* I **filtri di ambito** indicano al servizio di provisioning gli utenti e il gruppo nel sistema di origine di cui effettuare il provisioning e/o il deprovisioning nel sistema di destinazione. I filtri di ambito presentano due aspetti che vengono valutati insieme e determinano l'inclusione nell'ambito del provisioning:

* **Filtro in base ai valori di attributo**: il menu "Ambito dell'oggetto di origine" nei mapping degli attributi consente di filtrare valori di attributo specifici. Ad esempio, è possibile specificare che solo gli utenti con un attributo "Department" di "Sales" devono essere inclusi nell'ambito del provisioning.

* **Filtro in base alle assegnazioni**: il menu "Ambito" nella sezione Provisioning &gt; Impostazioni del portale consente di specificare se nell'ambito del provisioning devono essere inclusi solo gli utenti e gruppi "assegnati" o se va effettuato il provisioning di tutti gli utenti nella directory di Azure AD. Per informazioni sull'assegnazione di utenti e gruppi, vedere [Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* Le **impostazioni** permettono di controllare il funzionamento del servizio di provisioning per un'applicazione, incluso lo stato di esecuzione o meno.

* I **log di controllo** restituiscono i record di ogni operazione eseguita dal servizio di provisioning di Azure AD. Per altre informazioni, vedere la [guida alla creazione di report sul provisioning](active-directory-saas-provisioning-reporting.md).

![Impostazioni](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>Cosa accade durante il provisioning?

1. Quando si abilita per la prima volta il provisioning per un'applicazione, vengono eseguite le operazioni seguenti:
   * Azure AD tenterà di ottenere una corrispondenza tra gli utenti esistenti nell'app SaaS e le identità corrispondenti nella directory. Quando c’è una corrispondenza per un utente è una corrispondenza, *non* è automaticamente abilitato per il servizio di single sign-on. Affinché un utente possa accedere all'applicazione, deve essere esplicitamente assegnato all'applicazione in Azure AD, direttamente o tramite l'appartenenza al gruppo.
   * Se si è già specificato quali utenti devono essere assegnati all'applicazione e Azure AD non riesce a trovare account esistenti per tali utenti, Azure AD eseguirà il provisioning di nuovi account nell'applicazione.
2. Dopo aver completato la sincronizzazione iniziale come descritto in precedenza, Azure AD verificherà ogni 20 minuti la presenza delle modifiche seguenti:
   * Se sono stati assegnati nuovi utenti all'applicazione, direttamente o tramite l'appartenenza al gruppo, viene effettuato il provisioning di un nuovo account nell'app SaaS.
   * Se l'accesso di un utente è stato rimosso, il relativo account nell'app SaaS viene contrassegnato come disabilitato. Gli utenti infatti non vengono mai completamente rimossi, per evitare la perdita dei dati in caso di configurazione errata.
   * Se un utente è stato recentemente assegnato all'applicazione e dispone già di un account nell'app SaaS, tale account viene contrassegnato come abilitato e alcune proprietà utente possono essere aggiornate se obsolete rispetto alla directory.
   * Se sono state modificate nella directory le informazioni relative a un utente, ad esempio il numero di telefono o l'indirizzo dell'ufficio, tali informazioni vengono aggiornate anche nell'app SaaS.


## <a name="frequently-asked-questions"></a>Domande frequenti
**Con quale frequenza Azure AD scrive modifiche della directory nell'app SaaS?**

Al termine della sincronizzazione iniziale completa, in genere il servizio di provisioning di Azure AD controlla la presenza di modifiche ogni 20 minuti. 

Se l'app SaaS restituisce diversi errori (ad esempio in caso di credenziali di amministratore non valide), Azure AD rallenterà gradualmente la frequenza fino a un massimo di una volta al giorno, finché non vengono corretti gli errori. In tal caso, il processo di provisioning di Azure AD passa a uno stato di quarantena, indicato nel [report di riepilogo del provisioning](active-directory-saas-provisioning-reporting.md).

**Quanto tempo sarà necessario eseguire il provisioning degli utenti?**

Al termine della sincronizzazione iniziale completa, in genere le modifiche incrementali vengono applicate entro 20-40 minuti. Se si prova a effettuare il provisioning di gran parte della directory, il tempo necessario dipende dal numero di utenti e gruppi presenti. Le prestazioni dipendono dalle prestazioni dell'API di gestione utenti usata dal servizio di provisioning per leggere i dati dal sistema di origine e scriverli nel sistema di destinazione. 

Le prestazioni risultano ridotte anche se sono presenti troppi errori, registrati nei [log di controllo](active-directory-saas-provisioning-reporting.md), e il servizio di provisioning è in stato di quarantena.

**Che cos'è una sincronizzazione iniziale completa e perché richiede più tempo delle sincronizzazioni successive?**

La prima volta che viene eseguito il servizio di provisioning di Azure AD per un'app specifica, viene eseguito uno snapshot degli utenti e, facoltativamente, dei gruppi nella directory di origine. Questo snapshot permette al servizio di provisioning di ridurre il numero di round trip alle API di origine e destinazione e consente una maggiore efficienza delle successive sincronizzazioni differenziali. 

La sincronizzazione iniziale completa degli utenti richiede spesso pochi minuti per le directory molto piccole, ma può richiedere diverse ore per le directory di dimensioni maggiori. Per la sincronizzazione iniziale completa delle directory aziendali con centinaia di migliaia di utenti possono essere necessarie diverse ore. Tuttavia, dopo la sincronizzazione iniziale, le successive sincronizzazioni differenziali vengono eseguite molto più rapidamente.

> [!NOTE]
> Per le applicazioni che supportano il provisioning di gruppi e l'appartenenza a gruppi, abilitando questa opzione si aumenta notevolmente il tempo necessario per una sincronizzazione completa. Se non si vuole effettuare il provisioning di appartenenze e nomi di gruppo nell'applicazione, è possibile disabilitare questa opzione nei [mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md) della configurazione di provisioning.

**Come è possibile monitorare lo stato del processo di provisioning corrente?**

Vedere la [guida alla creazione di report sul provisioning](active-directory-saas-provisioning-reporting.md).

**Come si può stabilire se il provisioning utenti non è stato eseguito correttamente?**

Tutti gli errori vengono registrati nei log di controllo di Azure AD. Per altre informazioni, vedere la [guida alla creazione di report sul provisioning](active-directory-saas-provisioning-reporting.md).

**È possibile inviare commenti e suggerimenti al team di progettazione?**

È possibile contattare Microsoft tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](active-directory-saas-customizing-attribute-mappings.md)
* [Scrittura di espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Ambito dei filtri per il Provisioning utente](active-directory-saas-scoping-filters.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
* [Notifiche relative al provisioning dell'account](active-directory-saas-account-provisioning-notifications.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)

