---
title: Usare le applicazioni per il rendering - Azure Batch
description: Come usare le applicazioni per il rendering con Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4c93abdfb5c523d48ce115ed7d3251a346937f5f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789591"
---
# <a name="rendering-applications"></a>Applicazioni per il rendering

Per usare le applicazioni per il rendering è necessario creare attività e processi Batch. La proprietà della riga di comando dell'attività specifica la riga di comando e i parametri appropriati.  Il modo più semplice per creare attività di processo consiste nell'uso di modelli di Batch Explorer, come illustrato in [questo articolo](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  I modelli possono essere visualizzati e, se necessario, è possibile crearne versioni modificate.

Questo articolo presenta una breve descrizione di come eseguire ogni applicazione per il rendering.

## <a name="rendering-with-autodesk-3ds-max"></a>Rendering con Autodesk 3ds Max

### <a name="renderer-support"></a>Supporto dei renderer

Oltre ai renderer integrati in 3ds Max, nelle immagini delle macchine virtuali per il rendering sono disponibili i renderer seguenti a cui può fare riferimento il file di scena di 3ds Max:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Riga di comando dell'attività

Richiamare l'applicazione `3dsmaxcmdio.exe` per eseguire il rendering da riga di comando su un nodo del pool.  Questa applicazione si trova nel percorso quando viene eseguita l'attività. L'applicazione `3dsmaxcmdio.exe` ha gli stessi parametri disponibili dell'applicazione `3dsmaxcmd.exe`, come illustrato nella [documentazione di 3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Rendering | Sezione relativa al rendering da riga di comando).

Ad esempio: 

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Note:

* È necessario prestare particolare attenzione al fatto che i file di asset siano individuabili.  Verificare che i percorsi siano corretti e relativi usando la finestra **Verifica delle risorse** o il parametro `-bitmapPath` della riga di comando.
* Verificare se vi sono problemi di rendering, ad esempio impossibilità di trovare gli asset, controllando il file `stdout.txt` scritto da 3ds Max quando viene eseguita l'attività.

### <a name="batch-explorer-templates"></a>Modelli di Batch Explorer

È possibile accedere ai modelli di pool e processi dalla **Raccolta** in Batch Explorer.  I file di origine dei modelli sono disponibili nel [repository dei dati di Batch Explorer su GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Rendering con Autodesk Maya

### <a name="renderer-support"></a>Supporto dei renderer

Oltre ai renderer integrati in Maya, nelle immagini delle macchine virtuali per il rendering sono disponibili i renderer seguenti a cui può fare riferimento il file di scena di 3ds Max:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Riga di comando dell'attività

Il renderer della riga di comando `renderer.exe` viene usato nella riga di comando dell'attività. Il renderer della riga di comando è descritto nella [documentazione di Maya](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

Nell'esempio seguente viene usata un'attività di preparazione per copiare i file di scena e gli asset nella directory di lavoro di preparazione del processo, viene usata una cartella di output per archiviare l'immagine di rendering e viene eseguito il rendering del fotogramma 10.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Per il rendering di V-Ray, il file di scena di Maya specifica in genere V-Ray come renderer.  È anche possibile specificarlo nella riga di comando:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Per il rendering di Arnold, il file di scena di Maya specifica in genere Arnold come renderer.  È anche possibile specificarlo nella riga di comando:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Modelli di Batch Explorer

È possibile accedere ai modelli di pool e processi dalla **Raccolta** in Batch Explorer.  I file di origine dei modelli sono disponibili nel [repository dei dati di Batch Explorer su GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Passaggi successivi

Usare i modelli di pool e processi dal [repository dei dati in GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) con Batch Explorer.  Quando richiesto, creare nuovi modelli o modificare uno dei modelli disponibili.