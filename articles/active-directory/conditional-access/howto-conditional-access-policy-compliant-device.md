---
title: Accesso condizionale-Richiedi dispositivi conformi-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per richiedere i dispositivi conformi
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d19cc39147608dafc4576f4c8bad0533ad1b32d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77521915"
---
# <a name="conditional-access-require-compliant-devices"></a>Accesso condizionale: richiedere i dispositivi conformi

Le organizzazioni che hanno distribuito Microsoft Intune possono usare le informazioni restituite dai dispositivi per identificare i dispositivi che soddisfano i requisiti di conformità, ad esempio:

* Richiesta di sblocco di un PIN
* Richiesta della crittografia del dispositivo
* Richiesta di una versione minima o massima del sistema operativo
* La richiesta di un dispositivo non è jailbroken o rooted

Le informazioni di conformità dei criteri vengono trasmesse a Azure AD dove l'accesso condizionale può prendere decisioni per concedere o bloccare l'accesso alle risorse. Altre informazioni sui criteri di conformità del dispositivo sono disponibili nell'articolo [impostare regole sui dispositivi per consentire l'accesso alle risorse dell'organizzazione con Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Creare un criterio di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per richiedere che i dispositivi che accedono alle risorse siano contrassegnati come conformi ai criteri di conformità di Intune dell'organizzazione.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **utenti e gruppi** e scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **Includi**Selezionare **tutte le app Cloud**.
   1. Se è necessario escludere applicazioni specifiche dal criterio, è possibile selezionarle nella scheda **Escludi** in **Seleziona app Cloud escluse** e scegliere **Seleziona**.
   1. Selezionare **Operazione completata**.
1. In **controlli di accesso** > **concedere**Selezionare **Richiedi che il dispositivo sia contrassegnato come conforme**.
   1. Scegliere **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare per abilitare i criteri.

### <a name="known-behavior"></a>Comportamento noto

In Windows 7, iOS, Android, macOS e alcuni Web browser di terze parti Azure AD identifica il dispositivo usando un certificato client di cui viene effettuato il provisioning quando il dispositivo viene registrato con Azure AD. Quando un utente accede per la prima volta tramite il browser, all'utente viene richiesto di selezionare il certificato. L'utente finale deve selezionare questo certificato prima di poter continuare a utilizzare il browser.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Determinare l'effetto usando la modalità solo report di accesso condizionale](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)

[I criteri di conformità del dispositivo funzionano con Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
