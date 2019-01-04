---
title: Come visualizzare l'entità servizio di un'identità gestita nel portale di Azure
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita nel portale di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: c61220242a5a2ff84204c84b11aa132d00279e49
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726596"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Visualizzare l'entità servizio di un'identità gestita nel portale di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita con il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha già un account Azure, è possibile [registrarsi per ottenerne uno gratuito](https://azure.microsoft.com/free/).
- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) o nell'[applicazione](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Questa procedura illustra come visualizzare l'entità servizio di una macchina virtuale con un'identità abilitata di sistema assegnata (gli stessi passaggi si applicano a un'applicazione).

1. Fare clic su **Azure Active Directory** e quindi su **Applicazioni aziendali**.
2. In **Tipo di applicazione** selezionare **Tutte le applicazioni**.
3. Nella casella filtro di ricerca, digitare il nome della macchina virtuale o dell'applicazione che ha abilitato l'identità gestita, o sceglierla in alternativa dall'elenco visualizzato.

   ![Visualizzare le identità gestite dell'entità servizio nel portale](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Passaggi successivi

[Identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview)

