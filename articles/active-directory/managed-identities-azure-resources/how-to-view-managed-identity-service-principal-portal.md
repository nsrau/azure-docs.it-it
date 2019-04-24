---
title: Come visualizzare l'entità servizio di un'identità gestita nel portale di Azure
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita nel portale di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f6139062c4d302284cc653606ae838206d3691a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60290724"
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

