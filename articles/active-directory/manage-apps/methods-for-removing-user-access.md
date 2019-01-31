---
title: Come rimuovere l'accesso di un utente a un'applicazione | Microsoft Docs
description: Comprendere come rimuovere l'accesso di un utente a un'applicazione
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: barbkess
ms.openlocfilehash: 00f0df4612fe523b8b585bc2c9458386422256e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180501"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Come rimuovere l'accesso di un utente a un'applicazione

Questo articolo illustra come rimuovere l'accesso di un utente a un'applicazione.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Si vuole rimuovere un'assegnazione specifica di un utente o gruppo a un'applicazione

Per rimuovere un'assegnazione di un utente o di un gruppo da un'applicazione, seguire i passaggi elencati nell'articolo [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in anteprima di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Si vuole disabilitare tutti gli accessi a un'applicazione per tutti gli utenti

Per disabilitare tutti gli accessi utente a un'applicazione, seguire i passaggi elencati nell'articolo [Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Si vuole eliminare completamente un'applicazione

Per **eliminare un'applicazione**, seguire queste istruzioni:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione che si desidera eliminare.

7.  Dopo il caricamento dell'applicazione, fare clic sull'icona **Elimina** nella parte superiore del riquadro **Panoramica** dell'applicazione.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Si vuole disabilitare tutte le operazioni future di consenso da parte dell'utente a tutte le applicazioni

La disabilitazione di consenso da parte dell'utente per l'intera directory impedisce agli utenti finali di consentire l'accesso a qualsiasi applicazione. Gli amministratori possono sempre dare consenso per conto degli utenti. Per altre informazioni sul consenso alle applicazioni e sui motivi per cui si può desiderare o meno di dare il consenso, vedere [Informazioni sul consenso dell'utente e dell'amministratore](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Vedere anche [Autorizzazioni e consenso](../develop/v2-permissions-and-consent.md).

Per **disabilitare tutte le future operazioni di consenso utente nell'intera directory**, seguire queste istruzioni:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'estensione **Azure Active Directory** 

3.  Scegliere **Applicazioni aziendali** dal menu di spostamento.

5.  Fare clic su **Impostazioni utente**.

6.  Impostare l'interruttore **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** su **No** e fare clic sul pulsante Salva.


## <a name="next-steps"></a>Passaggi successivi

[Gestione dell'accesso alle app](what-is-access-management.md)
