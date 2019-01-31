---
title: Passaggio a un tenant B2C in Azure Active Directory B2C | Microsoft Docs
description: Come passare al contesto del tenant di Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9522e921b59ddc57a6039c625bf6b0af4c4b4af2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181810"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Passaggio al tenant di Azure AD B2C

Per configurare Azure AD B2C, è necessario trovarsi nel contesto del tenant di Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Accedere al tenant di Azure AD B2C

Per passare al tenant di Azure AD B2C, è necessario essere connessi al portale di Azure come amministratore globale del tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cambiare tenant facendo clic sull'indirizzo di posta elettronica o sull'immagine nell'angolo superiore destro.
1. Nell'elenco `Directory` visualizzato selezionare il tenant di Azure AD B2C da gestire.

Il portale di Azure viene aggiornato.  Si è ora connessi al portale di Azure nel contesto del tenant di Azure AD B2C.

## <a name="navigate-to-the-b2c-features-pane"></a>Passare al riquadro delle funzionalità B2C

1. Fare clic su **Sfoglia** nel riquadro di spostamento a sinistra.
1. Fare clic su **Tutti i servizi** e quindi cercare `Azure AD B2C` nel riquadro di spostamento di sinistra.  Per aggiungerlo alla schermata iniziale di sinistra, fare clic sulla stella a sinistra di Azure AD B2C.
1. Fare clic su **Azure AD B2C** per accedere al riquadro delle funzionalità B2C.
   
    ![Screenshot del riquadro delle funzionalità B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> È necessario avere diritti di amministratore globale del tenant B2C per poter accedere al riquadro delle funzionalità B2C. Un amministratore globale o un utente di qualsiasi altro tenant non può accedere.  È possibile passare al tenant B2C usando l'apposito controllo nell'angolo superiore destro del portale di Azure.
