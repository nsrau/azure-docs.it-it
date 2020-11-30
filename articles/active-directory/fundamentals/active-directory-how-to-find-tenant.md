---
title: Come trovare l'ID tenant-Azure Active Directory
description: Istruzioni su come trovare e Azure Active Directory ID tenant a una sottoscrizione di Azure esistente.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326591"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Come trovare l'ID tenant di Azure Active Directory

Le sottoscrizioni di Azure hanno una relazione di trust con Azure Active Directory (Azure AD). Azure AD è considerato attendibile per l'autenticazione di utenti, servizi e dispositivi per la sottoscrizione. A ogni sottoscrizione è associato un ID tenant e sono disponibili alcuni modi per trovare l'ID tenant per la sottoscrizione.

## <a name="find-tenant-id-through-the-azure-portal"></a>Trovare l'ID tenant tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
 
1. Selezionare **Azure Active Directory**.

1. Selezionare **Proprietà**.

1. Scorrere quindi verso il basso fino al campo **ID tenant** . L'ID tenant sarà presente nella casella.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-Properties-ID tenant-campo ID tenant":::

## <a name="find-tenant-id-with-powershell"></a>Trovare l'ID tenant con PowerShell

È anche possibile trovare il tenant a livello di codice. Per trovare l'ID tenant con Azure PowerShell, usare il cmdlet `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Per ulteriori informazioni, vedere questo Azure PowerShell riferimento ai cmdlet per [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Trovare l'ID tenant con l'interfaccia della riga di comando
Se si vuole usare un'interfaccia della riga di comando per trovare l'ID tenant, è possibile eseguire questa operazione con l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/). 

Per l'interfaccia della riga di comando di Azure, usare uno dei comandi **AZ login**, **AZ account list** o **AZ account tenant list** , come illustrato nell'esempio seguente. Si noti la proprietà **tenantId** per ogni sottoscrizione nell'output di ogni comando.

```azurecli-interactive
az login
az account list
az account tenant list
```

Per ulteriori informazioni, vedere informazioni di riferimento sui comandi [AZ login](/cli/azure/reference-index#az_login) , informazioni di riferimento sui comandi AZ [account](/cli/azure/ext/account/account) o il riferimento al comando [AZ account tenant](/cli/azure/ext/account/account/tenant) .


Per Microsoft 365 interfaccia della riga di comando, usare l' **ID tenant** del cmdlet come illustrato nell'esempio seguente:
 
```cli
m365 tenant id get
```

Per ulteriori informazioni, vedere la Guida di riferimento al comando Microsoft 365 [Tenant ID get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) .


## <a name="next-steps"></a>Passaggi successivi

- Per creare un nuovo tenant di Azure AD, vedere [Guida introduttiva: creare un nuovo tenant in Azure Active Directory](active-directory-access-create-new-tenant.md).

- Per informazioni su come associare o aggiungere una sottoscrizione a un tenant, vedere [associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).

- Per informazioni su come trovare l'ID oggetto, vedere [trovare l'ID oggetto utente](/partner-center/find-ids-and-domain-names#find-the-user-object-id).