---
title: Visualizzare l'entità servizio di un'identità gestita nel portale di Azure-Azure AD
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita nel portale di Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611929"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Visualizzare l'entità servizio di un'identità gestita nel portale di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita con il portale di Azure.

 > [!NOTE] 
 > Le entità servizio sono applicazioni aziendali. 

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha già un account Azure, è possibile [registrarsi per ottenerne uno gratuito](https://azure.microsoft.com/free/).
- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) o nell'[applicazione](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Questa procedura illustra come visualizzare l'entità servizio di una macchina virtuale con un'identità abilitata di sistema assegnata (gli stessi passaggi si applicano a un'applicazione).

1. Fare clic su **Azure Active Directory** e quindi su **Applicazioni aziendali**.
2. In **tipo di applicazione**scegliere **tutte le applicazioni** e quindi fare clic su **applica**.
3. Nella casella filtro di ricerca digitare il nome della risorsa di Azure in cui è abilitata l'identità gestita oppure selezionarla nell'elenco visualizzato.

   ![Visualizzare le identità gestite dell'entità servizio nel portale](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Passaggi successivi

[Identità gestite per le risorse di Azure](./overview.md)