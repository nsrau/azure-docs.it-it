---
title: Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure | Documentazione Microsoft
description: "Informazioni su come definire i ruoli personalizzati con il Controllo degli accessi in base al ruolo per una gestione più precisa delle identità nella sottoscrizione di Azure."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8e72f2c8095d13c4b6df3c6576bd58806a3c0f2f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure
Creare un ruolo personalizzato nel Controllo degli accessi in base al ruolo di Azure, se nessuno dei ruoli predefiniti soddisfa le esigenze di accesso specifiche. I ruoli personalizzati possono essere creati usando [Azure PowerShell](role-based-access-control-manage-access-powershell.md), l'[interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md) e l'[API REST](role-based-access-control-manage-access-rest.md). Analogamente ai ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi e applicazioni nell'ambito della sottoscrizione, del gruppo di risorse e delle risorse. I ruoli personalizzati vengono archiviati in un tenant di Azure AD e possono essere condivisi tra le sottoscrizioni.

Ogni tenant può creare fino a 2000 ruoli personalizzati. 

L'esempio seguente mostra un ruolo personalizzato, che consente il monitoraggio e il riavvio di macchine virtuali:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Azioni
La proprietà **Actions** di un ruolo personalizzato specifica le operazioni di Azure a cui il ruolo concede l'accesso. Si tratta di una raccolta di stringhe di operazione che identificano operazioni a protezione diretta dei provider di risorse di Azure. Le stringhe di operazione seguono il formato di `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Le stringhe di operazione che contengono caratteri jolly (\*) concedono l'accesso a tutte le operazioni corrispondenti alla stringa di operazione. Ad esempio:

* `*/read` concede l'accesso a operazioni di lettura per tutti i tipi di risorsa di tutti i provider di risorse di Azure.
* `Microsoft.Compute/*` concede l'accesso a tutte le operazioni per tutti i tipi di risorsa nel provider di risorse Microsoft.Compute.
* `Microsoft.Network/*/read` concede l'accesso a operazioni di lettura per tutti i tipi di risorsa nel provider di risorse Microsoft.Network di Azure.
* `Microsoft.Compute/virtualMachines/*` concede l'accesso a tutte le operazioni delle macchine virtuali e ai relativi tipi di risorse figlio.
* `Microsoft.Web/sites/restart/Action` concede l'accesso per il riavvio dei siti Web.

Usare `Get-AzureRmProviderOperation` in PowerShell o `azure provider operations show` nell'interfaccia della riga di comando di Azure per elencare le operazioni dei provider di risorse di Azure. È anche possibile usare questi comandi per verificare la validità di una stringa di operazione e per espandere le stringhe di operazione con caratteri jolly.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Schermata PowerShell - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Screenshot dell'interfaccia della riga di comando di Azure: azure provider operations show "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Usare la proprietà **NotActions** se il set di operazioni che si vuole consentire viene più facilmente definito escludendo le operazioni con restrizioni. L'accesso concesso da un ruolo personalizzato viene calcolato sottraendo le operazioni **NotActions** dalle operazioni **Actions**.

> [!NOTE]
> Se a un utente viene assegnato un ruolo che esclude un'operazione in **NotActions** e un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire questa operazione. **NotActions** non è una regola di negazione. È semplicemente un modo comodo per creare una serie di operazioni consentite quando è necessario escludere operazioni specifiche.
>
>

## <a name="assignablescopes"></a>AssignableScopes
La proprietà **AssignableScopes** del ruolo personalizzato specifica gli ambiti, ovvero sottoscrizioni, gruppi di risorse o risorse, entro i quali il ruolo personalizzato è disponibile per l'assegnazione. È possibile rendere disponibile il ruolo personalizzato per l'assegnazione solo nelle sottoscrizioni o nei gruppi di risorse che lo richiedono, in modo da non complicare l'esperienza utente per le altre sottoscrizioni o gli altri gruppi di risorse.

Ecco alcuni esempi di ambiti assegnabili validi:

* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624": rende disponibile il ruolo per l'assegnazione in due sottoscrizioni.
* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e": rende disponibile il ruolo per l'assegnazione in una singola sottoscrizione.
* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network": rende disponibile il ruolo per l'assegnazione solo nel gruppo di risorse Network.

> [!NOTE]
> È necessario usare almeno una sottoscrizione, un gruppo di risorse o un ID della risorsa.
>
>

## <a name="custom-roles-access-control"></a>Controllo di accesso ai ruoli personalizzati
La proprietà **AssignableScopes** del ruolo personalizzato controlla anche quali utenti possono visualizzare, modificare ed eliminare il ruolo.

* È necessario specificare gli utenti autorizzati a creare un ruolo personalizzato.
    I ruoli Proprietario e Amministratore Accessi utenti di sottoscrizioni, gruppi di risorse e risorse possono creare ruoli personalizzati da usare in questi ambiti.
    L'utente che crea il ruolo deve poter eseguire l'operazione `Microsoft.Authorization/roleDefinition/write` in tutte le proprietà **AssignableScopes** del ruolo.
* È necessario specificare gli utenti autorizzati a modificare un ruolo personalizzato.
    I ruoli Proprietario e Amministratore Accessi utenti di sottoscrizioni, gruppi di risorse e risorse possono modificare i ruoli personalizzati in questi ambiti. Gli utenti devono poter eseguire l'operazione `Microsoft.Authorization/roleDefinition/write` in tutte le proprietà **AssignableScopes** di un ruolo personalizzato.
* Chi può visualizzare i ruoli personalizzati
    Tutti i ruoli predefiniti nel Controllo degli accessi in base al ruolo di Azure consentono la visualizzazione dei ruoli disponibili per l'assegnazione. Gli utenti che possono eseguire l'operazione `Microsoft.Authorization/roleDefinition/read` a livello di ambito possono visualizzare i ruoli del Controllo degli accessi in base al ruolo disponibili per l'assegnazione in tale ambito.

## <a name="see-also"></a>Vedere anche
* [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* Informazioni su come gestire l'accesso con:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
  * [API REST](role-based-access-control-manage-access-rest.md)
* [Ruoli predefiniti](role-based-access-built-in-roles.md): informazioni dettagliate sui ruoli predefiniti del controllo degli accessi in base al ruolo.
