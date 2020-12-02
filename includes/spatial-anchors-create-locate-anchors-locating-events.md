---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993111"
---
Dopo la creazione del Watcher, l'evento `AnchorLocated` viene attivato per ogni ancoraggio richiesto. Questo evento viene generato quando viene individuato un ancoraggio o se non è possibile individuarlo. Se si verifica questa situazione, il motivo verrà indicato nello stato. Dopo che tutti gli ancoraggi per un Watcher vengono elaborati, che siano stati trovati o no, viene generato l'evento `LocateAnchorsCompleted`. È previsto un limite di 35 identificatori per Watcher. 
