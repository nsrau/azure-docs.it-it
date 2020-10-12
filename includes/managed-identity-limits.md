---
title: includere file
description: includere file
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971022"
---
- Ogni identità gestita viene conteggiata rispetto al limite di quota degli oggetti di un tenant di Azure AD, come descritto in [Restrizioni e limiti del servizio Azure AD](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md).
-   La frequenza con cui è possibile creare identità gestite prevede i limiti seguenti:

    1. Per tenant di Azure per area di Azure: 200 operazioni di creazione ogni 20 secondi.
    2. Per sottoscrizione di Azure per area di Azure: 40 operazioni di creazione ogni 20 secondi.

- Per la creazione di identità gestite assegnate dall'utente, sono supportati solo caratteri alfanumerici (0-9, a-z, A-Z) e il trattino (-). Per il corretto funzionamento dell'assegnazione a una macchina virtuale o a un set di scalabilità di macchine virtuali, il nome può essere composto da un massimo di 24 caratteri.
