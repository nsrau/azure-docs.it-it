---
title: Creare un membro del servizio Azure Blockchain - Interfaccia della riga di comando di Azure
description: Creare un membro del servizio Azure Blockchain per un consorzio blockchain con l'interfaccia della riga di comando di Azure
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: f97aab59d38e9b15838a78d0227bc2848615cd92
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504365"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Guida introduttiva: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure

In questo argomento di avvio rapido si distribuisce un nuovo membro e un consorzio blockchain nel servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

No.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa guida di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.51 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Accedere.

    Accedere usando il comando [az login](/cli/azure/reference-index#az-login) se si usa un'installazione locale dell'interfaccia della riga di comando.

    ```azurecli
    az login
    ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

1. Installare l'estensione dell'interfaccia della riga di comando di Azure.

    Quando si usano riferimenti a estensioni per l'interfaccia della riga di comando di Azure, è prima di tutto necessario installare l'estensione.  Le estensioni dell'interfaccia della riga di comando di Azure offrono l'accesso a comandi sperimentali e non definitivi che non sono stati ancora distribuiti come parte dell'interfaccia della riga di comando di base.  Per altre informazioni sulle estensioni, incluse le procedure di aggiornamento e disinstallazione, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

    Installare l'[estensione del servizio Azure Blockchain](/cli/azure/ext/blockchain/blockchain) eseguendo il comando seguente:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Creare un gruppo di risorse.

    Il servizio Azure Blockchain, come tutte le risorse di Azure, deve essere distribuito in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.

    Per questo avvio rapido, creare un gruppo di risorse denominato _myResourceGroup_ nell'area _eastus_ con il comando [az group create](/cli/azure/group#az-group-create) seguente:

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Creare un membro della blockchain

Un membro del servizio Azure Blockchain è un nodo blockchain in una rete blockchain del consorzio privato. Quando si effettua il provisioning di un membro, è possibile creare o partecipare alla rete di un consorzio. Per una rete del consorzio è necessario almeno un membro. Il numero di membri di blockchain necessari per i partecipanti dipende dallo scenario. I partecipanti del consorzio possono avere uno o più membri di blockchain oppure possono condividere i membri con altri partecipanti. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md).

È necessario passare diversi parametri e proprietà. Sostituire i parametri dell'esempio con i propri valori.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. Usare il gruppo di risorse creato nella sezione precedente.
| **nome** | Nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome viene usato per l'indirizzo dell'endpoint pubblico. Ad esempio: `myblockchainmember.blockchain.azure.com`.
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `westus2`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. Alcune funzionalità potrebbero non essere disponibili in alcune aree. Azure Blockchain Data Manager è disponibile nelle aree di Azure seguenti: ovvero Stati Uniti orientali ed Europa occidentale.
| **password** | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain.
| **protocol** | Protocollo Blockchain. Attualmente è supportato il protocollo *Quorum*.
| **consortium** | Nome del consorzio da creare o a cui eseguire l'aggiunta. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md).
| **consortium-management-account-password** | La password dell'account del consorzio è nota anche come password dell'account del membro. La password dell'account del membro viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. L'account del membro account e la password dell'account del membro si usano per la gestione del consorzio.
| **sku** | Tipo di livello di servizio. *Standard* o *Basic*. Usare il livello *Basic* per lo sviluppo, il test e i modelli di verifica. Usare il livello *Standard* per le distribuzioni di produzione. È necessario usare anche il livello *Standard* anche se si usa Blockchain Data Manager o si invia un volume elevato di transazioni private. Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard.

La creazione del membro della blockchain e delle risorse di supporto richiede circa 10 minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il membro della blockchain creato per l'esercitazione o la guida di avvio rapido successiva. Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato per l'avvio rapido.

Eseguire questo comando per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati distribuiti un membro del servizio Azure Blockchain e un nuovo consorzio. Provare l'avvio rapido successivo per usare Azure Blockchain Development Kit per Ethereum per collegarsi a un membro del servizio Azure Blockchain.

> [!div class="nextstepaction"]
> [Usare Visual Studio Code per connettersi al servizio Azure Blockchain](connect-vscode.md)
