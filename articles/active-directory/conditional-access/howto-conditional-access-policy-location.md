---
title: Accesso condizionale-Blocca accesso per località-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per bloccare l'accesso alle risorse in base alla posizione IP
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26299ca12071b521fc825a0b1ab1425f24af67e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803598"
---
# <a name="conditional-access-block-access-by-location"></a>Accesso condizionale: bloccare l'accesso in base alla località

Con la condizione location nell'accesso condizionale, è possibile controllare l'accesso alle app cloud in base al percorso di rete di un utente. La condizione di posizione viene comunemente usata per bloccare l'accesso dai paesi in cui l'organizzazione sa che il traffico non deve provenire da.

## <a name="define-locations"></a>Definire le posizioni

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** **sicurezza** >  > **l'accesso condizionale**.
1. Scegliere **nuova posizione**.
1. Assegnare un nome al percorso.
1. Scegliere **intervalli IP** se si conoscono gli intervalli di indirizzi IPv4 accessibili esternamente specifici che costituiscono la località o i **paesi/aree geografiche**.
   1. Specificare gli **intervalli di indirizzi IP** oppure selezionare i paesi o le **aree geografiche** per il percorso specificato.
      * Se si sceglie paesi/aree geografiche, è possibile scegliere di includere aree sconosciute.
1. Scegliere **Salva**

Altre informazioni sulla condizione di posizione nell'accesso condizionale sono disponibili nell'articolo, [Qual è la condizione di posizione in Azure Active Directory accesso condizionale](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** **sicurezza** >  > **l'accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti**.
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **includere**, selezionare **tutte le app Cloud**e selezionare **fine**.
1. In **conditions** > **location**.
   1. Imposta **Configura** su **Sì**
   1. **Includi** selezioni **località selezionate**
   1. Selezionare il percorso bloccato creato per l'organizzazione.
   1. Fare clic su **seleziona** > **done** > **done**.
1. In **controllo di accesso** > **blocco**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)
