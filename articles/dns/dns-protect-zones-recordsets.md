---
title: Protezione delle zone e dei record DNS - DNS di Azure
description: In this learning path, get started protecting DNS zones and record sets in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 89a945f146601084795b2e12a721a03a1b96aaea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371478"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Come proteggere le zone e i record DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le zone e i record DNS sono risorse critiche. L'eliminazione di una zona DNS o di un singolo record DNS può causare un'interruzione del servizio. È importante che le zone e i record DNS siano protetti da modifiche non autorizzate o accidentali.

Questo articolo illustra in che modo DNS di Azure consente di proteggere le zone e i record DNS privati da tali modifiche.  Applichiamo due potenti funzionalità relative ai titoli fornite da Azure Resource Manager: controllo degli [accessi in base](../role-based-access-control/overview.md) al ruolo e [blocchi delle risorse.](../azure-resource-manager/management/lock-resources.md)

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il Controllo degli accessi in base al ruolo di Azure consente una gestione degli accessi specifica per gli utenti, i gruppi e le risorse di Azure. Con il controllo degli accessi in base al ruolo, è possibile concedere il livello di accesso necessario agli utenti. Per altre informazioni su come il Controllo degli accessi in base al ruolo facilita la gestione degli accessi, vedere l'articolo relativo al [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Ruolo Collaboratore zona DNS

Il ruolo Contributore zona DNS è un ruolo predefinito per la gestione delle risorse DNS private. Questo ruolo applicato a un utente o a un gruppo consente loro di gestire le risorse DNS.

Il gruppo di risorse myResourceGroup contiene cinque zone per Contoso Corporation.The resource group *myResourceGroup* contains five zones for Contoso Corporation. La concessione dell'autorizzazione Collaboratore zona DNS all'amministratore DNS di questo gruppo di risorse consente il controllo completo su queste zone DNS. Evita di concedere autorizzazioni non necessarie. L'amministratore DNS non può creare o arrestare macchine virtuali.

Il modo più semplice per assegnare le autorizzazioni del Controllo degli accessi in base al ruolo è [tramite il portale di Azure](../role-based-access-control/role-assignments-portal.md).  

Aprire Controllo di **accesso (IAM)** per il gruppo di risorse, quindi selezionare **Aggiungi**, quindi selezionare il ruolo **Collaboratore zona DNS.** Selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni.

![Controllo degli accessi in base al ruolo a livello di gruppo di risorse tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Queste autorizzazioni possono essere concesse anche [tramite Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-rbac"></a>Controllo degli accessi in base al ruolo a livello di zona

Le regole del Controllo degli accessi in base al ruolo di Azure possono essere applicate a una sottoscrizione, a un gruppo di risorse o a una singola risorsa. Tale risorsa può essere una singola zona DNS o un singolo set di record.

Ad esempio, il gruppo di risorse *myResourceGroup* contiene la zona *contoso.com* e una sottozona *customers.contoso.com*. I record CNAME vengono creati per ogni account cliente. All'account amministratore utilizzato per gestire i record CNAME vengono assegnate le autorizzazioni per creare record nell'area *customers.contoso.com.* L'account può gestire solo *customers.contoso.com.*

È possibile concedere le autorizzazioni del Controllo degli accessi in base al ruolo a livello di zona tramite il portale di Azure.  Aprire Controllo di **accesso (IAM)** per la zona, selezionare **Aggiungi**, quindi selezionare il ruolo **Collaboratore zona DNS** e selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni.

![Controllo degli accessi in base al ruolo a livello di zona DNS tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Queste autorizzazioni possono essere concesse anche [tramite Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>Controllo degli accessi in base al ruolo a livello di set di record

Le autorizzazioni vengono applicate a livello di set di record.  All'utente viene concesso il controllo alle voci di cui ha bisogno e non è in grado di apportare altre modifiche.

Le autorizzazioni RBAC a livello di set di record possono essere configurate tramite il portale di Azure usando il pulsante Controllo di accesso (IAM) nella pagina del set di record:Record-set level RBAC permissions can be configured via the Azure portal, using **the Access Control (IAM)** button in the record set page:

![Controllo degli accessi in base al ruolo a livello di set di record tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Le autorizzazioni del Controllo degli accessi in base al ruolo a livello di set di record possono anche essere [concesse tramite Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Ruoli personalizzati

Il ruolo Collaboratore zona DNS predefinito consente il controllo completo su una risorsa DNS. È possibile creare ruoli di Azure personalizzati per fornire un controllo con granularità più fine.

All'account utilizzato per gestire i record CNAME viene concessa l'autorizzazione per gestire solo i record CNAME. L'account non è in grado di modificare record di altri tipi. L'account non è in grado di eseguire operazioni a livello di zona, ad esempio l'eliminazione della zona.

L'esempio seguente illustra la definizione di un ruolo personalizzato per gestire solo record CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

La proprietà Actions definisce le autorizzazioni specifiche di DNS seguenti:

* `Microsoft.Network/dnsZones/CNAME/*` concede il controllo completo sui record CNAME
* `Microsoft.Network/dnsZones/read` concede l'autorizzazione per leggere le zone DNS, ma non per modificarle, consentendo di visualizzare l'area in cui viene creato il record CNAME.

Le azioni rimanenti vengono copiate dal [ruolo di collaboratore zona DNS predefinito](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Non è efficace usare un ruolo personalizzato del Controllo degli accessi in base al ruolo per impedire l'eliminazione di set di record, consentendo tuttavia il relativo aggiornamento. Impedisce l'eliminazione di set di record, ma non la relativa modifica.  Le modifiche consentite includono l'aggiunta e la rimozione di record dal set di record, inclusa la rimozione di tutti i record per ottenere un set di record vuoto. Questo è lo stesso effetto ottenuto eliminando il set di record dal punto di vista della risoluzione DNS.

Le definizioni di ruolo personalizzate non possono attualmente essere definite tramite il portale di Azure.Custom role definitions can't currently be defined via the Azure portal. È possibile creare un ruolo personalizzato basato su questa definizione di ruolo tramite Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Può anche essere creato tramite l'interfaccia della riga di comando di Azure:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Il ruolo può quindi essere assegnato come avviene per i ruoli predefiniti, come descritto in precedenza in questo articolo.

Per altre informazioni su come creare, gestire e assegnare ruoli personalizzati, vedere [Ruoli personalizzati in Controllo degli accessi in](../role-based-access-control/custom-roles.md)base al ruolo di Azure.For more information on how to create, manage, and assign custom roles, see Custom Roles in Azure RBAC.

## <a name="resource-locks"></a>Blocchi risorse

Azure Resource Manager supporta un altro tipo di controllo di sicurezza, la possibilità di bloccare le risorse. I blocchi delle risorse vengono applicati alla risorsa e sono validi per tutti gli utenti e i ruoli. Per altre informazioni, vedere [Bloccare le risorse con Azure Resource Manager.For](../azure-resource-manager/management/lock-resources.md)more information, see Lock resources with Azure Resource Manager.

Esistono due tipi di blocco delle risorse: **CanNotDelete** e **ReadOnly**. Questi tipi di blocco possono essere applicati a una zona DNS privata o a un singolo set di record. Le sezioni seguenti descrivono diversi scenari comuni e come supportarli usando i blocchi risorse.

### <a name="protecting-against-all-changes"></a>Protezione da tutte le modifiche

Per impedire che vengano apportate modifiche, applicare un blocco ReadOnly alla zona. Questo blocco impedisce la creazione di nuovi set di record e la modifica o l'eliminazione di set di record esistenti.

I blocchi risorse a livello di zona possono essere creati tramite il portale di Azure.  Nella pagina della zona DNS, selezionare **Blocchi**, quindi selezionare **+Aggiungi**:

![Blocchi risorse a livello di zona tramite il portale di Azure](./media/dns-protect-zones-recordsets/locks1.png)

I blocchi delle risorse a livello di zona possono essere creati anche tramite Azure PowerShell:zone-level resource locks can also be created via [Azure PowerShell:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Protezione di singoli record

Per evitare che venga modificato un set di record DNS esistente, impostare il blocco ReadOnly al set di record.

> [!NOTE]
> L'applicazione di un blocco CanNotDelete a un set di record non è un controllo efficace. Impedisce l'eliminazione del set di record, ma non impedisce che venga modificato.  Le modifiche consentite includono l'aggiunta e la rimozione di record dal set di record, inclusa la rimozione di tutti i record per ottenere un set di record vuoto. Questo è lo stesso effetto ottenuto eliminando il set di record dal punto di vista della risoluzione DNS.

I blocchi risorse a livello di set di record possono attualmente essere configurati solo tramite Azure PowerShell.  Non sono supportati nel portale di Azure o nell'interfaccia della riga di comando di Azure.They aren't supported in the Azure portal or Azure CLI.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Protezione dall'eliminazione di zone

Quando una zona viene eliminata nel DNS di Azure, tutti i set di record nella zona vengono eliminati.  Questa operazione non può essere annullata. L'eliminazione accidentale di una zona critica può avere un impatto notevole.  È importante proteggersi dall'eliminazione accidentale delle zone.

L'applicazione di un blocco CanNotDelete a una zona impedisce l'eliminazione della zona. I blocchi vengono ereditati dalle risorse figlio. Un blocco impedisce l'eliminazione di qualsiasi set di record nella zona. Come descritto nella nota precedente, è inefficace poiché i record possono ancora essere rimossi dai set di record esistenti.

In alternativa, applicare un blocco CanNotDelete a un set di record nella zona, ad esempio il set di record SOA. La zona non viene eliminata senza eliminare anche i set di record. Questo blocco protegge dall'eliminazione della zona, pur consentendo la modifica libera dei set di record all'interno della zona. Se si tenta di eliminare la zona, Azure Resource Manager rileva questa rimozione. La rimozione eliminerebbe anche il set di record SOA, Azure Resource Manager blocca la chiamata perché il SOA è bloccato.  Nessun set di record viene eliminato.

Il comando PowerShell seguente crea un blocco CanNotDelete sul record SOA della zona specificata:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Un'altra opzione per impedire l'eliminazione accidentale di zone consiste nell'utilizzare un ruolo personalizzato. Questo ruolo garantisce che gli account utilizzati per gestire le aree non dispongano delle autorizzazioni di eliminazione delle zone. 

Quando è necessario eliminare una zona, è possibile applicare un'eliminazione in due passaggi:When you do need to delete a zone, you can enforce a two-step delete:

 - In primo luogo, concedere le autorizzazioni di eliminazione dell'area
 - In secondo luogo, concedere le autorizzazioni per eliminare la zona.

Il ruolo personalizzato funziona per tutte le zone a cui accedono tali account. Gli account con autorizzazioni di eliminazione dell'area, ad esempio il proprietario della sottoscrizione, possono comunque eliminare accidentalmente un'area.

È possibile utilizzare entrambi gli approcci, ovvero blocchi di risorse e ruoli personalizzati, contemporaneamente, come approccio di difesa in profondità alla protezione delle zone DNS.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso del Controllo degli accessi in base al ruolo, vedere l'argomento di [introduzione alla gestione degli accessi nel portale di Azure](../role-based-access-control/overview.md).
* Per altre informazioni sull'uso dei blocchi risorse, vedere [Bloccare le risorse con Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
