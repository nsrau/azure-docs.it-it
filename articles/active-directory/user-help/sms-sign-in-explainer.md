---
title: Esperienza utente di accesso SMS per il numero di telefono (anteprima) - Azure AD
description: Ulteriori informazioni sull'esperienza utente di accesso SMS per numeri di telefono nuovi o esistenti
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378834"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Utilizzare il numero di telefono come nome utente (anteprima)

La registrazione di un dispositivo consente al telefono di accedere ai servizi dell'organizzazione e non consente all'organizzazione di accedere al telefono. Se si è un amministratore, è possibile trovare ulteriori informazioni in [Configurare e abilitare gli utenti per l'autenticazione basata su SMS.](../authentication/howto-authentication-sms-signin.md)

Se la tua organizzazione non ha reso disponibile l'accesso SMS, non vedrai un'opzione per farlo quando registri un telefono con il tuo account.  

## <a name="when-you-have-a-new-phone-number"></a>Quando si dispone di un nuovo numero di telefono

Se si ottiene un nuovo telefono o un nuovo numero e lo si registra con un'organizzazione per la quale è disponibile l'accesso SMS, si verifica il normale processo di registrazione telefonica:

1. Selezionare **Aggiungi metodo**.
1. Selezionare **Telefono**.
1. Inserisci il numero di telefono e seleziona **Inviami un codice.**
1. Dopo aver immesso il codice, selezionare **Avanti**.
1. Verrà visualizzato un messaggio che dice "SMS verificato. Il telefono è stato registrato con successo."

> [!Important]
> A causa di un problema noto nell'anteprima, per un breve periodo di tempo l'aggiunta di numero di telefono non registrerà il numero per l'accesso SMS. Dovrai accedere con il numero aggiunto e quindi seguire le istruzioni per registrare il numero per l'accesso SMS.

### <a name="when-the-phone-number-is-in-use"></a>Quando il numero di telefono è in uso

Se si tenta di utilizzare un numero di telefono utilizzato da un altro utente dell'organizzazione, verrà visualizzato il messaggio seguente:

![Messaggio di errore quando il numero di telefono è già in uso](media/sms-sign-in-explainer/sms-sign-in-error.png)

Contatta l'amministratore per risolvere il problema.

## <a name="when-you-have-an-existing-number"></a>Quando si dispone di un numero esistente

Se si sta già utilizzando un numero di telefono con un'organizzazione e il numero di telefono come nome utente diventa disponibile, i passaggi seguenti consentono di accedere.

1. Quando è disponibile l'accesso tramite SMS, viene visualizzato un banner in cui viene chiesto se si desidera abilitare il numero di telefono per l'accesso SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Inoltre, viene visualizzato un pulsante **Abilita** se si seleziona il punto di inserimento nel riquadro del metodo del telefono:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Per abilitare il metodo, **selezionare Abilita**. Viene richiesto di confermare l'azione:

    ![Finestra di dialogo di conferma per abilitare l'accesso SMS per un numero di telefono](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selezionare **Abilita**.

## <a name="when-you-remove-your-phone-number"></a>Quando rimuovi il tuo numero di telefono

1. Per eliminare il numero di telefono, selezionare il pulsante Elimina nel riquadro Metodo del telefono di accesso SMS.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Quando viene richiesto di confermare l'azione, selezionare **OK**.

Non è possibile rimuovere un numero di telefono in uso come metodo di accesso predefinito. Per rimuovere il numero, è necessario modificare il metodo di accesso predefinito, quindi rimuovere nuovamente il numero di telefono.
