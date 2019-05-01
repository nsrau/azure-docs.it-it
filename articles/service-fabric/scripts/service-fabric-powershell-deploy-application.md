---
title: Esempio di script di Azure PowerShell - Distribuire un'applicazione in un cluster | Microsoft Docs
description: "Esempio di script di Azure PowerShell: Distribuire un'applicazione in un cluster di Service Fabric."
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 6de7a721a5042a20537d3f09ea9b8de1cc0e7169
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622034"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuire un'applicazione in un cluster di Service Fabric

Questo script di esempio copia un pacchetto dell'applicazione in un archivio immagini del cluster, registra il tipo di applicazione nel cluster, rimuove il pacchetto dell'applicazione non più necessario e crea un'istanza di applicazione dal tipo di applicazione.  Se nel manifesto dell'applicazione del tipo di applicazione di destinazione sono specificati servizi predefiniti, questi verranno creati in questa fase. Personalizzare i parametri in base alle esigenze. 

Se necessario, installare il modulo PowerShell in Service Fabric con il [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dell'esempio di script, lo script in [Rimuovere un'applicazione](service-fabric-powershell-remove-application.md) può essere usato per rimuovere l'istanza dell'applicazione, annullare la registrazione del tipo di applicazione ed eliminare il pacchetto dell'applicazione dall'archivio immagini.

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Crea una connessione a un cluster di Service Fabric. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copia un pacchetto dell'applicazione nell'archivio immagini del cluster.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registra un tipo di applicazione e la versione nel cluster. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Crea un'applicazione da un tipo di applicazione registrata. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Rimuove il pacchetto dell'applicazione di Service Fabric dall'archivio immagini.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo PowerShell in Service Fabric, vedere la [documentazione di Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Altri esempi di PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).
