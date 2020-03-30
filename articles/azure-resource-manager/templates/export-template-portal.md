---
title: Esporta modello nel portale di AzureExport template in Azure portal
description: Usare il portale di Azure per esportare un modello di Azure Resource Manager dalle risorse nella sottoscrizione.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273735"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Esportazione di risorse singole e multi-risorsa in un modello nel portale di AzureSingle and multi-resource export to a template in Azure portal

Per facilitare la creazione di modelli di Azure Resource Manager, è possibile esportare un modello dalle risorse esistenti. Il modello esportato consente di comprendere la sintassi JSON e le proprietà che distribuiscono le risorse. Per automatizzare le distribuzioni future, iniziare con il modello esportato e modificarlo per lo scenario.

Gestione risorse consente di selezionare una o più risorse per l'esportazione in un modello. Puoi concentrarti esattamente sulle risorse necessarie nel modello.

In questo articolo viene illustrato come esportare i modelli tramite il portale. È anche possibile usare [l'interfaccia della riga di comando](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)di Azure , Azure [PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)o [l'API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Scegli l'opzione di esportazione giusta

Per esportare un modello sono disponibili due modi:

* **Esportare dal gruppo di risorse o dalla risorsa**. Questa opzione genera un nuovo modello dalle risorse esistenti. Il modello esportato è uno "snapshot" dello stato corrente del gruppo di risorse. È possibile esportare un intero gruppo di risorse o risorse specifiche all'interno di tale gruppo di risorse.

* **Esportare prima della distribuzione o dalla cronologia**. Questa opzione recupera una copia esatta di un modello utilizzato per la distribuzione.

A seconda dell'opzione scelta, i modelli esportati hanno qualità diverse.

| Da gruppo di risorse o risorsa | Prima della distribuzione o dalla cronologia |
| --------------------- | ----------------- |
| Il modello è uno snapshot dello stato corrente delle risorse. Include tutte le modifiche manuali apportate dopo la distribuzione. | Il modello mostra solo lo stato delle risorse al momento della distribuzione. Le modifiche manuali apportate dopo la distribuzione non vengono incluse. |
| È possibile selezionare le risorse da un gruppo di risorse da esportare. | Sono incluse tutte le risorse per una distribuzione specifica. Non è possibile selezionare un sottoinsieme di tali risorse o aggiungere risorse aggiunte in un momento diverso. |
| Il modello include tutte le proprietà per le risorse, incluse alcune proprietà che normalmente non vengono impostate durante la distribuzione. È possibile rimuovere o eliminare queste proprietà prima di riutilizzare il modello. | Il modello include solo le proprietà necessarie per la distribuzione. Il modello è pronto per l'uso. |
| Il modello probabilmente non include tutti i parametri necessari per il riutilizzo. La maggior parte dei valori di proprietà sono hardcoded nel modello. Per ridistribuire il modello in altri ambienti, è necessario aggiungere parametri che aumentano la possibilità di configurare le risorse.  È possibile deselezionare **Includi parametri** in modo da poter creare parametri personalizzati. | Il modello include parametri che semplificano la ridistribuzione in ambienti diversi. |

Esportare il modello da un gruppo di risorse o una risorsa, quando:Export the template from a resource group or resource, when:

* È necessario acquisire le modifiche alle risorse apportate dopo la distribuzione originale.
* Si desidera selezionare le risorse da esportare.

Esportare il modello prima della distribuzione o dalla cronologia, quando:Export the template before deployment or from the history, when:

* Si desidera un modello facile da riutilizzare.
* Non è necessario includere le modifiche apportate dopo la distribuzione originale.

## <a name="limitations"></a>Limitazioni

Quando si esegue l'esportazione da un gruppo di risorse o una risorsa, il modello esportato viene generato dagli [schemi pubblicati](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) per ogni tipo di risorsa. In alcuni casi, lo schema non dispone della versione più recente per un tipo di risorsa. Controllare il modello esportato per assicurarsi che includa le proprietà necessarie. Se necessario, modificare il modello esportato per usare la versione dell'API necessaria.

La funzionalità del modello di esportazione non supporta l'esportazione delle risorse di Azure Data Factory.The export template feature doesn't support exporting Azure Data Factory resources. Per informazioni su come esportare le risorse di Data Factory, vedere [Copiare o clonare una data factory in Azure Data Factory.](https://aka.ms/exportTemplateViaAdf)

Per esportare le risorse create tramite il modello di distribuzione classica, è necessario [eseguirne la migrazione al modello](https://aka.ms/migrateclassicresourcetoarm)di distribuzione di Resource Manager.

## <a name="export-template-from-a-resource-group"></a>Esportare il modello da un gruppo di risorseExport template from a resource group

Per esportare una o più risorse da un gruppo di risorse:To export one or more resources from a resource group:

1. Selezionare il gruppo di risorse che contiene le risorse da esportare.

1. Selezionare una o più risorse selezionando le caselle di controllo.  Per selezionare tutto, selezionare la casella di controllo a sinistra di **Nome**. La voce di menu **Esporta modello** viene abilitata solo dopo aver selezionato almeno una risorsa.

   ![Esporta tutte le risorse](./media/export-template-portal/select-all-resources.png)

    Nella schermata è selezionato solo l'account di archiviazione.
1. Selezionare **Esporta modello**.

1. Viene visualizzato il modello esportato ed è disponibile per il download e la distribuzione.

   ![Mostrare il modello](./media/export-template-portal/show-template.png)

   **Includi parametri** è selezionato per impostazione predefinita.  Quando questa opzione è selezionata, tutti i parametri del modello verranno inclusi quando viene generato il modello. Se desideri creare i tuoi parametri, attiva questa casella di controllo per non includerli.

## <a name="export-template-from-a-resource"></a>Esportare un modello da una risorsaExport template from a resource

Per esportare una risorsa:

1. Selezionare il gruppo di risorse contenente la risorsa da esportare.

1. Selezionare la risorsa che si desidera esportare per aprire la risorsa.

1. Per tale risorsa, selezionare **Esporta modello** nel riquadro sinistro.

   ![Esporta risorsa](./media/export-template-portal/export-single-resource.png)

1. Viene visualizzato il modello esportato ed è disponibile per il download e la distribuzione. Il modello contiene solo la singola risorsa. **Includi parametri** è selezionato per impostazione predefinita.  Quando questa opzione è selezionata, tutti i parametri del modello verranno inclusi quando viene generato il modello. Se desideri creare i tuoi parametri, attiva questa casella di controllo per non includerli.

## <a name="export-template-before-deployment"></a>Esportare il modello prima della distribuzioneExport template before deployment

1. Selezionare il servizio di Azure da distribuire.

1. Immettere i valori per il nuovo servizio.

1. Dopo aver superato la convalida, ma prima di avviare la distribuzione, selezionare **Scarica un modello per l'automazione**.

   ![Scaricare il modello](./media/export-template-portal/download-before-deployment.png)

1. Il modello viene visualizzato ed è disponibile per il download e la distribuzione.


## <a name="export-template-after-deployment"></a>Esporta modello dopo la distribuzione

È possibile esportare il modello utilizzato per distribuire le risorse esistenti. Il modello che si ottiene è esattamente quello che è stato utilizzato per la distribuzione.

1. Selezionare il gruppo di risorse da esportare.

1. Selezionare il collegamento in **Distribuzioni**.

   ![Selezionare la cronologia di distribuzione](./media/export-template-portal/select-deployment-history.png)

1. Selezionare una delle distribuzioni dalla cronologia di distribuzione.

   ![Selezionare la distribuzione](./media/export-template-portal/select-details.png)

1. Selezionare **Modello**. Il modello utilizzato per questa distribuzione viene visualizzato ed è disponibile per il download.

   ![Selezionare il modello](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come esportare i modelli con [l'interfaccia della riga di comando](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)di Azure , Azure [PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)o [l'API REST.](/rest/api/resources/resourcegroups/exporttemplate)
- Per informazioni sulla sintassi dei modelli di Resource Manager, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](template-syntax.md)
- Per informazioni su come sviluppare modelli, consultate le [esercitazioni dettagliate.](/azure/azure-resource-manager/)
- Per visualizzare gli schemi di modello di Azure Resource Manager, vedere [riferimento al modello](/azure/templates/).
