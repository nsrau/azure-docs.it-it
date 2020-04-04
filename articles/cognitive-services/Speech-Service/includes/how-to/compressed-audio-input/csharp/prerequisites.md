---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637280"
---
La gestione dell'audio compresso viene implementata utilizzando [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza, i file binari GStreamer non vengono compilati e collegati a Speech SDK. Gli sviluppatori devono installare diverse dipendenze e plugin, vedere [Installazione su Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). I file binari Gstreamer devono trovarsi nel percorso di sistema, in modo che l'SDK vocale possa caricare i file binari gstreamer durante il runtime. Se speech SDK è in grado di trovare libgstreamer-1.0-0.dll durante il runtime, significa che i file binari gstreamer si trovano nel percorso di sistema.

