---
author: baanders
description: file di inclusione per il passaggio autorizzazioni di accesso nell'installazione dei dispositivi gemelli di Azure Digital
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009657"
---
Quando si configura un'istanza di Azure Digital Twins, è comune interagire con tale istanza tramite un'applicazione client. Per creare un'applicazione client funzionante, è necessario assicurarsi che l'app client sarà in grado di eseguire l'autenticazione con i dispositivi gemelli digitali di Azure. Questa operazione viene eseguita impostando una **registrazione dell'app** [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) per l'app client da usare.

Questa registrazione dell'app è la posizione in cui vengono configurate le autorizzazioni di accesso alle [API di Azure Digital gemelli](../articles/digital-twins/how-to-use-apis-sdks.md) Successivamente, l'app client eseguirà l'autenticazione con la registrazione dell'app e, di conseguenza, le autorizzazioni di accesso configurate alle API.

>[!TIP]
> In qualità di proprietario/amministratore della sottoscrizione, può essere preferibile configurare una nuova registrazione dell'app per ogni nuova istanza di Azure Digital gemelli *oppure* eseguire questa operazione una sola volta, stabilendo una singola registrazione dell'app che verrà condivisa tra tutte le istanze di dispositivi gemelli digitali di Azure nella sottoscrizione.

### <a name="create-the-registration"></a>Creare la registrazione