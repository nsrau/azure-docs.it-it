---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Rimuovere un'applicazione da un cluster
description: Esempio di script dell'interfaccia della riga di comando di Azure - Rimuovere un'applicazione da un cluster di Service Fabric.
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Rimuovere un'applicazione da un cluster di Service Fabric

Questo script di esempio elimina un'istanza di applicazione di Service Fabric in esecuzione, annulla la registrazione di un tipo di applicazione e della versione dal cluster.  L'eliminazione dell'istanza dell'applicazione elimina anche tutte le istanze del servizio associate a questa applicazione. I file dell'applicazione vengono quindi eliminati dall'archivio immagini. 

Se necessario, installare l'[interfaccia della riga di comando di Azure](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Script di esempio

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Rimuovere un'applicazione da un cluster")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Seleziona il cluster da usare. |
| [sf application delete](/cli/azure/sf/application#delete) | Elimina l'istanza dell'applicazione dal cluster. |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | Annulla la registrazione di un tipo e una versione di applicazione di Service Fabric dal cluster.|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | Rimuove il pacchetto dell'applicazione di Service Fabric dall'archivio immagini. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando di Azure](../service-fabric-azure-cli-2-0.md).

Altri esempi dell'interfaccia della riga di comando di Azure per Azure Service Fabric sono disponibili negli [esempi dell'interfaccia della riga di comando di Azure](../samples-cli.md).

