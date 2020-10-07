---
ms.openlocfilehash: 90e5ac4bbf315ae3f9f43b2a666c23dfd2c0a1d7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88684281"
---
Quando si configurano le risorse di Azure, nella VM Linux di Azure usata come dispositivo IoT Edge viene copiato un breve video del traffico in autostrada. In questa guida il file video viene usato per simulare uno streaming live.

Aprire un'applicazione, ad esempio il [lettore multimediale VLC](https://www.videolan.org/vlc/). Premere CTRL+N, quindi incollare un collegamento al [video](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) per avviare la riproduzione. Viene visualizzato il filmato di molti veicoli in transito nel traffico di un'autostrada.

In questo argomento di avvio rapido si userà Analisi video live in IoT Edge per rilevare oggetti come veicoli e persone. Gli eventi di inferenza associati verranno pubblicati nell'hub di IoT Edge.
