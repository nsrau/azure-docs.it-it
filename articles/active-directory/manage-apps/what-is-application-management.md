---
title: Gestione di applicazioni con Azure Active Directory | Documentazione Microsoft
description: Questo articolo illustra i vantaggi dell'integrazione di Azure Active Directory con le applicazioni locali, cloud e SaaS.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: arvinh
ms.openlocfilehash: 820c6a5cb0bf5fb2d98d27c171c1276866082700
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716186"
---
# <a name="application-management-with-azure-active-directory"></a>Gestione di applicazioni con Azure Active Directory

Azure Active Directory (Azure AD) offre un accesso efficiente e sicuro alle applicazioni cloud e locali. Gli utenti possono eseguire la procedura di accesso una sola volta e accedere a Office 365 e altre applicazioni aziendali Microsoft, nonché a migliaia di applicazioni SaaS (Software as a Service), applicazioni locali e app line-of-business (LOB). Consente inoltre di ridurre i costi amministrativi automatizzando il provisioning degli utenti e di usare l'autenticazione a più fattori e i criteri di accesso condizionale per fornire un accesso sicuro alle applicazioni.

![App federate tramite Azure AD](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Perché gestire le applicazioni con una soluzione cloud?

In molte organizzazioni sono presenti centinaia di applicazioni a cui devono ricorrere i dipendenti per poter portare a termine il proprio lavoro. Gli utenti accedono a queste applicazioni da dispositivi e luoghi diversi. Ogni giorno, inoltre, vengono aggiunte, sviluppate e ritirate nuove applicazioni. Con un numero così elevato di applicazioni e punti di accesso, è ormai essenziale usare una soluzione basata sul cloud per gestire l'accesso degli utenti a tutte le applicazioni.

## <a name="manage-risk-with-conditional-access-policies"></a>Gestione dei rischi con i criteri di accesso condizionale
Combinando l'accesso Single Sign-On (SSO) di Azure AD con i criteri di accesso condizionale, è possibile garantire elevati livelli di sicurezza per l'accesso alle applicazioni. Le funzionalità di sicurezza includono la protezione dell'identità a livello di cloud, il controllo degli accessi basato sul rischio, l'autenticazione a più fattori nativa e i criteri di accesso condizionale. Queste funzionalità consentono criteri di controllo granulare basati sulle applicazioni o sui gruppi che richiedono maggiori livelli di sicurezza.

## <a name="improve-productivity-with-single-sign-on"></a>Migliorare la produttività con l'accesso Single Sign-On
Con l'abilitazione dell'accesso Single Sign-On (SSO) nelle applicazioni e in Office 365, è possibile migliorare l'esperienza di accesso degli utenti esistenti riducendo o eliminando le richieste di accesso. L'ambiente dell'utente risulta inoltre più coerente ed è più difficile distrarsi senza dover gestire molteplici richieste di accesso o innumerevoli password. Il gruppo aziendale può gestire e approvare gli accessi tramite la modalità self-service e l'appartenenza dinamica. Consentendo al personale aziendale di competenza di gestire gli accessi a un'applicazione, è possibile anche migliorare la sicurezza del sistema delle identità.

L'accesso SSO migliora la sicurezza. *Senza l'accesso Single Sign-On*, gli amministratori devono creare e aggiornare account utente per ogni singola applicazione, con una considerevole perdita di tempo. Gli utenti devono inoltre tenere traccia di più credenziali per accedere alle applicazioni. Gli utenti, quindi, tendono a prendere nota delle password o a usare soluzioni di gestione delle password che comportano altri rischi per la sicurezza dei dati. 

## <a name="address-governance-and-compliance"></a>Gestione della governance e della conformità
Con Azure AD è possibile monitorare gli accessi alle applicazioni tramite report basati sugli strumenti di monitoraggio di eventi imprevisti della sicurezza. È possibile accedere ai report dal portale o dalle API. A livello di codice, è possibile anche controllare chi può accedere alle applicazioni e rimuovere l'accesso agli utenti inattivi tramite verifiche di accesso.

## <a name="manage-costs"></a>Gestire i costi
Eseguendo la migrazione ad Azure AD è possibile anche risparmiare sui costi ed eliminare le complessità correlate alla gestione dell'infrastruttura locale. Azure AD consente infatti l'accesso self-service alle applicazioni, con un considerevole risparmio di tempo per gli amministratori e gli utenti. L'accesso Single Sign-On permette inoltre di eliminare le password specifiche delle applicazioni, con una conseguente eliminazione dei costi correlati alla reimpostazione delle password e alla perdita di produttività durante il recupero delle password.

