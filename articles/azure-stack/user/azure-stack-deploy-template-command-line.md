---
title: Distribuire modelli con riga di comando di Azure Stack | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando multipiattaforma (CLI) per distribuire modelli di Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d40f9e8bf319f327879a71b23f9d9b82da924c2f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365435"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuire modelli in Azure Stack tramite la riga di comando

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Usare la riga di comando per distribuire i modelli di Azure Resource Manager nell'ambiente Azure Stack Development Kit. I modelli di Azure Resource Manager distribuiscono ed effettuare il provisioning di tutte le risorse per l'applicazione in un'unica operazione coordinata.

## <a name="before-you-begin"></a>Prima di iniziare

 - [Installare e connettere](azure-stack-version-profiles-azurecli2.md) ad Azure Stack con CLI di Azure.
 - Scaricare i file *azuredeploy. JSON* e *azuredeploy* dal [creare modello di esempio di account di archiviazione](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Distribuire il modello

Passare alla cartella in cui sono stati scaricati questi file ed eseguire il comando seguente per distribuire il modello:

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

Questo comando distribuisce il modello per il gruppo di risorse **cliRG** nel percorso predefinito dell'architettura POC di Azure Stack.

## <a name="validate-template-deployment"></a>Convalidare la distribuzione dei modelli

Per visualizzare questo gruppo di risorse e l'account di archiviazione, usare i comandi seguenti:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla distribuzione dei modelli, vedere:

[Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)

