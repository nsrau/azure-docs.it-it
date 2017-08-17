---
title: Esempio di script di Azure PowerShell - Distribuire un'applicazione in un cluster | Microsoft Docs
description: 'Esempio di script di Azure PowerShell: Distribuire un''applicazione in un cluster di Service Fabric.'
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b9aa30e18f6997050f29792e53946d5ec747e9ff
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuire un'applicazione in un cluster di Service Fabric

Questo script di esempio copia un pacchetto dell'applicazione in un archivio immagini del cluster, registra il tipo di applicazione nel cluster e crea un'istanza di applicazione dal tipo di applicazione.  Se nel manifesto dell'applicazione del tipo di applicazione di destinazione sono specificati servizi predefiniti, questi verranno creati in questa fase. Personalizzare i parametri in base alle esigenze. 

Se necessario, installare il modulo PowerShell in Service Fabric con il [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Distribuire un'applicazione in un cluster")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dell'esempio di script, lo script in [Rimuovere un'applicazione](service-fabric-powershell-remove-application.md) può essere usato per rimuovere l'istanza dell'applicazione, annullare la registrazione del tipo di applicazione ed eliminare il pacchetto dell'applicazione dall'archivio immagini.

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copiare un pacchetto dell'applicazione nell'archivio immagini del cluster.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registra un tipo di applicazione e la versione nel cluster. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Crea un'applicazione da un tipo di applicazione registrata. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo PowerShell in Service Fabric, vedere la [documentazione di Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Altri esempi di PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).

