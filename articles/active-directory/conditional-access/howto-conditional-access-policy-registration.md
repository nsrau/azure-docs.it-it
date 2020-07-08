---
title: Accesso condizionale - Informazioni di sicurezza combinate - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per la registrazione delle informazioni di sicurezza
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: acf17f4cef43fe4221781deedb130ef587208550
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993746"
---
# <a name="conditional-access-securing-security-info-registration"></a>Accesso condizionale: protezione della registrazione delle informazioni di sicurezza

È ora possibile proteggere i tempi e il modo in cui gli utenti eseguono la registrazione per Azure Multi-Factor Authentication e per la reimpostazione della password self-service con le azioni dell'utente nei criteri di accesso condizionale. Questa funzionalità in anteprima è disponibile per le organizzazioni che hanno abilitato l'[anteprima della registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md). Questa funzionalità può essere abilitata nelle organizzazioni che vogliono usare condizioni come un percorso di rete attendibile per limitare l'accesso per la registrazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service. Per altre informazioni sulle condizioni utilizzabili, vedere l'articolo [Accesso condizionale: condizioni](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare criteri per richiedere la registrazione da un percorso attendibile

I criteri seguenti si applicano a tutti gli utenti selezionati che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata e bloccano l'accesso a meno che la connessione non venga effettuata da un percorso contrassegnato come rete attendibile.

1. Nel **portale di Azure** andare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. In Nome immettere un nome per i criteri. Ad esempio, **Registrazione delle informazioni di sicurezza combinata su reti attendibili**.
1. In **Assegnazioni** selezionare **Utenti e gruppi** e quindi gli utenti e i gruppi a cui si vogliono applicare i criteri.

   > [!WARNING]
   > Gli utenti devono essere abilitati per la [registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md).

1. In **Applicazioni cloud o azioni** selezionare **Azioni utente** e quindi **Registra le informazioni di sicurezza**.
1. In **Condizioni** > **Percorsi**:
   1. Configurare **Sì**.
   1. Includere **Tutte le località**.
   1. Escludere **Tutte le posizioni attendibili**.
   1. Selezionare **Fine** nel pannello Percorsi.
   1. Selezionare **Fine** nel pannello Condizioni.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì** e selezionare **Fine**.
1. In **Controlli di accesso** > **Concedi**:
   1. Selezionare **Blocca accesso**.
   1. Quindi fare clic su **Seleziona**.
1. Impostare **Abilita criteri** su **Sì**.
1. Selezionare quindi **Salva**.

Al passaggio 6 per questi criteri, le organizzazioni possono effettuare alcune scelte. I criteri riportati sopra richiedono la registrazione da un percorso di rete attendibile. Le organizzazioni possono scegliere di utilizzare qualsiasi condizione disponibile invece di **Percorsi**. Tenere presente che si tratta di criteri di blocco, di conseguenza tutti gli elementi inclusi vengono bloccati e gli elementi non corrispondenti all'inclusione sono consentiti. 

Al passaggio 6 precedente si potrebbe scegliere di usare lo stato del dispositivo invece del percorso.

6. In **Condizioni** > **Stato dispositivo (anteprima)** :
   1. Configurare **Sì**.
   1. Includere **Tutti gli stati dei dispositivi**.
   1. Escludere **Dispositivo aggiunto ad Azure AD ibrido** e/o **Dispositivo contrassegnato come conforme**.
   1. Selezionare **Fine** nel pannello Percorsi.
   1. Selezionare **Fine** nel pannello Condizioni.

> [!WARNING]
> L'uso dello stato del dispositivo come condizione nei criteri potrebbe influire sugli utenti guest nella directory. La [modalità solo report](concept-conditional-access-report-only.md) consente di determinare l'impatto delle decisioni relative ai criteri.
> Si noti che la modalità solo report non è applicabile per i criteri di accesso condizionale con ambito "Azioni utente".

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)
