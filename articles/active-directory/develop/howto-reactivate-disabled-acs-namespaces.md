---
title: Come riattivare spazi dei nomi disabilitati del Servizio di controllo di accesso (ACS)
description: Informazioni su come trovare e abilitare gli spazi dei nomi disabilitati del Servizio di controllo di accesso (ACS) e richiedere un'estensione per mantenerli abilitati fino al 4 febbraio 2019.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fe1517cce8a1f9b1024085340ffb927b282f0fb
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540291"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Procedura: Riattivare spazi dei nomi del Servizio di controllo di accesso disabilitati

A novembre 2017 è stato annunciato il ritiro in data 7 novembre 2018 del Servizio di controllo di accesso di Microsoft Azure, un servizio di Azure Active Directory (Azure AD).

A partire dalla data dell'annuncio sono stati inviati messaggi di posta elettronica all'indirizzo di amministrazione delle sottoscrizioni di ACS in merito al ritiro di ACS 12 mesi, 9 mesi, 6 mesi, 3 mesi, 1 mese, 2 settimane, 1 settimana e 1 giorno prima della data di ritiro del 7 novembre 2018.

Il 3 ottobre 2018 è stata annunciata (tramite posta elettronica e un [post di blog](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) un'offerta di estensione per i clienti che non possono completare la migrazione prima del 7 novembre 2018. L'annuncio includeva anche le istruzioni per richiedere l'estensione.

## <a name="why-your-namespace-is-disabled"></a>Motivi per cui lo spazio dei nomi è disabilitato

Se il cliente non ha acconsentito esplicitamente all'estensione, la disabilitazione degli spazi dei nomi ACS è prevista a partire dal 7 novembre 2018. È necessario aver già richiesto l'estensione al 4 febbraio 2019. In caso contrario, non sarà possibile abilitare gli spazi dei nomi con PowerShell.

> [!NOTE]
> È necessario essere un amministratore o un coamministratore della sottoscrizione per eseguire i comandi di PowerShell e richiedere un'estensione.

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

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        dove `[Command-Name]` è il nome del comando ACS.
1. Connettersi ad ACS usando il cmdlet **Connect-AcsAccount**. 

    Potrebbe essere necessario modificare i criteri di esecuzione eseguendo **Set-ExecutionPolicy** prima di poter eseguire il comando.
1. Elencare le sottoscrizioni di Azure disponibili usando il cmdlet **Get-AcsSubscription**.
1. Elencare gli spazi dei nomi ACS usando il cmdlet **Get-AcsNamespace**.
1. Verificare che gli spazi dei nomi siano disabilitati verificando che `State` sia `Disabled`.

    [![Verificare che gli spazi dei nomi siano disabilitati](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    È anche possibile usare `nslookup {your-namespace}.accesscontrol.windows.net` per verificare se il dominio è ancora attivo.

1. Abilitare gli spazi dei nomi ACS usando il cmdlet **Enable-AcsNamespace**.

    Dopo aver abilitato uno o più spazi dei nomi, è possibile richiedere un'estensione in modo che non venga disabilitato di nuovo prima del 4 febbraio 2019. A partire da questa data tutte le richieste per il servizio ACS avranno esito negativo.

## <a name="request-an-extension"></a>Richiedere un'estensione

A partire dal 21 gennaio 2019 verranno accettate nuove richieste di estensione.

Si inizierà a disabilitare gli spazi dei nomi per i clienti che hanno richiesto estensioni al 4 febbraio 2019. È comunque possibile riabilitare gli spazi dei nomi tramite PowerShell, ma gli spazi dei nomi verranno disabilitati di nuovo dopo 48 ore.

Dopo il 4 marzo 2019 i clienti non potranno più riabilitare gli spazi dei nomi tramite PowerShell.

Ulteriori estensioni non verranno più approvate automaticamente. Se è necessario più tempo per eseguire la migrazione, contattare il [supporto tecnico di Azure](https://portal.azure.com/#create/Microsoft.Support) per fornire una sequenza temporale dettagliata della migrazione.

### <a name="to-request-an-extension"></a>Per richiedere un'estensione

1. Accedere al portale di Azure e creare una [nuova richiesta di supporto](https://portal.azure.com/#create/Microsoft.Support).
1. Compilare il nuovo modulo di richiesta di supporto, come illustrato nell'esempio seguente.

    | Campo della richiesta di supporto | Value |
    |-----------------------|--------------------|
    | **Tipo di problema** | `Technical` |
    | **Sottoscrizione** | Impostare sulla propria sottoscrizione |
    | **Servizio** | `All services` |
    | **Risorsa** | `General question/Resource not available` |
    | **Tipo di problema** | `ACS to SAS Migration` |
    | **Oggetto** | Descrivere il problema |

   ![Nuova richiesta di supporto tecnico](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Guida e supporto

- Se si verificano problemi dopo aver seguito questa procedura, contattare il [supporto tecnico di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Per domande o commenti e suggerimenti sul ritiro di ACS, contattare Microsoft all'indirizzo acsfeedback@microsoft.com.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare le informazioni sul ritiro di ACS in [Procedura: Eseguire la migrazione dal Servizio di controllo di accesso di Azure](active-directory-acs-migration.md).
