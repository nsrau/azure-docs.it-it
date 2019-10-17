---
title: Gestire le risorse di Azure usando il portale di Azure | Microsoft Docs
description: Usare il portale di Azure e Azure Resource Manager per gestire le risorse. Viene illustrato come distribuire ed eliminare risorse.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c52d54ca02436f7224346a65048244a5ec8d104c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390404"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gestire le risorse di Azure usando il portale di Azure

Informazioni su come usare la [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per gestire le risorse di Azure. Per la gestione dei gruppi di risorse, vedere [gestire i gruppi di risorse di Azure usando il portale di Azure](./manage-resource-groups-portal.md).

Altri articoli sulla gestione delle risorse:

- [Gestire le risorse di Azure usando l'interfaccia della riga di comando](./manage-resources-cli.md)
- [Gestire le risorse di Azure usando Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire le risorse in un gruppo di risorse

Dopo aver creato un modello di Gestione risorse, è possibile usare il portale di Azure per distribuire le risorse di Azure. Per la creazione di un modello, vedere [Guida introduttiva: creare e distribuire modelli di Azure Resource Manager usando il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Per la distribuzione di un modello tramite il portale, vedere [distribuire risorse con modelli e portale di Azure di gestione risorse](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Apri risorse

Le risorse di Azure sono organizzate in base ai servizi di Azure e ai gruppi di risorse. Le procedure seguenti illustrano come aprire un account di archiviazione denominato **mystorage0207**. La macchina virtuale risiede in un gruppo di risorse denominato **mystorage0207rg**.

Per aprire una risorsa in base al tipo di servizio:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro selezionare il servizio di Azure. In questo caso, gli **account di archiviazione**.  Se il servizio non è elencato, selezionare **tutti i servizi**e quindi selezionare il tipo di servizio.

    ![Aprire la risorsa di Azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selezionare la risorsa che si vuole aprire.

    ![Aprire la risorsa di Azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Un account di archiviazione ha un aspetto simile al seguente:

    ![Aprire la risorsa di Azure nel portale](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Per aprire una risorsa in base al gruppo di risorse:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro selezionare gruppi di **risorse** per elencare la risorsa all'interno del gruppo.
3. Selezionare la risorsa che si vuole aprire. 

## <a name="manage-resources"></a>Gestire risorse

Quando si visualizza una risorsa nel portale, si vedono le opzioni per la gestione di quella particolare risorsa.

![gestire le risorse di Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Lo screenshot mostra le opzioni di gestione per una macchina virtuale di Azure. È possibile eseguire operazioni quali l'avvio, il riavvio e l'arresto di una macchina virtuale.

## <a name="delete-resources"></a>Eliminare le risorse

1. Aprire la risorsa nel portale. Per la procedura, vedere [aprire risorse](#open-resources).
2. Selezionare **Elimina**. Lo screenshot seguente mostra le opzioni di gestione per una macchina virtuale.

    ![Elimina risorsa di Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digitare il nome della risorsa per confermare l'eliminazione, quindi selezionare **Elimina**.

Per altre informazioni su come Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [Azure Resource Manager eliminazione del gruppo di risorse](./resource-group-delete.md).

## <a name="move-resources"></a>Spostare le risorse

1. Aprire la risorsa nel portale. Per la procedura, vedere [aprire risorse](#open-resources).
2. Selezionare **Sposta**. Lo screenshot seguente mostra le opzioni di gestione per un account di archiviazione.

    ![spostare una risorsa di Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selezionare **Sposta in un altro gruppo di risorse** o **Sposta in un'altra sottoscrizione** a seconda delle esigenze.

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloccare le risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche, ad esempio la sottoscrizione di Azure, il gruppo di risorse o la risorsa. 

1. Aprire la risorsa nel portale. Per la procedura, vedere [aprire risorse](#open-resources).
2. Selezionare **blocchi**. Lo screenshot seguente mostra le opzioni di gestione per un account di archiviazione.

    ![blocca risorsa di Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selezionare **Aggiungi**e quindi specificare le proprietà del blocco.

Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

L'assegnazione di tag consente di organizzare logicamente il gruppo di risorse e le risorse. 

1. Aprire la risorsa nel portale. Per la procedura, vedere [aprire risorse](#open-resources).
2. Selezionare **Tag**. Lo screenshot seguente mostra le opzioni di gestione per un account di archiviazione.

    ![contrassegnare la risorsa di Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Specificare le proprietà dei tag e quindi selezionare **Salva**.

Per informazioni, vedere [uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Monitorare le risorse

Quando si apre una risorsa, il portale Visualizza grafici e tabelle predefiniti per il monitoraggio di tale tipo di risorsa. La schermata seguente mostra i grafici per una macchina virtuale:

![monitorare le risorse di Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

È possibile selezionare l'icona Aggiungi nell'angolo superiore destro dei grafici per aggiungere il grafico al dashboard. Per informazioni sull'uso dei dashboard, vedere [Creating and sharing dashboards in the Azure portal](../azure-portal/azure-portal-dashboards.md)(Creazione e condivisione di dashboard nel portale di Azure).

## <a name="manage-access-to-resources"></a>Gestire l'accesso alle risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).
