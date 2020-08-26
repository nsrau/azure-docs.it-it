---
title: Modelli di cartelle di lavoro di monitoraggio di Azure-sposta aree
description: Come spostare un modello di cartella di lavoro in un'area diversa
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: f37550d914cf7efb0c75ed3dfa8854e1ec7be7e0
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875681"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Spostare un modello di cartella di lavoro di Azure in un'altra area

Questo articolo descrive come spostare le risorse del modello di cartella di lavoro di Azure in un'altra area di Azure. È possibile spostare le risorse in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per distribuire le funzionalità o i servizi disponibili solo in aree specifiche, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità.

Attualmente non è disponibile un'interfaccia utente del portale per la creazione delle risorse del modello di cartella di lavoro. l'unico modo per crearli è [tramite Azure Resource Manager le distribuzioni](./workbooks-automate.md)del modello ARM. Di conseguenza, il modo più semplice per spostare un modello consiste nel riutilizzare il modello ARM precedente e aggiornarlo in modo che venga distribuito nella nuova area.

## <a name="prerequisites"></a>Prerequisiti

* Verificare che i modelli di cartella di lavoro siano supportati nell'area di destinazione.

## <a name="prepare"></a>Preparazione

* Identificare il modello ARM usato in precedenza per il modello di cartella di lavoro.

## <a name="move"></a>Sposta

1. Aggiornare il modello usato in precedenza per fare riferimento alla nuova area.

   > [!NOTE]
   > Per evitare qualsiasi nome duplicato, potrebbe essere necessario utilizzare un nuovo nome per il modello di cartella di lavoro.

2. Distribuire il modello aggiornato tramite la distribuzione del modello ARM per creare un nuovo modello di cartella di lavoro nell'area desiderata.

## <a name="verify"></a>Verifica

Usare l'interfaccia utente di esplorazione delle cartelle di lavoro di Azure per individuare il modello di cartella di lavoro appena distribuito. Verificare che il percorso sia il percorso di destinazione.

## <a name="clean-up"></a>Eseguire la pulizia

Dopo aver creato il modello di cartella di lavoro nella nuova area, eliminare il modello di cartella di lavoro originale nell'area precedente.
1. Trovare il modello di cartella di lavoro nell'interfaccia utente di esplorazione delle cartelle di lavoro di Azure.
2. Selezionare il modello di cartella di lavoro da eliminare.
3. Selezionare il comando "Elimina".

Se il modello di cartella di lavoro è stato rinominato per importarlo in una nuova area, è possibile rinominare il modello di cartella di lavoro con il nome precedente dopo che l'elemento originale è stato eliminato usando il comando "Rinomina" nella visualizzazione delle risorse del modello di cartella di lavoro di Azure.

## <a name="next-steps"></a>Passaggi successivi

È necessario spostare una cartella di lavoro anziché un modello? Vedere come [spostare una cartella di lavoro di Azure in un'altra area](./workbooks-move-region.md).

