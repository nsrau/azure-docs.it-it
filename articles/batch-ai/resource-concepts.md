---
title: Informazioni sulle risorse Batch per intelligenza artificiale - Azure | Microsoft Docs
description: Panoramica di aree di lavoro, cluster, file server ed esperimenti nel servizio Batch per intelligenza artificiale in Microsoft Azure.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 4a9e3529f9d68ecdc614ea69cffc6897891f4548
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44056815"
---
# <a name="overview-of-resources-in-batch-ai"></a>Panoramica delle risorse in Batch per intelligenza artificiale

Quando si inizia a usare il servizio Batch per intelligenza artificiale, è opportuno conoscere le risorse disponibili per questo servizio. Come per altri servizi di Azure, le risorse Batch per intelligenza artificiale dovranno essere create in uno o più *gruppi di risorse* di Azure. Creare una o più *aree di lavoro* Batch per intelligenza artificiale in un gruppo di risorse. Ogni area di lavoro contiene una combinazione di *cluster*,*file server* ed *esperimenti* Batch per intelligenza artificiale. Un esperimento Batch per intelligenza artificiale incapsula un gruppo di *processi*.

Nella figura seguente viene illustrata una gerarchia di risorse di esempio per Batch per intelligenza artificiale. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

Le sezioni seguenti illustrano in dettaglio le risorse Batch per intelligenza artificiale.

## <a name="workspace"></a>Area di lavoro

Un'area di lavoro in Batch per intelligenza artificiale è una raccolta di primo livello delle altre risorse Batch per intelligenza artificiale. Le aree di lavoro consentono di suddividere le operazioni appartenenti a diversi gruppi o progetti. Ad esempio, si potrebbero creare un'area di lavoro per lo sviluppo e una per i test.

## <a name="cluster"></a>HDInsight

Un cluster in Batch per intelligenza artificiale contiene le risorse di calcolo per l'esecuzione di processi. Tutti i nodi in un cluster hanno le stesse dimensioni macchina virtuale e immagine del sistema operativo. Batch per intelligenza artificiale offre numerose opzioni per la creazione di cluster personalizzati in base a diverse esigenze. In genere, si configura un cluster diverso per ogni categoria della potenza di elaborazione necessaria per completare un progetto. Aumentare o diminuire i cluster Batch per intelligenza artificiale in base alla richiesta e al budget. Per altre informazioni, vedere [Lavorare con i cluster Batch per intelligenza artificiale](clusters.md).

## <a name="file-server"></a>File server

Creare un file server in Batch per intelligenza artificiale per archiviare dati, script di training e log di output (facoltativo). Un file server Batch per intelligenza artificiale è un NFS a nodo singolo gestito che può essere montato automaticamente su nodi del cluster per offrire una posizione di archiviazione accessibile, facile e centrale per i processi. Nella maggior parte dei casi è necessario un solo file server in un'area di lavoro ed è possibile separare i dati per i processi di training in directory diverse. Se un NFS non è appropriato per i carichi di lavoro, Batch per intelligenza artificiale supporta altre opzioni di archiviazione, tra cui [Archiviazione di Azure](use-azure-storage.md) o soluzioni personalizzate, come ad esempio un file system Gluster o Lustre.

## <a name="experiment"></a>Esperimento

Un esperimento consente di raggruppare più processi correlati che vengono gestiti e su cui si eseguono query come gruppo. Ogni area di lavoro potrebbe contenere più esperimenti, in cui ognuno di essi tenta di risolvere un problema specifico.

## <a name="job"></a>Processo

Un processo è un'attività o uno script che deve essere eseguito, ad esempio per eseguire il training di un modello di apprendimento avanzato. Ogni processo esegue uno script specifico in un unico cluster nell'area di lavoro. (Lo script può essere archiviato in un file server Batch per intelligenza artificiale o in un'altra soluzione per l'archiviazione). Ogni processo Batch per intelligenza artificiale è associato a un tipo di framework: TensorFlow, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, MPI personalizzato, o personalizzato. Per ogni framework, il servizio Batch per intelligenza artificiale configura l'infrastruttura necessaria e gestisce le fasi del processo. Ogni esperimento può avere più processi simili, a parte alcune modifiche a parametri diversi.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire la prima volta [processo di formazione su Batch per intelligenza artificiale](quickstart-tensorflow-training-cli.md).

* Vedere i [recipe di training](https://github.com/Azure/BatchAI/tree/master/recipes) di esempio per i diversi framework.