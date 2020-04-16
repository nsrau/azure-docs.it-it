---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421687"
---
La gestione dell'audio compresso viene implementata utilizzando [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza, i file binari GStreamer non vengono compilati e collegati a Speech SDK. Gli sviluppatori devono installare diverse dipendenze e plugin, vedere [Installazione su Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). I file binari Gstreamer devono trovarsi nel percorso di sistema, in modo che l'SDK vocale possa caricare i file binari gstreamer durante il runtime. Se speech SDK è in grado di trovare libgstreamer-1.0-0.dll durante il runtime, significa che i file binari gstreamer si trovano nel percorso di sistema.

