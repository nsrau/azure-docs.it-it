---
title: Uso del portale di Azure per distribuire le risorse di Azure | Microsoft Docs
description: Utilizzare il portale di Azure e Azure Resource Manager per distribuire le risorse.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 2c98a4aa-8d9f-4a0a-b764-214dbe8ed009
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c0bf0f6ce533e13c9dde0f0b89325a404047a9d8
ms.lasthandoff: 03/31/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md)
> * [Portale](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Questo argomento illustra come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per distribuire le risorse di Azure. Per altre informazioni sulla gestione delle risorse, vedere [Gestire le risorse di Azure mediante il portale](resource-group-portal.md).

Non tutti i servizi attualmente supportano il portale o Gestione risorse. Per questi servizi sarà necessario usare il [portale classico](https://manage.windowsazure.com). Per lo stato di ogni servizio, vedere il [grafico della disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Creare un gruppo di risorse
1. Per creare un gruppo di risorse vuoto, selezionare **Nuovo** > **Gestione** > **Gruppo di risorse**.
   
    ![creare un gruppo di risorse vuoto](./media/resource-group-template-deploy-portal/create-empty-group.png)
2. Assegnare un nome e un percorso e, se necessario, selezionare una sottoscrizione. È necessario specificare un percorso per il gruppo di risorse perché nel gruppo di risorse vengono archiviati i metadati delle risorse. Per motivi di conformità può essere opportuno specificare dove vengono archiviati i metadati. In generale è consigliabile specificare un percorso in cui risiederà la maggior parte delle risorse. Usando lo stesso percorso è possibile semplificare il modello.
   
    ![impostare i valori del gruppo](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Distribuire le risorse da Marketplace
Dopo aver creato il gruppo di risorse, è possibile distribuire le risorse da Marketplace. Marketplace fornisce soluzioni predefinite per scenari comuni.

1. Per avviare una distribuzione, selezionare **Nuovo** e il tipo di risorsa da distribuire. Dopodiché, cercare la versione specifica della risorsa che si desidera distribuire.
   
    ![distribuire risorse](./media/resource-group-template-deploy-portal/deploy-resource.png)
2. Se la specifica soluzione che si desidera distribuire non è visualizzata, è possibile cercarla nel Marketplace.
   
    ![cercare nel Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)
3. A seconda del tipo di risorsa selezionato, sarà necessario impostare una serie di proprietà pertinenti prima della distribuzione. Le opzioni non sono visualizzate in questo articolo, perché variano in base al tipo di risorsa. Per tutti i tipi è necessario selezionare un gruppo di risorse di destinazione. L'immagine seguente mostra come creare un'app Web e distribuirla nel gruppo di risorse creato.
   
    ![Creare un gruppo di risorse](./media/resource-group-template-deploy-portal/select-existing-group.png)
   
    In alternativa, è possibile decidere di creare un gruppo di risorse durante la distribuzione delle risorse. Selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.
   
    ![creare un nuovo gruppo di risorse](./media/resource-group-template-deploy-portal/select-new-group.png)
4. La distribuzione ha inizio. La distribuzione può richiedere alcuni minuti. Al termine della distribuzione verrà visualizzata una notifica.
   
    ![visualizzare notifiche](./media/resource-group-template-deploy-portal/view-notification.png)
5. Dopo aver distribuito le risorse, è possibile aggiungerne altre al gruppo di risorse usando il comando **Aggiungi** nel pannello del gruppo corrispondente.
   
    ![aggiungere una risorsa](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuire risorse da un modello personalizzato
Se si desidera eseguire una distribuzione ma non usare i modelli in Marketplace, è possibile creare un modello personalizzato che definisce l'infrastruttura per la soluzione. Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).

1. Per distribuire un modello personalizzato tramite il portale, selezionare **Nuovo** e quindi iniziare a digitare **Distribuzione modello** nella casella di ricerca fino a quando non è selezionabile nelle opzioni.
   
    ![cercare la distribuzione del modello](./media/resource-group-template-deploy-portal/search-template.png)
2. Selezionare **Distribuzione modello** nelle risorse disponibili.
   
    ![selezionare la distribuzione del modello](./media/resource-group-template-deploy-portal/select-template.png)
3. Dopo aver avviato la distribuzione del modello, aprire il modello vuoto disponibile per la personalizzazione.
   
    ![creare un modello](./media/resource-group-template-deploy-portal/show-custom-template.png)
   
    Nell'editor, aggiungere la sintassi JSON che definisce le risorse da distribuire. Al termine, selezionare **Salva** . Per indicazioni sulla scrittura della sintassi JSON, vedere [Procedura dettagliata per un modello di Resource Manager](resource-manager-template-walkthrough.md).
   
    ![modificare un modello](./media/resource-group-template-deploy-portal/edit-template.png)
4. In alternativa, è possibile selezionare un modello preesistente dai [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). Questi modelli sono forniti dalla community. I modelli sono relativi a molti scenari comuni ed è possibile che qualcuno abbia aggiunto un modello simile a quello che si sta provando a distribuire. È possibile eseguire ricerche nei modelli per trovare aspetti corrispondenti al proprio scenario.
   
    ![selezionare un modello di Guida introduttiva](./media/resource-group-template-deploy-portal/select-quickstart-template.png)
   
    È possibile visualizzare il modello selezionato nell'editor.
5. Dopo aver fornito tutti gli altri valori, selezionare **Crea** per distribuire il modello. 
   
    ![distribuire un modello](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Distribuire risorse da un modello salvato nel proprio account
Il portale consente di salvare un modello nel proprio account Azure e di ridistribuirlo in un secondo momento. Per altre informazioni sull'uso dei modelli salvati, vedere [Introduzione ai modelli privati nel portale di Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Per trovare i modelli salvati, selezionare **Esplora** > **Modelli**.
   
    ![esplorare i modelli](./media/resource-group-template-deploy-portal/browse-templates.png)
2. Nell'elenco dei modelli salvati nel proprio account selezionare quello che si desidera utilizzare.
   
    ![modelli salvati](./media/resource-group-template-deploy-portal/saved-templates.png)
3. Selezionare **Distribuisci** per ridistribuire il modello salvato.
   
    ![distribuire un modello salvato](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare i log di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per risolvere gli errori di distribuzione, vedere [View deployment operations](resource-manager-deployment-operations.md) (Visualizzare le operazioni di distribuzione).
* Per recuperare un modello da un gruppo di risorse o di distribuzione, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
* Per una serie di quattro parti sull'automazione della distribuzione, vedere [Automazione della distribuzione di applicazioni nelle macchine virtuali di Azure](../virtual-machines/windows/dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questa serie illustra argomenti come architettura, accesso e sicurezza, disponibilità, scalabilità e distribuzione delle applicazioni.


