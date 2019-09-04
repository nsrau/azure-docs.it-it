---
title: Gestire il servizio Azure blockchain usando l'interfaccia della riga di comando
description: Come creare e gestire il servizio Azure blockchain con l'interfaccia della riga di comando di Azure
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: ee7e529593960c3a7c62021225122370c122b3c4
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240978"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gestire il servizio Azure blockchain usando l'interfaccia della riga di comando

Oltre alla portale di Azure, è possibile usare l'interfaccia della riga di comando di Azure per gestire i membri di blockchain e i nodi di transazione per il servizio blockchain di Azure.

Assicurarsi di aver installato la versione più recente dell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e di avere `az login`effettuato l'accesso a un account Azure in con.

Negli esempi seguenti sostituire example `<parameter names>` con valori personalizzati.

## <a name="create-blockchain-member"></a>Crea membro blockchain

Esempio crea un membro blockchain nel servizio blockchain di Azure che esegue il protocollo Ledger quorum in un nuovo Consortium.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome viene usato per l'indirizzo dell'endpoint pubblico. Ad esempio `myblockchainmember.blockchain.azure.com`. |
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. |
| **password** | Password dell'account del membro. Viene usata per eseguire l'autenticazione all'endpoint pubblico del membro della blockchain usando l'autenticazione di base. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), asterisco (*), virgolette indietro (\`), virgolette doppie ("), virgolette singole ('), trattini (-) e semicolumn (;)|
| **protocol** | L'anteprima pubblica supporta il quorum. |
| **consortium** | Nome del consorzio da creare o a cui eseguire l'aggiunta. |
| **consortiumManagementAccountPassword** | Password di gestione del consorzio. La password viene usata per l'aggiunta a un consorzio. |
| **ruleName** | Nome regola per l'elenco elementi consentiti di un intervallo di indirizzi IP. Parametro facoltativo per le regole del firewall.|
| **startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'elenco elementi consentiti. Parametro facoltativo per le regole del firewall. |
| **endIpAddress** | Fine dell'intervallo di indirizzi IP per l'elenco elementi consentiti. Parametro facoltativo per le regole del firewall. |
| **skuName** | Tipo di livello di servizio. Usare S0 per Standard e B0 per Basic. |

## <a name="change-blockchain-member-password"></a>Modificare la password del membro blockchain

Esempio modifica la password di un membro di blockchain.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parametro | DESCRIZIONE |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome che identifica il membro del servizio Azure blockchain. |
| **password** | Password dell'account del membro. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), asterisco (*), virgolette indietro (\`), virgolette doppie ("), virgolette singole ('), trattini (-) e punto e virgola (;). |

## <a name="create-transaction-node"></a>Creare il nodo di transazioni

Creare un nodo di transazione all'interno di un membro blockchain esistente. Aggiungendo nodi delle transazioni, è possibile aumentare l'isolamento della sicurezza e distribuire il carico. Ad esempio, è possibile disporre di un endpoint del nodo di transazione per diverse applicazioni client.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome del membro blockchain del servizio Azure blockchain che include anche il nome del nuovo nodo della transazione. |
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. |
| **password** | Password del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), asterisco (*), virgolette indietro (\`), virgolette doppie ("), virgolette singole ('), trattini (-) e punto e virgola (;). |
| **ruleName** | Nome regola per l'elenco elementi consentiti di un intervallo di indirizzi IP. Parametro facoltativo per le regole del firewall. |
| **startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'elenco elementi consentiti. Parametro facoltativo per le regole del firewall. |
| **endIpAddress** | Fine dell'intervallo di indirizzi IP per l'elenco elementi consentiti. Parametro facoltativo per le regole del firewall.|

## <a name="change-transaction-node-password"></a>Modificare la password del nodo Transaction

Esempio modifica la password di un nodo di transazione.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parametro | DESCRIZIONE |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **name** | Nome del membro blockchain del servizio Azure blockchain che include anche il nome del nuovo nodo della transazione. |
| **password** | Password del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), asterisco (*), virgolette indietro (\`), virgolette doppie ("), virgolette singole ('), trattini (-) e punto e virgola (;). |

## <a name="change-consortium-management-account-password"></a>Modificare la password dell'account di gestione del Consorzio

L'account di gestione del Consorzio viene usato per la gestione delle appartenenze ai consorzi. Ogni membro viene identificato in modo univoco da un account di gestione del Consorzio ed è possibile modificare la password di questo account con il comando seguente.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parametro | DESCRIZIONE |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome che identifica il membro del servizio Azure blockchain. |
| **consortiumManagementAccountPassword** | Password dell'account di gestione del Consorzio. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), asterisco (*), virgolette indietro (\`), virgolette doppie ("), virgolette singole ('), trattini (-) e punto e virgola (;). |
  
## <a name="update-firewall-rules"></a>Aggiornare le regole del firewall

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parametro | DESCRIZIONE |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **name** | Nome del membro blockchain del servizio Azure blockchain. |
| **ruleName** | Nome regola per l'elenco elementi consentiti di un intervallo di indirizzi IP. Parametro facoltativo per le regole del firewall.|
| **startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'elenco elementi consentiti. Parametro facoltativo per le regole del firewall.|
| **endIpAddress** | Fine dell'intervallo di indirizzi IP per l'elenco elementi consentiti. Parametro facoltativo per le regole del firewall.|

## <a name="list-api-keys"></a>Elencare le chiavi API

Le chiavi API possono essere usate per l'accesso ai nodi in modo analogo a nome utente e password. Sono disponibili due chiavi API per supportare la rotazione della chiave. Usare il comando seguente per elencare le chiavi API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **name** | Nome del membro blockchain del servizio Azure blockchain che include anche il nome del nuovo nodo della transazione. |

## <a name="regenerate-api-keys"></a>Rigenera chiavi API

Usare il comando seguente per rigenerare le chiavi API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **name** | Nome del membro blockchain del servizio Azure blockchain che include anche il nome del nuovo nodo della transazione. |
| **keyName** | Sostituire \<il valore\> di un valore con Key1 o Key2. |

## <a name="delete-a-transaction-node"></a>Eliminare un nodo di transazione

Esempio elimina un nodo di transazione membro blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **name** | Nome del membro blockchain del servizio Azure blockchain che include anche il nome del nuovo nodo di transazione da eliminare. |

## <a name="delete-a-blockchain-member"></a>Eliminare un membro blockchain

Esempio elimina un membro blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **name** | Nome del membro blockchain del servizio Azure blockchain da eliminare. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Concedi l'accesso per Azure AD utente

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametro | DESCRIZIONE |
|---------|-------------|
| **role** | Nome del ruolo Azure AD. |
| **assignee** | ID utente Azure AD. Ad esempio: `user@contoso.com` |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain del nodo Transaction. |

**Esempio:**

Concessione dell'accesso al nodo per Azure AD utente al **membro**blockchain:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Esempio:**

Concessione dell'accesso al nodo per Azure AD utente al **nodo di transazione**blockchain:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Concessione dell'accesso al nodo per Azure AD gruppo o ruolo applicazione

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parametro | Descrizione |
|---------|-------------|
| **role** | Nome del ruolo Azure AD. |
| **assignee-object-id** | ID gruppo Azure AD o ID applicazione. |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain del nodo Transaction. |

**Esempio:**

Concessione dell'accesso al nodo per il **ruolo applicazione**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Rimuovi accesso Azure AD nodo

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametro | Descrizione |
|---------|-------------|
| **role** | Nome del ruolo Azure AD. |
| **assignee** | ID utente Azure AD. Ad esempio: `user@contoso.com` |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain del nodo Transaction. |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare i nodi delle transazioni del servizio blockchain di Azure con il portale di Azure](configure-transaction-nodes.md)
