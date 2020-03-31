---
title: Accesso condizionale - Richiedi dispositivi conformi - Azure Active DirectoryConditional Access - Require compliant devices - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per richiedere dispositivi conformiCreate a custom Conditional Access policy to require compliant devices
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
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295209"
---
# <a name="conditional-access-require-compliant-devices"></a>Accesso condizionale: richiedi dispositivi conformiConditional Access: Require compliant devices

Le organizzazioni che hanno distribuito Microsoft Intune possono usare le informazioni restituite dai propri dispositivi per identificare i dispositivi che soddisfano i requisiti di conformità, ad esempio:Organizations who have deployed Microsoft Intune can use the information returned from their devices to identify devices that meet compliance requirements such as:

* Richiedere lo sblocco di un PIN
* Richiesta della crittografia del dispositivo
* Richiedere una versione minima o massima del sistema operativo
* Richiedere un dispositivo non è jailbroken o radicato

Queste informazioni sulla conformità dei criteri vengono inoltrate ad Azure AD in cui l'accesso condizionale può prendere decisioni per concedere o bloccare l'accesso alle risorse. Altre informazioni sui criteri di conformità dei dispositivi sono disponibili nell'articolo [Impostare le regole nei dispositivi per consentire l'accesso alle risorse nell'organizzazione usando IntuneMore](/intune/protect/device-compliance-get-started) information about device compliance policies can be found in the article, Set rules on devices to allow access to resources in your organization using Intune

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

I passaggi seguenti consentono di creare criteri di accesso condizionale per richiedere che i dispositivi che accedono alle risorse vengano contrassegnati come conformi ai criteri di conformità di Intune dell'organizzazione.

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o di rottavetro dell'organizzazione. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare Tutte le **app cloud**.
   1. Se è necessario escludere applicazioni specifiche dai criteri, è possibile sceglirle dalla scheda **Escludi** in **Selezionare le app cloud escluse** e scegliere **Seleziona**.
   1. Selezionare **Fatto**.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì**e selezionare **Fatto**.
1. In **Controlli di** > accesso**Concedi**selezionare **Richiedi che il dispositivo sia contrassegnato come conforme.**
   1. Selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare per abilitare i criteri.

> [!NOTE]
> È possibile registrare i nuovi dispositivi in Intune anche se si seleziona Richiedi che il **dispositivo sia contrassegnato come conforme** per Tutti gli **utenti** e Tutte le **app cloud** usando i passaggi precedenti. Richiedere che il **dispositivo sia contrassegnato come controllo conforme** non blocca la registrazione di Intune.Require device to be marked as compliant control does not block Intune enrollment. 

### <a name="known-behavior"></a>Comportamento noto

In Windows 7, iOS, Android, macOS e alcuni browser Web di terze parti Azure AD identifica il dispositivo usando un certificato client di cui viene eseguito il provisioning quando il dispositivo viene registrato con Azure AD. Quando un utente accede per la prima volta tramite il browser, all'utente viene richiesto di selezionare il certificato. L'utente finale deve selezionare questo certificato prima di poter continuare a utilizzare il browser.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)

[Funzionamento dei criteri di conformità del dispositivo con Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
