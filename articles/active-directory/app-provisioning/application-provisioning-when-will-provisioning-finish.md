---
title: Il provisioning degli utenti nell'app Galleria di Azure AD richiede o più ore
description: Come individuare il motivo per cui il provisioning per l'applicazione in uso può richiedere più tempo del previsto
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522646"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Il provisioning utenti per un'applicazione della raccolta di AD Azure richiede alcune ore o tempi maggiori

Quando si abilita per la prima volta il provisioning automatico per un'applicazione, il ciclo iniziale può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti nell'ambito per il provisioning. 

Le sincronizzazioni successive dopo il ciclo iniziale saranno più veloci, poiché il servizio di provisioning archivia le filigrane che rappresentano lo stato di entrambi i sistemi dopo il ciclo iniziale, migliorando le prestazioni delle sincronizzazioni successive.

## <a name="how-to-improve-provisioning-performance"></a>Come migliorare le prestazioni del provisioning

Se il ciclo iniziale richiede più di un paio d'ore, c'è una cosa che si può fare per migliorare le prestazioni:

-   **Filtri di ambito utente** I filtri di ambito consentono di ottimizzare i dati che il servizio di provisioning estrae da Azure AD, filtrando gli utenti in base a valori di attributo specifici. Per altre informazioni sui filtri di ambito, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md)

