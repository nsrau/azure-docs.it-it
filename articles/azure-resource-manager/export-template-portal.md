---
title: Esportare il modello di Azure Resource Manager usando il portale di Azure
description: Usare il portale di Azure per esportare un modello di Azure Resource Manager da risorse nella sottoscrizione.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515385"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Esportazione singola e a più risorse al modello nel portale di Azure

Per facilitare la creazione di modelli di Azure Resource Manager, è possibile esportare un modello da risorse esistenti. Il modello esportato aiuta a comprendere la sintassi JSON e proprietà di distribuire le risorse. Per automatizzare le distribuzioni future, iniziare con il modello esportato e modificarlo per lo scenario.

Resource Manager ti permette di selezionare una o più risorse per l'esportazione in un modello. È possibile concentrarsi sulla esattamente le risorse che necessarie nel modello.

## <a name="choose-the-right-export-option"></a>Scegliere l'opzione di esportazione corretto

Per esportare un modello sono disponibili due modi:

* **Esportare dal gruppo di risorse o risorsa**. Questa opzione genera un nuovo modello da risorse esistenti. Il modello esportato è uno "snapshot" dello stato corrente del gruppo di risorse. È possibile esportare un intero gruppo di risorse o risorse specifiche all'interno di tale gruppo di risorse.

* **Esportare prima della distribuzione o dalla cronologia**. Questa opzione consente di recuperare una copia esatta di un modello utilizzato per la distribuzione.

A seconda dell'opzione che scelto, i modelli esportati hanno caratteristiche diverse.

| Dal gruppo di risorse o risorsa | Prima della distribuzione o dalla cronologia |
| --------------------- | ----------------- |
| Modello è uno snapshot dello stato corrente delle risorse. Include le modifiche manuali apportate dopo la distribuzione. | Mostra solo lo stato delle risorse al momento della distribuzione. Eventuali modifiche manuali apportate dopo la distribuzione non sono inclusi. |
| È possibile selezionare le risorse da un gruppo di risorse da esportare. | Sono incluse tutte le risorse per una distribuzione specifica. Non è possibile selezionare un subset di tali risorse o aggiungere risorse che sono stati aggiunti in un momento diverso. |
| Modello include tutte le proprietà per le risorse, tra cui alcune proprietà che è non sarebbe in genere impostato durante la distribuzione. Si potrebbe voler rimuovere o eliminare queste proprietà prima di riusare il modello. | Modello include solo le proprietà necessarie per la distribuzione. Il modello è pronto da usare. |
| Modello probabilmente non include tutti i parametri che necessari per il riutilizzo. La maggior parte dei valori delle proprietà vengono impostate come hardcoded nel modello. Per ridistribuire il modello in altri ambienti, è necessario aggiungere i parametri che aumentano la possibilità di configurare le risorse. | Modello include i parametri che rendono semplice ridistribuire in ambienti diversi. |

Esportare il modello da un gruppo di risorse o risorsa, quando:

* È necessario acquisire le modifiche alle risorse che sono state apportate dopo la distribuzione originale.
* Si vuole selezionare le risorse a cui vengono esportate.

Esportare il modello prima della distribuzione o dalla cronologia quando:

* Si desidera un modello semplice riutilizzo.
* Non è necessario includere le modifiche apportate dopo la distribuzione originale.

## <a name="export-template-from-resource-group"></a>Esportare il modello da un gruppo di risorse

Per esportare una o più risorse da un gruppo di risorse:

1. Selezionare il gruppo di risorse che contiene le risorse da esportare.

1. Per esportare tutte le risorse nel gruppo di risorse, selezionare tutte e quindi **Esporta modello**. Il **Esporta modello** opzione risulta abilitata solo dopo avere selezionato almeno una risorsa.

   ![Esportare tutte le risorse](./media/export-template-portal/select-all-resources.png)

1. Per acquisire risorse specifiche per l'esportazione, selezionare le caselle di controllo accanto a tali risorse. Quindi, selezionare **Esporta modello**.

   ![Selezionare le risorse da esportare](./media/export-template-portal/select-resources.png)

1. Il modello esportato viene visualizzato e sia disponibile per il download.

   ![Mostra modello](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Esporta modello dalla risorsa

Per esportare una risorsa:

1. Selezionare il gruppo di risorse che contiene la risorsa da esportare.

1. Selezionare la risorsa da esportare.

   ![Selezione risorsa](./media/export-template-portal/select-link-resource.png)

1. Per la risorsa, selezionare **Esporta modello** nel riquadro sinistro.

   ![Esporta risorsa](./media/export-template-portal/export-single-resource.png)

1. Il modello esportato viene visualizzato e sia disponibile per il download. Il modello contiene solo la singola risorsa.

## <a name="export-template-before-deployment"></a>Esportare il modello prima della distribuzione

1. Selezionare il servizio di Azure da distribuire.

1. Immettere i valori per il nuovo servizio.

1. Dopo il superamento della convalida, ma prima di avviare la distribuzione, selezionare **scaricare un modello per l'automazione**.

   ![Scarica modello](./media/export-template-portal/download-before-deployment.png)

1. Il modello viene visualizzato e può essere scaricato.

   ![Mostra modello](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Esportare il modello dopo la distribuzione

È possibile esportare il modello che è stato usato per distribuire le risorse esistenti. Il modello che viene visualizzato è esattamente quello che è stato usato per la distribuzione.

1. Selezionare il gruppo di risorse da esportare.

1. Selezionare il collegamento sotto **distribuzioni**.

   ![Selezionare la cronologia di distribuzione](./media/export-template-portal/select-deployment-history.png)

1. Selezionare una delle distribuzioni dalla cronologia della distribuzione.

   ![Selezionare la distribuzione](./media/export-template-portal/select-details.png)

1. Selezionare **modello**. Il modello usato per questa distribuzione viene visualizzato e può essere scaricato.

   ![Seleziona modello](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per altre informazioni sulla sintassi del modello di Resource Manager, vedere [comprendere la struttura e sintassi di modelli Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere la [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere [riferimento a un modello](/azure/templates/).