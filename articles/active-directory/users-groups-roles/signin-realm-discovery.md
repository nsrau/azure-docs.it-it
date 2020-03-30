---
title: Ricerca del nome utente durante l'accesso - Azure Active Directory Documenti Microsoft
description: Come la messaggistica su schermo riflette la ricerca del nome utente durante l'accesso in Azure Active DirectoryHow on-screen messaging reflects username lookup during sign-in in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024244"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Individuazione dell'area di autenticazione principale per le pagine di accesso di Azure Active Directory

Stiamo modificando il nostro comportamento di accesso di Azure Active Directory (Azure AD) per fare spazio a nuovi metodi di autenticazione e migliorare l'usabilità. Durante l'accesso, Azure AD determina dove un utente deve eseguire l'autenticazione. Azure AD prende decisioni intelligenti leggendo le impostazioni dell'organizzazione e dell'utente per il nome utente immesso nella pagina di accesso. Questo è un passo verso un futuro senza password che consente credenziali aggiuntive come FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento di individuazione dell'area di autenticazione principale

Storicamente, l'individuazione dell'area di autenticazione principale era governata dal dominio fornito all'accesso o da un criterio di individuazione dell'area di autenticazione principale per alcune applicazioni legacy. Ad esempio, nel nostro comportamento di individuazione un utente di Azure Active Directory potrebbe digitare erroneamente il proprio nome utente, ma sarebbe comunque arrivato alla schermata di raccolta delle credenziali dell'organizzazione. Ciò si verifica quando l'utente fornisce correttamente il nome di dominio dell'organizzazione "contoso.com". Questo comportamento non consente alla granularità di personalizzare le esperienze per un singolo utente.

Per supportare una gamma più ampia di credenziali e aumentare l'usabilità, il comportamento di ricerca del nome utente di Azure Active Directory durante il processo di accesso è ora aggiornato. Il nuovo comportamento prende decisioni intelligenti leggendo le impostazioni a livello di tenant e utente in base al nome utente immesso nella pagina di accesso. A tale scopo, Azure Active Directory verificherà se il nome utente immesso nella pagina di accesso esiste nel dominio specificato o reindirizza l'utente a fornire le credenziali.

Un ulteriore vantaggio di questo lavoro è la messaggistica di errore migliorata. Ecco alcuni esempi dei messaggi di errore migliorati quando si accede a un'applicazione che supporta solo gli utenti di Azure Active Directory.Here are some examples of the improved error messaging when signing in to an application that supports Azure Active Directory only.

- The username is mistyped or the username has not yet been synced to Azure AD:
  
    ![il nome utente non è stato digitato in modo errato o non trovato](./media/signin-realm-discovery/typo-username.png)
  
- Il nome di dominio è digitato in modo errato:
  
    ![il nome di dominio non è stato digitato in modo errato o non viene trovato](./media/signin-realm-discovery/typo-domain.png)
  
- L'utente tenta di accedere con un dominio consumer noto:User tries to sign in with a known consumer domain:
  
    ![accedere con un dominio consumer noto](./media/signin-realm-discovery/consumer-domain.png)
  
- La password è digitata in modo errato, ma il nome utente è accurato:  
  
    ![password è stata digitata in modo errato con un buon nome utente](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Questa funzionalità potrebbe avere un impatto sui domini federati che si basano sul controllo dell'area di autenticazione principale a livello di dominio precedente per forzare la federazione. Per gli aggiornamenti relativi all'aggiunta del supporto per i domini federati, vedere [Individuazione dell'area di autenticazione principale durante l'accesso per i servizi di Microsoft 365.](https://azure.microsoft.com/updates/signin-hrd/) Nel frattempo, alcune organizzazioni hanno addestrato i propri dipendenti ad accedere con un nome utente che non esiste in Azure Active Directory ma contiene il nome di dominio corretto, perché i nomi di dominio instradano gli utenti attualmente all'endpoint di dominio dell'organizzazione. Il nuovo comportamento di accesso non consente questa operazione. L'utente riceve una notifica per correggere il nome utente e non è consentito accedere con un nome utente che non esiste in Azure Active Directory.
>
> Se l'utente o l'organizzazione prevede procedure che dipendono dal comportamento precedente, è importante che gli amministratori dell'organizzazione aggiornino la documentazione di accesso e autenticazione dei dipendenti e che i dipendenti utilizzino il nome utente di Azure Active Directory per l'accesso.
  
In caso di dubbi sul nuovo comportamento, lasciare le osservazioni nella sezione **Feedback** di questo articolo.  

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il branding per l'accesso](../fundamentals/add-custom-domain.md)
