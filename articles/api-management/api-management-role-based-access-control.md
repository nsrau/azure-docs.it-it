---
title: Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come usare i ruoli predefiniti e creare ruoli personalizzati in Gestione API di Azure
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b048a1db135d217b319541b92cf3c30b345d1a66
ms.openlocfilehash: cedab98452ad336748d805f0efbf50633badf8ab


---

# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure
Gestione API di Azure si basa sul controllo degli accessi in base al ruolo di Azure per abilitare la gestione degli accessi con granularità fine per i servizi e le entità Gestione API (ad esempio, API e criteri). Questo articolo offre una panoramica dei ruoli predefiniti e personalizzati in Gestione API. Per altri dettagli sulla gestione degli accessi nel portale di Azure, vedere [Introduzione alla gestione degli accessi nel portale di Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)

## <a name="built-in-roles"></a>Ruoli predefiniti
In Gestione API sono attualmente disponibili 3 ruoli predefiniti e altri 2 ne verranno aggiunti prossimamente. Questi ruoli possono essere assegnati ad ambiti diversi, tra cui una sottoscrizione, un gruppo di risorse e una singola istanza di Gestione API. Se ad esempio il ruolo lettura del servizio Gestione API di Azure è assegnato a un utente a livello di gruppo di risorse, l'utente disporrà di accesso in lettura a tutte le istanze di Gestione API all'interno del gruppo di risorse. 

La tabella seguente contiene descrizioni brevi dei ruoli predefiniti. È possibile assegnare questi ruoli tramite il portale di Azure o altri strumenti, tra cui Azure [PowerShell](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-powershell), l'[interfaccia della riga di comando](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-azure-cli) e l'[API REST](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-rest) di Azure. Per dettagli su come assegnare ruoli predefiniti, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/).

| Ruolo          | Accesso in lettura<sup>[1]</sup> | Accesso in scrittura<sup>[2]</sup> | Creazione, eliminazione e ridimensionamento di servizi, configurazione dominio personalizzato e VPN | Accesso al portale di pubblicazione legacy | Descrizione
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Collaboratore Servizio Gestione API di Azure | ✓ | ✓  | ✓  | ✓ | Utente con privilegi avanzati. Dispone di accesso CRUD completo a servizi ed entità Gestione API (ad esempio, API e criteri). Dispone di accesso al portale di pubblicazione legacy. |
| Ruolo lettura del servizio Gestione API di Azure | ✓ | | || Dispone di accesso in sola lettura a servizi ed entità Gestione API. |
| Operatore del servizio Gestione API di Azure | ✓ | | ✓ | | Può gestire i servizi Gestione API, ma non le entità.|
| Editor del servizio Gestione API di Azure<sup>*</sup> | ✓ | ✓ | |  | Può gestire le entità Gestione API, ma non i servizi.|
| Gestione contenuto del servizio Gestione API di Azure<sup>*</sup> | ✓ | | | ✓ | Può gestire il portale per sviluppatori. Accesso in sola lettura a servizi ed entità.|

<sup>[1] Accesso in lettura a servizi ed entità Gestione API (ad esempio, API e criteri)</sup>

<sup>[2] Accesso in scrittura a servizi ed entità Gestione API, tranne per le seguenti operazioni: 1) Creazione, eliminazione e ridimensionamento di istanze 2) Configurazione VPN  3) Impostazione del nome di dominio personalizzato</sup>

<sup>\*Il ruolo Editor del servizio sarà disponibile dopo la migrazione dell'intera interfaccia utente di amministrazione dal portale di pubblicazione esistente al portale di Azure. Il ruolo Gestione contenuto sarà disponibile dopo il refactoring del portale di pubblicazione affinché contenga solo le funzionalità relative alla gestione del portale per sviluppatori.</sup>  


## <a name="custom-roles"></a>Ruoli personalizzati
Se i ruoli predefiniti non soddisfano le proprie esigenze, è possibile creare ruoli personalizzati per fornire una gestione degli accessi più granulare per le entità di Gestione API. È ad esempio possibile creare un ruolo personalizzato che dispone dell'accesso in sola lettura a un servizio Gestione API, ma del solo accesso in scrittura a un'API specifica. Per altre informazioni sui ruoli personalizzati, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles). 

Quando si crea un ruolo personalizzato, è più semplice iniziare con uno dei ruoli predefiniti. Modificare gli attributi per aggiungere Actions, NotActions o AssignableScopes e quindi salvare le modifiche come nuovo ruolo. L'esempio seguente inizia con il ruolo di lettura del servizio Gestione API di Azure e crea un ruolo personalizzato denominato "Calculator API Editor" (Editor API di calcolo). Il ruolo personalizzato può essere assegnato solo a un'API specifica pertanto avrà accesso solo a tale API. 

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

## <a name="watch-a-video-overview"></a>Guardare un video introduttivo

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
> 
> 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul controllo degli accessi in base al ruolo in Azure
  * [Introduzione alla gestione degli accessi nel portale di Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)



<!--HONumber=Feb17_HO1-->


