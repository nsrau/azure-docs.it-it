---
title: Gestire il servizio di Blockchain di Azure con Azure CLI
description: Come creare e gestire il servizio di Azure Blockchain con Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154448"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Gestire il servizio Azure Blockchain con Azure CLI

Oltre al portale di Azure, è possibile usare CLI di Azure per creare e gestire rapidamente i membri di blockchain e nodi delle transazioni per il servizio di Azure Blockchain.

Assicurarsi di aver installato la versione più recente [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e ha effettuato l'accesso a un account di Azure con `az login`.

Negli esempi seguenti, sostituire l'esempio `<parameter names>` con i propri valori.

## <a name="create-blockchain-member"></a>Creare il membro blockchain

Esempio viene creato un membro di blockchain nel servizio di Blockchain di Azure che esegue il protocollo di contabilità Quorum in un nuovo consortium.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome viene usato per l'indirizzo dell'endpoint pubblico. Ad esempio: `myblockchainmember.blockchain.azure.com`. |
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. |
| **password** | Password dell'account del membro. Viene usata per eseguire l'autenticazione all'endpoint pubblico del membro della blockchain usando l'autenticazione di base. La password deve soddisfare tre dei quattro requisiti seguenti: lunghezza deve essere compresa tra 12 e 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale non numerico sign(#), identificaz, virgola (,), star(*), eseguire il backup preventivo (\`), fare doppio quote("), quote(') singolo, trattini (-) e semicolumn(;)|
| **protocol** | Supporta la versione di anteprima pubblica del Quorum. |
| **consortium** | Nome del consorzio da creare o a cui eseguire l'aggiunta. |
| **consortiumManagementAccountPassword** | Password di gestione del consorzio. La password viene usata per la partecipazione a un consortium. |
| **ruleName** | Nome della regola per un intervallo di indirizzi IP nell'elenco elementi consentiti. Parametro facoltativo per le regole del firewall.|
| **startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'inserimento nella Whitelist. Parametro facoltativo per le regole del firewall. |
| **endIpAddress** | Fine dell'intervallo di indirizzi IP per l'inserimento nella Whitelist. Parametro facoltativo per le regole del firewall. |
| **skuName** | Tipo di livello di servizio. Usare S0 per Standard e B0 per Basic. |

## <a name="change-blockchain-member-password"></a>Modificare la password del membro blockchain

Esempio di modifica password di un membro blockchain.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome che identifica il membro servizio Blockchain di Azure. |
| **password** | Password dell'account del membro. La password deve soddisfare tre dei quattro requisiti seguenti: lunghezza deve essere compresa tra 12 e 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale non numerico sign(#), identificaz, virgola (,), star(*), eseguire il backup preventivo (\`), fare doppio quote("), quote(') singolo, trattini (-) e semicolon(;). |


## <a name="create-transaction-node"></a>Creare il nodo di transazioni

Creare un nodo transazione all'interno di un membro di blockchain esistente. Quando si aggiungono nodi di transazione, è possibile incrementare l'isolamento di sicurezza e distribuire il carico. Ad esempio, hai un endpoint del nodo delle transazioni per le applicazioni client diverse.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome del servizio di Azure Blockchain blockchain membro che include anche il nuovo nome del nodo delle transazioni. |
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. |
| **password** | La password di nodo di transazione. La password deve soddisfare tre dei quattro requisiti seguenti: lunghezza deve essere compresa tra 12 e 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale non numerico sign(#), identificaz, virgola (,), star(*), eseguire il backup preventivo (\`), fare doppio quote("), quote(') singolo, trattini (-) e semicolon(;). |
| **ruleName** | Nome della regola per un intervallo di indirizzi IP nell'elenco elementi consentiti. Parametro facoltativo per le regole del firewall. |
| **startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'inserimento nella Whitelist. Parametro facoltativo per le regole del firewall. |
| **endIpAddress** | Fine dell'intervallo di indirizzi IP per l'inserimento nella Whitelist. Parametro facoltativo per le regole del firewall.|

## <a name="change-transaction-node-password"></a>Modificare la password del nodo delle transazioni

Esempio viene modificata una password di nodo di transazione.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome gruppo di risorse in cui sono presenti delle risorse del servizio di Azure Blockchain. |
| **name** | Nome del servizio di Azure Blockchain blockchain membro che include anche il nuovo nome del nodo delle transazioni. |
| **password** | La password di nodo di transazione. La password deve soddisfare tre dei quattro requisiti seguenti: lunghezza deve essere compresa tra 12 e 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale non numerico sign(#), identificaz, virgola (,), star(*), eseguire il backup preventivo (\`), fare doppio quote("), quote(') singolo, trattini (-) e semicolon(;). |

## <a name="change-consortium-management-account-password"></a>Password dell'account Gestione consortium modifica

L'account di gestione consortium viene usato per la gestione delle appartenenze consortium. Ogni membro viene identificata da un account di gestione consortium ed è possibile modificare la password dell'account con il comando seguente.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **name** | Nome che identifica il membro servizio Blockchain di Azure. |
| **consortiumManagementAccountPassword** | La password dell'account Gestione consortium. La password deve soddisfare tre dei quattro requisiti seguenti: lunghezza deve essere compresa tra 12 e 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale non numerico sign(#), identificaz, virgola (,), star(*), eseguire il backup preventivo (\`), fare doppio quote("), quote(') singolo, trattini (-) e semicolon(;). |
  
## <a name="update-firewall-rules"></a>Aggiornare le regole del firewall

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome gruppo di risorse in cui sono presenti delle risorse del servizio di Azure Blockchain. |
| **name** | Nome del servizio di Azure Blockchain blockchain membro. |
| **ruleName** | Nome della regola per un intervallo di indirizzi IP nell'elenco elementi consentiti. Parametro facoltativo per le regole del firewall.|
| **startIpAddress** | Inizio dell'intervallo di indirizzi IP per l'inserimento nella Whitelist. Parametro facoltativo per le regole del firewall.|
| **endIpAddress** | Fine dell'intervallo di indirizzi IP per l'inserimento nella Whitelist. Parametro facoltativo per le regole del firewall.|

## <a name="list-api-keys"></a>Chiavi di elenco API

Le chiavi API sono utilizzabile per l'accesso nodo simile a nome utente e password. Sono disponibili due chiavi API per supportare la rotazione della chiave. Usare il comando seguente per elencare le chiavi API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome gruppo di risorse in cui sono presenti delle risorse del servizio di Azure Blockchain. |
| **name** | Nome del servizio di Azure Blockchain blockchain membro che include anche il nuovo nome del nodo delle transazioni. |

## <a name="regenerate-api-keys"></a>Rigenerare le chiavi API

Usare il comando seguente per rigenerare le chiavi API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome gruppo di risorse in cui sono presenti delle risorse del servizio di Azure Blockchain. |
| **name** | Nome del servizio di Azure Blockchain blockchain membro che include anche il nuovo nome del nodo delle transazioni. |
| **keyName** | Sostituire \<keyValue\> con key1 o key2. |

## <a name="delete-a-transaction-node"></a>Eliminare un nodo transazione

Esempio elimina il nodo di una transazione di membro blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome gruppo di risorse in cui sono presenti delle risorse del servizio di Azure Blockchain. |
| **name** | Nome del servizio di Azure Blockchain blockchain membro che include anche il nuovo nome di nodo di transazione da eliminare. |

## <a name="delete-a-blockchain-member"></a>Eliminare un membro di blockchain

Esempio di Elimina un membro di blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome gruppo di risorse in cui sono presenti delle risorse del servizio di Azure Blockchain. |
| **name** | Nome del servizio di Azure Blockchain blockchain membro da eliminare. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Concedere l'accesso per utente di Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametro | Descrizione |
|---------|-------------|
| **role** | Nome del ruolo di Azure AD. |
| **assignee** | ID utente Azure AD. Ad esempio: `user@contoso.com` |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain del nodo della transazione. |

**Esempio:**

Concedere l'accesso di nodo per utente di Azure AD fino alle blockchain **membro**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Esempio:**

Concedere l'accesso di nodo per utente di Azure AD fino alle blockchain **nodo transazione**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Concedere l'accesso a nodi per gruppo di Azure AD o l'applicazione ruolo

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parametro | Descrizione |
|---------|-------------|
| **role** | Nome del ruolo di Azure AD. |
| **assignee-object-id** | ID del gruppo Azure AD o applicazione ID. |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain del nodo della transazione. |

**Esempio:**

Concedere l'accesso di nodo per **ruolo applicazione**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Rimuovere l'accesso di nodo di Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametro | Descrizione |
|---------|-------------|
| **role** | Nome del ruolo di Azure AD. |
| **assignee** | ID utente Azure AD. Ad esempio: `user@contoso.com` |
| **scope** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain del nodo della transazione. |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare i nodi delle transazioni di servizio di Azure Blockchain con il portale di Azure](configure-transaction-nodes.md)
