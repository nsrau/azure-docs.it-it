---
title: 'Guida introduttiva: Creare una query condivisa con i modelli'
description: Questa guida di avvio rapido illustra come usare un modello di Resource Manager per creare una query condivisa di Resource Graph che conta le macchine virtuali in base al sistema operativo.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254242"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Guida introduttiva: Creare una query condivisa usando un modello di Resource Manager

Le query di Resource Graph possono essere salvate come _query private_ o _query condivise_. Una query privata viene salvata nel profilo del portale dei singoli utenti e non è visibile ad altri. Una query condivisa è un oggetto di Resource Manager che può essere condiviso con altri utenti tramite le autorizzazioni e l'accesso in base al ruolo. Una query condivisa fornisce un'esecuzione comune e coerente dell'individuazione delle risorse. Questa guida di avvio rapido usa un modello di Resource Manager per creare una query condivisa.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-shared-query"></a>Creare una query condivisa

In questa guida di avvio rapido verrà creata una query condivisa denominata _Conta macchine virtuali per sistema operativo_. Per provare questa query nell'SDK o nel portale con Resource Graph Explorer, vedere [Esempi - Contare le macchine virtuali per tipo di sistema operativo](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

Nel modello è definita la risorsa seguente:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Distribuire il modello

> [!NOTE]
> Il servizio Azure Resource Graph è gratuito. Per altre informazioni, vedere [Panoramica di Azure Resource Graph](./overview.md).

1. Fare clic sull'immagine seguente per accedere al portale di Azure e aprire il modello:

   [![Distribuire il modello in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Selezionare o immettere i valori seguenti:

   | Nome | valore |
   |------|-------|
   | Subscription | Selezionare la sottoscrizione di Azure. |
   | Resource group | Selezionare **Crea nuovo**, specificare un nome e quindi fare clic su **OK**. |
   | Location | Scegliere un'area, Ad esempio **Stati Uniti centrali**. |
   | Nome query | Lasciare il valore predefinito **Conta macchine virtuali per sistema operativo**. |
   | Codice query | Lasciare il valore predefinito `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Descrizione query | Lasciare il valore predefinito **This shared query counts all virtual machine resources and summarizes by the OS type** (Questa query condivisa conta tutte le risorse macchina virtuale e le riepiloga in base al tipo di sistema operativo). |
   | Accetto le condizioni riportate sopra | (selezionare) |

1. Selezionare **Acquisto**.

Alcune risorse aggiuntive:

- Per altri modelli di esempio, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Per informazioni di riferimento sul modello, vedere [Informazioni di riferimento sui modelli di Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Per informazioni su come sviluppare modelli di Resource Manager, vedere [Documentazione di Resource Manager in Azure](../../azure-resource-manager/management/overview.md).
- Per informazioni sulla distribuzione a livello di sottoscrizione, vedere [Creare gruppi di risorse e risorse a livello di sottoscrizione](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Per eseguire la nuova query condivisa, seguire questa procedura:

1. Dalla barra di ricerca del portale cercare **Query di Resource Graph** e selezionare la voce.

1. Selezionare la query condivisa denominata **Conta macchine virtuali per sistema operativo**, quindi selezionare la scheda **Risultati** nella pagina **Panoramica**.

In alternativa, è possibile aprire la query condivisa da Resource Graph Explorer:

1. Dalla barra di ricerca del portale cercare **Resource Graph Explorer** e selezionare la voce.

1. Selezionare il pulsante **Apri una query**.

1. Modificare il valore di **Tipo** e impostarlo su _Query condivise_. Se la query **Conta macchine virtuali per sistema operativo** non è visualizzata nell'elenco, usare la casella filtro per limitare i risultati. Quando la query condivisa **Conta macchine virtuali per sistema operativo** è visibile, selezionarne il nome.

1. Una volta caricata la query, selezionare il pulsante **Esegui query**. I risultati vengono visualizzati nella scheda **Risultati** riportata sotto.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere la query condivisa creata, seguire questa procedura:

1. Dalla barra di ricerca del portale cercare **Query di Resource Graph** e selezionare la voce.

1. Selezionare la casella di controllo accanto alla query condivisa denominata **Conta macchine virtuali per sistema operativo**.

1. Selezionare il pulsante **Elimina** nella parte superiore della pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata una query condivisa di Resource Graph.

Per altre informazioni sulle query condivise, passare all'esercitazione:

> [!div class="nextstepaction"]
> [Gestire le query nel portale di Azure](./tutorials/create-share-query.md)