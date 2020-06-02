---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006479"
---
## <a name="provide-frames-to-the-session"></a>Fornire fotogrammi alla sessione

Durante la sessione di ancoraggio nello spazio viene eseguito il mapping dello spazio intorno all'utente. Questa operazione consente di determinare dove si trovano gli ancoraggi. Le piattaforme per dispositivi mobili (iOS e Android) richiedono una chiamata nativa al feed della fotocamera per ottenere i fotogrammi dalla libreria AR della piattaforma. Al contrario, HoloLens analizza continuamente l'ambiente e, di conseguenza, non è necessario eseguire una chiamata specifica come avviene nelle piattaforme per dispositivi mobili.