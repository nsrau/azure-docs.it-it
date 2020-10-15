---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "71180056"
---
Dopo la creazione del Watcher, l'evento `AnchorLocated` viene attivato per ogni ancoraggio richiesto. Questo evento viene generato quando viene individuato un ancoraggio o se non è possibile individuarlo. Se si verifica questa situazione, il motivo verrà indicato nello stato. Dopo che tutti gli ancoraggi per un Watcher vengono elaborati, che siano stati trovati o no, viene generato l'evento `LocateAnchorsCompleted`. È previsto un limite di 35 identificatori per Watcher. 
