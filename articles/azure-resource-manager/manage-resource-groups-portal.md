---
title: Gestire i gruppi di Azure Resource Manager usando il portale di Azure | Microsoft Docs
description: Usare il portale di Azure per gestire i gruppi di Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 559c1874c119eabef2c35a954961c1e669df3c06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507220"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gestire i gruppi di risorse di Azure Resource Manager usando il portale di Azure

Informazioni su come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per gestire i gruppi di risorse di Azure. Per gestire le risorse di Azure, vedere [la gestione di Azure Resource Manager usando il portale di Azure](./manage-resources-portal.md).

Altri articoli sulla gestione dei gruppi di risorse:

- [Gestire i gruppi di risorse di Azure utilizzando CLI di Azure](./manage-resources-cli.md)
- [Gestire i gruppi di risorse di Azure usando Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica in cui vengono archiviati i metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **gruppi di risorse**

    ![aggiungere un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selezionare **Aggiungi**.
4. Immettere i valori seguenti:

   - **Sottoscrizione** Selezionare la sottoscrizione di Azure. 
   - **Gruppo di risorse**: Immettere il nome di un nuovo gruppo di risorse. 
   - **Area**: Selezionare una località di Azure, ad esempio **Stati Uniti centrali**.

     ![Creare un gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selezionare **Rivedi e crea**
6. Selezionare **Create**. Bastano pochi secondi per creare un gruppo di risorse.
7. Selezionare **Aggiorna** dal menu in alto per aggiornare l'elenco di gruppi di risorse e quindi selezionare il gruppo di risorse appena creato per aprirlo. Oppure selezionare **Notification**(l'icona a forma di campanello) dall'inizio, quindi selezionare **passare al gruppo di risorse** per aprire il gruppo di risorse appena creato

    ![passare al gruppo di risorse](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Per elencare i gruppi di risorse, selezionare **gruppi di risorse**

    ![esplorare i gruppi di risorse](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Per personalizzare le informazioni visualizzate per i gruppi di risorse, selezionare **Upravit sloupce**. Lo screenshot seguente mostra le colonne di aggiunta che è possibile aggiungere alla visualizzazione:

## <a name="open-resource-groups"></a>Gruppi di risorse Open

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Gruppi di risorse**.
3. Selezionare il gruppo di risorse che si desidera aprire.

## <a name="delete-resource-groups"></a>Eliminare gruppi di risorse

1. Aprire il gruppo di risorse che si desidera eliminare.  Visualizzare [aprire gruppi di risorse](#open-resource-groups).
2. Selezionare **Elimina gruppo di risorse**.

    ![eliminare il gruppo di risorse di azure](./media/manage-resource-groups-portal/delete-group.png)

Per altre informazioni sulla modalità Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [eliminazione del gruppo di risorse Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Distribuire risorse in un gruppo di risorse

Dopo aver creato un modello di Resource Manager, è possibile usare il portale di Azure per distribuire le risorse di Azure. Per creare un modello, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Per distribuire un modello usando il portale, vedere [distribuire le risorse con modelli di Resource Manager e portale di Azure](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Spostare in un gruppo di risorse o sottoscrizione

È possibile spostare le risorse nel gruppo a un altro gruppo di risorse. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Gruppi di risorse di blocco

Il blocco impedisce ad altri utenti nell'organizzazione modifichino o eliminino accidentalmente risorse critiche, come sottoscrizione di Azure, gruppo di risorse o risorsa. 

1. Aprire il gruppo di risorse che si desidera eliminare.  Visualizzare [aprire gruppi di risorse](#open-resource-groups).
2. Nel riquadro sinistro, selezionare **blocca**.
3. Per aggiungere un blocco per il gruppo di risorse, selezionare **Add**.
4. Immettere **il nome del blocco**, **tipo di blocco**, e **note**. I tipi di blocco includono **Read-only**, e **eliminare**.

    ![gruppo di risorse di azure di blocco](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Per altre informazioni, vedere [bloccare le risorse per impedire modifiche impreviste](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Gruppi di risorse di tag

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere [usando i tag per organizzare le risorse di Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Esportare i gruppi di risorse in modelli

Per informazioni sull'esportazione di modelli, vedere [esportazione singola e a più risorse al modello - portale](export-template-portal.md).

### <a name="fix-export-issues"></a>Risolvere i problemi di esportazione

Non tutti i tipi di risorse supportano la funzione di esportazione del modello. Si verificano problemi di esportazione solo quando si esporta da un gruppo di risorse invece che dalla cronologia della distribuzione. Se la distribuzione più recente rappresenta con precisione lo stato corrente del gruppo di risorse, è consigliabile esportare il modello dalla cronologia della distribuzione invece che dal gruppo di risorse. Eseguire l'esportazione da un gruppo di risorse solo quando sono state apportate al gruppo di risorse modifiche non definite in un singolo modello.

Per risolvere i problemi di esportazione, aggiungere manualmente le risorse mancanti al modello. Il messaggio di errore include i tipi di risorsa che non possono essere esportati. Trovare il tipo di risorsa nelle [informazioni di riferimento sui modelli](/azure/templates/). Per aggiungere manualmente un gateway di rete virtuale, ad esempio, vedere le [informazioni di riferimento sul modello Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). Il riferimento al modello fornisce il formato JSON per aggiungere la risorsa al modello.

Dopo avere ottenuto il formato JSON per la risorsa, è necessario ottenere i valori della risorsa. È possibile ottenere i valori per la risorsa usando l'operazione GET nell'API REST per il tipo di risorsa. Ad esempio, per ottenere i valori per il gateway di rete virtuale, vedere [Gateway di rete virtuale - Get](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per altre informazioni sulla sintassi del modello di Resource Manager, vedere [comprendere la struttura e sintassi di modelli Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere la [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere [riferimento a un modello](/azure/templates/).