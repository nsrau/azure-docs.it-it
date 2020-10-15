---
title: Cartelle di lavoro di monitoraggio di Azure-sposta aree
description: Come spostare una cartella di lavoro in un'area diversa
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d3b8bfbef544e754f684421daa847f1724435d53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875682"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Spostare una cartella di lavoro di Azure in un'altra area

Questo articolo descrive come spostare le risorse di una cartella di lavoro di Azure in un'altra area di Azure. È possibile spostare le risorse in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per distribuire le funzionalità o i servizi disponibili solo in aree specifiche, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità.

## <a name="prerequisites"></a>Prerequisiti

* Verificare che le cartelle di lavoro siano supportate nell'area di destinazione.

* Queste istruzioni si applicano sia alle cartelle di lavoro condivise ( `microsoft.insights/workbooks` ) che alle cartelle di lavoro private ( `microsoft.insights/myworkbooks` ) salvate in monitoraggio di Azure e nella maggior parte dei tipi di risorse.

  Tuttavia, per le cartelle di lavoro specificamente collegate al tipo Application Insights risorsa, tali cartelle di lavoro vengono archiviate nell'area di Azure in cui viene salvata la risorsa Application Insights.

  Non è possibile spostare singolarmente tali cartelle di lavoro in un'altra area.

## <a name="move"></a>Spostamento

Il modo più semplice per spostare una cartella di lavoro di Azure consiste nell'usare "Salva con nome" nello strumento cartelle di lavoro nell'interfaccia utente del portale:

1. Aprire la cartella di lavoro di destinazione nel visualizzatore cartella di lavoro.
2. Usare il pulsante della barra degli strumenti "modifica" per passare alla modalità di modifica.
3. Usare il pulsante della barra degli strumenti "Salva con nome".
4. Nel modulo Salva scegliere un nome e l'area desiderata per la cartella di lavoro. Verificare che gli altri campi sottoscrizione, gruppo di risorse e condivisione siano appropriati.

   > [!NOTE]
   > Per evitare qualsiasi nome duplicato, potrebbe essere necessario utilizzare un nuovo nome per la cartella di lavoro.

5. Salvare. 

## <a name="verify"></a>Verifica

Usare l'interfaccia utente di esplorazione delle cartelle di lavoro di Azure per individuare la nuova cartella di lavoro. Verificare che il percorso sia il percorso di destinazione.

## <a name="clean-up"></a>Eseguire la pulizia

Una volta creata la cartella di lavoro nella nuova area, eliminare la cartella di lavoro originale nell'area precedente.
1. Aprire la cartella di lavoro originale nel visualizzatore cartelle di lavoro.
2. Usare il pulsante della barra degli strumenti "modifica" per passare alla modalità di modifica.
3. Dall'elenco a discesa Modifica strumenti (icona a matita) scegliere "Elimina cartella di lavoro".

Se la cartella di lavoro è stata rinominata per importarla in una nuova area, è possibile rinominare la cartella di lavoro con il nome precedente dopo che la cartella di lavoro originale è stata eliminata tramite l'elemento della barra degli strumenti Edit Mode (Rinomina) della barra degli strumenti della modalità di modifica.

## <a name="next-steps"></a>Passaggi successivi

È necessario spostare un modello di cartella di lavoro anziché una cartella di lavoro? Vedere come [spostare un modello di cartella di lavoro di Azure in un'altra area](./workbook-templates-move-region.md).

Vedere come [distribuire cartelle di lavoro e modelli di cartella di lavoro](./workbooks-automate.md) tramite modelli ARM.
