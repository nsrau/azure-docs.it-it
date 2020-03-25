---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694363"
---
## <a name="provide-frames-to-the-session"></a>Fornire fotogrammi alla sessione

Durante la sessione di ancoraggio nello spazio viene eseguito il mapping dello spazio intorno all'utente. Questa operazione consente di determinare dove si trovano gli ancoraggi. Le piattaforme per dispositivi mobili (iOS e Android) richiedono una chiamata nativa al feed della fotocamera per ottenere i fotogrammi dalla libreria AR della piattaforma. Al contrario, HoloLens analizza continuamente l'ambiente, di conseguenza non è necessario eseguire una chiamata specifica come avviene nelle piattaforme per dispositivi mobili.