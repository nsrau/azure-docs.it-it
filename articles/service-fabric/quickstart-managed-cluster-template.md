---
title: Distribuire un cluster gestito di Service Fabric (anteprima) con Azure Resource Manager
description: Informazioni su come creare un cluster gestito di Service Fabric con un modello di Azure Resource Manager
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410420"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Avvio rapido: Distribuire un cluster gestito di Service Fabric (anteprima) con modello di Azure Resource Manager

I cluster gestiti di Service Fabric sono un'evoluzione del modello di risorse cluster di Azure Service Fabric che semplifica l'esperienza di gestione della distribuzione e dei cluster. I cluster gestiti di Service Fabric sono una risorsa completamente incapsulata che consente di distribuire una singola risorsa cluster di Service Fabric invece di dover distribuire tutte le risorse sottostanti del cluster. Questo articolo descrive come distribuire un cluster gestito di Service Fabric in Azure usando un modello di Azure Resource Manager (modello ARM).

Il cluster SKU Basic a tre nodi distribuito in questa esercitazione deve essere usato solo a scopo istruttivo (e non per i carichi di lavoro di produzione). Per altre informazioni, vedere [SKU dei cluster gestiti di Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo avvio rapido:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di cluster di Service Fabric in Azure Samples](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT).

## <a name="create-a-client-certificate"></a>Creare un certificato client

I cluster gestiti di Service Fabric usano un certificato client come chiave per il controllo di accesso. Se si ha già un certificato client che si vuole usare per il controllo di accesso al cluster, è possibile ignorare questo passaggio.

Se è necessario creare un nuovo certificato client, seguire la procedura descritta in [Impostare e recuperare un certificato da Azure Key Vault](../key-vault/certificates/quick-create-portal.md).

Prendere nota dell'identificazione personale del certificato perché verrà chiesto di specificarla per distribuire il modello nel passaggio successivo.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello.

      [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti

    Per questo avvio rapido, specificare i propri valori per i parametri del modello seguenti:

    * **Sottoscrizione** Selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: Selezionare **Crea nuovo**. Specificare un nome univoco per il gruppo di risorse, ad esempio *myResourceGroup* e quindi scegliere **OK**.
    * **Località**: selezionare una località, ad esempio **eastus2**. Le aree supportate per i cluster gestiti di Service Fabric includono `centraluseuap`, `eastus2euap`, `eastasia`, `northeurope`, `westcentralus` e `eastus2`.
    * **Nome del cluster**: immettere un nome univoco per il cluster, ad esempio *mysfcluster*.
    * **Nome utente amministratore**: immettere un nome per l'amministratore da usare per RDP nelle macchine virtuali sottostanti del cluster.
    * **Password amministratore**: immettere una password per l'amministratore da usare per RDP nelle macchine virtuali sottostanti del cluster.
    * **Identificazione personale del certificato client**: specificare l'identificazione personale del certificato client che si vuole usare per accedere al cluster. Se non si ha un certificato, seguire la procedura riportata in [Impostare e recuperare un certificato](../key-vault/certificates/quick-create-portal.md) per creare un certificato autofirmato.
    * **Nome del tipo di nodo**: immettere un nome univoco per il tipo di nodo, ad esempio *nt1*.
    * **Accetto le condizioni riportate sopra**: selezionare questa casella per accettare. 

3. Selezionare **Acquisto**.

4. Per la distribuzione del cluster gestito di Service Fabric sono necessari alcuni minuti. Attendere il completamento della distribuzione prima di proseguire con i passaggi successivi.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

### <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Al termine della distribuzione, cercare il valore Service Fabric Explorer nell'output e aprire l'indirizzo in un Web browser per visualizzare il cluster in Service Fabric Explorer. Quando viene richiesto un certificato, usare il certificato per il quale è stata specificata l'identificazione personale del client nel modello.

> [!NOTE]
> È possibile trovare l'output della distribuzione nella scheda distribuzioni del gruppo di risorse nel portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse per il cluster gestito di Service Fabric. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella *Cerca* nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido è stato distribuito un cluster gestito di Service Fabric. Per altre informazioni su come ridimensionare un cluster, vedere:

> [!div class="nextstepaction"]
> [Aumentare il numero di istanze in un cluster gestito di Service Fabric](tutorial-managed-cluster-scale.md)
