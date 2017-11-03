---
title: Gestire le autorizzazioni alle risorse per ogni utente nello Stack di Azure (amministratore del servizio e tenant) | Documenti Microsoft
description: Come amministratore del servizio o tenant, informazioni su come gestire le autorizzazioni RBAC.
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e558f9de9bc3182bbe20ceb9d8f3f96e47fa542c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control"></a>Gestire il controllo degli accessi in base al ruolo

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Un utente in Azure Stack può essere un lettore, proprietario o collaboratore per ogni istanza di una sottoscrizione, un gruppo di risorse o un servizio. Ad esempio, l'utente A può avere autorizzazioni di lettura per la Sottoscrizione 1, ma autorizzazioni di proprietario per la Macchina virtuale 7.

* Lettore: l'utente può visualizzare tutti gli elementi, ma non può apportare modifiche.
* Collaboratore: l'utente può gestire qualsiasi elemento, ad eccezione dell'accesso alle risorse.
* Proprietario: l'utente può gestire qualsiasi elemento, incluso l'accesso alle risorse.

## <a name="set-access-permissions-for-a-user"></a>Impostare le autorizzazioni di accesso per un utente
1. Accedere con un account con autorizzazioni di proprietario per la risorsa da gestire.
2. Nel pannello della risorsa, fare clic su di **accesso** icona ![](media/azure-stack-manage-permissions/image1.png).
3. Nel **utenti** pannello, fare clic su **ruoli**.
4. Nel **ruoli** pannello, fare clic su **Aggiungi** per aggiungere autorizzazioni per l'utente.

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere un tenant di Azure Stack](azure-stack-add-new-user-aad.md)

