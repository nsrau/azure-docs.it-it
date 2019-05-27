---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121643"
---
## <a name="register-your-application"></a>Registrare l'applicazione

È possibile registrare l'applicazione in uno dei due modi descritti di seguito.

### <a name="option-1-express-mode"></a>Opzione 1: modalità Rapida

È possibile registrare rapidamente l'applicazione seguendo questa procedura:
1. Passare alla [registrazione delle applicazioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Immettere un nome per l'applicazione e fare clic su **Registra**.
1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.

### <a name="option-2-advanced-mode"></a>Opzione 2: Modalità Avanzata

Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, seguire questa procedura:
1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
   - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `Win-App-calling-MsGraph`.
   - Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)**.
   - Selezionare **Registra** per creare l'applicazione.
1. Nell'elenco delle pagine per l'app selezionare **Autenticazione**.
   1. Nella sezione **URI di reindirizzamento**, nell'elenco URI di reindirizzamento:
   1. Nella colonna **TIPO** selezionare **Client pubblico (per dispositivi mobili e desktop)**.
   1. Immettere `urn:ietf:wg:oauth:2.0:oob` nella colonna **URI DI REINDIRIZZAMENTO**.
1. Selezionare **Salva**.
1. Passare a Visual Studio, aprire il file *App.xaml.cs* e quindi sostituire `Enter_the_Application_Id_here` con l'ID applicazione appena registrato e copiato.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
