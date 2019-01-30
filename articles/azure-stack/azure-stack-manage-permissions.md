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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f20cd877e4cc53490016d251c5bdb343ab0cb4b0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250334"
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

