---
title: Distribuire i modelli dalla riga di comando nello Stack di Azure | Documenti Microsoft
description: Informazioni su come utilizzare l'interfaccia della riga di comando (CLI) multipiattaforma per distribuire modelli dello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316196"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuire modelli in Azure Stack tramite la riga di comando

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare la riga di comando per distribuire modelli di gestione risorse di Azure il Kit di sviluppo dello Stack di Azure. Modelli di gestione risorse di Azure distribuiscono ed eseguire il provisioning di tutte le risorse per l'applicazione in un'operazione singola, coordinata.

## <a name="before-you-begin"></a>Prima di iniziare
 - [Installazione e la connessione](azure-stack-version-profiles-azurecli2.md) allo Stack di Azure con CLI di Azure
 - Scaricare i file *azuredeploy.json* e *azuredeploy.parameters.json* dal [creare il modello di esempio account di archiviazione](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Distribuire il modello
Passare alla cartella in cui questi file sono stati scaricati ed eseguire il comando seguente per distribuire il modello:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Questo comando consente di distribuire il modello al gruppo di risorse **cliRG** nel percorso predefinito della prova di Stack Azure.

## <a name="validate-template-deployment"></a>Convalidare la distribuzione dei modelli
Per visualizzare questo gruppo di risorse e l'account di archiviazione, usare i comandi seguenti:

    azure group list

    azure storage account list




