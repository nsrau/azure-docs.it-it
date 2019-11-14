---
title: Ricerca nome utente durante l'accesso Azure Active Directory | Microsoft Docs
description: In che modo la messaggistica su schermo riflette la ricerca del nome utente durante l'accesso in Azure Active Directory
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024244"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Individuazione dell'area di autenticazione principale per le pagine di accesso Azure Active Directory

Stiamo apportando modifiche al comportamento di accesso di Azure Active Directory (Azure AD) per aggiungere nuovi metodi di autenticazione e migliorare l'usabilità. Durante l'accesso Azure AD determina la posizione in cui un utente deve eseguire l'autenticazione. Azure AD prende decisioni intelligenti mediante la lettura delle impostazioni di organizzazioni e utenti per il nome utente immesso nella pagina di accesso. Questo è un passaggio utile per favorire un futuro senza password che consente l'uso di credenziali aggiuntive come FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento dell'individuazione dell'area di autenticazione principale

In passato, l'individuazione dell'area di autenticazione principale era governata dal dominio fornito al momento dell'accesso o da un criterio di individuazione dell'area di autenticazione principale per alcune applicazioni legacy. Nel comportamento di individuazione, ad esempio, un utente Azure Active Directory potrebbe digitarne il nome utente, ma arriverebbe comunque alla schermata di raccolta delle credenziali dell'organizzazione. Questo errore si verifica quando l'utente specifica correttamente il nome di dominio dell'organizzazione "contoso.com". Questo comportamento non consente la granularità necessaria per personalizzare le esperienze per un singolo utente.

Per supportare una gamma più ampia di credenziali e aumentare l'usabilità, il comportamento di ricerca del nome utente di Azure Active Directory durante il processo di accesso è ora aggiornato. Il nuovo comportamento prende decisioni intelligenti leggendo le impostazioni a livello di tenant e utente in base al nome utente immesso nella pagina di accesso. Per ottenere questo possibile, Azure Active Directory verificherà se il nome utente immesso nella pagina di accesso esiste nel dominio specificato o reindirizza l'utente per fornire le credenziali.

Un ulteriore vantaggio di questo lavoro è la messaggistica degli errori migliorata. Di seguito sono riportati alcuni esempi della messaggistica di errore migliorata quando si accede a un'applicazione che supporta solo Azure Active Directory utenti.

- Il nome utente non è stato digitato in modo improprio o il nome utente non è ancora stato sincronizzato con Azure AD:
  
    ![il nome utente non è tipizzato o non è stato trovato](./media/signin-realm-discovery/typo-username.png)
  
- Il nome di dominio non è tipizzato:
  
    ![il nome di dominio non è tipizzato o non è stato trovato](./media/signin-realm-discovery/typo-domain.png)
  
- L'utente tenta di accedere con un dominio consumer noto:
  
    ![accesso con un dominio consumer noto](./media/signin-realm-discovery/consumer-domain.png)
  
- La password è digitata in modo non corretto, ma il nome utente è accurato:  
  
    ![la password non è stata digitata correttamente con un nome utente valido](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Questa funzionalità potrebbe avere un effetto sui domini federati che si basano sull'individuazione dell'area di autenticazione principale a livello di dominio precedente per forzare la Federazione. Per gli aggiornamenti su quando verrà aggiunto il supporto del dominio federato, vedere [individuazione dell'area di autenticazione principale durante l'accesso per i servizi Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Nel frattempo, alcune organizzazioni hanno preparato i dipendenti per l'accesso con un nome utente che non esiste in Azure Active Directory ma contiene il nome di dominio appropriato, perché i nomi di dominio indirizzano gli utenti attualmente all'endpoint di dominio dell'organizzazione. Il nuovo comportamento di accesso non consente questa operazione. L'utente riceve una notifica per correggere il nome utente e non è autorizzato ad accedere con un nome utente che non esiste nel Azure Active Directory.
>
> Se l'utente o l'organizzazione dispone di procedure che dipendono dal comportamento precedente, è importante per gli amministratori dell'organizzazione aggiornare la documentazione di accesso e autenticazione dei dipendenti e per eseguire il training dei dipendenti in modo da usare il nome utente Azure Active Directory per accedere.
  
In caso di problemi con il nuovo comportamento, lasciare le osservazioni nella sezione **feedback** di questo articolo.  

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare la personalizzazione dell'accesso](../fundamentals/add-custom-domain.md)
