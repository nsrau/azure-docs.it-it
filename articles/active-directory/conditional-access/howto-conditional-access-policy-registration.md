---
title: Accesso condizionale-informazioni di sicurezza combinate-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per la registrazione delle informazioni di sicurezza
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457910"
---
# <a name="conditional-access-securing-security-info-registration"></a>Accesso condizionale: protezione della registrazione delle informazioni di sicurezza

È ora possibile proteggere quando e come gli utenti si registrano per Azure Multi-Factor Authentication e la reimpostazione della password self-service con le azioni dell'utente nei criteri di accesso condizionale. Questa funzionalità di anteprima è disponibile per le organizzazioni che hanno abilitato l' [anteprima della registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md). Questa funzionalità può essere abilitata nelle organizzazioni in cui si vuole usare condizioni come il percorso di rete attendibile per limitare l'accesso per la registrazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service (SSPR). Per ulteriori informazioni sulle condizioni utilizzabili, vedere l'articolo relativo all' [accesso condizionale: Conditions](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare un criterio per richiedere la registrazione da un percorso attendibile

I criteri seguenti si applicano a tutti gli utenti selezionati, che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata e bloccano l'accesso a meno che non si connettano da un percorso contrassegnato come rete attendibile.

1. Nella **portale di Azure**individuare **Azure Active Directory** > **Security** > **accesso condizionale**di sicurezza.
1. Selezionare **Nuovi criteri**.
1. In nome immettere un nome per il criterio. Ad esempio, la **registrazione delle informazioni di sicurezza combinata su reti attendibili**.
1. In **assegnazioni**selezionare **utenti e gruppi**e selezionare gli utenti e i gruppi a cui si desidera applicare questo criterio.

   > [!WARNING]
   > Gli utenti devono essere abilitati per la [registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md).

1. In **azioni o app Cloud**selezionare **azioni utente**e selezionare **registra informazioni di sicurezza**.
1. In **Conditions** > **posizioni**delle condizioni.
   1. Configurare **Sì**.
   1. Includere **qualsiasi percorso**.
   1. Escludere **tutti i percorsi attendibili**.
   1. Selezionare **fatto** nel pannello percorsi.
   1. Selezionare **fatto** nel pannello condizioni.
1. In **condizioni** > **app client (anteprima)** impostare **Configura** su **Sì**e fare clic su **fine**.
1. In **controllo** > di accesso**concedere**.
   1. Selezionare **Blocca accesso**.
   1. Quindi fare clic su **Seleziona**.
1. Impostare **Abilita criterio** su **Sì**.
1. Selezionare quindi **Salva**.

Al passaggio 6 di questo criterio, le organizzazioni hanno scelte che possono apportare. Il criterio precedente richiede la registrazione da un percorso di rete attendibile. Le organizzazioni possono scegliere di utilizzare le condizioni disponibili al posto delle **località**. Tenere presente che questo criterio è un criterio di blocco in modo che tutti gli oggetti inclusi siano bloccati e che tutto ciò che non corrisponde all'inclusione sia consentito. 

Alcuni possono scegliere di usare lo stato del dispositivo invece della posizione nel passaggio 6 precedente:

6. In **condizioni** > **stato del dispositivo (anteprima)**.
   1. Configurare **Sì**.
   1. Includere **tutto lo stato del dispositivo**.
   1. Escludi il **dispositivo Azure ad ibrido aggiunto** e/o **dispositivo contrassegnato come conforme**
   1. Selezionare **fatto** nel pannello percorsi.
   1. Selezionare **fatto** nel pannello condizioni.

> [!WARNING]
> Se si usa lo stato del dispositivo come condizione nel criterio, questo può influito sugli utenti guest nella directory. La [modalità solo report](concept-conditional-access-report-only.md) consente di determinare l'effetto delle decisioni sui criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Determinare l'effetto usando la modalità solo report di accesso condizionale](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)
