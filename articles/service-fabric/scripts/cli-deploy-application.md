---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Distribuire l'applicazione in un cluster
description: Esempio di script dell'interfaccia della riga di comando di Azure - Distribuire un'applicazione in un cluster di Service Fabric.
services: service-fabric
documentationcenter: 
author: Thraka
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
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuire un'applicazione in un cluster di Service Fabric

Questo script di esempio copia un pacchetto dell'applicazione in un archivio immagini del cluster, registra il tipo di applicazione nel cluster e crea un'istanza di applicazione dal tipo di applicazione.  Se nel manifesto dell'applicazione del tipo di applicazione di destinazione sono specificati servizi predefiniti, questi verranno creati in questa fase.

Se necessario, installare l'[interfaccia della riga di comando di Azure](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Script di esempio

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Distribuire un'applicazione in un cluster")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dell'esempio di script, lo script in [Rimuovere un'applicazione](cli-remove-application.md) può essere usato per rimuovere l'istanza dell'applicazione, annullare la registrazione del tipo di applicazione ed eliminare il pacchetto dell'applicazione dall'archivio immagini.

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.


| Comando | Note |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Seleziona il cluster da usare. |
| [sf application upload](/cli/azure/sf/application#upload) | Consente di caricare i file e i manifesti dei file. |
| [sf application provision](/cli/azure/sf/application#provision) | Consente di registrare l'applicazione sul cluster.|
| [sf application create](/cli/azure/sf/application#create) | Consente di creare un'istanza dell'applicazione e di distribuire eventuali servizi definiti nei nodi. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando di Azure](../service-fabric-azure-cli-2-0.md).

Altri esempi dell'interfaccia della riga di comando di Azure per Azure Service Fabric sono disponibili negli [esempi dell'interfaccia della riga di comando di Azure](../samples-cli.md).

