---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421687"
---
La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza i binari GStreamer non vengono compilati e collegati con l'SDK di riconoscimento vocale. Gli sviluppatori devono installare diverse dipendenze e plug- [in, vedere installazione in Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). I file binari GStreamer devono trovarsi nel percorso di sistema, in modo che l'SDK di riconoscimento vocale possa caricare i file binari GStreamer durante il Runtime. Se l'SDK di riconoscimento vocale è in grado di trovare libgstreamer-1.0 0. dll durante il runtime, significa che i file binari GStreamer si trovano nel percorso di sistema.

