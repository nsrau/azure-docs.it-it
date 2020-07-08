---
title: Configurazione post-distribuzione tramite le estensioni
description: Informazioni su come usare le estensioni dei modelli di Azure Resource Manager per fornire configurazioni post-distribuzione.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: be55138a2aa6dc0552c7556438ffd43705687c87
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055048"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornire configurazioni post-distribuzione tramite le estensioni

Le estensioni del modello sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle risorse di Azure. Le più conosciute sono le estensioni della macchina virtuale. Vedere [Estensioni e funzionalità della macchina virtuale per Windows](../../virtual-machines/extensions/features-windows.md) e [Estensioni e funzionalità della macchina virtuale per Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Estensioni

Le estensioni esistenti sono:

- [Microsoft. Compute/virtualMachines/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Per scoprire tutte le estensioni disponibili, selezionare la [documentazione di riferimento dei modelli](/azure/templates/). In **Filtra in base al titolo** immettere **estensione**.

Per informazioni su come usare queste estensioni, vedere:

- [Esercitazione: distribuire le estensioni delle macchine virtuali con modelli di Azure Resource Manager](template-tutorial-deploy-vm-extensions.md).
- [Esercitazione: Importare file BACPAC di SQL con modelli di Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
