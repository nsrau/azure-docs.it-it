---
title: Abilitare l'estensione della macchina virtuale da portale di Azure
description: Questo articolo descrive come distribuire le estensioni delle macchine virtuali nei server abilitati per Azure Arc in esecuzione in ambienti cloud ibridi dal portale di Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: dcab7cb441c329a60b2c6fa3256aeedb2bb5b33d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462941"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Abilitare le estensioni di VM di Azure dalla portale di Azure

Questo articolo illustra come distribuire e disinstallare le estensioni delle macchine virtuali di Azure, supportate da server abilitati per Azure Arc, in un computer ibrido Linux o Windows tramite il portale di Azure.

## <a name="enable-extensions-from-the-portal"></a>Abilitare le estensioni dal portale

Le estensioni della macchina virtuale possono essere applicate all'arco per il computer gestito dal server tramite il portale di Azure.

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Nel portale passare a **Server-Azure Arc** e selezionare il computer ibrido nell'elenco.

3. Scegliere **estensioni**, quindi selezionare **Aggiungi**. Scegliere l'estensione desiderata dall'elenco di quelle disponibili e quindi seguire le istruzioni della procedura guidata. In questo esempio verrà distribuita l'estensione della macchina virtuale Log Analytics.

    ![Selezionare l'estensione della macchina virtuale per il computer selezionato](./media/manage-vm-extensions/add-vm-extensions.png)

    Nell'esempio seguente viene illustrata l'installazione dell'estensione della macchina virtuale Log Analytics dal portale di Azure:

    ![Installare Log Analytics estensione VM](./media/manage-vm-extensions/mma-extension-config.png)

    Per completare l'installazione, è necessario specificare l'ID e la chiave primaria dell'area di lavoro. Se non si ha familiarità con la ricerca di queste informazioni, vedere [ottenere l'ID e la chiave dell'area di lavoro](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Dopo aver confermato le informazioni necessarie, selezionare **Crea**. Viene visualizzato un riepilogo della distribuzione ed è possibile esaminare lo stato della distribuzione.

>[!NOTE]
>Sebbene sia possibile raggruppare ed elaborare più estensioni, queste vengono installate in modo seriale. Al termine dell'installazione della prima estensione, viene tentata l'installazione dell'estensione successiva.

## <a name="uninstall-extension"></a>Disinstalla estensione

È possibile rimuovere una o più estensioni da un server abilitato per l'arco dal portale di Azure. Per rimuovere un'estensione, attenersi alla procedura riportata di seguito.

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Nel portale passare a **Server-Azure Arc** e selezionare il computer ibrido nell'elenco.

3. Scegliere **estensioni**, quindi selezionare un'estensione nell'elenco delle estensioni installate.

4. Selezionare **Disinstalla** e quando viene richiesto di verificare, selezionare **Sì** per continuare.

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire, gestire e rimuovere le estensioni di VM usando l'interfaccia della riga di comando di [Azure](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md)o i [modelli di Azure Resource Manager](manage-vm-extensions-template.md).

- Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida Risoluzione dei problemi delle estensioni VM](troubleshoot-vm-extensions.md).