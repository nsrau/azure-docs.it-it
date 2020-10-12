---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282738"
---
La gestione dell'audio compresso viene implementata utilizzando [`GStreamer`](https://gstreamer.freedesktop.org) . Per motivi di licenza `GStreamer` i file binari non vengono compilati e collegati con l'SDK di riconoscimento vocale. Gli sviluppatori devono installare diverse dipendenze e plug- [in, vedere installazione in Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer` i file binari devono trovarsi nel percorso di sistema, in modo che l'SDK di riconoscimento vocale possa caricare i file binari durante il Runtime. Se l'SDK di riconoscimento vocale è in grado di trovarsi `libgstreamer-1.0-0.dll` durante il runtime, significa che i file binari si trovano nel percorso di sistema.

