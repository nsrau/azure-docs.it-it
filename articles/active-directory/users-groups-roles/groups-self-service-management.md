---
title: Configurare la gestione gruppi self-service - Azure Active Directory | Microsoft Docs
description: Creare e gestire gruppi di sicurezza o gruppi di Office 365 in Azure Active Directory e richiedere l'appartenenza al gruppo di sicurezza o al gruppo di Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541deb5cf44ad5440e31641b673ed5da5b5d2b26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768553"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Configurare la gestione dei gruppi self-service in Azure Active DirectorySet up self-service group management in Azure Active Directory 

È possibile consentire agli utenti di creare e gestire i propri gruppi di sicurezza o gruppi di Office 365 in Azure Active Directory (Azure AD). Il proprietario del gruppo può approvare o rifiutare le richieste di appartenenza e delegare il controllo dell'appartenenza al gruppo. Le funzionalità di gestione dei gruppi in modalità self-service non sono disponibili per i gruppi di sicurezza abilitati alla posta elettronica o le liste di distribuzione.

## <a name="self-service-group-membership-defaults"></a>Impostazioni predefinite di appartenenza a gruppi self-service

Quando i gruppi di sicurezza vengono creati nel portale di Azure o usando Azure AD PowerShell, solo i proprietari del gruppo possono aggiornare l'appartenenza. I gruppi di sicurezza creati dalla modalità self-service nel [pannello Accesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) e in tutti i gruppi di Office 365 sono disponibili per l'aggiunta a tutti gli utenti, sia approvati dal proprietario che con approvazione automatica. Nel pannello Accesso, è possibile modificare le opzioni di appartenenza quando si crea il gruppo.

Gruppi creati in | Comportamento predefinito del gruppo di sicurezza | Comportamento predefinito del gruppo di Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Solo i proprietari possono aggiungere membri<br>Visibile ma non disponibile per l'aggiunta nel pannello Accesso | Apri per partecipare a tutti gli utenti
[Azure portal](https://portal.azure.com) | Solo i proprietari possono aggiungere membri<br>Visibile ma non disponibile per l'aggiunta nel pannello Accesso<br>Il proprietario non viene assegnato automaticamente al momento della creazione del gruppo | Apri per partecipare a tutti gli utenti
[Pannello di accesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Apri per partecipare a tutti gli utenti<br>Le opzioni di appartenenza possono essere modificate quando il gruppo viene creato | Apri per partecipare a tutti gli utenti<br>Le opzioni di appartenenza possono essere modificate quando il gruppo viene creato

## <a name="self-service-group-management-scenarios"></a>Scenari di gestione dei gruppi in modalità self-service

* **Gestione dei gruppi delegati** Un esempio è un amministratore che gestisce l'accesso a un'applicazione SaaS che l'azienda sta utilizzando. In questo caso la gestione dei diritti di accesso diventa più onerosa e pertanto l'amministratore chiede al titolare dell'organizzazione di creare un nuovo gruppo. L'amministratore assegna l'accesso per l'applicazione al nuovo gruppo e aggiunge al gruppo tutte le persone che accedono già all'applicazione. Il titolare dell'organizzazione può quindi aggiungere altri utenti, per i quali il provisioning nell'applicazione viene effettuato automaticamente. Il titolare dell'organizzazione non deve attendere che l'amministratore gestisca l'accesso degli utenti. Se l'amministratore concede la stessa autorizzazione a un manager in un gruppo di business diverso, tale persona può anche gestire l'accesso per i propri membri del gruppo. Né il proprietario dell'azienda né il manager possono visualizzare o gestire le appartenenze ai gruppi dell'altro. L'amministratore può comunque visualizzare tutti gli utenti che hanno accesso all'applicazione e, se necessario, revocare i diritti di accesso.
* **Gestione dei gruppi self-service** Un esempio di questo scenario è costituito da due utenti che dispongono entrambi di siti di SharePoint Online impostati in modo indipendente. Vogliono dare ai rispettivi team l'accesso ai loro siti. A questo scopo, possono creare un gruppo in Azure AD che può essere selezionato da ognuno in SharePoint Online per consentire l'accesso al proprio sito. Se un utente desidera accedere, effettua la richiesta dal pannello di accesso e dopo aver ottenuto l'approvazione potrà accedere automaticamente a entrambi i siti di SharePoint Online. Se successivamente uno degli utenti decide che tutte le persone che accedono al sito devono poter accedere anche a una determinata applicazione SaaS, l'amministratore dell'applicazione SaaS può aggiungere i diritti di accesso per l'applicazione al sito di SharePoint Online. A questo punto, tutte le richieste approvate garantiranno l'accesso ai due siti di SharePoint Online e anche all'applicazione SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Rendere disponibile un gruppo per gli utenti in modalità self-service

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale per la directory.
1. Selezionare **Gruppi**, quindi **Impostazioni generali.**
1. Impostare i proprietari possono gestire le richieste di **appartenenza** al gruppo nel pannello di accesso su **Sì**.
1. Impostare **Limita accesso ai gruppi nel pannello di accesso** su **No**.
1. Se si imposta Gli utenti possono creare gruppi di **sicurezza nei portali** di Azure o gli utenti possono creare gruppi di **Office 365 nei portali** di Azure per

    - **Sì:** a tutti gli utenti dell'organizzazione di Azure AD è consentito creare nuovi gruppi di sicurezza e aggiungere membri a questi gruppi. Questi nuovi gruppi saranno visibili anche nel pannello di accesso per tutti gli altri utenti. Se l'impostazione dei criteri nel gruppo lo consente, altri utenti possono creare richieste per unirsi a questi gruppi
    - **No:** gli utenti non possono creare gruppi e non possono modificare quelli esistenti per i quali sono proprietari. Possono tuttavia gestire l'appartenenza a tali gruppi e approvare le richieste di partecipazione provenienti da altri utenti.

È anche possibile usare **i proprietari che possono assegnare i membri come proprietari** di gruppi nei portali di Azure e i proprietari che possono **assegnare i membri come proprietari** di gruppi nei portali di Azure per ottenere un controllo di accesso più granulare sulla gestione dei gruppi self-service per gli utenti.

Quando gli utenti possono creare gruppi, tutti gli utenti dell'organizzazione sono autorizzati a creare nuovi gruppi e quindi possono, come proprietario predefinito, aggiungere membri a questi gruppi. Non è possibile specificare individui che possono creare i propri gruppi. È possibile specificare individui solo per rendere un proprietario di un gruppo a un membro del gruppo di un altro membro del gruppo.

> [!NOTE]
> Una licenza di Azure Active Directory Premium (P1 o P2) è necessaria per consentire agli utenti di richiedere di partecipare a un gruppo di sicurezza o a un gruppo di Office 365 e ai proprietari di approvare o rifiutare le richieste di appartenenza. Senza una licenza di Azure Active Directory Premium, gli utenti possono comunque gestire i propri gruppi nel pannello di accesso, ma non possono creare un gruppo che richiede l'approvazione del proprietario nel pannello di accesso e non possono richiedere l'aggiunta a un gruppo. 

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestire l'accesso alle risorse tramite i gruppi di Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](groups-settings-cmdlets.md)
* [Gestione di applicazioni con Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Informazioni su Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrare le identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
