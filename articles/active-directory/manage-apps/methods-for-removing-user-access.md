---
title: Come rimuovere l'accesso di un utente a un'applicazione | Microsoft Docs
description: Comprendere come rimuovere l'accesso di un utente a un'applicazione
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "65826105"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Come rimuovere l'accesso di un utente a un'applicazione

Questo articolo illustra come rimuovere l'accesso di un utente a un'applicazione.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Si vuole rimuovere un'assegnazione specifica di un utente o gruppo a un'applicazione

Per rimuovere un'assegnazione di un utente o di un gruppo da un'applicazione, seguire i passaggi elencati nell'articolo [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in anteprima di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Si vuole disabilitare tutti gli accessi a un'applicazione per tutti gli utenti

Per disabilitare tutti gli accessi utente a un'applicazione, seguire i passaggi elencati nell'articolo [Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Si vuole eliminare completamente un'applicazione

Per **eliminare un'applicazione**, seguire queste istruzioni:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **applicazioni aziendali** nel menu di spostamento a sinistra Azure Active Directory.

5. Fare clic su **tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se non viene visualizzata l'applicazione che si vuole visualizzare qui, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni.**

6. Selezionare l'applicazione che si desidera eliminare.

7. Dopo il caricamento dell'applicazione, fare clic sull'icona **Elimina** nella parte superiore del riquadro **Panoramica** dell'applicazione.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Si vuole disabilitare tutte le operazioni future di consenso da parte dell'utente a tutte le applicazioni

La disabilitazione di consenso da parte dell'utente per l'intera directory impedisce agli utenti finali di consentire l'accesso a qualsiasi applicazione. Gli amministratori possono comunque acconsentire per conto dell'utente. Per altre informazioni sul consenso alle applicazioni e sui motivi per cui si può desiderare o meno di dare il consenso, vedere [Informazioni sul consenso dell'utente e dell'amministratore](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Vedere anche [Autorizzazioni e consenso](../develop/v2-permissions-and-consent.md).

Per **disabilitare tutte le future operazioni di consenso utente nell'intera directory**, seguire queste istruzioni:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2.  Aprire l'estensione **Azure Active Directory** 

3.  Scegliere **Applicazioni aziendali** dal menu di spostamento.

5.  Fare clic su **Impostazioni utente**.

6.  Impostare l'interruttore **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** su **No** e fare clic sul pulsante Salva.


## <a name="next-steps"></a>Passaggi successivi

[Gestione dell'accesso alle app](what-is-access-management.md)
