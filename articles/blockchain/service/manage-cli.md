---
title: Gestire il servizio Blockchain di Azure usando l'interfaccia della riga di comando di AzureManage Azure Blockchain Service using Azure CLI
description: Come gestire il servizio Blockchain di Azure con l'interfaccia della riga di comando di AzureHow to manage Azure Blockchain Service with Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455592"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gestire il servizio Blockchain di Azure usando l'interfaccia della riga di comando di AzureManage Azure Blockchain Service using Azure CLI

Oltre al portale di Azure, è possibile usare l'interfaccia della riga di comando di Azure per gestire i membri blockchain e i nodi di transazione per il servizio Blockchain di Azure.In addition to the Azure portal, you can use Azure CLI to manage blockchain members and transaction nodes for your Azure Blockchain Service.

Assicurarsi di aver installato l'interfaccia [della riga](https://docs.microsoft.com/cli/azure/install-azure-cli) di `az login`comando di Azure più recente e di aver eseguito l'accesso a un account Azure con .

Negli esempi seguenti sostituire `<parameter names>` l'esempio con valori personalizzati.

## <a name="create-blockchain-member"></a>Crea membro blockchain

Nell'esempio viene creato un membro blockchain nel servizio Blockchain di Azure che esegue il protocollo di contabilità quorum in un nuovo consorzio.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome viene usato per l'indirizzo dell'endpoint pubblico. Ad esempio: `myblockchainmember.blockchain.azure.com`. |
| **Posizione** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. |
| **password utente** | Password dell'account del membro. Viene usata per eseguire l'autenticazione all'endpoint pubblico del membro della blockchain usando l'autenticazione di base. La password deve soddisfare tre dei seguenti quattro requisiti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1\`numero e 1 carattere speciale diverso dal simbolo di cancelletto, percentuale ()%), virgola (,), stella ('), virgolette doppie(), virgolette singole('), trattino (-) e semicolumn(;)|
| **Protocollo** | L'anteprima pubblica supporta quorum. |
| **Consorzio** | Nome del consorzio da creare o a cui eseguire l'aggiunta. |
| **consortiumManagementAccountPassword** | Password di gestione del consorzio. La password viene utilizzata per entrare a far parte di un consorzio. |
| **Rulename** | Nome della regola per l'inserimento nella whitelist di un intervallo di indirizzi IP. Parametro facoltativo per le regole del firewall.|
| **indirizzo startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'inserimento nella whitelist. Parametro facoltativo per le regole del firewall. |
| **indirizzoipendno** | Fine dell'intervallo di indirizzi IP per l'inserimento nella whitelist. Parametro facoltativo per le regole del firewall. |
| **SkuName (nome sku)** | Tipo di livello di servizio. Usare S0 per Standard e B0 per Basic. |

## <a name="change-blockchain-member-password"></a>Modificare la password del membro blockchain

Ne viene modificata la password di un membro della blockchain.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome che identifica il membro del servizio Blockchain di Azure.Name that identifies your Azure Blockchain Service member. |
| **password utente** | Password dell'account del membro. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1\`numero e 1 carattere speciale diverso dal simbolo di cancelletto, percentuale (%), virgola (,), stella ('), virgolette doppie(), virgolette singole('), trattino (-) e 1 punto e virgola (;). |

## <a name="create-transaction-node"></a>Creare il nodo di transazioni

Creare un nodo di transazione all'interno di un membro blockchain esistente. Aggiungendo nodi di transazione, è possibile aumentare l'isolamento di sicurezza e distribuire il carico. Ad esempio, è possibile avere un endpoint del nodo di transazione per applicazioni client diverse.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome del membro blockchain del servizio Blockchain di Azure che include anche il nuovo nome del nodo della transazione. |
| **Posizione** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. |
| **password utente** | Password del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1\`numero e 1 carattere speciale diverso dal simbolo di cancelletto, percentuale (%), virgola (,), stella ('), virgolette doppie(), virgolette singole('), trattino (-) e 1 punto e virgola (;). |
| **Rulename** | Nome della regola per l'inserimento nella whitelist di un intervallo di indirizzi IP. Parametro facoltativo per le regole del firewall. |
| **indirizzo startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'inserimento nella whitelist. Parametro facoltativo per le regole del firewall. |
| **indirizzoipendno** | Fine dell'intervallo di indirizzi IP per l'inserimento nella whitelist. Parametro facoltativo per le regole del firewall.|

## <a name="change-transaction-node-password"></a>Modificare la password del nodo della transazione

Nell'esempio viene modificata la password di un nodo di transazione.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui sono presenti le risorse del servizio Blockchain di Azure.Resource group name where Azure Blockchain Service resources exist. |
| **name** | Nome del membro blockchain del servizio Blockchain di Azure che include anche il nuovo nome del nodo della transazione. |
| **password utente** | Password del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1\`numero e 1 carattere speciale diverso dal simbolo di cancelletto, percentuale (%), virgola (,), stella ('), virgolette doppie(), virgolette singole('), trattino (-) e 1 punto e virgola (;). |

## <a name="change-consortium-management-account-password"></a>Modificare la password dell'account di gestione del consorzio

L'account di gestione del consorzio viene utilizzato per la gestione dell'appartenenza al consorzio. Ogni membro è identificato in modo univoco da un account di gestione del consorzio ed è possibile modificare la password di questo account con il seguente comando.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome che identifica il membro del servizio Blockchain di Azure.Name that identifies your Azure Blockchain Service member. |
| **consortiumManagementAccountPassword** | Password dell'account di gestione del consorzio. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1\`numero e 1 carattere speciale diverso dal simbolo di cancelletto, percentuale (%), virgola (,), stella ('), virgolette doppie(), virgolette singole('), trattino (-) e 1 punto e virgola (;). |
  
## <a name="update-firewall-rules"></a>Aggiornare le regole del firewall

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui sono presenti le risorse del servizio Blockchain di Azure.Resource group name where Azure Blockchain Service resources exist. |
| **name** | Nome del membro blockchain del servizio Blockchain di Azure.Name of the Azure Blockchain Service blockchain member. |
| **Rulename** | Nome della regola per l'inserimento nella whitelist di un intervallo di indirizzi IP. Parametro facoltativo per le regole del firewall.|
| **indirizzo startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'inserimento nella whitelist. Parametro facoltativo per le regole del firewall.|
| **indirizzoipendno** | Fine dell'intervallo di indirizzi IP per l'inserimento nella whitelist. Parametro facoltativo per le regole del firewall.|

## <a name="list-api-keys"></a>Elenca chiavi API

Le chiavi API possono essere utilizzate per l'accesso ai nodi in modo simile al nome utente e alla password. Esistono due chiavi API per supportare la rotazione delle chiavi. Utilizzare il comando seguente per elencare le chiavi API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui sono presenti le risorse del servizio Blockchain di Azure.Resource group name where Azure Blockchain Service resources exist. |
| **name** | Nome del membro blockchain del servizio Blockchain di Azure che include anche il nuovo nome del nodo della transazione. |

## <a name="regenerate-api-keys"></a>Rigenerare le chiavi APIRegenerate API keys

Utilizzare il comando seguente per rigenerare le chiavi API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui sono presenti le risorse del servizio Blockchain di Azure.Resource group name where Azure Blockchain Service resources exist. |
| **name** | Nome del membro blockchain del servizio Blockchain di Azure che include anche il nuovo nome del nodo della transazione. |
| **Keyname** | Sostituire \<keyValue\> con key1 o key2. |

## <a name="delete-a-transaction-node"></a>Eliminare un nodo di transazioneDelete a transaction node

Nell'esempio viene eliminato un nodo di transazione membro blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui sono presenti le risorse del servizio Blockchain di Azure.Resource group name where Azure Blockchain Service resources exist. |
| **name** | Nome del membro blockchain del servizio Blockchain di Azure che include anche il nome del nodo della transazione da eliminare. |

## <a name="delete-a-blockchain-member"></a>Eliminare un membro blockchainDelete a blockchain member

Nell'esempio viene eliminato un membro blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **gruppo di risorse** | Nome del gruppo di risorse in cui sono presenti le risorse del servizio Blockchain di Azure.Resource group name where Azure Blockchain Service resources exist. |
| **name** | Nome del membro blockchain del servizio Blockchain di Azure da eliminare. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Concedere l'accesso per l'utente di Azure ADGrant access for Azure AD user

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametro | Descrizione |
|---------|-------------|
| **Ruolo** | Nome del ruolo di Azure AD. |
| **assignee** | ID utente di Azure AD. Ad esempio, usare `user@contoso.com` |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain o un nodo di transazione. |

**Esempio:**

Concedere l'accesso al nodo per l'utente di Azure AD al membro blockchain:Grant node access for Azure AD user to blockchain **member:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Esempio:**

Concedere l'accesso al nodo per l'utente di Azure AD al **nodo delle transazioni**blockchain:Grant node access for Azure AD user to blockchain transaction node:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Concedere l'accesso al nodo per il gruppo o il ruolo applicazione di Azure ADGrant node access for Azure AD group or application role

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parametro | Descrizione |
|---------|-------------|
| **Ruolo** | Nome del ruolo di Azure AD. |
| **assignee-object-id** | ID gruppo di Azure AD o ID applicazione. |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain o un nodo di transazione. |

**Esempio:**

Concedere **l'accesso** al nodo per il ruolo applicazioneGrant node access for application role

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Rimuovere l'accesso ai nodi di Azure ADRemove Azure AD node access

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametro | Descrizione |
|---------|-------------|
| **Ruolo** | Nome del ruolo di Azure AD. |
| **assignee** | ID utente di Azure AD. Ad esempio, usare `user@contoso.com` |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain o un nodo di transazione. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare i nodi di transazione del servizio Blockchain](configure-transaction-nodes.md)di Azure con il portale di Azure.
