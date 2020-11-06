---
title: Modificare il tipo di autenticazione del sottodominio usando PowerShell e Graph-Azure Active Directory | Microsoft Docs
description: Modificare le impostazioni di autenticazione del sottodominio predefinite ereditate dalle impostazioni del dominio radice in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b85bc6485c4ea21cbdc224a6978db40d8dcdf105
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93402209"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Modificare il tipo di autenticazione del sottodominio in Azure Active Directory

Dopo l'aggiunta di un dominio radice a Azure Active Directory (Azure AD), tutti i sottodomini successivi aggiunti a tale radice nell'organizzazione Azure AD ereditano automaticamente l'impostazione di autenticazione dal dominio radice. Tuttavia, se si desidera gestire le impostazioni di autenticazione del dominio indipendentemente dalle impostazioni del dominio radice, è ora possibile usare l'API Microsoft Graph. Se, ad esempio, si dispone di un dominio radice federato, ad esempio contoso.com, questo articolo può essere utile per verificare un sottodominio come child.contoso.com come gestito anziché federato.

Nel portale di Azure AD, quando il dominio padre è federato e l'amministratore tenta di verificare un sottodominio gestito nella pagina **nomi di dominio personalizzati** , si otterrà l'errore ' Impossibile aggiungere il dominio ' con il motivo "una o più proprietà contengono valori non validi". Se si tenta di aggiungere questo sottodominio dall'interfaccia di amministrazione di Microsoft 365, verrà visualizzato un errore simile. Per ulteriori informazioni sull'errore, vedere [un dominio figlio non eredita le modifiche del dominio padre in Office 365, Azure o Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Come verificare un sottodominio personalizzato

Poiché i sottodomini ereditano il tipo di autenticazione del dominio radice per impostazione predefinita, è necessario alzare di livello il sottodominio a un dominio radice in Azure AD usando il Microsoft Graph per poter impostare il tipo di autenticazione sul tipo desiderato.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Aggiungere il sottodominio e visualizzarne il tipo di autenticazione

1. Usare PowerShell per aggiungere il nuovo sottodominio, che ha il tipo di autenticazione predefinito del dominio radice. I centri di amministrazione Azure AD e Microsoft 365 non supportano ancora questa operazione.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Usare [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) per ottenere il dominio. Poiché il dominio non è un dominio radice, eredita il tipo di autenticazione del dominio radice. Il comando e i risultati possono avere un aspetto simile al seguente, usando il proprio ID tenant:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Usare Azure AD API Graph Explorer per creare questo dominio radice

Usare il comando seguente per innalzare di livello il sottodominio:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Modificare il tipo di autenticazione del sottodominio

1. Usare il comando seguente per modificare il tipo di autenticazione del sottodominio:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Verificare tramite GET in Azure AD Graph Explorer che il tipo di autenticazione del sottodominio è ora gestito:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere nomi di dominio personalizzati](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Gestire i nomi di dominio](domains-manage.md)
- [Eseguire l'operazione ForceDelete su un nome di dominio personalizzato con l'API Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)