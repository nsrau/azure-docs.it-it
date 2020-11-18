---
title: Come rimuovere l'accesso di un utente a un'applicazione in Azure Active Directory
description: Informazioni su come rimuovere l'accesso di un utente a un'applicazione in Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 28b31d98f283dc957927ab2a35f0ab95bf066473
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654117"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Come rimuovere l'accesso di un utente a un'applicazione

Questo articolo illustra come rimuovere l'accesso di un utente a un'applicazione.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Si vuole rimuovere un'assegnazione specifica di un utente o gruppo a un'applicazione

Per rimuovere un'assegnazione di un utente o di un gruppo da un'applicazione, seguire i passaggi elencati nell'articolo [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in anteprima di Azure Active Directory](./assign-user-or-group-access-portal.md).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Si vuole disabilitare tutti gli accessi a un'applicazione per tutti gli utenti

Per disabilitare tutti gli accessi utente a un'applicazione, seguire i passaggi elencati nell'articolo [Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory](./disable-user-sign-in-portal.md).

## <a name="i-want-to-delete-an-application-entirely"></a>Si vuole eliminare completamente un'applicazione

La [serie di guide introduttive sulla gestione delle applicazioni](delete-application-portal.md) include istruzioni sull'eliminazione di un'applicazione dal tenant di Azure Active Directory.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Si vuole disabilitare tutte le operazioni future di consenso da parte dell'utente a tutte le applicazioni

La disabilitazione di consenso da parte dell'utente per l'intera directory impedisce agli utenti finali di consentire l'accesso a qualsiasi applicazione. Gli amministratori possono comunque acconsentire per conto dell'utente. Per altre informazioni sul consenso alle applicazioni e sui motivi per cui si può desiderare o meno di dare il consenso, vedere [Informazioni sul consenso dell'utente e dell'amministratore](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Vedere anche [Autorizzazioni e consenso](../develop/v2-permissions-and-consent.md).

Per **disabilitare tutte le future operazioni di consenso utente nell'intera directory**, seguire queste istruzioni:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'estensione **Azure Active Directory** 

3.  Scegliere **Applicazioni aziendali** dal menu di spostamento.

5.  Fare clic su **Impostazioni utente**.

6.  Impostare l'interruttore **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** su **No** e fare clic sul pulsante Salva.


## <a name="next-steps"></a>Passaggi successivi

[Gestione dell'accesso alle app](what-is-access-management.md)