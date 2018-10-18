---
title: Spostamento dei dati da e verso l'archiviazione BLOB di Azure | Documentazione Microsoft
description: Spostamento dei dati da e verso l'archiviazione BLOB di Azure
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 717fdd2053cae28234458e197f8211ef25cf7f9d
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394392"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Spostamento dei dati da e verso l'archivio BLOB di Azure
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Quale sia il metodo adatto dipenderà dallo scenario. L'articolo [Scenari per l'analisi avanzata in Azure Machine Learning](plan-sample-scenarios.md) consente di determinare le risorse necessarie per un'ampia gamma di flussi di lavoro di analisi scientifica dei dati usati nel processo di analisi avanzata.

> [!NOTE]
> Per un'introduzione completa all'archiviazione BLOB di Azure, vedere [Informazioni di base su BLOB di Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

In alternativa, è possibile utilizzare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per: 

* creare e pianificare una pipeline che scarica i dati dall'archiviazione BLOB di Azure; 
* trasmetterla a un servizio Web di Azure Machine Learning pubblicato; 
* ricevere i risultati di analisi predittiva; 
* caricare i risultati nell'archiviazione. 

Per altre informazioni, vedere [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti
In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e chiavi di archiviazione corrispondenti per quell'account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure.

* Per configurare una sottoscrizione di Azure, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).

