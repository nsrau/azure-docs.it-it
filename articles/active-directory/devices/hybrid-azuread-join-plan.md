---
title: Come pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido in Azure Active Directory (Azure AD) | Microsoft Docs
description: Informazioni su come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64dd8067654246f7c9a077d027c068df820f439d
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688710"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procedura: Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido

In modo analogo a un utente, un dispositivo è un'altra identità core che si desidera proteggere e usarlo per proteggere le risorse in qualsiasi momento e da qualsiasi posizione. È possibile raggiungere questo obiettivo attraverso l'integrazione e la gestione delle identità dei dispositivi in Azure AD usando uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti tramite il Single Sign-On (SSO) in tutte le risorse locali e nel cloud. Analogamente, è possibile proteggere l'accesso alle risorse locali e nel cloud con l'[accesso condizionale](../active-directory-conditional-access-azure-portal.md).

Se è disponibile un ambiente Active Directory (AD) locale e si desidera aggiungere i computer aggiunti a un dominio AD Azure ad, è possibile farlo eseguendo l'aggiunta ad Azure AD ibrido. Questo articolo fornisce i passaggi correlati per implementare un'aggiunta ad Azure AD ibrido nell'ambiente. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che abbia familiarità con le [Introduzione alla gestione delle identità di dispositivo in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minima richiesta per i livelli di funzionalità foresta e dominio funzionale per il join di Windows 10 ad Azure AD ibrido è Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

Per pianificare l'implementazione di Azure AD ibrido, è necessario acquisire familiarità con:

|   |   |
| --- | --- |
| ![Controllo][1] | Esaminare i dispositivi supportati |
| ![Controllo][1] | Esaminare le informazioni utili |
| ![Controllo][1] | Esaminare convalida controllata di aggiunta ad Azure AD ibrido |
| ![Controllo][1] | Selezionare lo scenario basato sull'infrastruttura di identità |
| ![Controllo][1] | La revisione da sito locale AD UPN il supporto per la gestione ibrida aggiunta ad Azure AD |

## <a name="review-supported-devices"></a>Esaminare i dispositivi supportati

L'aggiunta ad Azure AD ibrido supporta un'ampia gamma di dispositivi Windows. Poiché la configurazione per i dispositivi che eseguono versioni precedenti di Windows richiede passaggi aggiuntivi o diversi, i dispositivi supportati sono raggruppati in due categorie:

### <a name="windows-current-devices"></a>Dispositivi Windows correnti

- Windows 10
- Windows Server 2016
- Windows Server 2019

Per i dispositivi che eseguono il sistema operativo desktop Windows, versione supportate sono elencate in questo articolo [informazioni sulla versione di Windows 10](https://docs.microsoft.com/windows/release-information/). Come procedura consigliata, si consiglia di che eseguire l'aggiornamento alla versione più recente di Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivi Windows di livello inferiore

- Windows 8.1
- Windows 7. Per informazioni sul supporto in Windows 7, vedere questo articolo [sta terminando il supporto per Windows 7](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Come primo passaggio della pianificazione, è consigliabile esaminare l'ambiente e determinare se è necessario supportare dispositivi Windows di livello inferiore.

## <a name="review-things-you-should-know"></a>Esaminare le informazioni utili

Aggiunta ad Azure AD ibrido non è attualmente supportata se l'ambiente è costituito da una singola foresta AD, sincronizzando i dati di identità a più di un tenant di Azure AD.

Aggiunta ad Azure AD ibrido non è supportato quando si usa virtual desktop infrastructure (VDI).

Identità ibrida di Azure AD non è supportata per i moduli TPM compatibili con FIPS. Se i dispositivi dispongono di moduli TPM compatibili con FIPS, è necessario disabilitare prima di procedere con l'aggiunta ad Azure AD ibrido. Microsoft non fornisce gli strumenti per la disabilitazione della modalità FIPS per TPM come dipende dal produttore del TPM. Per supporto, contattare l'OEM di hardware.

Aggiunta ad Azure AD ibrido non è supportato per Windows Server che esegue il ruolo di Controller di dominio (DC).

Aggiunta ad Azure AD ibrido non è supportato nei dispositivi di livello inferiore di Windows quando si usa il roaming delle credenziali o roaming del profilo utente.

Se si intende usare l'utilità preparazione sistema (Sysprep) e se si usa un' **precedenti a Windows 10 1809** immagine per l'installazione, assicurarsi che tale immagine non da un dispositivo che è già registrato con Azure AD come aggiunta all'identità ibrida di Azure AD.

Se ci si basa su uno snapshot macchina virtuale (VM) per creare altre macchine virtuali, assicurarsi che lo snapshot non proviene da una macchina virtuale che è già registrata con Azure AD come aggiunta all'identità ibrida di Azure AD.

Se i dispositivi aggiunti a un dominio di Windows 10 sono già [registrati in Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) nel tenant, è consigliabile rimuovere questo stato prima di abilitare l'aggiunta ad Azure AD ibrido. Dalla versione 1809 di Windows 10 sono state introdotte le modifiche seguenti per evitare questo stato doppio:

- L'eventuale stato esistente di registrato in Azure AD viene rimosso automaticamente dopo l'aggiunta del dispositivo ad Azure AD ibrido.
- È possibile impedire che il dispositivo aggiunto al dominio in Azure AD registrata mediante l'aggiunta di questa chiave del Registro di sistema - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" runasppl"=DWORD:00000001.
- Questa modifica è ora disponibile per il rilascio di Windows 10 1803 con KB4489894 applicato. Tuttavia, se si dispone di Windows Hello for Business configurati, l'utente è obbligatorio per Ri-il programma di installazione Windows Hello for Business dopo il doppio dello stato di pulizia.


## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Esaminare convalida controllata di aggiunta ad Azure AD ibrido

Quando tutti i prerequisiti sono presenti, i dispositivi di Windows verranno automaticamente registrati come dispositivi nel tenant di Azure AD. Lo stato di queste identità dei dispositivi in Azure AD è definito come l'aggiunta ad Azure AD ibrido. Altre informazioni sui concetti illustrati in questo articolo sono disponibili negli articoli [Introduzione alla gestione delle identità di dispositivo in Azure Active Directory](overview.md) e [prevede l'aggiunta di Azure Active Directory ibrido implementazione](hybrid-azuread-join-plan.md).

Le organizzazioni potrebbe essere necessario eseguire una convalida controllata di aggiunta ad Azure AD ibrido prima di abilitarlo per tutte contemporaneamente l'intera organizzazione. Vedere l'articolo [controllata la convalida dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-control.md) per comprendere come ottenere questo risultato.


## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selezionare lo scenario basato sull'infrastruttura di identità

Aggiunta ad Azure AD ibrido funziona con gli ambienti, gestiti sia federativi.  

### <a name="managed-environment"></a>Ambiente gestito

Un ambiente gestito può essere distribuita tramite [sincronizzazione Hash Password (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) oppure [passare attraverso sull'autenticazione](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) con [Seamless Single Sign On](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.

### <a name="federated-environment"></a>Ambiente federato

Un ambiente federato deve avere un provider di identità che supporta i requisiti seguenti:

- **Protocollo WS-Trust:** Questo protocollo è necessario eseguire l'autenticazione di Windows corrente Azure AD ibrido i dispositivi con Azure AD.
- **Attestazione WIAORMULTIAUTHN:** Questa attestazione è necessaria eseguire l'operazione di aggiunta ad Azure AD ibrido per i dispositivi legacy di Windows.

Se si dispone di un ambiente federato con Active Directory Federation Services (ADFS), i requisiti precedenti sono già supportati.

> [!NOTE]
> Azure AD non supporta certificati o smart card nei domini gestiti.

A partire dalla versione 1.1.819.0, in Azure AD Connect è presente una procedura guidata per configurare l'aggiunta ad Azure AD ibrido La procedura guidata semplifica enormemente il processo di configurazione. Se non si può prendere in considerazione l'installazione della versione richiesta di Azure AD Connect, vedere [come configurare manualmente la registrazione dei dispositivi](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Basato sullo scenario che corrisponde a infrastruttura di identità, vedere:

- [Configurare aggiunta di Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
- [Configurare aggiunta all'identità ibrida Azure Active Directory per un ambiente gestito](hybrid-azuread-join-managed-domains.md)



## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Revisione UPN AD supporto locale per l'aggiunta ad Azure AD ibrido

I nomi dell'entità utente di AD locale a volte possono essere diversi da quelli di Azure AD. In questi casi l'aggiunta ad Azure AD ibrido di Windows 10 offre supporto limitato per i nomi dell'entità utente di AD locale in base al [metodo di autenticazione](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), al tipo di dominio e alla versione di Windows 10. Nell'ambiente possono essere presenti due tipi di nomi dell'entità utente di AD locale:

- Nome dell'entità utente instradabile: ha un dominio verificato valido, registrato con un registrar. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.org è il dominio primario in AD locale di proprietà di Contoso e [verificato in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).
- Nome dell'entità utente non instradabile: non ha un dominio verificato. È applicabile solo all'interno della rete privata dell'organizzazione. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.local è il dominio primario in AD locale, ma non è un dominio verificabile in Internet e viene usato solo nella rete di Contoso.

La tabella seguente contiene informazioni sul supporto per questi nomi dell'entità utente di AD locale nell'aggiunta ad Azure AD ibrido di Windows 10

| Tipo di nome dell'entità utente di AD locale | Tipo di dominio | Versione di Windows 10 | Descrizione |
| ----- | ----- | ----- | ----- |
| Instradabile | Federato | Dalla versione 1703 | Disponibile a livello generale |
| Non instradabile | Federato | Dalla versione 1803 | Disponibile a livello generale |
| Instradabile | Gestito | Non supportate | |
| Non instradabile | Gestito | Non supportate | |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiunta di Azure Active Directory ibrido Configura per un ambiente federato](hybrid-azuread-join-federated-domains.md)
> [aggiunta di Azure Active Directory ibrido Configura per un ambiente gestito](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
