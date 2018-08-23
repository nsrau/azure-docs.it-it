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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42139566"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuire modelli in Azure Stack tramite la riga di comando

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Usare la riga di comando per distribuire modelli di Azure Resource Manager di Azure Stack Development Kit. I modelli di Azure Resource Manager distribuiscono ed effettuare il provisioning di tutte le risorse per l'applicazione in un'unica operazione coordinata.

## <a name="before-you-begin"></a>Prima di iniziare
 - [Installare e connettere](azure-stack-version-profiles-azurecli2.md) ad Azure Stack con CLI di Azure
 - Scaricare i file *azuredeploy. JSON* e *azuredeploy* dal [creare modello di esempio di account di archiviazione](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Distribuire il modello
Passare alla cartella in cui questi file sono stati scaricati ed eseguire il comando seguente per distribuire il modello:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Questo comando distribuisce il modello per il gruppo di risorse **cliRG** nel percorso predefinito dell'architettura POC di Azure Stack.

## <a name="validate-template-deployment"></a>Convalidare la distribuzione dei modelli
Per visualizzare questo gruppo di risorse e l'account di archiviazione, usare i comandi seguenti:

    azure group list

    azure storage account list




