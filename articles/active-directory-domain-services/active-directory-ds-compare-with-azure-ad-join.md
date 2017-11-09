---
title: Confrontare l'aggiunta ad Azure AD e Azure Active Directory Domain Services | Microsoft Docs
description: Scelta tra l'aggiunta ad Azure AD e Azure AD Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 8f34688c53dc909b0ec97df34bbfc7a24209a314
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Scegliere tra l'aggiunta ad Azure Active Directory e Azure Active Directory Domain Services
Questo articolo descrive le differenze tra l'aggiunta ad Azure Active Directory (AD) e Azure AD Domain Services e facilita la scelta in base ai casi d'uso.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Dispositivi registrati in Azure AD e dispositivi aggiunti ad Azure AD
Azure AD consente di gestire l'identità dei dispositivi usati dall'organizzazione e di controllare l'accesso alle risorse aziendali da questi dispositivi. Gli utenti possono registrare il dispositivo personale (Bring Your Own Device) con Azure AD, che fornisce un'identità al dispositivo stesso. Azure AD sarà quindi in grado di autenticare i dispositivi con cui gli utenti accedono ad Azure AD per usare risorse protette. È anche possibile gestire i dispositivi con un software di gestione di dispositivi mobili (MDM, Mobile Device Management) come Microsoft Intune. Questa funzionalità consente l'accesso alle risorse sensibili solo tramite dispositivi gestiti e conformi ai criteri.

È anche possibile aggiungere ad Azure AD dispositivi di proprietà dell'organizzazione. Questo meccanismo offre gli stessi vantaggi della registrazione di un dispositivo personale con Azure AD. Gli utenti possono anche accedere al dispositivo usando le credenziali aziendali. I dispositivi aggiunti ad Azure AD offrono i vantaggi seguenti:
* Single sign-on (SSO) per le applicazioni protette da Azure AD
* Roaming conforme ai criteri dell'organizzazione per le impostazioni utente su più dispositivi.
* Accesso a Windows Store for Business tramite le credenziali aziendali.
* Windows Hello for Business
* Accesso alle app e alle risorse solo tramite dispositivi conformi ai criteri aziendali.

| **Tipo di dispositivo** | **Piattaforme** | **Meccanismo** |
|:---| --- | --- |
| Dispositivi personali | Windows 10, iOS, Android, Mac OS | Registrazione in Azure AD |
| Dispositivo di proprietà dell'organizzazione non aggiunto ad AD locale | Windows 10 | Aggiunta ad Azure AD |
| Dispositivo di proprietà dell'organizzazione aggiunto ad AD locale | Windows 10 | Aggiunta a Azure AD ibrido |

In un dispositivo aggiunto o registrato con Azure AD, l'autenticazione utente avviene tramite protocolli moderni basati su OAuth/OpenID Connect. Questi protocolli, progettati per funzionare via Internet, sono estremamente utili negli scenari per dispositivi mobili in cui gli utenti accedono alle risorse aziendali da qualsiasi posizione.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Aggiunta a domini gestiti di Azure AD Domain Services
In una rete virtuale di Azure, Azure AD Domain Services rende disponibile un dominio di AD gestito. Per aggiungere computer al dominio gestito è possibile usare meccanismi tradizionali di aggiunta al dominio. Al dominio gestito è possibile aggiungere client Windows (Windows 7, Windows 10) e computer Windows Server. Al dominio gestito è anche possibile aggiungere computer Linux e Mac OS. Quando si aggiunge un computer a un dominio di AD, gli utenti possono accedere al computer usando le credenziali aziendali. Questi computer possono essere gestiti tramite Criteri di gruppo, imponendo quindi la conformità ai criteri di sicurezza dell'organizzazione.

In un computer aggiunto al dominio, l'autenticazione utente avviene tramite il protocollo di autenticazione NTLM o Kerberos. Perché l'autenticazione utente funzioni, il computer aggiunto al dominio deve avere visibilità sui controller di dominio del dominio gestito. Il computer aggiunto al dominio, quindi, deve essere nella stessa rete virtuale del dominio gestito. In alternativa, il computer aggiunto al dominio deve essere connesso al dominio gestito attraverso una rete virtuale in peering o tramite una connessione VPN/ExpressRoute da sito a sito. Di conseguenza, questo meccanismo non è molto adatto ai dispositivi mobili o ai dispositivi che si connettono alle risorse dall'esterno della rete aziendale.

> [!NOTE]
> Dal punto di vista tecnico è possibile aggiungere una workstation client locale al dominio gestito tramite una connessione VPN o ExpressRoute da sito a sito. Per i dispositivi degli utenti finali, tuttavia, è consigliabile la registrazione con Azure AD (dispositivi personali) o l'aggiunta ad Azure AD (dispositivi aziendali). Questo meccanismo funziona meglio via Internet e consente agli utenti di lavorare ovunque. Azure AD Domain Services è perfetto per le macchine virtuali server Windows o Linux distribuite nelle reti virtuali di Azure in cui sono distribuite le applicazioni.


## <a name="summary---key-differences"></a>Riepilogo: differenze principali
| **Aspetto** | **Aggiunta ad Azure AD** | **Servizi di dominio Azure Active Directory** |
|:---| --- | --- |
| Dispositivo controllato da | Azure AD | Dominio gestito di Azure AD Domain Services |
| Rappresentazione nella directory | Oggetti dispositivo nella directory di Azure AD. | Oggetti computer nel dominio gestito di AAD-DS. |
| Autenticazione | Protocolli basati su OAuth/OpenID Connect | Protocolli Kerberos e NTLM |
| gestione | Software di gestione di dispositivi mobili (MDM), ad esempio Intune | Criteri di gruppo |
| Rete | Funziona attraverso Internet | Richiede che i computer si trovino nella stessa rete virtuale del dominio gestito.|
| Ideale per... | Dispositivi mobili o desktop degli utenti finali | Macchine virtuali server distribuite in Azure |


## <a name="next-steps"></a>Passaggi successivi
### <a name="learn-more-about-azure-ad-domain-services"></a>Ulteriori informazioni su Azure AD Domain Services
* [Panoramica di Azure AD Domain Services](active-directory-ds-overview.md)
* [Funzionalità](active-directory-ds-features.md)
* [Scenari di distribuzione](active-directory-ds-scenarios.md)
* [Scoprire se Azure AD Domain Services sono appropriati ai casi d'uso](active-directory-ds-comparison.md)
* [Comprendere come Azure AD Domain Services si sincronizza con la directory di Azure AD](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Altre informazioni sull'aggiunta ad Azure AD
* [Introduction to device management in Azure Active Directory](../active-directory/device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)

### <a name="get-started-with-azure-ad-domain-services"></a>Introduzione ai servizi di dominio Azure AD
* [Abilitare Azure AD Domain Services tramite il portale di Azure](active-directory-ds-getting-started.md)
