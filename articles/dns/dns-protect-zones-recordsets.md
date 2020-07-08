---
title: Protezione di Zone DNS e record-DNS di Azure
description: In questo percorso di apprendimento iniziare a proteggere le zone DNS e i set di record in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 81a58657a4ab8f7403006ce02fb8f585feea86b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711273"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Come proteggere le zone e i record DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le zone e i record DNS sono risorse critiche. L'eliminazione di una zona DNS o di un singolo record DNS può causare un'interruzione del servizio. È importante che le zone e i record DNS siano protetti da modifiche non autorizzate o accidentali.

Questo articolo illustra il modo in cui DNS di Azure consente di proteggere le zone e i record DNS privati rispetto a tali modifiche.  Si applicano due potenti funzionalità di titoli fornite da Azure Resource Manager: il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) e i [blocchi delle risorse](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il Controllo degli accessi in base al ruolo di Azure consente una gestione degli accessi specifica per gli utenti, i gruppi e le risorse di Azure. Con RBAC è possibile concedere il livello di accesso necessario agli utenti. Per altre informazioni su come il Controllo degli accessi in base al ruolo facilita la gestione degli accessi, vedere l'articolo relativo al [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Ruolo Collaboratore zona DNS

Il ruolo Collaboratore zona DNS è un ruolo predefinito per la gestione delle risorse DNS private. Questo ruolo è stato applicato a un utente o a un gruppo che consente di gestire le risorse DNS.

Il gruppo di risorse *myResourceGroup* contiene cinque zone per Contoso Corporation. La concessione dell'autorizzazione Collaboratore zona DNS all'amministratore DNS di questo gruppo di risorse consente il controllo completo su queste zone DNS. Evita la concessione di autorizzazioni non necessarie. L'amministratore DNS non può creare o arrestare le macchine virtuali.

Il modo più semplice per assegnare le autorizzazioni del Controllo degli accessi in base al ruolo è [tramite il portale di Azure](../role-based-access-control/role-assignments-portal.md).  

Aprire **controllo di accesso (IAM)** per il gruppo di risorse, quindi selezionare **Aggiungi**, quindi selezionare il ruolo **collaboratore zona DNS** . Selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni.

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

Ad esempio, il gruppo di risorse *myResourceGroup* contiene la zona *contoso.com* e una sottozona *Customers.contoso.com*. I record CNAME vengono creati per ogni account del cliente. All'account amministratore usato per gestire i record CNAME vengono assegnate le autorizzazioni per la creazione di record nella zona *Customers.contoso.com* . L'account può gestire solo *Customers.contoso.com* .

È possibile concedere le autorizzazioni del Controllo degli accessi in base al ruolo a livello di zona tramite il portale di Azure.  Aprire **controllo di accesso (IAM)** per la zona, selezionare **Aggiungi**, quindi selezionare il ruolo **collaboratore zona DNS** e selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni.

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

Le autorizzazioni vengono applicate a livello di set di record.  All'utente viene concesso il controllo delle voci necessarie e non è possibile apportare altre modifiche.

Le autorizzazioni RBAC a livello di set di record possono essere configurate tramite il portale di Azure, usando il pulsante di **controllo di accesso (IAM)** nella pagina del set di record:

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

Il ruolo Collaboratore zona DNS predefinito consente il controllo completo su una risorsa DNS. È possibile creare ruoli personalizzati di Azure per fornire un controllo più granulare.

All'account utilizzato per gestire i CNAME viene concessa l'autorizzazione per la gestione solo dei record CNAME. L'account non è in grado di modificare i record di altri tipi. L'account non è in grado di eseguire operazioni a livello di zona, ad esempio l'eliminazione della zona.

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

Le definizioni di ruolo personalizzate attualmente non possono essere definite tramite il portale di Azure. È possibile creare un ruolo personalizzato basato su questa definizione di ruolo tramite Azure PowerShell:

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

Per altre informazioni su come creare, gestire e assegnare ruoli personalizzati, vedere [ruoli personalizzati nel](../role-based-access-control/custom-roles.md)controllo degli accessi in base al ruolo di Azure.

## <a name="resource-locks"></a>Blocchi risorse

Azure Resource Manager supporta un altro tipo di controllo di sicurezza, la possibilità di bloccare le risorse. I blocchi delle risorse vengono applicati alla risorsa e sono efficaci in tutti gli utenti e i ruoli. Per altre informazioni, vedere [bloccare le risorse con Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Esistono due tipi di blocco delle risorse: **CanNotDelete** e **ReadOnly**. Questi tipi di blocco possono essere applicati a una zona DNS privato o a un singolo set di record. Le sezioni seguenti descrivono diversi scenari comuni e come supportarli usando i blocchi risorse.

### <a name="protecting-against-all-changes"></a>Protezione da tutte le modifiche

Per evitare che vengano apportate modifiche, applicare un blocco ReadOnly alla zona. Questo blocco impedisce la creazione di nuovi set di record e la modifica o l'eliminazione dei set di record esistenti.

I blocchi risorse a livello di zona possono essere creati tramite il portale di Azure.  Nella pagina della zona DNS, selezionare **Blocchi**, quindi selezionare **+Aggiungi**:

![Blocchi risorse a livello di zona tramite il portale di Azure](./media/dns-protect-zones-recordsets/locks1.png)

I blocchi di risorse a livello di zona possono essere creati anche tramite [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest):

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

I blocchi risorse a livello di set di record possono attualmente essere configurati solo tramite Azure PowerShell.  Non sono supportati nel portale di Azure o nell'interfaccia della riga di comando di Azure.

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

Quando viene eliminata una zona in DNS di Azure, vengono eliminati tutti i set di record nella zona.  Questa operazione non può essere annullata. L'eliminazione accidentale di una zona critica può avere un impatto notevole.  È importante proteggersi da eliminazioni accidentali della zona.

L'applicazione di un blocco CanNotDelete a una zona impedisce l'eliminazione della zona. I blocchi vengono ereditati dalle risorse figlio. Un blocco impedisce l'eliminazione di tutti i set di record nella zona. Come descritto nella nota precedente, è inefficace perché i record possono comunque essere rimossi dai set di record esistenti.

In alternativa, applicare un blocco CanNotDelete a un set di record nella zona, ad esempio il set di record SOA. La zona non viene eliminata senza eliminare anche i set di record. Questo blocco protegge dall'eliminazione della zona, consentendo comunque la modifica gratuita dei set di record all'interno della zona. Se viene effettuato un tentativo di eliminare la zona, Azure Resource Manager rileva questa rimozione. La rimozione eliminerà anche il set di record SOA, Azure Resource Manager blocca la chiamata perché SOA è bloccato.  Nessun set di record viene eliminato.

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

Un'altra opzione per evitare l'eliminazione accidentale di una zona consiste nell'usare un ruolo personalizzato. Questo ruolo garantisce che gli account usati per gestire le zone non dispongano delle autorizzazioni di eliminazione della zona. 

Quando è necessario eliminare una zona, è possibile applicare un'eliminazione in due passaggi:

 - Per prima cosa, concedere le autorizzazioni di eliminazione zona
 - In secondo luogo, concedere le autorizzazioni per eliminare la zona.

Il ruolo personalizzato funziona per tutte le zone accessibili da tali account. Gli account con autorizzazioni di eliminazione della zona, ad esempio il proprietario della sottoscrizione, possono comunque eliminare accidentalmente una zona.

È possibile usare entrambi gli approcci, ovvero i blocchi delle risorse e i ruoli personalizzati, allo stesso tempo, come un approccio di difesa in profondità alla protezione della zona DNS.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso del Controllo degli accessi in base al ruolo, vedere l'argomento di [introduzione alla gestione degli accessi nel portale di Azure](../role-based-access-control/overview.md).
* Per altre informazioni sull'uso dei blocchi risorse, vedere [Bloccare le risorse con Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
