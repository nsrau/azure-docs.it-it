---
title: Plan hybrid Azure Active Directory join - Azure Active Directory
description: Informazioni su come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a4f85aeaf2fb263ba2df8f34a51f9e25c212aff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379328"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>How To: Plan your hybrid Azure Active Directory join implementation

Analogamente agli utenti, i dispositivi sono un'altra identità di base da proteggere e da usare per proteggere le risorse ovunque e in qualsiasi momento. È possibile raggiungere questo obiettivo trasferendo e gestendo le identità dei dispositivi in Azure AD con uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti tramite il Single Sign-On (SSO) in tutte le risorse locali e nel cloud. At the same time, you can secure access to your cloud and on-premises resources with [Conditional Access](../active-directory-conditional-access-azure-portal.md).

If you have an on-premises Active Directory (AD) environment and you want to join your AD domain-joined computers to Azure AD, you can accomplish this by doing hybrid Azure AD join. Questo articolo fornisce i passaggi correlati per implementare un'aggiunta ad Azure AD ibrido nell'ambiente. 

## <a name="prerequisites"></a>Prerequisiti

This article assumes that you are familiar with the [Introduction to device identity management in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> The minimum required domain controller version for Windows 10 hybrid Azure AD join is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

Per pianificare l'implementazione di Azure AD ibrido, è necessario acquisire familiarità con:

|   |   |
| --- | --- |
| ![Controllo][1] | Esaminare i dispositivi supportati |
| ![Controllo][1] | Esaminare le informazioni utili |
| ![Controllo][1] | Review controlled validation of hybrid Azure AD join |
| ![Controllo][1] | Select your scenario based on your identity infrastructure |
| ![Controllo][1] | Review on-premises AD UPN support for hybrid Azure AD join |

## <a name="review-supported-devices"></a>Esaminare i dispositivi supportati

L'aggiunta ad Azure AD ibrido supporta un'ampia gamma di dispositivi Windows. Poiché la configurazione per i dispositivi che eseguono versioni precedenti di Windows richiede passaggi aggiuntivi o diversi, i dispositivi supportati sono raggruppati in due categorie:

### <a name="windows-current-devices"></a>Dispositivi Windows correnti

- Windows 10
- Windows Server 2016
- Windows Server 2019

For devices running the Windows desktop operating system, supported version are listed in this article [Windows 10 release information](https://docs.microsoft.com/windows/release-information/). As a best practice, Microsoft recommends you upgrade to the latest version of Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivi Windows di livello inferiore

- Windows 8.1
- Windows 7. For support information on Windows 7, see [Support for Windows 7 is ending](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. For support information on Windows Server 2008 and 2008 R2, see [Prepare for Windows Server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

Come primo passaggio della pianificazione, è consigliabile esaminare l'ambiente e determinare se è necessario supportare dispositivi Windows di livello inferiore.

## <a name="review-things-you-should-know"></a>Esaminare le informazioni utili

Hybrid Azure AD join is currently not supported if your environment consists of a single AD forest synchronizing identity data to more than one Azure AD tenant.

If your environment uses virtual desktop infrastructure (VDI), see [Device identity and desktop virtualization](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Hybrid Azure AD join is supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Hybrid Azure AD join. Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. Starting from WIndows 10 1903 release, TPMs 1.2 are not used for hybrid Azure AD join and devices with those TPMs will be considered as if they don't have a TPM.

Hybrid Azure AD join is not supported for Windows Server running the Domain Controller (DC) role.

Hybrid Azure AD join is not supported on Windows down-level devices when using credential roaming or user profile roaming or mandatory profile.

If you are relying on the System Preparation Tool (Sysprep) and if you are using a **pre-Windows 10 1809** image for installation, make sure that image is not from a device that is already registered with Azure AD as Hybrid Azure AD join.

If you are relying on a Virtual Machine (VM) snapshot to create additional VMs, make sure that snapshot is not from a VM that is already registered with Azure AD as Hybrid Azure AD join.

If your Windows 10 domain joined devices are [Azure AD registered](overview.md#getting-devices-in-azure-ad) to your tenant, it could lead to a dual state of Hybrid Azure AD joined and Azure AD registered device. We recommend upgrading to Windows 10 1803 (with KB4489894 applied) or above to automatically address this scenario. In pre-1803 releases, you will need to remove the Azure AD registered state manually before enabling Hybrid Azure AD join. In 1803 and above releases, the following changes have been made to avoid this dual state:

- Any existing Azure AD registered state would be automatically removed <i>after the device is Hybrid Azure AD joined</i>.
- You can prevent your domain joined device from being Azure AD registered by adding this registry key - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- In Windows 10 1803, if you have Windows Hello for Business configured, the user needs to re-setup Windows Hello for Business after the dual state clean up.This issue has been addressed with KB4512509

> [!NOTE]
> The Azure AD registered device will not be automatically removed if it is managed by Intune.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Review controlled validation of hybrid Azure AD join

When all of the pre-requisites are in place, Windows devices will automatically register as devices in your Azure AD tenant. The state of these device identities in Azure AD is referred as hybrid Azure AD join. More information about the concepts covered in this article can be found in the article [Introduction to device identity management in Azure Active Directory](overview.md).

Organizations may want to do a controlled validation of hybrid Azure AD join before enabling it across their entire organization all at once. Review the article [controlled validation of hybrid Azure AD join](hybrid-azuread-join-control.md) to understand how to accomplish it.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Select your scenario based on your identity infrastructure

Hybrid Azure AD join works with both, managed and federated environments depending on whether the UPN is routable or non-routable. See bottom of the page for table on supported scenarios.  

### <a name="managed-environment"></a>Ambiente gestito

Un ambiente gestito può essere distribuito tramite la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) o l'[autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) con [Single Sign-On facile](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.

### <a name="federated-environment"></a>Ambiente federato

Un ambiente federato deve includere un provider di identità che supporta i requisiti riportati di seguito. Se l'ambiente federato usa Active Directory Federation Services (AD FS), i requisiti seguenti sono già supportati.

- **WIAORMULTIAUTHN claim:** This claim is required to do hybrid Azure AD join for Windows down-level devices.
- **WS-Trust protocol:** This protocol is required to authenticate Windows current hybrid Azure AD joined devices with Azure AD. Quando si usa AD FS, è necessario abilitare gli endpoint WS-Trust seguenti: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Sia **adfs/services/trust/2005/windowstransport** che **adfs/services/trust/13/windowstransport** devono essere abilitati solo come endpoint per Intranet e NON devono essere esposti come endpoint per Extranet tramite Proxy applicazione Web. Per altre informazioni su come disabilitare gli endpoint Windows WS-Trust, vedere [Disabilitare gli endpoint Windows WS-Trust sul proxy](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). È possibile verificare gli endpoint abilitati nella console di gestione di AD FS, in **Servizio** > **Endpoint**.

> [!NOTE]
> Azure AD non supporta certificati o smart card nei domini gestiti.

A partire dalla versione 1.1.819.0, in Azure AD Connect è presente una procedura guidata per configurare l'aggiunta ad Azure AD ibrido che semplifica enormemente il processo di configurazione. Se non si può prendere in considerazione l'installazione della versione richiesta di Azure AD Connect, vedere [come configurare manualmente la registrazione dei dispositivi](hybrid-azuread-join-manual.md). 

Based on the scenario that matches your identity infrastructure, see:

- [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Review on-premises AD UPN support for Hybrid Azure AD join

I nomi dell'entità utente di AD locale a volte possono essere diversi da quelli di Azure AD. In questi casi l'aggiunta ad Azure AD ibrido di Windows 10 offre supporto limitato per i nomi dell'entità utente di AD locale in base al [metodo di autenticazione](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), al tipo di dominio e alla versione di Windows 10. Nell'ambiente possono essere presenti due tipi di nomi dell'entità utente di AD locale:

- Routable UPN: A routable UPN has a valid verified domain, that is registered with a domain registrar. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.org è il dominio primario in AD locale di proprietà di Contoso e [verificato in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).
- Non-routable UPN: A non-routable UPN does not have a verified domain. È applicabile solo all'interno della rete privata dell'organizzazione. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.local è il dominio primario in AD locale, ma non è un dominio verificabile in Internet e viene usato solo nella rete di Contoso.

La tabella seguente contiene informazioni sul supporto per questi nomi dell'entità utente di AD locale nell'aggiunta ad Azure AD ibrido di Windows 10

| Tipo di nome dell'entità utente di AD locale | Tipo di dominio | Versione di Windows 10 | Description |
| ----- | ----- | ----- | ----- |
| Instradabile | Federato | Dalla versione 1703 | Disponibile a livello generale |
| Non instradabile | Federato | Dalla versione 1803 | Disponibile a livello generale |
| Instradabile | Managed Disks | Dalla versione 1803 | Generally available, Azure AD SSPR on Windows lockscreen is not supported |
| Non instradabile | Managed Disks | Supporto non disponibile | |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
> [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
