---
title: Accesso condizionale - Blocca l'accesso per posizione - Azure Active DirectoryConditional Access - Block access by location - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per bloccare l'accesso alle risorse in base alla posizione IPCreate a custom Conditional Access policy to block access to resources by IP location
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
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295178"
---
# <a name="conditional-access-block-access-by-location"></a>Accesso condizionale: bloccare l'accesso in base alla posizioneConditional access: Block access by location

Con la condizione di posizione in Accesso condizionale, è possibile controllare l'accesso alle app cloud in base al percorso di rete di un utente. La condizione di posizione viene comunemente utilizzata per bloccare l'accesso da paesi in cui l'organizzazione sa che il traffico non deve provenire.

## <a name="define-locations"></a>Definire le ubicazioni

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a**Percorsi denominati****per l'accesso** > condizionale della**sicurezza** > di **Azure Active Directory** > .
1. Scegliere **Nuova posizione**.
1. Assegna un nome alla tua posizione.
1. Scegliere **gli intervalli IP** se si conoscono gli intervalli di indirizzi IPv4 accessibili esternamente specifici che costituiscono tale località o **Paesi/aree**.
   1. Specificare gli **intervalli IP** o selezionare I paesi o le **aree** geografiche per la località specificata.
      * Se si sceglie Paesi/Aree geografiche, è possibile scegliere di includere aree sconosciute.
1. Scegliere **Salva**

Ulteriori informazioni sulla condizione di posizione in Accesso condizionale sono disponibili nell'articolo Informazioni sulla condizione di [posizione in Azure Active Directory Conditional Access](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare Tutte le **app cloud**e **Infine**.
1. In**Posizione** **condizioni** > .
   1. Impostare **Configura su** **Sì**
   1. **Includi** selezionate **posizioni selezionate**
   1. Selezionare la posizione bloccata creata per l'organizzazione.
   1. Fare clic su **Seleziona** > **Fatto** > **Done**.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì**e selezionare **Fatto**.
1. In **Controlli di** > accesso**Blocco**e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)
