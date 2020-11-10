---
title: includere file
description: includere file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426569"
---
## <a name="trusted-microsoft-services"></a>Servizi Microsoft attendibili
Quando si Abilita l'impostazione **Consenti ai servizi Microsoft attendibili di ignorare questo firewall** , ai servizi seguenti viene concesso l'accesso alle risorse del bus di servizio.

| Servizio attendibile | Scenari di utilizzo supportati | 
| --------------- | ------------------------- | 
| Griglia di eventi di Azure | Consente a griglia di eventi di Azure di inviare eventi a code o argomenti nello spazio dei nomi del bus di servizio. È anche necessario eseguire i passaggi seguenti: <ul><li>Abilitare l'identità assegnata dal sistema per un argomento o un dominio</li><li>Aggiungere l'identità al ruolo mittente dati del bus di servizio di Azure nello spazio dei nomi del bus di servizio</li><li>Configurare quindi la sottoscrizione dell'evento che usa una coda o un argomento del bus di servizio come endpoint per usare l'identità assegnata dal sistema.</li></ul> <p>Per altre informazioni, vedere [recapito di eventi con un'identità gestita](../articles/event-grid/managed-service-identity.md)</p>|
