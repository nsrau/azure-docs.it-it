---
title: Uso del portale di Azure per distribuire le risorse di Azure | Microsoft Docs
description: Utilizzare il portale di Azure e Azure Resource Manager per distribuire le risorse.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 39680d4e70cc47cfa119b3a46e8d51ea87fa618d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure

Questo argomento illustra come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per distribuire le risorse di Azure. Per altre informazioni sulla gestione delle risorse, vedere [Gestire le risorse di Azure mediante il portale](resource-group-portal.md).

## <a name="create-resource-group"></a>Creare un gruppo di risorse

1. Per creare un gruppo di risorse vuoto, selezionare **Gruppi di risorse**.

   ![Selezionare i gruppi di risorse](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. In Gruppi di risorse selezionare **Aggiungi**.

   ![Aggiungere il gruppo di risorse](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Assegnare un nome e un percorso e, se necessario, selezionare una sottoscrizione. È necessario specificare un percorso per il gruppo di risorse perché nel gruppo di risorse vengono archiviati i metadati delle risorse. Per motivi di conformità può essere opportuno specificare dove vengono archiviati i metadati. In generale è consigliabile specificare un percorso in cui risiederà la maggior parte delle risorse. Usando lo stesso percorso è possibile semplificare il modello.

   ![Impostare i valori del gruppo](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Al termine dell'operazione di impostazione delle proprietà selezionare **Crea**.

1. Per visualizzare il nuovo gruppo di risorse, selezionare **Aggiorna**.

   ![Aggiornare i gruppi di risorse](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Distribuire le risorse da Marketplace

Dopo aver creato il gruppo di risorse, è possibile distribuire le risorse da Marketplace. Marketplace fornisce soluzioni predefinite per scenari comuni.

1. Per avviare una distribuzione, selezionare **Crea una risorsa**.

   ![Nuova risorsa](./media/resource-group-template-deploy-portal/new-resources.png)

1. Trovare il tipo di risorsa che si vuole distribuire.

   ![Selezionare il tipo di risorsa](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Se la specifica soluzione che si desidera distribuire non è visualizzata, è possibile cercarla nel Marketplace. Ad esempio, per trovare una soluzione Wordpress, iniziare a digitare **Wordpress** e selezionare l'opzione desiderata.

   ![Cercare nel Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. A seconda del tipo di risorsa selezionato, sarà necessario impostare una serie di proprietà pertinenti prima della distribuzione. Per tutti i tipi è necessario selezionare un gruppo di risorse di destinazione. L'immagine seguente mostra come creare un'app Web e distribuirla nel gruppo di risorse creato.

   ![Creare un gruppo di risorse](./media/resource-group-template-deploy-portal/select-existing-group.png)

   In alternativa, è possibile decidere di creare un gruppo di risorse durante la distribuzione delle risorse. Selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.

   ![Creare un nuovo gruppo di risorse](./media/resource-group-template-deploy-portal/select-new-group.png)

1. La distribuzione ha inizio. La distribuzione può richiedere alcuni minuti. Al termine della distribuzione verrà visualizzata una notifica.

   ![Visualizzare la notifica](./media/resource-group-template-deploy-portal/view-notification.png)

1. Dopo avere distribuito le risorse, è possibile aggiungerne altre al gruppo di risorse selezionando il comando **Aggiungi**.

   ![Aggiungere una risorsa](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuire risorse da un modello personalizzato

Se si desidera eseguire una distribuzione ma non usare i modelli in Marketplace, è possibile creare un modello personalizzato che definisce l'infrastruttura per la soluzione. Per informazioni sulla creazione di modelli, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).

1. Per distribuire un modello personalizzato tramite il portale, selezionare **Crea una risorsa** e quindi cercare **Distribuzione modello** fino a quando non è selezionabile nelle opzioni.

   ![Cercare la distribuzione del modello](./media/resource-group-template-deploy-portal/search-template.png)

1. Selezionare **Create**.

   ![Selezionare Crea](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Verranno visualizzate diverse opzioni per la creazione di un modello. Selezionare **Creare un modello personalizzato nell'editor**.

   ![Visualizzare le opzioni](./media/resource-group-template-deploy-portal/see-options.png)

1. È disponibile un modello vuoto che è possibile personalizzare.

   ![Creare il modello](./media/resource-group-template-deploy-portal/blank-template.png)

1. È possibile modificare manualmente la sintassi JSON o selezionare un modello predefinito dalla [raccolta di modelli di avvio rapido](https://azure.microsoft.com/resources/templates/). Per questo articolo usare tuttavia l'opzione **Aggiungi risorsa**.

   ![Modifica del modello](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Fornire **Account di archiviazione** e fornire un nome. Dopo avere fornito i valori, selezionare **OK**.

   ![Selezionare l'account di archiviazione](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. L'editor aggiunge automaticamente JSON per il tipo di risorsa. Si noti che include un parametro per la definizione del tipo di account di archiviazione. Selezionare **Salva**.

   ![Mostrare il modello](./media/resource-group-template-deploy-portal/show-json.png)

1. A questo punto è possibile distribuire le risorse definite nel modello. Per eseguire la distribuzione, accettare i termini e le condizioni e selezionare **Acquista**.

   ![Distribuire il modello](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Distribuire risorse da un modello salvato nel proprio account

Il portale consente di salvare un modello nel proprio account Azure e di ridistribuirlo in un secondo momento. Per altre informazioni sull'uso dei modelli salvati, vedere [Introduzione ai modelli privati nel portale di Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Per trovare i modelli salvati, selezionare **Altri servizi**.

   ![Altri servizi](./media/resource-group-template-deploy-portal/more-services.png)

1. Cercare i **modelli** e selezionare questa opzione.

   ![Cercare i modelli](./media/resource-group-template-deploy-portal/find-templates.png)

1. Nell'elenco dei modelli salvati nel proprio account selezionare quello che si desidera utilizzare.

   ![Modelli salvati](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Selezionare **Distribuisci** per ridistribuire il modello salvato.

   ![Distribuire il modello salvato](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare i log di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per risolvere gli errori di distribuzione, vedere [View deployment operations](resource-manager-deployment-operations.md) (Visualizzare le operazioni di distribuzione).
* Per recuperare un modello da un gruppo di risorse o di distribuzione, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
