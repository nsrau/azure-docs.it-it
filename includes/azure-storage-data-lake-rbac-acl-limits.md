---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131633"
---
| Mechanism | Ambito |Limiti | Livello di autorizzazione supportato |
|---|---|---|---|
| Controllo degli accessi in base al ruolo | Account di archiviazione, contenitori. <br>Assegnazioni di ruolo RBAC tra le risorse a livello di sottoscrizione o di gruppo di risorse. | 2000 assegnazioni di ruolo RBAC in una sottoscrizione | Ruoli RBAC (predefiniti o personalizzati) |
| ACL| Directory, file |32 voci ACL (in effetti 28 voci ACL) per file e per directory. Gli ACL di accesso e predefiniti hanno ciascuno il rispettivo limite di immissione di 32 ACL. |Autorizzazione ACL|
