---
title: Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure | Microsoft Docs
description: Informazioni su come usare i ruoli predefiniti e creare ruoli personalizzati in Gestione API di Azure
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: apimpm
ms.openlocfilehash: a3872aae3a9eb8da0b881ec9388f54546e84b08b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure
Gestione API di Azure si basa sul controllo degli accessi in base al ruolo di Azure per abilitare la gestione degli accessi con granularità fine per i servizi e le entità di Gestione API (ad esempio, API e criteri). Questo articolo offre una panoramica dei ruoli predefiniti e personalizzati in Gestione API. Per altre informazioni sulla gestione degli accessi nel portale di Azure, vedere [Introduzione alla gestione degli accessi nel portale di Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Ruoli predefiniti
In Gestione API sono attualmente disponibili tre ruoli predefiniti e altri due ne verranno aggiunti prossimamente. Questi ruoli possono essere assegnati ad ambiti diversi, tra cui una sottoscrizione, un gruppo di risorse e una singola istanza di Gestione API. Ad esempio, se il ruolo di lettura del servizio Gestione API di Azure viene assegnato a un utente a livello di gruppo di risorse, l'utente ha accesso in lettura a tutte le istanze di Gestione API all'interno del gruppo di risorse. 

La tabella seguente contiene descrizioni brevi dei ruoli predefiniti. È possibile assegnare questi ruoli tramite il portale di Azure o altri strumenti, tra cui Azure [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), l'[interfaccia della riga di comando di Azure ](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) e l'[API REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Per dettagli su come assegnare ruoli predefiniti, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

| Ruolo          | Accesso in lettura<sup>[1]</sup> | Accesso in scrittura<sup>[2]</sup> | Creazione, eliminazione e ridimensionamento di servizi, VPN e configurazione dominio personalizzato | Accesso al portale di pubblicazione legacy | Descrizione
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Collaboratore Servizio Gestione API di Azure | ✓ | ✓  | ✓  | ✓ | Utente con privilegi avanzati. Dispone di accesso CRUD completo a servizi ed entità di Gestione API (ad esempio, API e criteri). Dispone di accesso al portale di pubblicazione legacy. |
| Ruolo lettura del servizio Gestione API di Azure | ✓ | | || Dispone di accesso in sola lettura a servizi ed entità Gestione API. |
| Operatore del servizio Gestione API di Azure | ✓ | | ✓ | | Può gestire i servizi di Gestione API, ma non le entità.|
| Editor del servizio Gestione API di Azure<sup>*</sup> | ✓ | ✓ | |  | Può gestire le entità di Gestione API, ma non i servizi.|
| Gestione contenuto del servizio Gestione API di Azure<sup>*</sup> | ✓ | | | ✓ | Può gestire il portale per sviluppatori. Accesso in sola lettura a servizi ed entità.|

<sup>[1] Accesso in lettura a servizi ed entità di Gestione API (ad esempio, API e criteri).</sup>

<sup>[2] Accesso in scrittura a servizi ed entità di Gestione API, tranne per le operazioni seguenti: creazione, eliminazione e ridimensionamento di istanze; configurazione di VPN; configurazione di domini personalizzati.</sup>

<sup>\* Il ruolo Editor del servizio sarà disponibile dopo la migrazione dell'intera interfaccia utente di amministrazione dal portale di pubblicazione esistente al portale di Azure. Il ruolo Gestione contenuto sarà disponibile dopo il refactoring del portale di pubblicazione affinché contenga solo le funzionalità relative alla gestione del portale per sviluppatori.</sup>  

## <a name="custom-roles"></a>Ruoli personalizzati
Se i ruoli predefiniti non soddisfano le proprie esigenze, è possibile creare ruoli personalizzati per fornire una gestione degli accessi più granulare per le entità di Gestione API. È ad esempio possibile creare un ruolo personalizzato con accesso in sola lettura a un servizio di Gestione API, ma che ha accesso in scrittura solo a un'API specifica. Per altre informazioni sui ruoli personalizzati, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). 

Quando si crea un ruolo personalizzato, è più semplice iniziare con uno dei ruoli predefiniti. Modificare gli attributi per aggiungere **Actions**, **NotActions** o **AssignableScopes** e quindi salvare le modifiche come nuovo ruolo. L'esempio seguente inizia con il ruolo "Azure API Management Service Reader Role" (Ruolo lettura del servizio Gestione API di Azure) e crea un ruolo personalizzato denominato "Calculator API Editor" (Editor API di calcolo). È possibile assegnare il ruolo personalizzato a un'API specifica. Di conseguenza, questo ruolo può accedere solo a tale API. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Nell'articolo [Operazioni di provider di risorse con Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement) è disponibile l'elenco delle autorizzazioni che possono essere concesse a livello di Gestione API.

## <a name="watch-a-video-overview"></a>Guardare un video introduttivo

Per altre informazioni, è possibile guardare il video [Role-Based Access Control in API Management](https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player) (Controllo degli accessi in base al ruolo in Gestione API).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul controllo degli accessi in base al ruolo in Azure, vedere gli articoli seguenti:
  * [Introduzione alla gestione degli accessi nel portale di Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)
  * [Operazioni di provider di risorse con Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement)

