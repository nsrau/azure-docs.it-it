---
title: Protezione di record e zone DNS | Microsoft Docs
description: Come proteggere le zone e i set di record in DNS di Microsoft Azure.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 5d73d1203faf485d715354e68ce2ccde32562611
ms.openlocfilehash: f5865e07df4e1f253079ac1c8b257e2525da5ef3

---

# <a name="how-to-protect-dns-zones-and-records"></a>Come proteggere le zone e i record DNS

Le zone e i record DNS sono risorse critiche. L'eliminazione di una zona DNS o persino di un singolo record DNS può comportare un'interruzione del servizio totale.  È importante proteggere le zone e i record DNS critici da modifiche non autorizzate o accidentali.

Questo articolo spiega come è possibile proteggere i record e le zone DNS da queste modifiche con DNS di Azure.  Si usano due potenti funzionalità di sicurezza fornite da Azure Resource Manager: il [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md) e i [blocchi risorse](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il Controllo degli accessi in base al ruolo di Azure consente una gestione degli accessi specifica per gli utenti, i gruppi e le risorse di Azure. Il Controllo degli accessi in base al ruolo permette di concedere agli utenti esattamente il livello di accesso necessario per eseguire i propri processi. Per altre informazioni su come il Controllo degli accessi in base al ruolo facilita la gestione degli accessi, vedere l'articolo relativo al [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md).

### <a name="the-dns-zone-contributor-role"></a>Il ruolo di "collaboratore zona DNS"

Il ruolo di "collaboratore zona DNS" è un ruolo predefinito fornito da Azure per la gestione delle risorse DNS.  L'assegnazione di autorizzazioni come collaboratore zona DNS a un utente o a un gruppo consente a questo gruppo di gestire le risorse DNS, ma non le risorse di altro tipo.

Si supponga, ad esempio, che il gruppo di risorse "zonepersonali" contenga cinque zone per Contoso Corporation. La concessione di autorizzazioni come "collaboratore zona DNS" all'amministratore DNS per questo gruppo di risorse consente il controllo completo su queste zone DNS. Si evita anche di concedere autorizzazioni non necessarie, ad esempio l'amministratore DNS non può creare o arrestare macchine virtuali.

Il modo più semplice per assegnare le autorizzazioni del Controllo degli accessi in base al ruolo è [tramite il portale di Azure](../active-directory/role-based-access-control-configure.md).  Aprire il pannello "Controllo di accesso (IAM)" per il gruppo di risorse, fare clic su "Aggiungi", quindi selezionare il ruolo di "collaboratore zona DNS" e selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni.

![Controllo degli accessi in base al ruolo a livello di gruppo di risorse tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Queste autorizzazioni possono essere concesse anche [tramite Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Controllo degli accessi in base al ruolo a livello di zona

Le regole del Controllo degli accessi in base al ruolo di Azure possono essere applicate a una sottoscrizione, a un gruppo di risorse o a una singola risorsa. Nel caso di DNS di Azure, questa risorsa può essere una singola zona DNS o persino un singolo set di record.

Si supponga, ad esempio, che il gruppo di risorse "zone personali" contenga la zona "contoso.com" e una sottozona "customers.contoso.com" in cui vengono creati i record CNAME per ciascun account cliente.  All'account usato per gestire i record CNAME devono essere assegnate le autorizzazioni per creare i record solo nella zona "customers.contoso.com", senza consentire l'accesso alle altre zone.

È possibile concedere le autorizzazioni del Controllo degli accessi in base al ruolo a livello di zona tramite il portale di Azure.  Aprire il pannello "Controllo di accesso (IAM)" per la zona, fare clic su "Aggiungi", quindi selezionare il ruolo di "collaboratore zona DNS" e selezionare gli utenti o i gruppi necessari per concedere le autorizzazioni.

![Controllo degli accessi in base al ruolo a livello di zona DNS tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Queste autorizzazioni possono essere concesse anche [tramite Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Controllo degli accessi in base al ruolo a livello di set di record

È possibile fare un ulteriore passo in avanti. L'amministratore di posta elettronica per Contoso Corporation, ad esempio, necessita dell'accesso ai record MX e TXT al vertice della zona "contoso.com".  Non ha necessità di accedere agli altri record MX o TXT oppure ai record di altro tipo.  DNS di Azure consente di assegnare le autorizzazioni a livello di set di record con precisione per i record a cui l'amministratore di posta elettronica deve accedere.  All'amministratore di posta elettronica viene concesso esattamente il controllo di cui necessita, senza poter apportare altre modifiche.

Le autorizzazioni del Controllo degli accessi in base al ruolo a livello di set di record possono essere configurate tramite il portale di Azure, usando il pulsante "Utenti" nel pannello dei set di record:

![Controllo degli accessi in base al ruolo a livello di set di record tramite il portale di Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Le autorizzazioni del Controllo degli accessi in base al ruolo a livello di set di record possono anche essere [concesse tramite Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Il comando equivalente è [disponibile anche tramite l'interfaccia della riga di comando di Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Ruoli personalizzati

Il ruolo di "collaboratore zona DNS" predefinito consente il controllo completo su una risorsa DNS. È anche possibile creare ruoli di Azure di clienti, per fornire un controllo più capillare.

Si consideri di nuovo l'esempio in cui viene creato un record CNAME nella zona "customers.contoso.com" per ogni account cliente di Contoso Corporation.  All'account usato per gestire i record CNAME deve essere concessa l'autorizzazione per gestire solo i record CNAME.  Non è quindi possibile modificare i record di altri tipi, ad esempio i record MX, oppure eseguire operazioni a livello di zona, ad esempio l'eliminazione di una zona.

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
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

La proprietà Actions definisce le autorizzazioni specifiche di DNS seguenti:

* `Microsoft.Network/dnsZones/CNAME/*` concede il controllo completo sui record CNAME
* `Microsoft.Network/dnsZones/read` concede l'autorizzazione per leggere le zone DNS, ma non per modificarle, consentendo di visualizzare l'area in cui viene creato il record CNAME.

Le azioni rimanenti vengono copiate dal [ruolo di collaboratore zona DNS predefinito](../active-directory/role-based-access-built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Non è efficace usare un ruolo personalizzato del Controllo degli accessi in base al ruolo per impedire l'eliminazione di set di record, consentendo tuttavia il relativo aggiornamento. Impedisce l'eliminazione di set di record, ma non la relativa modifica.  Alcune modifiche consentite sono l'aggiunta e la rimozione di record dal set di record, inclusa la rimozione di tutti i record per ottenere un set di record "vuoto". Questo è lo stesso effetto ottenuto eliminando il set di record dal punto di vista della risoluzione DNS.

Attualmente non è possibile specificare definizioni di ruoli personalizzati tramite il portale di Azure. È possibile creare un ruolo personalizzato basato su questa definizione di ruolo tramite Azure PowerShell:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Può anche essere creato tramite l'interfaccia della riga di comando di Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Il ruolo può quindi essere assegnato come avviene per i ruoli predefiniti, come descritto in precedenza in questo articolo.

Per altre informazioni su come creare, gestire e assegnare ruoli personalizzati, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-custom-roles.md).

## <a name="resource-locks"></a>Blocchi risorse

Oltre al Controllo degli accessi in base al ruolo, Azure Resource Manager supporta un altro tipo di controllo di sicurezza, vale a dire la possibilità di "bloccare" le risorse. Le regole del Controllo degli accessi in base al ruolo di Azure consentono di controllare le azioni di utenti e gruppi specifici,mentre i blocchi risorse vengono applicati alla risorsa e hanno effetto su tutti gli utenti e i ruoli. Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](../azure-resource-manager/resource-group-lock-resources.md).

Esistono due tipi di blocchi risorse: **DoNotDelete** e **ReadOnly**. Questi blocchi possono essere applicati a una zona DNS o a un singolo set di record.  Le sezioni seguenti descrivono diversi scenari comuni e come supportarli usando i blocchi risorse.

### <a name="protecting-against-all-changes"></a>Protezione da tutte le modifiche

Per evitare che vengano apportate modifiche, applicare un blocco ReadOnly alla zona.  In questo modo non è possibile creare nuovi set di record oppure modificare o eliminare i set di record esistenti.

I blocchi risorse a livello di zona possono essere creati tramite il portale di Azure.  Nel pannello Zona DNS, fare clic su "Blocchi", quindi su "Aggiungi":

![Blocchi risorse a livello di zona tramite il portale di Azure](./media/dns-protect-zones-recordsets/locks1.png)

I blocchi risorse a livello di zona possono essere creati anche tramite Azure PowerShell:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

La configurazione di blocchi risorse di Azure non è attualmente supportata tramite l'interfaccia della riga di comando di Azure.

### <a name="protecting-individual-records"></a>Protezione di singoli record

Per evitare che venga modificato un set di record DNS esistente, impostare il blocco ReadOnly al set di record.

> [!NOTE]
> L'applicazione di un blocco DoNotDelete a un set di record non è un controllo efficace. Impedisce l'eliminazione del set di record, ma non impedisce che venga modificato.  Alcune modifiche consentite sono l'aggiunta e la rimozione di record dal set di record, inclusa la rimozione di tutti i record per ottenere un set di record "vuoto". Questo è lo stesso effetto ottenuto eliminando il set di record dal punto di vista della risoluzione DNS.

I blocchi risorse a livello di set di record possono attualmente essere configurati solo tramite Azure PowerShell.  Non sono supportati nel portale di Azure o nell'interfaccia della riga di comando di Azure.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Protezione dall'eliminazione di zone

Quando viene eliminata una zona in DNS di Azure, vengono eliminati anche tutti i set di record in essa contenuti.  Questa operazione non può essere annullata.  L'eliminazione accidentale di una zona critica può avere un impatto notevole.  È quindi molto importante evitare l'eliminazione accidentale di una zona.

L'applicazione di un blocco DoNotDelete a una zona impedisce l'eliminazione della zona.  Tuttavia, poiché i blocchi vengono ereditati dalle risorse figlio, impedisce anche che vengano eliminati set di record nella zona, creando un effetto potenzialmente indesiderato.  Inoltre, come descritto in precedenza, questo controllo non è efficace poiché i record possono essere rimossi dai set di record esistenti.

In alternativa, è possibile applicare un blocco DoNotDelete a un set di record nella zona, ad esempio il set di record SOA.  Poiché la zona non può essere eliminata senza eliminare anche i set di record, è possibile evitare l'eliminazione della zona, consentendo comunque di modificare i set di record all'interno della zona. Se viene eseguito un tentativo di eliminare la zona, Azure Resource Manager rileva che questa operazione potrebbe eliminare anche il set di record SOA e blocca la chiamata perché il record SOA è bloccato.  Nessun set di record viene eliminato.

Il comando PowerShell seguente crea un blocco DoNotDelete sul record SOA della zona specificata:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Un altro modo per impedire di eliminare accidentalmente la zona è usare un ruolo personalizzato per garantire che l'account operatore e l'account di servizio usati per gestire le zone non dispongano delle autorizzazioni per eliminare le zone. Quando è necessario eliminare una zona, è possibile farlo in due passaggi, prima concedendo le autorizzazioni per eliminare la zona (nell'ambito della zona stessa, per impedire di eliminarne una sbagliata) e poi eliminandola.

Questo secondo approccio ha il vantaggio di essere adatto a tutte le zone accessibili da tali account, senza che sia necessario ricordarsi di creare i blocchi. Lo svantaggio, invece, è che tutti gli account con autorizzazioni per eliminare le zone, ad esempio il proprietario della sottoscrizione, possono sempre eliminare una zona critica accidentalmente.

Usare entrambi gli approcci contemporaneamente (blocchi di risorse e ruoli personalizzati) è un metodo di difesa avanzato per la protezione delle zone DNS.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso del Controllo degli accessi in base al ruolo, vedere l'argomento di [introduzione alla gestione degli accessi nel portale di Azure](../active-directory/role-based-access-control-what-is.md).
* Per altre informazioni sull'uso dei blocchi risorse, vedere [Bloccare le risorse con Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).
* Per altre informazioni sulla protezione delle risorse di Azure, vedere [Considerazioni sulla sicurezza per Azure Resource Manager](../best-practices-resource-manager-security.md).




<!--HONumber=Dec16_HO4-->


