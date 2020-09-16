---
title: Accesso condizionale - Bloccare l'autenticazione legacy - Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per bloccare i protocolli di autenticazione legacy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b4a0a411f51eed3a262ee59eb8e93b983755af3
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601555"
---
# <a name="conditional-access-block-legacy-authentication"></a>Accesso condizionale: Bloccare l'autenticazione legacy

A causa dell'aumento dei rischi associati ai protocolli di autenticazione legacy, Microsoft consiglia alle organizzazioni di bloccare le richieste di autenticazione che usano questi protocolli e di richiedere l'autenticazione moderna.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

I passaggi seguenti consentono di creare un criterio di accesso condizionale per bloccare le richieste di autenticazione legacy. Questo criterio viene inserito inizialmente in [Modalità solo report](howto-conditional-access-insights-reporting.md) in modo che gli amministratori possano determinare l'effetto che avrà sugli utenti esistenti. Quando gli amministratori hanno verificato che il criterio funziona come previsto, lo possono **attivare** o gestirne la distribuzione aggiungendo gruppi specifici ed escludendone altri.

1. Accedere al **portale di Azure** come amministratore globale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. Per le organizzazioni è consigliabile creare uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere tutti gli account che devono continuare a poter usare l'autenticazione legacy. Escludere almeno un account evitare di venire bloccati. Se non si esclude alcun account, non sarà possibile creare questo criterio.
   1. Selezionare **Operazione completata**.
1. In **App Cloud o azioni** selezionare **Tutte le app Cloud**.
   1. Selezionare **Operazione completata**.
1. In **condizioni**  >  **app client**impostare **Configura** su **Sì**.
   1. Controllare solo le caselle **client di Exchange ActiveSync** e **altri client**.
   1. Selezionare **Operazione completata**.
1. In **Controlli di accesso** > **Concedi** selezionare **Blocca accesso**.
   1. Scegliere **Seleziona**.
1. Confermare le impostazioni e impostare **Attiva criterio** su **Solo report**.
1. Selezionare **Crea** per creare e abilitare il criterio.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-insights-reporting.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)

[Come configurare un dispositivo multifunzione o un'applicazione per l'invio di messaggi di posta elettronica tramite Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
