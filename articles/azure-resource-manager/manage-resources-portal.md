---
title: Gestire le risorse di Azure usando il portale di Azure | Microsoft Docs
description: Usare il portale di Azure e Azure Resource Manager per gestire le risorse.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550705"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gestire le risorse di Azure usando il portale di Azure

Informazioni su come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per gestire le risorse di Azure. Per la gestione dei gruppi di risorse, vedere [gruppi di risorse di Azure di gestire tramite il portale di Azure](./manage-resource-groups-portal.md).

Altri articoli sulla gestione delle risorse:

- [Gestire le risorse di Azure utilizzando CLI di Azure](./manage-resources-cli.md)
- [Gestire le risorse di Azure usando Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire risorse in un gruppo di risorse

Dopo aver creato un modello di Resource Manager, è possibile usare il portale di Azure per distribuire le risorse di Azure. Per creare un modello, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Per distribuire un modello usando il portale, vedere [distribuire le risorse con modelli di Resource Manager e portale di Azure](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Risorse aperte

Le risorse di Azure sono ordinate per servizi di Azure e da gruppi di risorse. Le procedure seguenti viene illustrato come aprire un account di archiviazione denominato **mystorage0207**. La macchina virtuale si trova in un gruppo di risorse denominato **mystorage0207rg**.

Per aprire una risorsa dal tipo di servizio:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro, selezionare il servizio di Azure. In questo caso **gli account di archiviazione**.  Se il servizio indicato non è visualizzato, selezionare **tutti i servizi**e quindi selezionare il tipo di servizio.

    ![aprire risorse di azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selezionare la risorsa che si desidera aprire.

    ![aprire risorse di azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Un account di archiviazione è simile a:

    ![aprire risorse di azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Per aprire un gruppo di risorse da risorse:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro, selezionare **gruppi di risorse** per elencare le risorse all'interno del gruppo.
3. Selezionare la risorsa che si desidera aprire. 

## <a name="manage-resources"></a>Gestire risorse

Quando si visualizza una risorsa nel portale, si vedono le opzioni per la gestione di quella particolare risorsa.

![gestire le risorse di Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Lo screenshot Mostra le opzioni di gestione per una macchina virtuale di Azure. È possibile eseguire operazioni come avvio, il riavvio e arresto di una macchina virtuale.

## <a name="delete-resources"></a>Eliminare le risorse

1. Aprire la risorsa nel portale. Per istruzioni, vedere [aprire risorse](#open-resources).
2. Selezionare **Elimina**. Lo screenshot seguente mostra le opzioni di gestione per una macchina virtuale.

    ![eliminare risorse di azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digitare il nome della risorsa per confermare l'eliminazione e quindi selezionare **Elimina**.

Per altre informazioni sulla modalità Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [eliminazione del gruppo di risorse Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Spostare le risorse

1. Aprire la risorsa nel portale. Per istruzioni, vedere [aprire risorse](#open-resources).
2. Selezionare **Sposta**. Lo screenshot seguente mostra le opzioni di gestione per un account di archiviazione.

    ![spostare risorse di azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selezionare **spostare in un altro gruppo di risorse** oppure **Moeve a un'altra sottoscrizione** in base alle esigenze.

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloccare le risorse

Il blocco impedisce ad altri utenti nell'organizzazione modifichino o eliminino accidentalmente risorse critiche, come sottoscrizione di Azure, gruppo di risorse o risorsa. 

1. Aprire la risorsa nel portale. Per istruzioni, vedere [aprire risorse](#open-resources).
2. Selezionare **blocca**. Lo screenshot seguente mostra le opzioni di gestione per un account di archiviazione.

    ![risorsa di blocco di azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selezionare **Add**e quindi specificare le proprietà di blocco.

Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

L'assegnazione di tag consente di organizzare logicamente il gruppo di risorse e le risorse. 

1. Aprire la risorsa nel portale. Per istruzioni, vedere [aprire risorse](#open-resources).
2. Selezionare **Tag**. Lo screenshot seguente mostra le opzioni di gestione per un account di archiviazione.

    ![risorse di azure di tag](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Specificare le proprietà di tag e quindi selezionare **salvare**.

Per informazioni, vedere [usando i tag per organizzare le risorse di Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Monitorare le risorse

Quando si apre una risorsa, il portale Visualizza grafici e tabelle per il monitoraggio di quel tipo di risorsa predefiniti. Lo screenshot seguente mostra i grafici per una macchina virtuale:

![risorse di monitoraggio di azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

È possibile selezionare l'icona della puntina in alto a destra dei grafici per aggiungere il grafico al dashboard. Per informazioni sull'uso dei dashboard, vedere [Creating and sharing dashboards in the Azure portal](../azure-portal/azure-portal-dashboards.md)(Creazione e condivisione di dashboard nel portale di Azure).

## <a name="manage-access-to-resources"></a>Gestire l'accesso alle risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per altre informazioni sulla sintassi del modello di Resource Manager, vedere [comprendere la struttura e sintassi di modelli Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere la [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere [riferimento a un modello](/azure/templates/).