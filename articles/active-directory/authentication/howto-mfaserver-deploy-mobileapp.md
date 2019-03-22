---
title: Servizio di Azure MFA Server Web di App per dispositivi mobili - Azure Active Directory
description: Configurare il server MFA per l'invio di notifiche push agli utenti con l'app Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac59b68f1ddda695fba8f1a4ceacb90bfa4102a2
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313703"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Abilitare l'autenticazione con il server Azure Multi-Factor Authentication

L'app Microsoft Authenticator offre un'opzione di verifica fuori banda aggiuntiva. Invece di effettuare una chiamata automatizzata o di inviare un SMS all'utente durante l'accesso, Azure Multi-Factor Authentication inoltra una notifica push all'app Microsoft Authenticator sul tablet o sullo smartphone dell'utente. L'utente tocca semplicemente **Verifica** (o immette un PIN e tocca "Esegui autenticazione") nell'app per eseguire l'accesso.

Quando la ricezione del telefono non è affidabile, è preferibile usare un'app per dispositivi mobili per la verifica in due passaggi. Se si usa l'app come un generatore di token OATH, non è necessaria una connessione di rete o Internet.

> [!IMPORTANT]
> Se è stato installato il server Azure Multi-Factor Authentication versione 8.x o successive, non è necessario eseguire la maggior parte dei passaggi indicati di seguito. È possibile configurare l'autenticazione delle app per dispositivi mobili seguendo la procedura disponibile in [Configurare l'app per dispositivi mobili](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Requisiti

Per usare l'app Microsoft Authenticator, è necessario eseguire il server Azure Multi-Factor Authentication versione 8.x o successive

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurare le impostazioni dell'app per dispositivi mobili nel server Azure Multi-Factor Authentication

1. Nella console del server Multi-Factor Authentication fare clic sull'icona del portale utenti. Se gli utenti sono autorizzati a controllare i metodi di autenticazione, nella scheda Impostazioni, in **Consenti agli utenti di selezionare il metodo**, selezionare **App per dispositivi mobili**. Se questa funzionalità non è abilitata, agli utenti finali viene richiesto di contattare l'help desk per completare l'attivazione dell'app per dispositivi mobili.
2. Selezionare la casella **Consenti agli utenti di attivare l'app mobile**.
3. Selezionare la casella **Consenti registrazione utente**.
4. Fare clic sull'icona dell'**app per dispositivi mobili**.
5. Popolare il campo **Nome account** con il nome della società o dell'organizzazione da visualizzare nell'app per dispositivi mobili per questo account.
   ![Impostazioni dell'app per dispositivi mobili per la configurazione del server MFA](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Passaggi successivi

- [Scenari avanzati con Azure Multi-Factor Authentication e soluzioni VPN di terze parti](howto-mfaserver-nps-vpn.md).
