---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135027"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Provisioning degli utenti da un'applicazione HR basata sul cloud ad Azure Active Directory

In passato, il personale IT si affidava a metodi manuali per creare, aggiornare ed eliminare dipendenti. Usava metodi come il caricamento di file CSV o l'esecuzione di script personalizzati per sincronizzare i dati dei dipendenti. Questi processi di provisioning sono soggetti a errori, non sono sicuri e sono difficili da gestire.

Per gestire i cicli di vita delle identità di dipendenti, fornitori o lavoratori occasionali, il [servizio di provisioning di Azure Active Directory (Azure AD)](../articles/active-directory/app-provisioning/user-provisioning.md) offre l'integrazione con applicazioni per la gestione delle risorse umane (HR) basate sul cloud, come Workday o SuccessFactors.

Azure AD usa questa integrazione per abilitare i flussi di lavoro seguenti per le applicazioni HR basate sul cloud:

- **Provisioning di utenti in Active Directory:** effettuare il provisioning di gruppi di utenti selezionati da un'app HR basata sul cloud in uno o più domini di Active Directory.
- **Provisioning dei soli utenti cloud in Azure AD:** negli scenari in cui Active Directory non viene usato, effettuare il provisioning degli utenti direttamente dall'app HR basata sul cloud ad Azure AD.
- **Writeback nell'app HR basata sul cloud:** scrivere gli attributi di nome utente e indirizzo di posta elettronica da Azure AD all'app HR basata sul cloud.


## <a name="enabled-hr-scenarios"></a>Scenari HR abilitati

Il servizio di provisioning utenti di Azure AD consente l'automazione dei seguenti scenari di gestione del ciclo di vita delle identità basati sulle risorse umane:

- **Assunzione di nuovi dipendenti:** quando un nuovo dipendente viene aggiunto all'app HR basata sul cloud, viene creato automaticamente un account utente in Active Directory e Azure AD con l'opzione di writeback degli attributi di nome utente e indirizzo di posta elettronica nell'app HR.
- **Aggiornamento dei profili e degli attributi dei dipendenti:** quando un record di un dipendente, ad esempio il nome, la posizione professionale o il manager, viene aggiornato nell'app HR basata sul cloud, il relativo account utente viene aggiornato automaticamente in Active Directory e Azure AD.
- **Termine del rapporto con i dipendenti:** quando un dipendente viene eliminato dall'app HR basata sul cloud, il relativo account utente viene disabilitato automaticamente in Active Directory e Azure AD.
- **Riassunzioni di dipendenti:** quando un dipendente viene nuovamente aggiunto nell'app HR basata sul cloud, il relativo account utente precedente può essere automaticamente riattivato o sottoposto di nuovo a provisioning in Active Directory e Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Per chi è più adatta questa integrazione?

L'integrazione dell'app HR basata sul cloud con il servizio di provisioning utenti di Azure AD è particolarmente adatta per le organizzazioni che:

- Vogliono una soluzione predefinita basata sul cloud per il provisioning degli utenti nell'app HR basata sul cloud.
- Hanno l'esigenza di effettuare il provisioning degli utenti direttamente dall'app HR basata sul cloud ad Active Directory o Azure AD.
- Hanno l'esigenza di effettuare il provisioning degli utenti usando i dati ottenuti dall'app HR basata sul cloud.
- Vogliono che le operazioni di aggiunta, spostamento ed eliminazione di utenti siano sincronizzate con uno o più domini, foreste o unità organizzative di Active Directory solo in base alle informazioni modificate rilevate nell'app HR basata sul cloud.
- Usano Office 365 per la posta elettronica.


### <a name="key-benefits"></a>Vantaggi principali

Il provisioning IT basato su risorse umane offre alle aziende gli importanti vantaggi seguenti:

- **Aumento della produttività:** è ora possibile automatizzare l'assegnazione degli account utente e delle licenze di Office 365 e fornire l'accesso ai gruppi principali. L'automazione delle assegnazioni consente ai nuovi assunti di accedere immediatamente ai propri strumenti di lavoro, aumentando la produttività.
- **Gestione dei rischi:** è possibile aumentare la sicurezza automatizzando le modifiche in base allo stato dei dipendenti o alle appartenenze ai gruppi con i flussi di dati provenienti dall'app HR basata sul cloud. L'automazione delle modifiche garantisce che le identità degli utenti e l'accesso alle app principali vengano aggiornati automaticamente quando gli utenti entrano o escono dall'organizzazione.
- **Gestione della conformità e della governance:** Azure AD supporta i log di controllo nativi per le richieste di provisioning utenti eseguite dalle app dei sistemi di origine e di destinazione. La funzionalità di controllo consente di monitorare chi ha accesso alle app da un'unica schermata.
- **Gestione dei costi:** il provisioning automatico riduce i costi evitando le inefficienze e gli errori umani associati ai processi di provisioning manuali. Riduce inoltre la necessità di usare soluzioni di provisioning utenti personalizzate sviluppate nel tempo usando piattaforme legacy e obsolete.
