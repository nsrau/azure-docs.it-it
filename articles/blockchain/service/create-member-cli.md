---
title: Creare un servizio Azure Blockchain con l'interfaccia della riga di comando di Azure
description: Usare il servizio Azure Blockchain per creare un membro della blockchain usando l'interfaccia della riga di comando di Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 88b0336d7be1852d1bb2ae522f3d37ebed8d1fa0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029989"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Guida introduttiva: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure

Il servizio Azure Blockchain è una piattaforma per blockchain che consente di eseguire la logica di business in un contratto intelligente. Questa guida di avvio rapido illustra come iniziare a creare un membro della blockchain usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.51 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Creare un membro della blockchain

Creare un membro della blockchain nel servizio Azure Blockchain che esegue il protocollo di libro mastro Quorum in un nuovo consorzio.

È necessario passare diversi parametri e proprietà. Sostituire i parametri seguenti con i propri valori.

| Parametro | DESCRIZIONE |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. Usare il gruppo di risorse creato nella sezione precedente.
| **nome** | Nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome viene usato per l'indirizzo dell'endpoint pubblico. Ad esempio: `myblockchainmember.blockchain.azure.com`.
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure.
| **password** | Password dell'account del membro. Viene usata per eseguire l'autenticazione all'endpoint pubblico del membro della blockchain usando l'autenticazione di base.
| **consortium** | Nome del consorzio da creare o a cui eseguire l'aggiunta.
| **consortiumManagementAccountPassword** | Password di gestione del consorzio. Viene usata per l'aggiunta a un consorzio.
| **skuName** | Tipo di livello di servizio. Usare S0 per Standard e B0 per Basic.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

La creazione del membro della blockchain e delle risorse di supporto richiede circa 10 minuti.

Di seguito è riportato l'output di esempio di un'operazione di creazione completata correttamente.

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il membro della blockchain creato per l'esercitazione o la guida di avvio rapido successiva. Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato dal servizio Azure Blockchain.

Eseguire questo comando per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un membro della blockchain, provare una delle guide di avvio rapido per la connessione al nodo della transazione con [Geth](connect-geth.md), [MetaMask](connect-metamask.md) o [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Usare Truffle per connettersi a una rete del servizio Azure Blockchain](connect-truffle.md)
