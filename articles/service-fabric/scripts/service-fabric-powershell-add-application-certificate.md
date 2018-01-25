---
title: Esempio di script di Azure PowerShell - Aggiungere la certificazione dell'applicazione a un cluster | Microsoft Docs
description: Esempio di script di Azure PowerShell - Aggiungere la certificazione dell'applicazione a un cluster di Service Fabric.
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
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: c9cf6485c2621f839b93da162e5f4d82a8d287a4
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Aggiungere la certificazione dell'applicazione a un cluster di Service Fabric.

Questo script di esempio crea un certificato autofirmato nell'insieme di credenziali delle chiavi di Azure e lo installa in tutti i nodi del cluster di Service Fabric. Il certificato viene scaricato anche in una cartella locale. Il nome del certificato scaricato è identico al nome del certificato nell'insieme di credenziali delle chiavi. Personalizzare i parametri in base alle esigenze.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i seguenti comandi: ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | Aggiungere un nuovo certificato di applicazione al set di scalabilità di macchine virtuali che costituisce il cluster.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).
