---
title: Provisioning utenti automatizzato per app SaaS in Azure AD | Documentazione Microsoft
description: "Introduzione all&quot;uso di Azure AD per eseguire automaticamente il provisioning, il deprovisioning e l&quot;aggiornamento continuo degli account utente in più applicazioni SaaS di terze parti."
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
ms.date: 02/09/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 1bee9573e23e1c814626d19a3df1eb7ab12486ab


---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Informazioni sul provisioning utenti automatizzato per app SaaS
Azure Active Directory (Azure AD) consente di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), ad esempio Dropbox, Salesforce, ServiceNow e così via.

**Di seguito sono riportati alcuni esempi di operazioni che questa funzionalità consente di eseguire:**

* Creare automaticamente nuovi account nell'app SaaS appropriata per nuovi membri del team.
* Disattivare automaticamente degli account dalle app SaaS quando i membri lasciano il team.
* Assicurarsi che le identità nelle app SaaS siano sempre aggiornate in base alle modifiche nella directory.
* Eseguire il provisioning di oggetti non utente, ad esempio di gruppi, in app SaaS che li supportano.

**Il provisioning utenti automatico include anche le funzionalità seguenti:**

* La possibilità di ottenere una corrispondenza tra identità esistenti in Azure AD e in app SaaS.
* Opzioni di personalizzazione per adattare Azure AD alle configurazioni correnti delle app SaaS attualmente in uso nell'organizzazione.
* Avvisi di posta elettronica facoltativi per errori di provisioning.
* Log di report e attività per facilitare il monitoraggio e la risoluzione dei problemi.

## <a name="why-use-automated-provisioning"></a>Perché usare il provisioning automatico?
Di seguito sono riportate alcune motivazioni comuni per l'uso di questa funzionalità:

* Per evitare i costi, le inefficienze e gli errori umani associati ai processi di provisioning manuale.
* Per proteggere l'organizzazione, rimuovendo immediatamente dalle app SaaS principali le identità degli utenti che lasciano l'organizzazione.
* Per importare facilmente un blocco di utenti in una determinata applicazione SaaS.
* Per sfruttare la praticità dell'uso da parte della soluzione di provisioning degli stessi criteri di accesso alle app definiti per Single Sign-On di Azure AD.

## <a name="frequently-asked-questions"></a>Domande frequenti
**Con quale frequenza Azure AD scrive modifiche della directory nell'app SaaS?**

Azure AD verifica la presenza di modifiche ogni cinque o dieci minuti. Se l'app SaaS restituisce diversi errori (ad esempio in caso di credenziali di amministratore non valide), Azure AD rallenterà gradualmente la frequenza fino a un massimo di una volta al giorno, finché non vengono corretti gli errori.

**Quanto tempo sarà necessario eseguire il provisioning degli utenti?**

Le modifiche incrementali si verificano quasi immediatamente, ma se si sta tentando di eseguire il provisioning di gran parte delle directory, il tempo necessario dipende dal numero di utenti e gruppi presenti. Per le directory di piccole dimensioni sono necessari solo pochi minuti, per le directory di medie dimensioni alcuni minuti e per le directory di grandi dimensioni potrebbero essere necessarie diverse ore.

**Come è possibile monitorare lo stato del processo di provisioning corrente?**

È possibile esaminare il rapporto di provisioning dell'account nella sezione Report della directory. Un'altra opzione consiste nel visitare la scheda Dashboard per l'applicazione SaaS in cui si sta eseguendo il provisioning e cercare nella sezione "Stato integrazione" nella parte inferiore della pagina.

**Come si può stabilire se il provisioning utenti non è stato eseguito correttamente?**

Al termine della configurazione guidata del provisioning viene visualizzata un'opzione per effettuare la sottoscrizione alle notifiche di posta elettronica per gli errori di provisioning. È inoltre possibile controllare il report degli errori di provisioning per verificare per quali utenti il provisioning non è riuscito e il motivo dell'errore.

**AD Azure può riscrivere le modifiche dell'app SaaS nella directory?**

Per la maggior parte delle app SaaS il provisioning è solo in uscita, pertanto gli utenti vengono scritti dalla directory nell'applicazione e le modifiche apportate nell'applicazione non possono essere riscritte nella directory. Per [Workday](https://msdn.microsoft.com/library/azure/dn762434.aspx), tuttavia, il provisioning è solo in ingresso, pertanto gli utenti vengono importati nella directory da Workday e le modifiche nella directory non vengono riscritte in Workday.

**È possibile inviare commenti e suggerimenti al team di progettazione?**

Contattare Microsoft tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="how-does-automated-provisioning-work"></a>Come funziona il provisioning automatizzato?
Azure AD esegue il provisioning degli utenti nelle app SaaS connettendosi agli endpoint di provisioning forniti dal produttore dell'applicazione. Questi endpoint consentono ad Azure AD di creare, aggiornare e rimuovere gli utenti a livello di programmazione. Di seguito è riportata una breve panoramica dei vari passaggi eseguiti da Azure AD per automatizzare il provisioning.

1. Quando si abilita per la prima volta il provisioning per un'applicazione, vengono eseguite le operazioni seguenti:
   * Azure AD tenterà di ottenere una corrispondenza tra gli utenti esistenti nell'app SaaS e le identità corrispondenti nella directory. Quando c’è una corrispondenza per un utente è una corrispondenza, *non* è automaticamente abilitato per il servizio di single sign-on. Affinché un utente possa accedere all'applicazione, deve essere esplicitamente assegnato all'applicazione in Azure AD, direttamente o tramite l'appartenenza al gruppo.
   * Se si è già specificato quali utenti devono essere assegnati all'applicazione e Azure AD non riesce a trovare account esistenti per tali utenti, Azure AD eseguirà il provisioning di nuovi account nell'applicazione.
2. Dopo aver completato la sincronizzazione iniziale come descritto in precedenza, Azure AD verificherà ogni 10 minuti la presenza delle modifiche seguenti:
   * Se sono stati assegnati nuovi utenti all'applicazione, direttamente o tramite l'appartenenza al gruppo, ne verrà eseguito il provisioning in un nuovo account nell'app SaaS.
   * Se l'accesso di un utente è stato rimosso, il relativo account nell'app SaaS verrà contrassegnato come disabilitato. Gli utenti infatti non vengono mai completamente rimossi, per evitare la perdita dei dati in caso di configurazione errata.
   * Se un utente è stato recentemente assegnato all'applicazione e dispone già di un account nell'app SaaS, tale account verrà contrassegnato come abilitato e alcune proprietà utente possono essere aggiornate se obsolete rispetto alla directory.
   * Se sono state modificate nella directory le informazioni relative a un utente, ad esempio il numero di telefono, l'indirizzo dell'ufficio e così via, queste informazioni verranno aggiornate anche nell'applicazione SaaS.

Per altre informazioni sul mapping degli attributi tra Azure AD e l'app SaaS, vedere l'articolo [Personalizzazione dei mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md).

## <a name="list-of-apps-that-support-automated-user-provisioning"></a>Elenco di applicazioni che supportano il provisioning utenti automatizzato
Fare clic su un'app per visualizzare un'esercitazione sulla configurazione del provisioning automatizzato per tale app:

* [Box](http://go.microsoft.com/fwlink/?LinkId=286016)
* [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
* [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
* [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
* [Dropbox for Business](http://go.microsoft.com/fwlink/?LinkId=309581)
* [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
* [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
* [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
* [Sandbox Salesforce](http://go.microsoft.com/fwlink/?LinkId=327869)
* [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
* [Workday](http://go.microsoft.com/fwlink/?LinkId=690250) (provisioning in ingresso)

Affinché un'applicazione supporti il provisioning utenti automatizzato, deve innanzitutto fornire gli endpoint necessari che consentono ai programmi esterni di automatizzare la creazione, la manutenzione e la rimozione degli utenti. Pertanto, non tutte le app SaaS sono compatibili con questa funzionalità. Per le app che supportano questa funzionalità, il team di progettazione di Azure AD potrà creare un connettore di provisioning e l'ordine di priorità di questa operazione è stabilito in base alle esigenze dei clienti attuali e potenziali.

Per contattare il team di progettazione di Azure AD per richiedere supporto sul provisioning di applicazioni aggiuntive, inviare un messaggio tramite il [forum dei commenti di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](active-directory-saas-customizing-attribute-mappings.md)
* [Scrittura di espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Ambito dei filtri per il Provisioning utente](active-directory-saas-scoping-filters.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
* [Notifiche relative al provisioning dell'account](active-directory-saas-account-provisioning-notifications.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)




<!--HONumber=Feb17_HO2-->


