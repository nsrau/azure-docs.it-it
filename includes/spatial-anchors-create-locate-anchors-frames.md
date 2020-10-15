---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006479"
---
## <a name="provide-frames-to-the-session"></a>Fornire fotogrammi alla sessione

Durante la sessione di ancoraggio nello spazio viene eseguito il mapping dello spazio intorno all'utente. Questa operazione consente di determinare dove si trovano gli ancoraggi. Le piattaforme per dispositivi mobili (iOS e Android) richiedono una chiamata nativa al feed della fotocamera per ottenere i fotogrammi dalla libreria AR della piattaforma. Al contrario, HoloLens analizza continuamente l'ambiente e, di conseguenza, non è necessario eseguire una chiamata specifica come avviene nelle piattaforme per dispositivi mobili.