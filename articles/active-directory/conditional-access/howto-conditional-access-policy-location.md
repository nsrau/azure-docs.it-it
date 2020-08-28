---
title: Accesso condizionale - Bloccare l'accesso in base alla posizione - Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per bloccare l'accesso alle risorse in base alla posizione IP
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
ms.openlocfilehash: a531692264a768e4f6cb8e6475807789df049d03
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049147"
---
# <a name="conditional-access-block-access-by-location"></a>Accesso condizionale: Bloccare l'accesso in base alla località

Con la condizione relativa alla posizione nell'accesso condizionale, è possibile controllare l'accesso alle app cloud in base al percorso di rete dell'utente. La condizione relativa alla posizione viene comunemente usata per bloccare l'accesso da paesi o aree geografiche da cui non dovrebbe provenire traffico verso l'organizzazione.

## <a name="define-locations"></a>Definire le posizioni

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale** > **Posizioni specifiche**.
1. Scegliere **Nuova posizione**.
1. Assegnare un nome alla posizione.
1. Scegliere **Intervalli IP** se si conoscono gli specifici intervalli di indirizzi IPv4 accessibili dall'esterno che fanno parte di tale posizione o di tali **Paesi/aree geografiche**.
   1. Specificare gli **intervalli IP** oppure selezionare i **paesi o le aree geografiche** per la posizione specificata.
      * Se si sceglie Paesi/aree geografiche, è possibile scegliere di includere aree sconosciute.
1. Scegliere **Salva**

Altre informazioni sulla condizione relativa alla posizione nell'accesso condizionale sono disponibili nell'articolo [Condizione relativa alla posizione nell'accesso condizionale di Azure Active Directory](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. Per le organizzazioni è consigliabile creare uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**
   1. In **Includi** selezionare **Tutti gli utenti**.
1. In **app Cloud o azioni**  >  **includere**e selezionare **tutte le app Cloud**.
1. In **Condizioni** > **Posizione**
   1. impostare **Configura** su **Sì**
   1. In **Includi**selezionare le **località selezionate**
   1. Selezionare la posizione bloccata creata per l'organizzazione.
   1. Fare clic su **Seleziona**.
1. In **controlli di accesso** > selezionare **Blocca accesso**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare i criteri di accesso condizionale.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-insights-reporting.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)
