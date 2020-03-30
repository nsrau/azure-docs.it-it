---
title: Pianificare l'aggiunta di Azure Active Directory ibrido - Azure Active DirectoryPlan hybrid Azure Active Directory join - Azure Active Directory
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
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129248"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procedura: Pianificare l'implementazione di join ibridi di Azure Active DirectoryHow To: Plan your hybrid Azure Active Directory join implementation

Analogamente agli utenti, i dispositivi sono un'altra identità di base da proteggere e da usare per proteggere le risorse ovunque e in qualsiasi momento. È possibile raggiungere questo obiettivo trasferendo e gestendo le identità dei dispositivi in Azure AD con uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti tramite il Single Sign-On (SSO) in tutte le risorse locali e nel cloud. Allo stesso tempo, è possibile proteggere l'accesso alle risorse cloud e locali con [accesso condizionale](../active-directory-conditional-access-azure-portal.md).

Se si dispone di un ambiente Active Directory (AD) locale e si desidera aggiungere i computer aggiunti al dominio di Active Directory ad Azure AD, è possibile eseguire l'aggiunta ibrida ad Azure AD. Questo articolo fornisce i passaggi correlati per implementare un'aggiunta ad Azure AD ibrido nell'ambiente. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con l'introduzione alla gestione delle identità dei [dispositivi in Azure Active Directory.](../device-management-introduction.md)

> [!NOTE]
> La versione minima richiesta del controller di dominio per l'aggiunta ad Azure AD ibrido di Windows 10 è Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

Per pianificare l'implementazione di Azure AD ibrido, è necessario acquisire familiarità con:

|   |   |
| --- | --- |
| ![Controllo][1] | Esaminare i dispositivi supportati |
| ![Controllo][1] | Esaminare le informazioni utili |
| ![Controllo][1] | Esaminare la convalida controllata dell'aggiunta ibrida ad Azure ADReview controlled validation of hybrid Azure AD join |
| ![Controllo][1] | Selezionare lo scenario in base all'infrastruttura di identità |
| ![Controllo][1] | Esaminare il supporto UPN di Active Directory locale per l'aggiunta ibrida ad Azure ADReview on-premises AD UPN support for hybrid Azure AD join |

## <a name="review-supported-devices"></a>Esaminare i dispositivi supportati

L'aggiunta ad Azure AD ibrido supporta un'ampia gamma di dispositivi Windows. Poiché la configurazione per i dispositivi che eseguono versioni precedenti di Windows richiede passaggi aggiuntivi o diversi, i dispositivi supportati sono raggruppati in due categorie:

### <a name="windows-current-devices"></a>Dispositivi Windows correnti

- Windows 10
- Windows Server 2016
  - **Nota:** i clienti del cloud nazionale di Azure richiedono la versione 1809Note : Azure National cloud customers require version 1809
- Windows Server 2019

Per i dispositivi che eseguono il sistema operativo desktop Windows, la versione supportata è elencata in questo articolo [Informazioni sulla versione di Windows 10](/windows/release-information/). Come procedura consigliata, Microsoft consiglia di eseguire l'aggiornamento alla versione più recente di Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivi Windows di livello inferiore

- Windows 8.1
- Il supporto per Windows 7 è terminato il 14 gennaio 2020. Per ulteriori informazioni, vedere [Supporto per Windows 7 terminato](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Per informazioni di supporto su Windows Server 2008 e 2008 R2, vedere [Preparazione per windows Server 2008 fine del supporto](https://www.microsoft.com/cloud-platform/windows-server-2008).

Come primo passaggio della pianificazione, è consigliabile esaminare l'ambiente e determinare se è necessario supportare dispositivi Windows di livello inferiore.

## <a name="review-things-you-should-know"></a>Esaminare le informazioni utili

### <a name="unsupported-scenarios"></a>Scenari non supportati
- L'aggiunta ad Azure AD ibrido non è attualmente supportata se l'ambiente è costituito da una singola foresta di Active Directory che sincronizza i dati di identità in più tenant di Azure AD.

- L'aggiunta ad Azure AD ibrido non è supportata per Windows Server che esegue il ruolo Controller di dominio (DC).

- L'aggiunta ad Azure AD ibrido non è supportata nei dispositivi di livello inferiore di Windows quando si usano il roaming delle credenziali o il roaming del profilo utente o il profilo obbligatorio.

- Server Core OS non supporta alcun tipo di registrazione del dispositivo.

### <a name="os-imaging-considerations"></a>Considerazioni sull'imaging del sistema operativo
- Se si utilizza l'Utilità preparazione sistema (Sysprep) e si usa un'immagine precedente a **Windows 10 1809** per l'installazione, assicurarsi che l'immagine non provenga da un dispositivo già registrato con Azure AD come aggiunta ibrida ad Azure AD.

- Se si utilizza uno snapshot della macchina virtuale (VM) per creare macchine virtuali aggiuntive, assicurarsi che lo snapshot non provenga da una macchina virtuale già registrata con Azure AD come join ibrido di Azure AD.

- Se si usa il filtro scrittura [unificato](/windows-hardware/customize/enterprise/unified-write-filter) e tecnologie simili che cancellano le modifiche al disco al riavvio, è necessario applicarle dopo che il dispositivo è stato aggiunto ad Azure AD ibrido. L'abilitazione di tali tecnologie prima del completamento del join di Azure AD ibrido comporterà l'unjoin del dispositivo ad ogni riavvioEnabling such technologies before completion of Hybrid Azure AD join will result in the device getting unjoined on every reboot

### <a name="handling-devices-with-azure-ad-registered-state"></a>Gestione dei dispositivi con lo stato registrato di Azure ADHandling devices with Azure AD registered state
Se i dispositivi aggiunti al dominio Windows 10 sono [adesempio registrati](overview.md#getting-devices-in-azure-ad) nel tenant, potrebbe portare a un doppio stato di aggiunta ad Azure AD ibrido e dispositivo registrato di Azure AD. Ti consigliamo di eseguire l'aggiornamento a Windows 10 1803 (con KB4489894 applicato) o superiore per risolvere automaticamente questo scenario. Nelle versioni precedenti alla 1803 è necessario rimuovere manualmente lo stato registrato di Azure AD prima di abilitare l'aggiunta ad Azure AD ibrido. Nelle versioni 1803 e successive, sono state apportate le seguenti modifiche per evitare questo doppio stato:

- Qualsiasi stato registrato di Azure AD esistente per un utente verrà rimosso automaticamente <i>dopo che il dispositivo è stato aggiunto ad Azure AD ibrido e lo stesso utente accede.</i> Ad esempio, se l'utente A aveva uno stato registrato di Azure AD nel dispositivo, lo stato doppio per l'utente viene pulito solo quando l'utente accede al dispositivo. se sono presenti più utenti sullo stesso dispositivo, lo stato doppio viene pulito singolarmente quando tali utenti accedono.
- È possibile impedire che il dispositivo aggiunto al dominio venga registrato in Azure AD aggiungendo questa chiave del Registro di sistema: HKLM SOFTWARE , Policies , Microsoft Windows WorkplaceJoin, "BlockAADWorkplaceJoin", dword:00000001.
- In Windows 10 1803, se hai configurato Windows Hello for Business, l'utente deve riconfigurare Windows Hello for Business dopo la pulizia del doppio stato. Questo problema è stato risolto con KB4512509

> [!NOTE]
> Il dispositivo registrato di Azure AD non verrà rimosso automaticamente se gestito da Intune.The Azure AD registered device will not be automatically removed if it is managed by Intune.

### <a name="additional-considerations"></a>Altre considerazioni
- Se l'ambiente utilizza l'infrastruttura VDI (Virtual Desktop Infrastructure), vedere [Identità del dispositivo e virtualizzazione desktop](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

- L'aggiunta ad Azure AD ibrido è supportata per TPM 2.0 conforme allo standard FIPS e non per TPM 1.2. Se i dispositivi dispongono di TPM 1.2 conforme allo standard FIPS, è necessario disabilitarli prima di procedere con l'aggiunta ad Azure AD ibrido. Microsoft non fornisce alcuno strumento per disabilitare la modalità FIPS per i TPM in quanto dipende dal produttore del TPM. Contattare l'OEM hardware per assistenza. 

- A partire dalla versione 10 1903, i TPM 1.2 non vengono usati con l'aggiunta ibrida ad Azure AD e i dispositivi con tali TPM verranno considerati come se non avessero un TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Esaminare la convalida controllata dell'aggiunta ibrida ad Azure ADReview controlled validation of hybrid Azure AD join

Quando tutti i prerequisiti sono presenti, i dispositivi Windows verranno registrati automaticamente come dispositivi nel tenant di Azure AD. Lo stato di queste identità del dispositivo in Azure AD viene definito aggiunta ibrida ad Azure AD. Altre informazioni sui concetti illustrati in questo articolo sono disponibili nell'articolo Introduzione alla gestione delle identità dei dispositivi in Azure Active Directory.More information about the concepts covered in this article can be found in the [article Introduction to device identity management in Azure Active Directory.](overview.md)

Le organizzazioni potrebbero voler eseguire una convalida controllata della partecipazione ibrida ad Azure AD prima di abilitarla nell'intera organizzazione contemporaneamente. Esaminare la [convalida controllata dell'aggiunta ibrida ad Azure AD](hybrid-azuread-join-control.md) per comprendere come esediarlo.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selezionare lo scenario in base all'infrastruttura di identità

L'aggiunta di Azure AD ibrido funziona con ambienti gestiti e federati a seconda che l'UPN sia instradabile o non instradabile. Vedere la parte inferiore della pagina per la tabella negli scenari supportati.  

### <a name="managed-environment"></a>Ambiente gestito

Un ambiente gestito può essere distribuito tramite la [sincronizzazione dell'hash delle password](/azure/active-directory/hybrid/whatis-phs) o l'[autenticazione pass-through](/azure/active-directory/hybrid/how-to-connect-pta) con [Single Sign-On facile](/azure/active-directory/hybrid/how-to-connect-sso).

Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.

### <a name="federated-environment"></a>Ambiente federato

Un ambiente federato deve includere un provider di identità che supporta i requisiti riportati di seguito. Se l'ambiente federato usa Active Directory Federation Services (AD FS), i requisiti seguenti sono già supportati.

- **Rivendicazione WIAORMULTIAUTHN:** Questa attestazione è necessaria per eseguire l'aggiunta ibrida ad Azure AD per i dispositivi di livello inferiore di Windows.This claim is required to do hybrid Azure AD join for Windows down-level devices.
- **Protocollo WS-Trust:** Questo protocollo è necessario per autenticare i dispositivi aggiunti ad Azure AD ibridi di Windows con Azure AD. Quando si usa AD FS, è necessario abilitare gli endpoint WS-Trust seguenti: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Sia **adfs/services/trust/2005/windowstransport** che **adfs/services/trust/13/windowstransport** devono essere abilitati solo come endpoint per Intranet e NON devono essere esposti come endpoint per Extranet tramite Proxy applicazione Web. Per altre informazioni su come disabilitare gli endpoint Windows WS-Trust, vedere [Disabilitare gli endpoint Windows WS-Trust sul proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). È possibile visualizzare gli endpoint abilitati tramite la console di gestione di ADFS in**Endpoint** **di servizio.** > 

> [!NOTE]
> Azure AD non supporta certificati o smart card nei domini gestiti.

A partire dalla versione 1.1.819.0, in Azure AD Connect è presente una procedura guidata per configurare l'aggiunta ad Azure AD ibrido che semplifica in modo significativo il processo di configurazione. Se non si può prendere in considerazione l'installazione della versione richiesta di Azure AD Connect, vedere [come configurare manualmente la registrazione dei dispositivi](hybrid-azuread-join-manual.md). 

In base allo scenario che corrisponde all'infrastruttura di identità, vedere:Based on the scenario that matches your identity infrastructure, see:

- [Configurare l'aggiunta di Azure Active Directory ibrido per l'ambiente federatoConfigure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configurare l'aggiunta ibrida ad Azure Active Directory per l'ambiente gestitoConfigure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Esaminare il supporto UPN degli utenti di Active Directory locali per l'aggiunta ad Azure AD ibridoReview on-premises AD users UPN support for Hybrid Azure AD join

In alcuni casi, gli UPN degli utenti di Active Directory locali potrebbero essere diversi dagli UPN di Azure AD. In questi casi l'aggiunta ad Azure AD ibrido di Windows 10 offre supporto limitato per i nomi dell'entità utente di AD locale in base al [metodo di autenticazione](/azure/security/fundamentals/choose-ad-authn), al tipo di dominio e alla versione di Windows 10. Nell'ambiente possono essere presenti due tipi di nomi dell'entità utente di AD locale:

- UpN degli utenti instradabili: un UPN instradabile ha un dominio verificato valido, registrato con un registrar. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.org è il dominio primario in AD locale di proprietà di Contoso e [verificato in Azure AD](/azure/active-directory/fundamentals/add-custom-domain).
- UPN utenti non instradabili: un UPN non instradabile non dispone di un dominio verificato. È applicabile solo all'interno della rete privata dell'organizzazione. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.local è il dominio primario in AD locale, ma non è un dominio verificabile in Internet e viene usato solo nella rete di Contoso.

> [!NOTE]
> Le informazioni contenute in questa sezione si applicano solo a un UPN degli utenti locali. Non è applicabile a un suffisso di dominio del computer locale (esempio: computer1.contoso.local).

La tabella seguente contiene informazioni sul supporto per questi nomi dell'entità utente di AD locale nell'aggiunta ad Azure AD ibrido di Windows 10

| Tipo di nome dell'entità utente di AD locale | Tipo di dominio | Versione di Windows 10 | Descrizione |
| ----- | ----- | ----- | ----- |
| Instradabile | Federato | Dalla versione 1703 | Disponibile a livello generale |
| Non instradabile | Federato | Dalla versione 1803 | Disponibile a livello generale |
| Instradabile | Gestito | Dalla versione 1803 | Disponibile in genere, Azure AD SSPR nella schermata di blocco di Windows non è supportato |
| Non instradabile | Gestito | Non supportate | |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'aggiunta ibrida ad Azure Active Directory per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
> [Configurare l'aggiunta di Azure Active Directory ibrido per l'ambiente gestitoConfigure](hybrid-azuread-join-managed-domains.md) hybrid Azure Active Directory join for federated environment Configure hybrid Azure Active Directory join for managed environment

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
