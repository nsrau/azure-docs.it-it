---
title: Gestire le autorizzazioni alle risorse per ogni utente in Azure Stack (amministratore del servizio e tenant) | Microsoft Docs
description: Come amministratore del servizio o tenant, informazioni su come gestire le autorizzazioni RBAC.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 26ce7139b856fc2f8d7d2cad549b3dd3c0f5e406
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304687"
---
# <a name="manage-role-based-access-control"></a>Gestire il controllo degli accessi in base al ruolo

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Un utente in Azure Stack può essere un lettore, proprietario o collaboratore per ogni istanza di una sottoscrizione, un gruppo di risorse o un servizio. Ad esempio, l'utente potrebbe disporre delle autorizzazioni di lettura per una sottoscrizione, ma dispone delle autorizzazioni di proprietario per sette di macchina virtuale.

 - Lettore: Utente può visualizzare tutto, ma non è possibile apportare modifiche.
 - Collaboratore: Utente può gestire tutto ad eccezione degli accessi alle risorse.
 - Proprietario: Utente può gestire tutto, incluso l'accesso alle risorse.

## <a name="set-access-permissions-for-a-user"></a>Impostare le autorizzazioni di accesso per un utente

1. Accedere con un account con autorizzazioni di proprietario per la risorsa da gestire.
2. Nel pannello della risorsa, fare clic sui **Access** icona ![](media/azure-stack-manage-permissions/image1.png).
3. Nel **gli utenti** pannello, fare clic su **ruoli**.
4. Nel **ruoli** pannello, fare clic su **Add** per aggiungere autorizzazioni per l'utente.

## <a name="set-access-permissions-for-a-universal-group"></a>Impostare le autorizzazioni di accesso per un gruppo universale 

> [!Note]  
Applicabile solo ad Active Directory Federated Services (ADFS).

1. Accedere con un account con autorizzazioni di proprietario per la risorsa da gestire.
2. Nel pannello della risorsa, fare clic sui **Access** icona ![](media/azure-stack-manage-permissions/image1.png).
3. Nel **gli utenti** pannello, fare clic su **ruoli**.
4. Nel **ruoli** pannello, fare clic su **Add** aggiungere autorizzazioni per Universal gruppo gruppo di Active Directory.

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere un tenant di Azure Stack](azure-stack-add-new-user-aad.md)

