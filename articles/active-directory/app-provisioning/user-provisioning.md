---
title: Provisioning di utenti automatizzato per app SaaS in Azure AD | Microsoft Docs
description: Introduzione all'uso di Azure AD per eseguire automaticamente il provisioning, il deprovisioning e l'aggiornamento continuo degli account utente in più applicazioni SaaS di terze parti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454534"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizzare il provisioning e il deprovisioning degli utenti nelle applicazioni con Azure Active DirectoryAutomate user provisioning and deprovisioning to applications with Azure Active Directory

In Azure Active Directory (Azure AD) il termine provisioning delle **app** si riferisce alla creazione automatica di identità utente e ruoli nelle applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano. Gli scenari comuni includono il provisioning di un utente di Azure AD in applicazioni come [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) e altre ancora.

![Diagramma di panoramica del provisioning](./media/user-provisioning/provisioning-overview.png)

Questa funzione consente di:

- **Automatizzare il provisioning:** crea automaticamente nuovi account nei sistemi giusti per le nuove persone quando si uniscono al tuo team o organizzazione.
- **Automatizzare il deprovisioning:** Disattiva automaticamente gli account nei sistemi giusti quando gli utenti lasciano il team o l'organizzazione.
- **Sincronizzare i dati tra i sistemi:** Assicurati che le identità nelle app e nei sistemi siano aggiornate in base alle modifiche apportate alla directory o al sistema delle risorse umane.
- **Effettuare il provisioning dei gruppi:** Eseguire il provisioning dei gruppi nelle applicazioni che li supportano.
- **Governare l'accesso:** Monitorare e controllare chi è stato sottoposto a provisioning nelle applicazioni.
- **Distribuisci senza problemi in scenari di campo marrone:** Abbinare le identità esistenti tra i sistemi e consentire una facile integrazione, anche quando gli utenti esistono già nel sistema di destinazione.
- **Utilizzare la personalizzazione avanzata:** Sfrutta i mapping degli attributi personalizzabili che definiscono quali dati utente devono essere trasportati dal sistema di origine al sistema di destinazione.
- **Ricevi avvisi per gli eventi critici:** Il servizio di provisioning fornisce avvisi per gli eventi critici e consente l'integrazione di Log Analytics in cui è possibile definire avvisi personalizzati per soddisfare le esigenze aziendali.

## <a name="benefits-of-automatic-provisioning"></a>Vantaggi del provisioning automatico

Poiché il numero di applicazioni utilizzate nelle organizzazioni moderne continua a crescere, gli amministratori IT hanno il compito di gestire gli accessi su larga scala. Gli standard come SAML (Security Assertions Markup Language) o OIDC (Open ID Connect) consentono agli amministratori di configurare rapidamente l'accesso Single Sign-On (SSO), ma l'accesso richiede anche il provisioning degli utenti nell'app. Per molti amministratori, il provisioning significa creare manualmente ogni account utente o caricare file CSV ogni settimana, ma questi processi richiedono molto tempo, costano e sono soggetti a errori. Soluzioni come SAML just-in-time (JIT) sono state adottate per automatizzare il provisioning, ma le aziende hanno anche bisogno di una soluzione per eseguire il deprovisioning degli utenti quando lasciano l'organizzazione o non richiedono più l'accesso a determinate app in base alla modifica del ruolo.

Di seguito sono incluse alcune motivazioni comuni per l'utilizzo del provisioning automatico:Some common motivations for using automatic provisioning include:

- Massimizzando l'efficienza e l'accuratezza dei processi di provisioning.
- Risparmio sui costi associati all'hosting e alla gestione di script e soluzioni di provisioning sviluppati su misura.
- Protezione dell'organizzazione rimuovendo immediatamente le identità degli utenti dalle principali app SaaS quando lasciano l'organizzazione.
- Importa facilmente un gran numero di utenti in una particolare applicazione o sistema SaaS.
- Disporre di un unico set di criteri per determinare chi viene eseguito il provisioning e chi può accedere a un'app.

Il provisioning degli utenti di Azure AD consente di risolvere questi problemi. Per altre informazioni su come i clienti hanno usato il provisioning degli utenti di Azure AD, è possibile leggere il [case study ASOS.](https://aka.ms/asoscasestudy) Il video seguente offre una panoramica del provisioning degli utenti in Azure AD:The video below provides an overview of user provisioning in Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quali applicazioni e sistemi è possibile usare con il provisioning utenti automatico di Azure AD?

Azure AD offre il supporto preintegrato per molte app SaaS e sistemi di risorse umane popolari e il supporto generico per le app che implementano parti specifiche dello [standard SCIM 2.0.](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)

* **Applicazioni pre-integrate (gallery applicazioni SaaS)**. Sono disponibili tutte le applicazioni per le quali Azure AD supporta un connettore di provisioning preintegrato [nell'elenco delle esercitazioni](../saas-apps/tutorial-list.md)sulle applicazioni per il provisioning degli utenti. Le applicazioni preintegrate elencate nella raccolta utilizzano in genere le API di gestione degli utenti basate su SCIM 2.0 per il provisioning. 

   ![Logo Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Se si desidera richiedere una nuova applicazione per il provisioning, è possibile [richiedere l'integrated dell'applicazione con la raccolta di app.](../develop/howto-app-gallery-listing.md) Per una richiesta di provisioning degli utenti, è necessario che l'applicazione disponga di un endpoint conforme a SCIM. Si prega di richiedere che il fornitore dell'applicazione segua lo standard SCIM in modo da poter abordo dell'applicazione per la nostra piattaforma rapidamente.

* **Applicazioni che supportano SCIM 2.0**. Per informazioni su come connettere in modo generico le applicazioni che implementano le API di gestione degli utenti basate su SCIM 2.0, vedere [Creare un endpoint SCIM e configurare](use-scim-to-provision-users-and-groups.md)il provisioning degli utenti.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Che cos'è System for Cross-domain Identity Management (SCIM)?

Per automatizzare il provisioning e il deprovisioning, le app espongono API proprietarie di utenti e gruppi. Tuttavia, chiunque abbia tentato di gestire gli utenti in più app ti dirà che ogni app tenta di eseguire le stesse semplici azioni, come la creazione o l'aggiornamento di utenti, l'aggiunta di utenti ai gruppi o il deprovisioning degli utenti. Tuttavia, tutte queste semplici azioni vengono implementate in modo leggermente diverso, usando percorsi endpoint diversi, metodi diversi per specificare le informazioni utente e uno schema diverso per rappresentare ogni elemento di informazioni.

Per affrontare queste sfide, la specifica SCIM fornisce uno schema utente comune per consentire agli utenti di spostarsi all'interno, all'esterno e all'interno delle app. SCIM sta diventando lo standard de facto per il provisioning e, se utilizzato in combinazione con gli standard federativi come SAML o OpenID Connect, fornisce agli amministratori una soluzione end-to-end basata su standard per la gestione degli accessi.

Per istruzioni dettagliate sullo sviluppo di un endpoint SCIM per automatizzare il provisioning e il deprovisioning di utenti e gruppi in un'applicazione, vedere [Creare un endpoint SCIM e configurare](use-scim-to-provision-users-and-groups.md)il provisioning degli utenti. Per le applicazioni preintegrate nella raccolta (Slack, Azure Databricks, Snowflake e così via), è possibile ignorare la documentazione per gli sviluppatori e usare le esercitazioni fornite [qui](../saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Provisioning manuale o automatico

Le applicazioni nella raccolta di Azure AD supportano una delle due modalità di provisioning:Applications in the Azure AD gallery support one of two provisioning modes:

* Il provisioning **manuale** indica che non esiste ancora un connettore di provisioning automatico di Azure AD per l'app. Gli account utente devono essere creati manualmente, ad esempio aggiungendo utenti direttamente nel portale amministrativo dell'app o caricando un foglio di calcolo con i dettagli dell'account utente. Consulta la documentazione fornita dall'app o contatta lo sviluppatore dell'app per determinare quali meccanismi sono disponibili.

* **Automatico** significa che è stato sviluppato un connettore di provisioning Azure AD per l'applicazione. È consigliabile seguire l'esercitazione di configurazione specifica per configurare il provisioning per l'applicazione. Le esercitazioni per le applicazioni si trovano nell'[Elenco di esercitazioni sulla procedura di integrazione delle applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

Nella raccolta di Azure AD le applicazioni che supportano il provisioning automatico sono designate da un'icona **Provisioning.In** the Azure AD gallery, applications that support automatic provisioning are designated by a Provisioning icon. Passare alla nuova esperienza di anteprima della raccolta per visualizzare queste icone (nel banner nella parte superiore della **pagina Aggiungi un'applicazione**, selezionare il collegamento **Fare clic qui per provare la raccolta di app nuova e migliorata).**

![Icona di provisioning nella raccolta applicazioni](./media/user-provisioning/browse-gallery.png)

La modalità di provisioning supportata da un'applicazione è visibile anche nella scheda **Provisioning** dopo aver aggiunto l'applicazione alle **app aziendali.**

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

Per le applicazioni preintegrate elencate nella raccolta, sono disponibili istruzioni dettagliate per la configurazione del provisioning automatico. Vedere [l'elenco delle esercitazioni per le app della raccolta integrate](../saas-apps/tutorial-list.md). Il video seguente illustra come configurare il provisioning automatico degli utenti per SalesForce.The following video demonstrates how to set up automatic user provisioning for SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Per altre applicazioni che supportano SCIM 2.0, seguire i passaggi nell'articolo [Creare un endpoint SCIM e configurare](use-scim-to-provision-users-and-groups.md)il provisioning degli utenti.


## <a name="related-articles"></a>Articoli correlati

- [Elenco di tutorial su come integrare le app SaaS](../saas-apps/tutorial-list.md)
- [Personalizzazione dei mapping degli attributi per il provisioning degli utenti](customize-application-attributes.md)
- [Scrittura di espressioni per i mapping di attributiWriting expressions for attribute mappings](../app-provisioning/functions-for-customizing-application-data.md)
- [Applicazione dell'ambito ai filtri per il provisioning degli utenti](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Creare un endpoint SCIM e configurare il provisioning degli utenti](use-scim-to-provision-users-and-groups.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
