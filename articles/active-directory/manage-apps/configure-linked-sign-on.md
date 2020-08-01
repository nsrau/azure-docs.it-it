---
title: Configurare l'accesso collegato in Azure Active Directory
description: Configurare l'accesso collegato in Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 2269a8f7f58d35fee5e2ca30a77af5e8cba83678
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459335"
---
# <a name="configure-linked-sign-on"></a>Configurare l'accesso collegato

Nella [serie di guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni si è appreso come usare Azure ad come provider di identità (IDP) per un'applicazione. Nella Guida introduttiva viene configurato l'accesso Single Sign-on basato su SAML. È stata **collegata**un'altra opzione. Questo articolo illustra in modo più dettagliato l'opzione collegata.

L'opzione **collegata** consente di configurare il percorso di destinazione quando un utente seleziona l'app nel portale [app personali](https://myapplications.microsoft.com/) o Office 365 dell'organizzazione.

Di seguito sono riportati alcuni scenari comuni in cui l'opzione di collegamento è utile:
- Aggiungere un collegamento a un'applicazione Web personalizzata che attualmente usa la Federazione, ad esempio Active Directory Federation Services (AD FS).
- Aggiungere collegamenti profondi a pagine di SharePoint specifiche o ad altre pagine Web che si desidera visualizzare solo nei pannelli di accesso dell'utente.
- Aggiungere un collegamento a un'app che non richiede l'autenticazione. 
 
 L'opzione **collegata** non fornisce funzionalità di accesso tramite le credenziali Azure ad. Tuttavia, è comunque possibile utilizzare alcune delle altre funzionalità delle **applicazioni aziendali**. Ad esempio, è possibile usare i log di controllo e aggiungere un logo personalizzato e il nome dell'app.

## <a name="before-you-begin"></a>Prima di iniziare

Per iniziare rapidamente, esaminare la serie di [guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni. Nella Guida introduttiva, in cui è possibile configurare Single Sign-On, sarà anche presente l'opzione **collegata** . 

L'opzione **collegata** non fornisce funzionalità di accesso tramite Azure ad. L'opzione imposta semplicemente il percorso a cui gli utenti verranno inviati quando selezionano l'app nelle app [personali](https://myapplications.microsoft.com/) o nell'utilità di avvio dell'app Microsoft 365.

> [!IMPORTANT] 
> Esistono alcuni scenari in cui l'opzione **Single Sign-on** non verrà spostata per un'applicazione nelle **applicazioni aziendali**. 
>
> Se l'applicazione è stata registrata usando **registrazioni app** , la funzionalità Single Sign-on è configurata in modo da usare OAuth OIDC per impostazione predefinita. In questo caso, l'opzione **Single Sign-on** non verrà visualizzata nella finestra di navigazione in **applicazioni aziendali**. Quando si usa **registrazioni app** per aggiungere l'app personalizzata, si configurano le opzioni nel file manifesto. Per ulteriori informazioni sul file manifesto, vedere [Azure Active Directory manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Per altre informazioni sugli standard SSO, vedere [autenticazione e autorizzazione con la piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Gli altri scenari in cui l' **accesso Single Sign-on** non sarà presente nella navigazione includono quando un'applicazione è ospitata in un altro tenant o se l'account non ha le autorizzazioni necessarie (amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio). Le autorizzazioni possono anche causare uno scenario in cui è possibile aprire l' **accesso Single Sign-on** , ma non sarà possibile salvarlo. Per ulteriori informazioni sui ruoli amministrativi Azure AD, vedere ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Configura collegamento

Per impostare un collegamento per un'app, selezionare **collegato** nella pagina **Single Sign-on** . Quindi, immettere il collegamento e selezionare **Salva**. Serve un promemoria per trovare queste opzioni? Vedere la [serie di avvio rapido](view-applications-portal.md).
 
Dopo aver configurato un'app, assegnarvi utenti e gruppi. Quando si assegnano utenti, è possibile controllare quando l'applicazione viene visualizzata in [app personali](https://myapplications.microsoft.com/) o nell'utilità di avvio dell'app Microsoft 365.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
