---
title: Gestire il servizio Azure blockchain usando l'interfaccia della riga di comando
description: Come gestire il servizio Azure blockchain con l'interfaccia della riga di comando di Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170863"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gestire il servizio Azure blockchain usando l'interfaccia della riga di comando

Oltre alla portale di Azure, è possibile usare l'interfaccia della riga di comando di Azure per gestire i membri di blockchain e i nodi di transazione per il servizio blockchain di Azure.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, vedere [Install Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Accedere.

    Accedere usando il comando [az login](/cli/azure/reference-index#az-login) se si usa un'installazione locale dell'interfaccia della riga di comando.

    ```azurecli
    az login
    ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

1. Installare l'estensione dell'interfaccia della riga di comando di Azure.

    Quando si usano riferimenti a estensioni per l'interfaccia della riga di comando di Azure, è prima di tutto necessario installare l'estensione.  Le estensioni dell'interfaccia della riga di comando di Azure offrono l'accesso a comandi sperimentali e non definitivi che non sono stati ancora distribuiti come parte dell'interfaccia della riga di comando di base.  Per altre informazioni sulle estensioni, incluse le procedure di aggiornamento e disinstallazione, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

    Installare l' [estensione per il servizio Azure blockchain](/cli/azure/ext/blockchain/blockchain) eseguendo il comando seguente:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Crea membro blockchain

Esempio [Crea un membro blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) nel servizio blockchain di Azure che esegue il protocollo Ledger quorum in un nuovo Consortium.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **nome** | Nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome viene usato per l'indirizzo dell'endpoint pubblico. Ad esempio: `myblockchainmember.blockchain.azure.com`. |
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. Alcune funzionalità potrebbero non essere disponibili in alcune aree. |
| **password** | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), stella (*), virgolette () \` , virgolette doppie;)|
| **protocol** | Protocollo blockchain. Attualmente è supportato il protocollo *quorum* . |
| **consortium** | Nome del consorzio da creare o a cui eseguire l'aggiunta. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md). |
| **Consortium-Management-account-password** | La password dell'account del consorzio è nota anche come password dell'account del membro. La password dell'account del membro viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. L'account del membro account e la password dell'account del membro si usano per la gestione del consorzio. |
| **SKU** | Tipo di livello di servizio. *Standard* o *Basic*. Usare il livello *Basic* per lo sviluppo, il test e i modelli di verifica. Usare il livello *Standard* per le distribuzioni di produzione. È necessario usare anche il livello *Standard* anche se si usa Blockchain Data Manager o si invia un volume elevato di transazioni private. Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Modificare le password o le regole del firewall del membro blockchain

Esempio [Aggiorna la password di un membro di blockchain, la](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)password di gestione del Consorzio e la regola del firewall.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **nome** | Nome che identifica il membro del servizio Azure blockchain. |
| **password** | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), stella (*), virgolette () \` , virgolette doppie;)|
| **Consortium-Management-account-password** | La password dell'account del consorzio è nota anche come password dell'account del membro. La password dell'account del membro viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. L'account del membro account e la password dell'account del membro si usano per la gestione del consorzio. |
| **Firewall-regole** | Indirizzo IP iniziale e finale per l'elenco di indirizzi IP consentiti. |

## <a name="create-transaction-node"></a>Creare il nodo di transazioni

[Creare un nodo di transazione](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) all'interno di un membro blockchain esistente. Aggiungendo nodi delle transazioni, è possibile aumentare l'isolamento della sicurezza e distribuire il carico. Ad esempio, è possibile disporre di un endpoint del nodo di transazione per diverse applicazioni client.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **location** | Area di Azure del membro blockchain. |
| **nome membro** | Nome che identifica il membro del servizio Azure blockchain. |
| **password** | Password per il nodo della transazione. Utilizzare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), stella (*), virgolette () \` , virgolette doppie;)|
| **nome** | Nome del nodo di transazioni. |

## <a name="change-transaction-node-password"></a>Modificare la password del nodo Transaction

Esempio [Aggiorna la password di un nodo di transazione](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) .

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **nome membro** | Nome che identifica il membro del servizio Azure blockchain. |
| **password** | Password per il nodo della transazione. Utilizzare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non è un simbolo di cancelletto (#), percentuale (%), virgola (,), stella (*), virgolette () \` , virgolette doppie;)|
| **nome** | Nome del nodo di transazioni. |

## <a name="list-api-keys"></a>Elencare le chiavi API

Le chiavi API possono essere usate per l'accesso ai nodi in modo analogo a nome utente e password. Sono disponibili due chiavi API per supportare la rotazione della chiave. Usare il comando seguente per [elencare le chiavi API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **nome** | Nome del membro blockchain del servizio Azure blockchain |

## <a name="regenerate-api-keys"></a>Rigenera chiavi API

Usare il comando seguente per [rigenerare le chiavi API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key).

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **nome** | Nome del membro blockchain del servizio Azure blockchain. |
| **keyName** | Sostituire \<keyValue\> con Key1, Key2 o entrambi. |

## <a name="delete-a-transaction-node"></a>Eliminare un nodo di transazione

Esempio [Elimina un nodo di transazione membro blockchain](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **nome membro** | Nome del membro blockchain del servizio Azure blockchain che include anche il nome del nodo di transazione da eliminare. |
| **nome** | Nome del nodo di transazione da eliminare. |

## <a name="delete-a-blockchain-member"></a>Eliminare un membro blockchain

Esempio [Elimina un membro blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui si trovano le risorse del servizio Azure blockchain. |
| **nome** | Nome del membro blockchain del servizio Azure blockchain da eliminare. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Concedi l'accesso per Azure AD utente

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametro | Descrizione |
|---------|-------------|
| **ruolo** | Nome del ruolo Azure AD. |
| **assignee** | ID utente Azure AD. Ad esempio, usare `user@contoso.com` |
| **ambito** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain o un nodo di transazione. |

**Esempio:**

Concessione dell'accesso al nodo per Azure AD utente al **membro**blockchain:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Esempio:**

Concessione dell'accesso al nodo per Azure AD utente al **nodo di transazione**blockchain:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Concessione dell'accesso al nodo per Azure AD gruppo o ruolo applicazione

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parametro | Descrizione |
|---------|-------------|
| **ruolo** | Nome del ruolo Azure AD. |
| **assegnatario-oggetto-ID** | ID gruppo Azure AD o ID applicazione. |
| **ambito** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain o un nodo di transazione. |

**Esempio:**

Concessione dell'accesso al nodo per il **ruolo applicazione**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Rimuovi accesso Azure AD nodo

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametro | Descrizione |
|---------|-------------|
| **ruolo** | Nome del ruolo Azure AD. |
| **assignee** | ID utente Azure AD. Ad esempio, usare `user@contoso.com` |
| **ambito** | Ambito dell'assegnazione di ruolo. Può essere un membro blockchain o un nodo di transazione. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare i nodi delle transazioni del servizio blockchain di Azure con il portale di Azure](configure-transaction-nodes.md).
