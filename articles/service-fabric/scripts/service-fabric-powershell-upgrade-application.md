---
title: Esempio di script di Azure PowerShell - Aggiornare un'applicazione di Service Fabric | Microsoft Docs
description: Esempio di script di Azure PowerShell - Aggiornare un'applicazione di Service Fabric.
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
ms.topic: sample
ms.date: 08/23/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 43c1c0d38d12a36c39a3962a3399d9c05937e8b1
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="upgrade-a-service-fabric-application"></a>Aggiornare un'applicazione di Service Fabric

Questo script di esempio aggiorna un'istanza di applicazione di Service Fabric in esecuzione alla versione 1.3.0. Lo script copia il nuovo pacchetto di applicazione nell'archivio immagini del cluster, registra il tipo di applicazione, avvia un aggiornamento monitorato e controlla continuamente lo stato dell'aggiornamento fino a quando l'aggiornamento non viene completato o viene eseguito il rollback. Personalizzare i parametri in base alle esigenze. 

Se necessario, installare il modulo PowerShell in Service Fabric con il [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[principale](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Aggiornare un'applicazione")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Ottiene tutte le applicazioni del cluster di Service Fabric o un'applicazione specifica.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Ottiene lo stato dell'aggiornamento di un'applicazione di Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Ottiene i tipi di applicazioni di Service Fabric registrate nel cluster di Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Annulla la registrazione del tipo di applicazione di Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia il pacchetto dell'applicazione di Service Fabric nell'archivio immagini.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Registra un tipo di applicazione di Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Aggiorna un'applicazione di Service Fabric alla versione del tipo di applicazione specificata. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo PowerShell in Service Fabric, vedere la [documentazione di Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Altri esempi di PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).

