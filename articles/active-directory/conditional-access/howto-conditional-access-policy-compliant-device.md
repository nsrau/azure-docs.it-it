---
title: Accesso condizionale-Richiedi dispositivi conformi-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per richiedere i dispositivi conformi
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
ms.openlocfilehash: c65b4ede6f4851418bf17d42db5b3215dafa9234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995174"
---
# <a name="conditional-access-require-compliant-devices"></a>Accesso condizionale: richiedere i dispositivi conformi

Le organizzazioni che hanno distribuito Microsoft Intune possono usare le informazioni restituite dai dispositivi per identificare i dispositivi che soddisfano i requisiti di conformità, ad esempio:

* Richiesta di sblocco di un PIN
* Richiesta della crittografia del dispositivo
* Richiesta di una versione minima o massima del sistema operativo
* La richiesta di un dispositivo non è jailbroken o rooted

Le informazioni di conformità dei criteri vengono trasmesse a Azure AD dove l'accesso condizionale può prendere decisioni per concedere o bloccare l'accesso alle risorse. Altre informazioni sui criteri di conformità del dispositivo sono disponibili nell'articolo [impostare regole sui dispositivi per consentire l'accesso alle risorse dell'organizzazione con Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per richiedere che i dispositivi che accedono alle risorse siano contrassegnati come conformi ai criteri di conformità di Intune dell'organizzazione.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni**  >  **Includi**selezionare **tutte le app Cloud**.
   1. Se è necessario escludere applicazioni specifiche dal criterio, è possibile selezionarle nella scheda **Escludi** in **Seleziona app Cloud escluse** e scegliere **Seleziona**.
   1. Selezionare **Operazione completata**.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì** e selezionare **Fine**.
1. In **Access controls**  >  **concessione**controlli di accesso selezionare **Richiedi che il dispositivo sia contrassegnato come conforme**.
   1. Scegliere **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

> [!NOTE]
> È possibile registrare i nuovi dispositivi in Intune anche se si seleziona Richiedi che i dispositivi **siano contrassegnati come conformi** per **tutti gli utenti** e **tutte le app Cloud** usando la procedura precedente. **Richiedi che il dispositivo sia contrassegnato come controllo conforme** non blocca la registrazione di Intune. 

### <a name="known-behavior"></a>Comportamento noto

In Windows 7, iOS, Android, macOS e alcuni Web browser di terze parti Azure AD identifica il dispositivo usando un certificato client di cui viene effettuato il provisioning quando il dispositivo viene registrato con Azure AD. Quando un utente accede per la prima volta tramite il browser, all'utente viene richiesto di selezionare il certificato. L'utente finale deve selezionare questo certificato prima di poter continuare a utilizzare il browser.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)

[Funzionamento dei criteri di conformità del dispositivo con Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
