---
title: Come riattivare spazi dei nomi disabilitati del Servizio di controllo di accesso (ACS)
description: Informazioni su come trovare e abilitare gli spazi dei nomi disabilitati del Servizio di controllo di accesso (ACS) e richiedere un'estensione per mantenerli abilitati fino al 4 febbraio 2019.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 298afda7549690a9ea0314bff63a714be50a33b9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019880"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Procedura: Riattivare gli spazi dei nomi disabilitati del Servizio di controllo di accesso

A novembre 2017 è stato annunciato il ritiro in data 7 novembre 2018 del Servizio di controllo di accesso di Microsoft Azure, un servizio di Azure Active Directory (Azure AD).

A partire dalla data dell'annuncio sono stati inviati diversi messaggi di posta elettronica all'indirizzo di amministrazione delle sottoscrizioni di ACS in merito al ritiro di ACS 12 mesi, 9 mesi, 6 mesi, 3 mesi, 1 mese, 2 settimane, 1 settimana e 1 giorno prima della data di ritiro del 7 novembre 2018.

Il 3 ottobre 2018 è stata annunciata (tramite posta elettronica e un [post di blog](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) un'offerta di estensione per i clienti che non possono completare la migrazione prima del 7 novembre 2018. L'annuncio includeva anche le istruzioni per richiedere l'estensione.

## <a name="why-your-namespace-is-disabled"></a>Motivi per cui lo spazio dei nomi è disabilitato

Se il cliente non ha acconsentito esplicitamente all'estensione, la disabilitazione degli spazi dei nomi ACS è prevista a partire dal 7 novembre 2018. Se si sono verificati problemi nelle comunicazioni e si vuole acconsentire esplicitamente all'estensione fino al 4 febbraio 2019, seguire le istruzioni riportate nelle sezioni seguenti.

> [!NOTE]
> È necessario essere un amministratore della sottoscrizione per eseguire i comandi di PowerShell e richiedere un'estensione.

## <a name="find-and-enable-your-acs-namespaces"></a>Trovare e abilitare gli spazi dei nomi ACS

È possibile usare ACS PowerShell per elencare tutti gli spazi dei nomi ACS e riattivare quelli che sono stati disabilitati.

1. Scaricare e installare il modulo PowerShell per ACS:
    1. Passare a PowerShell Gallery e scaricare [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Installare il modulo:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Ottenere un elenco di tutti i comandi disponibili:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Per ottenere informazioni su un comando specifico, eseguire:

        ```
        Get-Help [Command-Name] -Full
        ```
    
        dove `[Command-Name]` è il nome del comando ACS.
1. Elencare le sottoscrizioni di Azure disponibili usando il cmdlet **Get-AcsSubscription**.
1. Elencare gli spazi dei nomi ACS usando il cmdlet **Get-AcsNamespace**.
1. Verificare che gli spazi dei nomi siano disabilitati verificando che `State` sia `Disabled`.

    [![Verificare che gli spazi dei nomi siano disabilitati](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    È anche possibile usare `nslookup {your-namespace}.accesscontrol.windows.net` per verificare se il dominio è ancora attivo.

1. Abilitare gli spazi dei nomi ACS usando il cmdlet **Enable-AcsNamespace**.

    Dopo aver abilitato uno o più spazi dei nomi, è possibile richiedere un'estensione in modo che non venga disabilitato di nuovo prima del 4 febbraio 2019. A partire da questa data tutte le richieste per il servizio ACS avranno esito negativo.

## <a name="request-an-extension"></a>Richiedere un'estensione

1. Passare al portale di gestione degli spazi dei nomi ACS passando a `https://{your-namespace}.accesscontrol.windows.net`.
1. Selezionare il pulsante **Leggere le condizioni** per leggere le [Condizioni per l'utilizzo aggiornate](https://azure.microsoft.com/support/legal/access-control/); si verrà indirizzati a una pagina con le Condizioni per l'utilizzo aggiornate.

    [![Selezionare il pulsante Leggere le condizioni](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Selezionare **Richiedi estensione** sul banner nella parte superiore della pagina. Il pulsante viene abilitato solo dopo aver letto le [Condizioni per l'utilizzo aggiornate](https://azure.microsoft.com/support/legal/access-control/).

    [![Selezionare il pulsante Richiedi estensione](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. Dopo la registrazione della richiesta di estensione, la pagina si aggiorna con un nuovo banner nella parte superiore della pagina.

    [![Pagina aggiornata con il banner aggiornato](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Guida e supporto

- Se si verificano problemi dopo aver seguito questa procedura, contattare il [supporto tecnico di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Per domande o commenti e suggerimenti sul ritiro di ACS, contattare Microsoft all'indirizzo acsfeedback@microsoft.com.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare le informazioni sul ritiro di ACS in [Procedura: Eseguire la migrazione da Servizio di controllo di accesso di Azure](active-directory-acs-migration.md).
