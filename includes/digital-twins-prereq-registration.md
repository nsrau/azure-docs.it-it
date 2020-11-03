---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - prerequisito per configurare una registrazione dell'app
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124857"
---
Per autenticare tutte le risorse usate in questo articolo, è necessario configurare una **registrazione dell'app** di [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md). Seguire le istruzioni in [*Procedura dettagliata: Creare una registrazione dell'app*](../articles/digital-twins/how-to-create-app-registration.md) per configurare questa impostazione. 

Una volta eseguita la registrazione dell'app, è necessario avere l' **_ID applicazione (client)_** e l' **_ID directory (tenant)_** della registrazione, [reperibili nel portale di Azure](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id). Prendere nota di questi valori perché verranno usati in un secondo momento per concedere l'accesso alle API di Gemelli digitali di Azure.