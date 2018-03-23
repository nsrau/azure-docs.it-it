---
title: Configurare la governance delle macchine virtuali di Azure con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: "Esercitazione: gestire macchine virtuali di Azure applicando Controllo degli accessi in base al ruolo, criteri, blocchi e tag con l'interfaccia della riga di comando di Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: ac6f7b0d32479e9e7e9945f83dc63a5847cba6a4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>Governance delle macchine virtuali con l'interfaccia della riga di comando di Azure

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Informazioni sull'ambito

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

In questa esercitazione tutte le impostazioni di gestione vengono applicate a un gruppo di risorse per semplificare la rimozione delle impostazioni stesse al termine delle operazioni.

Creare il gruppo di risorse.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Il gruppo di risorse è attualmente vuoto.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Si vuole che gli utenti dell'organizzazione dispongano del giusto livello di accesso a queste risorse. Non si vuole concedere un accesso illimitato agli utenti, ma si vuole comunque avere la certezza che tutti possano svolgere il proprio lavoro. Il [controllo degli accessi in base al ruolo](../../active-directory/role-based-access-control-what-is.md) consente di stabilire quali utenti sono autorizzati a completare specifiche azioni in un determinato ambito.

Per creare e rimuovere assegnazioni di ruoli, gli utenti devono disporre di accesso `Microsoft.Authorization/roleAssignments/*`. Questo accesso viene concesso tramite il ruolo Proprietario o Amministratore accessi utente.

Per la gestione di soluzioni di macchine virtuali, sono disponibili tre ruoli specifici delle risorse che forniscono l'accesso in genere necessario:

* [Collaboratore macchine virtuali](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Collaboratore di rete](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Collaboratore account di archiviazione](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Invece di assegnare ruoli ai singoli utenti, è spesso più facile [creare un gruppo di Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) per gli utenti che devono eseguire azioni simili. Assegnare quindi tale gruppo al ruolo appropriato. Per semplificare questo articolo, creare un gruppo di Azure Active Directory senza membri. È comunque possibile assegnare questo gruppo a un ruolo per un ambito. 

L'esempio seguente crea un gruppo di Azure Active Directory denominato *VMDemoContributors* con il nome alternativo di posta elettronica *vmDemoGroup*. Il nome alternativo di posta elettronica funge da alias del gruppo.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Dopo la restituzione del risultato del prompt dei comandi, la propagazione del gruppo in Azure Active Directory richiede pochi istanti. Dopo aver atteso 20 o 30 secondi, usare il comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) per assegnare il nuovo gruppo di Azure Active Directory al ruolo Collaboratore Macchina virtuale per il gruppo di risorse.  Se si esegue il comando seguente prima della propagazione, si riceve un messaggio di errore che informa che **l'entità <guid> non esiste nella directory**. Provare a eseguire di nuovo il comando.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

In genere si ripete il processo per *Collaboratore Rete* e *Collaboratore Account di archiviazione* per assicurarsi che vengano assegnati utenti per la gestione delle risorse distribuite. In questo articolo è possibile ignorare questi passaggi.

## <a name="azure-policies"></a>Criteri di Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Applicare i criteri

La sottoscrizione include già alcune definizioni dei criteri. Per visualizzare le definizioni dei criteri disponibili, usare il comando [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Vengono visualizzate le definizioni dei criteri esistenti. Il tipo di criterio è **BuiltIn** o **Custom**. Esaminare le definizioni per individuare i criteri che descrivono una condizione da assegnare. In questo articolo vengono assegnati criteri che consentono di:

* Limitare le posizioni per tutte le risorse.
* Limitare gli SKU per le macchine virtuali.
* Controllare le macchine virtuali che non usano dischi gestiti.

L'esempio seguente recupera tre definizioni di criteri in base al nome visualizzato. Viene usato il comando [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) per assegnare queste definizioni al gruppo di risorse. Per alcuni criteri occorre fornire i valori dei parametri per specificare i valori consentiti.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

L'esempio precedente presuppone che si conoscano già i parametri di un criterio. Se occorre visualizzare i parametri, usare:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Distribuire la macchina virtuale

Sono stati assegnati ruoli e criteri, quindi è ora possibile distribuire la soluzione. Le dimensioni predefinite sono Standard_DS1_v2, che corrispondono a uno degli SKU consentiti. Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Al termine della distribuzione è possibile applicare altre impostazioni di gestione alla soluzione.

## <a name="lock-resources"></a>Bloccare le risorse

I [blocchi delle risorse](../../azure-resource-manager/resource-group-lock-resources.md) impediscono agli utenti dell'organizzazione di modificare o eliminare accidentalmente risorse di importanza fondamentale. Diversamente dal controllo degli accessi in base al ruolo, i blocchi delle risorse consentono di applicare una restrizione a tutti gli utenti e i ruoli. È possibile impostare il livello di blocco *CanNotDelete* o *ReadOnly*.

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/locks/*`. Dei ruoli predefiniti, solo **Proprietario** e **Amministratore Accesso utenti** garantiscono tali azioni.

Per bloccare la macchina virtuale e il gruppo di sicurezza di rete, usare il comando [az lock create](/cli/azure/lock#az_lock_create):

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Per testare i blocchi, provare a eseguire il comando seguente:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Viene visualizzato un messaggio di errore che informa che l'operazione di eliminazione non può essere eseguita a causa di un blocco. Il gruppo di risorse può essere eliminato solo se si rimuovono in modo specifico i blocchi. Questo passaggio è illustrato in [Pulire le risorse](#clean-up-resources).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

I [tag](../../azure-resource-manager/resource-group-using-tags.md) vengono applicati alle risorse di Azure per organizzarle in modo logico in categorie. Ogni tag è costituito da un nome e un valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Per applicare tag a una macchina virtuale, usare il comando [az resource tag](/cli/azure/resource#az_resource_tag). Gli eventuali tag già applicati alla risorsa non vengono conservati.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Trovare le risorse in base al tag

Per trovare le risorse tramite un nome e un valore di tag, usare il comando [az resource list](/cli/azure/resource#az_resource_list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

È possibile usare i valori restituiti per attività di gestione quali l'arresto di tutte le macchine virtuali con un valore di tag.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Visualizzare i costi in base ai valori dei tag

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare il gruppo di sicurezza di rete bloccato solo dopo la rimozione del blocco. Per rimuovere il blocco, recuperare gli ID dei blocchi e fornirli al comando [az lock delete](/cli/azure/lock#az_lock_delete):

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Chiudere la sessione SSH per la macchina virtuale, quindi eliminare le risorse come segue:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine di macchina virtuale personalizzata. Si è appreso come:

> [!div class="checklist"]
> * Assegnare utenti a un ruolo
> * Applicare criteri che applicano gli standard
> * Proteggere le risorse critiche con blocchi
> * Applicare tag alle risorse per semplificare fatturazione e gestione

Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Monitorare le macchine virtuali](tutorial-monitoring.md)

