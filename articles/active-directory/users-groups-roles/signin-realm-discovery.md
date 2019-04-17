---
title: Ricerca nome utente durante l'autenticazione dell'accesso - Azure Active Directory | Microsoft Docs
description: La modalità su schermo messaggistica riflette ricerca nome utente durante l'accesso
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b6846c5f907c41db16e99883be7041a68357586
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608776"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Individuazione dell'area di autenticazione per le pagine di accesso Azure Active Directory

Stiamo apportando modifiche al comportamento di accesso di Azure Active Directory (Azure AD) per aggiungere nuovi metodi di autenticazione e migliorare l'usabilità. Durante l'accesso Azure AD determina la posizione in cui un utente deve eseguire l'autenticazione. Azure AD prende decisioni intelligenti mediante la lettura delle impostazioni di organizzazioni e utenti per il nome utente immesso nella pagina di accesso. Questo è un passaggio utile per favorire un futuro senza password che consente l'uso di credenziali aggiuntive come FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento di individuazione dell'area di autenticazione

In passato, individuazione dell'area di autenticazione è stata regolata dal dominio in cui viene fornito al momento dell'accesso o da un criterio Home Realm Discovery per alcune applicazioni legacy. Il comportamento di individuazione, ad esempio, un utente di Azure Active Directory è stato digitato in modo errato il nome utente ma sarebbe ugualmente nella schermata di insieme di credenziali della propria organizzazione. Ciò si verifica quando l'utente fornisce correttamente il nome di dominio "contoso.com" dell'organizzazione. Questo comportamento non consente la granularità necessaria per personalizzare le esperienze per un singolo utente.

Per supportare una gamma più ampia di credenziali e aumentare l'usabilità, il comportamento di ricerca di Azure Active Directory nome utente durante il processo di accesso è stato aggiornato. Il nuovo comportamento prende decisioni intelligenti mediante la lettura dei tenant e utente impostazioni a livello base il nome utente immesso nella pagina di accesso. A tale scopo, Azure Active Directory controllerà se il nome utente immesso nella pagina di accesso esiste nel dominio specificato o reindirizza l'utente per fornire le proprie credenziali.

Un ulteriore vantaggio di questo miglioramento è funzionalità ottimizzate di errori di messaggistica. Di seguito sono riportati alcuni esempi di errore migliorata di messaggistica durante l'accesso a un'applicazione che supporta solo gli utenti di Azure Active Directory.

1. È stato digitato correttamente il nome utente o il nome utente non è ancora stato sincronizzato con Azure AD:
  
    ![il nome utente è stato digitato correttamente o non trovato](./media/signin-realm-discovery/typo-username.png)
  
2. È stato digitato correttamente il nome di dominio:
  
    ![il nome di dominio è stato digitato correttamente o non trovato](./media/signin-realm-discovery/typo-domain.png)
  
3. Utente tenta di accedere con un dominio noti:
  
    ![Accedi con un dominio noto](./media/signin-realm-discovery/consumer-domain.png)
  
4. È stato digitato correttamente la password, ma il nome utente è preciso:  
  
    ![è stato digitato correttamente la password con il nome utente valido](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Questa funzionalità potrebbe influire sui domini federati di affidarsi a livello di dominio precedente Home Realm Discovery per forzare la federazione. Per aggiornamenti su quando verrà aggiunto il supporto di dominio federato, vedere [individuazione dell'area di autenticazione durante l'accesso per i servizi Microsoft 365](https://azure.microsoft.com/en-us/updates/signin-hrd/). Nel frattempo, alcune organizzazioni hanno eseguito il training di ai propri dipendenti di accedere con un nome utente che non esiste in Azure Active Directory ma contiene il nome di dominio corretto, perché i nomi di dominio indirizza gli utenti attualmente agli endpoint del dominio della propria organizzazione. Il nuovo comportamento di accesso non lo consente. L'utente viene informato per correggere il nome utente e che non possono accedere con un nome utente che non esiste in Azure Active Directory.
>
> Se l'organizzazione dispone di procedure consigliate che dipendono dal comportamento precedente, è importante per gli amministratori dell'organizzazione per aggiornare la documentazione di accesso e l'autenticazione di dipendenti e per il training di ai dipendenti di usare il nome utente di Azure Active Directory per l'accesso.
  
Se hai problemi con il nuovo comportamento, lasciare le osservazioni nel **commenti e suggerimenti** sezione di questo articolo.  

## <a name="next-steps"></a>Passaggi successivi

[Personalizzazione di accesso](../fundamentals/add-custom-domain.md)
