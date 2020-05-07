---
title: Provisioning utenti app SaaS automatizzato in Azure AD
description: Introduzione all'uso di Azure AD per eseguire automaticamente il provisioning, il deprovisioning e l'aggiornamento continuo degli account utente in più applicazioni SaaS di terze parti.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh, celested
ms.openlocfilehash: 1e72d885858b543999090a4a0521845d556802fd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593115"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-ad"></a>Automatizzare il provisioning e il deprovisioning utenti in applicazioni con Azure AD

In Azure Active Directory (Azure AD), il termine **provisioning app** si riferisce alla creazione automatica di identità e ruoli utente nelle applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano. Gli scenari comuni includono il provisioning di un utente di Azure AD in applicazioni come [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) e altre ancora.

![Diagramma della panoramica del provisioning](./media/user-provisioning/provisioning-overview.png)

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

* **Applicazioni preintegrate (app SaaS della raccolta)**. È possibile trovare tutte le applicazioni per le quali Azure AD supporta un connettore di provisioning pre-integrato nell' [elenco delle esercitazioni sulle applicazioni per il provisioning degli utenti](../saas-apps/tutorial-list.md). Le applicazioni preintegrate elencate nella raccolta usano in genere le API di gestione utenti basate su SCIM 2,0 per il provisioning. 

   ![Logo Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Se si vuole richiedere una nuova applicazione per il provisioning, è possibile [richiedere che l'applicazione sia integrata con la raccolta di app](../develop/howto-app-gallery-listing.md). Per una richiesta di provisioning dell'utente, è necessario che l'applicazione disponga di un endpoint conforme a SCIM. Chiedere al fornitore dell'applicazione di seguire lo standard SCIM per poter caricare rapidamente l'app sulla piattaforma.

* **Applicazioni che supportano SCIM 2,0**. Per informazioni su come connettere in modo generico le applicazioni che implementano le API di gestione utenti basate su SCIM 2,0, vedere [creare un endpoint scim e configurare il provisioning utenti](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Che cos'è il sistema per la gestione delle identità tra domini (SCIM)?

Per automatizzare il provisioning e il deprovisioning, le app espongono API di utenti e gruppi proprietarie. Tuttavia, chiunque abbia provato a gestire gli utenti in più app indica che ogni app tenta di eseguire le stesse azioni semplici, ad esempio la creazione o l'aggiornamento degli utenti, l'aggiunta di utenti a gruppi o il deprovisioning degli utenti. Tuttavia, tutte queste semplici azioni vengono implementate in modo leggermente diverso, usando percorsi di endpoint diversi, metodi diversi per specificare le informazioni utente e uno schema diverso per rappresentare ogni elemento di informazioni.

Per risolvere questi problemi, la specifica SCIM fornisce uno schema utente comune che consente agli utenti di spostarsi in, da e verso le app. SCIM sta diventando lo standard de facto per il provisioning e, se usato in combinazione con gli standard di federazione come SAML o OpenID Connect, fornisce agli amministratori una soluzione basata su standard end-to-end per la gestione degli accessi.

Per istruzioni dettagliate sullo sviluppo di un endpoint SCIM per automatizzare il provisioning e il deprovisioning di utenti e gruppi in un'applicazione, vedere [creare un endpoint scim e configurare il provisioning degli](use-scim-to-provision-users-and-groups.md)utenti. Per le applicazioni preintegrate nella raccolta (Slack, Azure Databricks, fiocco di neve e così via), è possibile ignorare la documentazione per gli sviluppatori e usare le esercitazioni fornite [qui](../saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Provisioning manuale o automatico

Le applicazioni nella raccolta Azure AD supportano una delle due modalità di provisioning:

* Il provisioning **manuale** significa che non è ancora disponibile un connettore di provisioning automatico Azure ad per l'app. Gli account utente devono essere creati manualmente, ad esempio tramite l'aggiunta di utenti direttamente nel portale di amministrazione dell'app o il caricamento di un foglio di calcolo con i dettagli dell'account utente. Consultare la documentazione fornita dall'app o contattare lo sviluppatore dell'app per determinare quali meccanismi sono disponibili.

* **Automatico** significa che è stato sviluppato un connettore di provisioning Azure AD per l'applicazione. È necessario seguire l'esercitazione di installazione specifica per configurare il provisioning per l'applicazione. Le esercitazioni per le applicazioni si trovano nell'[Elenco di esercitazioni sulla procedura di integrazione delle applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

Nella raccolta di Azure AD, le applicazioni che supportano il provisioning automatico sono indicate da un'icona di **provisioning** . Passare alla nuova esperienza di anteprima della raccolta per visualizzare queste icone (nel banner nella parte superiore della **pagina Aggiungi un'applicazione**, selezionare il collegamento **fare clic qui per provare la raccolta di app nuova e migliorata**).

![Icona del provisioning nella raccolta di applicazioni](./media/user-provisioning/browse-gallery.png)

La modalità di provisioning supportata da un'applicazione è visibile anche nella scheda **provisioning** dopo aver aggiunto l'applicazione alle **app aziendali**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

Per le applicazioni preintegrate elencate nella raccolta, sono disponibili istruzioni dettagliate per la configurazione del provisioning automatico. Vedere l' [elenco delle esercitazioni per le app integrate della raccolta](../saas-apps/tutorial-list.md). Il video seguente illustra come configurare il provisioning utenti automatico per SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Per altre applicazioni che supportano SCIM 2,0, seguire i passaggi illustrati nell'articolo [creare un endpoint scim e configurare il provisioning degli utenti](use-scim-to-provision-users-and-groups.md).


## <a name="related-articles"></a>Articoli correlati

- [Elenco delle esercitazioni su come integrare le app SaaS](../saas-apps/tutorial-list.md)
- [Personalizzazione dei mapping degli attributi per il provisioning degli utenti](customize-application-attributes.md)
- [Scrittura di espressioni per i mapping degli attributi](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtri di ambito per il provisioning degli utenti](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Compilare un endpoint SCIM e configurare il provisioning utenti](use-scim-to-provision-users-and-groups.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
