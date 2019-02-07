---
title: Rimuovere un'applicazione registrata con Microsoft Identity Platform | Azure
description: Informazioni su come rimuovere un'applicazione registrata con Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 55866d5ff28c707c26141683bb90c8f2873b17c8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818907"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform-preview"></a>Guida introduttiva: Rimuovere un'applicazione registrata con Microsoft Identity Platform (anteprima)

Gli sviluppatori aziendali e i provider SaaS (Software as a Service) che hanno registrato applicazioni con Microsoft Identity Platform potrebbero dover rimuovere la registrazione di un'applicazione.

In questa guida introduttiva si apprende come:

* Rimuovere un'applicazione creata dall'utente o dalla relativa organizzazione
* Rimuovere un'applicazione creata da un'altra organizzazione

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, assicurarsi di soddisfare questi prerequisiti:

* Avere un tenant in cui sono registrate applicazioni. Per informazioni su come aggiungere e registrare app, vedere [Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md).
* Acconsentire esplicitamente all'esperienza di anteprima per le registrazioni di app nel portale di Azure. Le procedure illustrate in questa guida introduttiva corrispondono alla nuova interfaccia utente e funzionano solo se si è acconsentito esplicitamente all'esperienza di anteprima.

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Rimuovere un'applicazione creata dall'utente o dalla relativa organizzazione

Le applicazioni registrate dall'utente o dalla relativa organizzazione vengono rappresentate nel tenant sia da un oggetto applicazione che da un oggetto entità servizio. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md).

### <a name="to-remove-an-application"></a>Per rimuovere un'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
2. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app (anteprima)**. Trovare e selezionare l'applicazione che si vuole configurare. Dopo la selezione dell'app verrà visualizzata la pagina **Panoramica** dell'applicazione.
4. Nella pagina **Panoramica** selezionare **Elimina**.
5. Selezionare **Sì** per confermare l'eliminazione dell'app.

  > [!NOTE]
  > Per eliminare un'applicazione, è necessario essere un proprietario dell'applicazione o avere privilegi di amministratore.

## <a name="remove-an-application-authored-by-another-organization"></a>Rimuovere un'applicazione creata da un'altra organizzazione

Se si visualizza **Registrazioni app** nel contesto di un tenant, un sottoinsieme delle applicazioni visualizzate nella scheda **Tutte le app** appartiene a un altro tenant ed è stato registrato nel tenant durante il processo di consenso. Più precisamente, sono rappresentate solo da un oggetto entità servizio nel tenant, senza oggetto applicazione corrispondente. Per altre informazioni sulle differenze fra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure AD](active-directory-application-objects.md).

Per rimuovere l'accesso di un'applicazione alla directory (dopo aver concesso il consenso), l'amministratore della società deve rimuovere la relativa entità servizio. L'amministratore deve avere l'accesso di amministratore globale e può rimuovere l'applicazione tramite il portale di Azure oppure usare i [cmdlet di Azure AD PowerShell](https://go.microsoft.com/fwlink/?LinkId=294151) per rimuovere l'accesso.

## <a name="next-steps"></a>Passaggi successivi

Vedere le altre guide introduttive correlate sulla gestione delle app:

* [Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md)
* [Configurare un'applicazione client per l'accesso ad API Web](quickstart-configure-app-access-web-apis.md)
* [Configurare un'applicazione per l'esposizione di API Web](quickstart-configure-app-expose-web-apis.md)
* [Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md)
