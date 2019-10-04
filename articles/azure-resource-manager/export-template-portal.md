---
title: Esportare il modello di Azure Resource Manager usando il portale di Azure
description: Usare portale di Azure per esportare un modello di Azure Resource Manager dalle risorse nella sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: tomfitz
ms.openlocfilehash: 15f527dfe517dcb3329b8b61243d7c5054eedb56
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959706"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Esportazione di una singola e più risorse in un modello in portale di Azure

Per semplificare la creazione di modelli di Azure Resource Manager, è possibile esportare un modello da risorse esistenti. Il modello esportato consente di comprendere la sintassi e le proprietà JSON che distribuiscono le risorse. Per automatizzare le distribuzioni future, iniziare con il modello esportato e modificarlo per lo scenario.

Gestione risorse consente di selezionare una o più risorse da esportare in un modello. È possibile concentrarsi esattamente sulle risorse necessarie nel modello.

Questo articolo illustra come esportare i modelli tramite il portale. È anche possibile usare l'interfaccia della riga di comando di [Azure](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)o l' [API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Scegliere l'opzione di esportazione corretta

Per esportare un modello sono disponibili due modi:

* **Esportare da un gruppo di risorse o una risorsa**. Questa opzione genera un nuovo modello da risorse esistenti. Il modello esportato è uno "snapshot" dello stato corrente del gruppo di risorse. È possibile esportare un intero gruppo di risorse o risorse specifiche all'interno di tale gruppo di risorse.

* **Esporta prima della distribuzione o dalla cronologia**. Questa opzione consente di recuperare una copia esatta di un modello utilizzato per la distribuzione.

A seconda dell'opzione scelta, i modelli esportati hanno qualità differenti.

| Da gruppo di risorse o risorsa | Prima della distribuzione o dalla cronologia |
| --------------------- | ----------------- |
| Il modello è uno snapshot dello stato corrente delle risorse. Sono incluse eventuali modifiche manuali apportate dopo la distribuzione. | Il modello Mostra solo lo stato delle risorse al momento della distribuzione. Eventuali modifiche manuali apportate dopo la distribuzione non sono incluse. |
| È possibile selezionare le risorse da esportare in un gruppo di risorse. | Sono incluse tutte le risorse per una distribuzione specifica. Non è possibile selezionare un subset di tali risorse o aggiungere risorse aggiunte in un momento diverso. |
| Il modello include tutte le proprietà per le risorse, incluse alcune proprietà che in genere non vengono impostate durante la distribuzione. Potrebbe essere necessario rimuovere o pulire queste proprietà prima di riutilizzare il modello. | Il modello include solo le proprietà necessarie per la distribuzione. Il modello è pronto per l'uso. |
| Il modello probabilmente non include tutti i parametri necessari per il riutilizzo. La maggior parte dei valori delle proprietà è hardcoded nel modello. Per ridistribuire il modello in altri ambienti, è necessario aggiungere parametri che aumentano la capacità di configurare le risorse. | Il modello include parametri che facilitano la ridistribuzione in ambienti diversi. |

Esportare il modello da un gruppo di risorse o una risorsa quando:

* È necessario acquisire le modifiche apportate alle risorse effettuate dopo la distribuzione originale.
* Si desidera selezionare le risorse esportate.

Esportare il modello prima della distribuzione o dalla cronologia, quando:

* Si desidera un modello di facile utilizzo.
* Non è necessario includere le modifiche apportate dopo la distribuzione originale.

## <a name="export-template-from-resource-group"></a>Esportare il modello da un gruppo di risorse

Per esportare una o più risorse da un gruppo di risorse:

1. Selezionare il gruppo di risorse che contiene le risorse che si desidera esportare.

1. Per esportare tutte le risorse nel gruppo di risorse, selezionare tutti e quindi **Esporta modello**. L'opzione **Esporta modello** viene abilitata solo dopo aver selezionato almeno una risorsa.

   ![Esporta tutte le risorse](./media/export-template-portal/select-all-resources.png)

1. Per selezionare risorse specifiche per l'esportazione, selezionare le caselle di controllo accanto a tali risorse. Selezionare quindi **Esporta modello**.

   ![Selezionare le risorse da esportare](./media/export-template-portal/select-resources.png)

1. Il modello esportato viene visualizzato ed è disponibile per il download.

   ![Mostrare il modello](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Esporta modello dalla risorsa

Per esportare una risorsa:

1. Selezionare il gruppo di risorse contenente la risorsa che si vuole esportare.

1. Selezionare la risorsa da esportare.

   ![Selezione risorsa](./media/export-template-portal/select-link-resource.png)

1. Per tale risorsa selezionare **Esporta modello** nel riquadro sinistro.

   ![Esporta risorsa](./media/export-template-portal/export-single-resource.png)

1. Il modello esportato viene visualizzato ed è disponibile per il download. Il modello contiene solo la risorsa singola.

## <a name="export-template-before-deployment"></a>Esporta modello prima della distribuzione

1. Selezionare il servizio di Azure che si vuole distribuire.

1. Immettere i valori per il nuovo servizio.

1. Dopo aver superato la convalida, ma prima di avviare la distribuzione selezionare **scaricare un modello per l'automazione**.

   ![Scarica modello](./media/export-template-portal/download-before-deployment.png)

1. Il modello viene visualizzato ed è disponibile per il download.

   ![Mostrare il modello](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Esporta modello dopo la distribuzione

È possibile esportare il modello usato per distribuire le risorse esistenti. Il modello che si ottiene è esattamente quello usato per la distribuzione.

1. Selezionare il gruppo di risorse che si vuole esportare.

1. Selezionare il collegamento in **distribuzioni**.

   ![Seleziona cronologia di distribuzione](./media/export-template-portal/select-deployment-history.png)

1. Selezionare una delle distribuzioni dalla cronologia di distribuzione.

   ![Selezionare la distribuzione](./media/export-template-portal/select-details.png)

1. Selezionare **modello**. Viene visualizzato il modello usato per questa distribuzione ed è disponibile per il download.

   ![Seleziona modello](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come esportare modelli con l'interfaccia della riga di comando di [Azure](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)o l' [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).
