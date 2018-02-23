---
title: Gestire le autorizzazioni alle risorse per ogni utente nello Stack di Azure (amministratore del servizio e tenant) | Documenti Microsoft
description: Come amministratore del servizio o tenant, informazioni su come gestire le autorizzazioni RBAC.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: fenila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: mabrigg
ms.reviewer: thomas.roettinger
ms.openlocfilehash: 0e50ea44ebb0b0a7285dab04666dd55cad480c6a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-role-based-access-control"></a>Gestire il controllo degli accessi in base al ruolo

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Un utente in Azure Stack può essere un lettore, proprietario o collaboratore per ogni istanza di una sottoscrizione, un gruppo di risorse o un servizio. Ad esempio, l'utente potrebbe disporre delle autorizzazioni di lettura per una sottoscrizione, ma dispone delle autorizzazioni di proprietario per sette di macchina virtuale.

 - Lettore: l'utente può visualizzare tutti gli elementi, ma non può apportare modifiche.
 - Collaboratore: l'utente può gestire qualsiasi elemento, ad eccezione dell'accesso alle risorse.
 - Proprietario: l'utente può gestire qualsiasi elemento, incluso l'accesso alle risorse.

## <a name="set-access-permissions-for-a-user"></a>Impostare le autorizzazioni di accesso per un utente

1. Accedere con un account con autorizzazioni di proprietario per la risorsa da gestire.
2. Nel pannello della risorsa, fare clic su di **accesso** icona ![](media/azure-stack-manage-permissions/image1.png).
3. Nel **utenti** pannello, fare clic su **ruoli**.
4. Nel **ruoli** pannello, fare clic su **Aggiungi** per aggiungere autorizzazioni per l'utente.

## <a name="set-access-permissions-for-a-universal-group"></a>Impostare le autorizzazioni di accesso per un gruppo universale 

> [!Note]  
Applicabile solo in Active Directory Federated Services (ADFS).

1. Accedere con un account con autorizzazioni di proprietario per la risorsa da gestire.
2. Nel pannello della risorsa, fare clic su di **accesso** icona ![](media/azure-stack-manage-permissions/image1.png).
3. Nel **utenti** pannello, fare clic su **ruoli**.
4. Nel **ruoli** pannello, fare clic su **Aggiungi** per aggiungere autorizzazioni per Universal gruppo gruppo di Active Directory.

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere un tenant di Azure Stack](azure-stack-add-new-user-aad.md)

