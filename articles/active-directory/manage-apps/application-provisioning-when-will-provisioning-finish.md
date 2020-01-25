---
title: Il provisioning utenti in Azure AD app della raccolta richiede più di ore
description: Come individuare il motivo per cui il provisioning per l'applicazione in uso può richiedere più tempo del previsto
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0c2586b129935043ae7b2eccd11cc3d65a385c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712083"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Il provisioning utenti per un'applicazione della raccolta di AD Azure richiede alcune ore o tempi maggiori

Quando si Abilita per la prima volta il provisioning automatico per un'applicazione, il ciclo iniziale può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory Azure AD e del numero di utenti nell'ambito del provisioning. 

Le sincronizzazioni successive dopo il ciclo iniziale risultano più veloci, poiché il servizio di provisioning archivia le filigrane che rappresentano lo stato di entrambi i sistemi dopo il ciclo iniziale, migliorando le prestazioni delle sincronizzazioni successive.

## <a name="how-to-improve-provisioning-performance"></a>Come migliorare le prestazioni del provisioning

Se il ciclo iniziale richiede più di alcune ore, è possibile eseguire un'operazione per migliorare le prestazioni:

-   **Filtri di ambito utente** I filtri di ambito consentono di ottimizzare i dati che il servizio di provisioning estrae da Azure AD, filtrando gli utenti in base a valori di attributo specifici. Per altre informazioni sui filtri di ambito, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md)

