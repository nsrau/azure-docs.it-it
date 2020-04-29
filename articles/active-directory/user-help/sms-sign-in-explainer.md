---
title: Esperienza utente di accesso SMS per numero di telefono (anteprima)-Azure AD
description: Altre informazioni sull'esperienza utente di accesso SMS per numeri di telefono nuovi o esistenti
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378834"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Usa il tuo numero di telefono come nome utente (anteprima)

La registrazione di un dispositivo consente al telefono di accedere ai servizi dell'organizzazione e non consente all'organizzazione di accedere al telefono. Se si è un amministratore, è possibile trovare altre informazioni in [configurare e abilitare gli utenti per l'autenticazione basata su SMS](../authentication/howto-authentication-sms-signin.md).

Se l'organizzazione non ha reso disponibile l'accesso SMS, non verrà visualizzata alcuna opzione per la registrazione di un telefono con l'account.  

## <a name="when-you-have-a-new-phone-number"></a>Quando si dispone di un nuovo numero di telefono

Se si ottiene un nuovo telefono o un nuovo numero e lo si registra con un'organizzazione per la quale è disponibile l'accesso a SMS, si verifica la normale procedura di registrazione del telefono:

1. Selezionare **Aggiungi metodo**.
1. Selezionare **telefono**.
1. Immettere il numero di telefono e selezionare **Invia un messaggio di testo a un codice**.
1. Dopo aver immesso il codice, fare clic su **Avanti**.
1. Verrà visualizzato il messaggio "SMS Verified". Il telefono è stato registrato correttamente. "

> [!Important]
> A causa di un problema noto nell'anteprima, per un breve periodo di tempo l'aggiunta del numero di telefono non registrerà il numero per l'accesso a SMS. Dovrai accedere con il numero aggiunto e seguire le istruzioni per registrare il numero per l'accesso a SMS.

### <a name="when-the-phone-number-is-in-use"></a>Quando il numero di telefono è in uso

Se si prova a usare un numero di telefono usato da un altro utente nell'organizzazione, verrà visualizzato il messaggio seguente:

![Messaggio di errore quando il numero di telefono è già in uso](media/sms-sign-in-explainer/sms-sign-in-error.png)

Rivolgersi all'amministratore per correggere il problema.

## <a name="when-you-have-an-existing-number"></a>Quando si dispone di un numero esistente

Se si usa già un numero di telefono con un'organizzazione e si usa il numero di telefono come nome utente, i passaggi seguenti consentono di eseguire l'accesso.

1. Quando è disponibile l'accesso SMS, viene visualizzato un banner che chiede se si vuole abilitare il numero di telefono per l'accesso a SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Inoltre, viene visualizzato un pulsante **Abilita** se si seleziona il punto di inserimento nel riquadro metodo telefono:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Per abilitare il metodo, selezionare **Abilita**. Viene richiesto di confermare l'azione:

    ![Finestra di dialogo di conferma per abilitare l'accesso SMS per un numero di telefono](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selezionare **Abilita**.

## <a name="when-you-remove-your-phone-number"></a>Quando si rimuove il numero di telefono

1. Per eliminare il numero di telefono, selezionare il pulsante Elimina nel riquadro metodo telefonico di accesso SMS.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Quando viene richiesto di confermare l'azione, fare clic su **OK**.

Non è possibile rimuovere un numero di telefono in uso come metodo di accesso predefinito. Per rimuovere il numero, è necessario modificare il metodo di accesso predefinito, quindi rimuovere di nuovo il numero di telefono.
