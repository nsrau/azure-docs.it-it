---
title: Come pianificare l'implementazione dell'aggiunta ad Azure Active Directory (Azure AD) | Microsoft Docs
description: Descrive i passaggi necessari per implementare dispositivi aggiunti ad Azure AD nell'ambiente in uso.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 34b2658ef4b25b3d545932ceffd2f3cf8969034e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309363"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Procedura: Pianificare l'implementazione dell'aggiunta ad Azure AD


L'aggiunta ad Azure AD permette di aggiungere dispositivi direttamente ad Azure AD senza la necessità di eseguire l'aggiunta ad Active Directory locale, mantenendo la produttività e la sicurezza degli utenti. L'aggiunta ad Azure AD è di livello aziendale per le distribuzioni su larga scala e con ambito.   

Questo articolo contiene le informazioni necessarie per pianificare l'implementazione dell'aggiunta ad Azure AD.

 
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con quanto descritto in [Introduzione alla gestione dei dispositivi in Azure Active Directory](../device-management-introduction.md).



## <a name="plan-your-implementation"></a>Pianificare l'implementazione

Per pianificare l'implementazione di Azure AD ibrido, è necessario acquisire familiarità con:

|   |   |
|---|---|
|![Segno di spunta][1]|Esame degli scenari|
|![Segno di spunta][1]|Esame dell'infrastruttura di gestione delle identità|
|![Segno di spunta][1]|Valutazione della gestione dei dispositivi|
|![Segno di spunta][1]|Analisi delle considerazioni relative a risorse e applicazioni|
|![Segno di spunta][1]|Identificazione delle opzioni di provisioning|
|![Segno di spunta][1]|Configurazione di Enterprise State Roaming|
|![Segno di spunta][1]|Configurazione dell'accesso condizionale|







## <a name="review-your-scenarios"></a>Esame degli scenari 

Anche se per determinati scenari può essere preferibile l'aggiunta ad Azure AD ibrido, l'aggiunta ad Azure AD permette la transizione verso un modello basato sul cloud con Windows. Se si prevede di modernizzare la gestione dei dispositivi e ridurre i costi IT correlati ai dispositivi, l'aggiunta ad Azure AD offre una base importante per il raggiungimento di questi obiettivi.  

 
È consigliabile prendere in considerazione l'aggiunta ad Azure AD se gli obiettivi sono allineati ai criteri seguenti:

- Si intende adottare Microsoft 365 come famiglia di prodotti per la produttività per gli utenti.

- Si vuole gestire i dispositivi con una soluzione di gestione dei dispositivi basata sul cloud.

- Si vuole semplificare il provisioning dei dispositivi per utenti geograficamente distribuiti.

- Si prevede di modernizzare l'infrastruttura delle applicazioni.
 

 

## <a name="review-your-identity-infrastructure"></a>Esame dell'infrastruttura di gestione delle identità  

L'aggiunta ad Azure AD funziona sia con ambienti gestiti sia con ambienti federati.  


### <a name="managed-environment"></a>Ambiente gestito

Un ambiente gestito può essere distribuito tramite la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) oppure l'[autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) con Seamless Single Sign On.

Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.


### <a name="federated-environment"></a>Ambiente federato

Un ambiente federato deve includere un provider di identità che supporta i protocolli WS-Trust e WS-Fed:

- **WS-Fed:** questo protocollo è necessario per aggiungere un dispositivo ad Azure AD.

- **WS-Trust:** questo protocollo è necessario per accedere a un dispositivo aggiunto ad Azure AD. 

Se il provider di identità non supporta questi protocolli, l'aggiunta ad Azure AD non funziona in modo nativo. A partire da Windows 10 1809, gli utenti possono accedere a un dispositivo aggiunto ad Azure AD con un provider di identità basato su SAML attraverso l'[accesso Web in Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Attualmente, l'accesso Web è una funzionalità solo in versione di anteprima.


### <a name="smartcards-and-certificate-based-authentication"></a>Smart card e autenticazione basata su certificato

Non è possibile usare smart card o l'autenticazione basata su certificato per aggiungere dispositivi ad Azure AD. Tuttavia, è possibile usare smart card per accedere ai dispositivi aggiunti ad Azure AD se è stato configurato AD FS.

**Consiglio:** implementare Windows Hello for Business per un'autenticazione sicura e senza password ai dispositivi Windows 10.


### <a name="user-configuration"></a>Configurazione degli utenti

Se si creano utenti in:

- **Active Directory locale**, è necessario sincronizzarli con Azure AD tramite [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 

- **Azure AD**, non sono necessarie altre configurazioni.

Gli [ID di accesso alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) non sono supportati nei dispositivi aggiunti ad Azure AD. Se gli utenti usano un ID di accesso alternativo, è consigliabile valutare di passare all'uso dei rispettivi UPN primari in Azure AD.



## <a name="assess-your-device-management"></a>Valutazione della gestione dei dispositivi

### <a name="supported-devices"></a>Dispositivi supportati

L'aggiunta ad Azure AD:

- È applicabile solo a dispositivi Windows 10. 

- Non è applicabile alle versioni precedenti di Windows o ad altri sistemi operativi. Se sono presenti dispositivi Windows 7/8.1, è necessario eseguire l'aggiornamento a Windows 10 per distribuire l'aggiunta ad Azure AD.
 
**Consiglio:** usare sempre la versione più recente di Windows 10 per sfruttare i vantaggi delle funzionalità aggiornate.


### <a name="management-platform"></a>Piattaforma di gestione

La gestione dei dispositivi aggiunti ad Azure AD è basata su una piattaforma MDM come Intune e su provider di servizi cloud MDM. Windows 10 include un agente MDM integrato che funziona con tutte le soluzioni MDM compatibili.

Esistono due approcci per la gestione dei dispositivi aggiunti ad Azure AD:

- **Solo MDM**: un dispositivo viene gestito in modo esclusivo da un provider MDM come Intune. Tutti i criteri vengono forniti come parte del processo di registrazione MDM. Per i clienti di Azure AD Premium o EMS, la registrazione MDM è un passaggio automatico integrato nell'aggiunta ad Azure AD.

- **Co-gestione**: un dispositivo viene gestito da un provider MDM e da SCCM. In questo approccio l'agente SCCM è installato in un dispositivo gestito da MDM per l'amministrazione di determinati aspetti.

Poiché i dispositivi aggiunti ad Azure AD non sono connessi ad Active Directory locale, i criteri di gruppo non sono supportati.


Se si usano criteri di gruppo, valutare la parità dei criteri MDM usando [MDM Migration Analysis Tool (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Esaminare i criteri supportati e non supportati per determinare se sia possibile usare una soluzione MDM anziché criteri di gruppo. Per i criteri non supportati, considerare quanto segue:

- I criteri non supportati sono necessari per utenti o dispositivi aggiunti ad Azure AD?

- I criteri non supportati sono applicabili in una distribuzione basata sul cloud?

Se la soluzione MDM non è disponibile tramite la raccolta di app Azure AD, è possibile aggiungerla seguendo il processo descritto in [Azure Active Directory integration with MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm) (Integrazione di Azure Active Directory con MDM). 

Tramite la co-gestione, è possibile usare SCCM per gestire determinati aspetti dei dispositivi, mentre i criteri vengono distribuiti tramite la piattaforma MDM. Microsoft Intune permette la co-gestione con SCCM. Per altre informazioni, vedere [Co-gestione per dispositivi Windows 10](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Se si usa un prodotto MDM diverso da Intune, verificare con il provider MDM gli scenari di co-gestione applicabili.

**Consiglio:** prendere in considerazione la gestione solo MDM per i dispositivi aggiunti ad Azure AD.



## <a name="understand-considerations-for-applications-and-resources"></a>Analisi delle considerazioni relative a risorse e applicazioni

È consigliabile eseguire la migrazione delle applicazioni da un ambiente locale al cloud per ottenere un'esperienza utente e un controllo di accesso migliori. Tuttavia, i dispositivi aggiunti ad Azure AD possono fornire uniformemente l'accesso sia ad applicazioni locali sia ad applicazioni cloud. Per altre informazioni, vedere [Funzionamento dell'accesso SSO alle risorse locali nei dispositivi aggiunti ad Azure AD](azuread-join-sso.md).

Le sezioni seguenti elencano alcune considerazioni per i diversi tipi di applicazioni e risorse.

### <a name="cloud-based-applications"></a>Applicazioni basate sul cloud

Se un'applicazione viene aggiunta alla raccolta di app Azure AD, gli utenti ottengono SSO tramite i dispositivi aggiunti ad Azure AD. Non sono necessarie altre configurazioni. Gli utenti ottengono SSO sia in Microsoft Edge sia in Chrome. Per Chrome, è necessario distribuire l'[estensione per gli account Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Tutte le applicazioni Win32 che:

- Si basano su Gestione account Web (WAM) per le richieste di token ottengono anch'esse SSO nei dispositivi aggiunti ad Azure AD. 

- Non si basano su WAM possono chiedere agli utenti di eseguire l'autenticazione. 


### <a name="on-premises-web-applications"></a>Applicazioni Web locali

Se le app sono compilate in modo personalizzato e/o ospitate in locale, è necessario aggiungerle ai siti attendibili del browser per:

- Abilitare il funzionamento dell'autenticazione integrata di Windows 
- Offrire agli utenti un'esperienza SSO senza richieste. 

Se si usa AD FS, vedere [Verificare e gestire l'accesso Single Sign-On con AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Consiglio:** per un'esperienza migliore, prendere in considerazione l'hosting nel cloud (ad esempio, Azure) e l'integrazione con Azure AD.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Applicazioni locali basate su protocolli legacy

Gli utenti ottengono SSO dai dispositivi aggiunti ad Azure AD se il dispositivo ha accesso a un controller di dominio. 

**Consiglio:** distribuire il [proxy di app Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) per abilitare l'accesso sicuro per queste applicazioni.


### <a name="on-premises-network-shares"></a>Condivisioni di rete locali

Gli utenti ottengono SSO dai dispositivi aggiunti ad Azure AD se un dispositivo ha accesso a un controller di dominio locale.

### <a name="printers"></a>Stampanti

Per le stampanti, è necessario distribuire la [stampa su cloud ibrido](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) per individuare le stampanti nei dispositivi aggiunti ad Azure AD. 

Mentre le stampanti non possono essere individuate automaticamente in un ambiente solo cloud, gli utenti possono usare anche il percorso UNC delle stampanti per aggiungerle direttamente. 


### <a name="on-premises-applications-relying-on-machine-authentication"></a>Applicazioni locali basate sull'autenticazione del computer

I dispositivi aggiunti ad Azure AD non supportano le applicazioni locali basate sull'autenticazione del computer. 

**Consiglio:** valutare se ritirare queste applicazioni e passare ad applicazioni alternative più moderne.

### <a name="remote-desktop-services"></a>Servizi Desktop remoto

La connessione Desktop remoto a dispositivi aggiunti ad Azure AD richiede che il computer host sia stato aggiunto ad Azure AD o ad Azure AD ibrido. Desktop remoto da un dispositivo non aggiunto o non Windows non è supportato. Per altre informazioni, vedere [Connettersi a un PC remoto aggiunto ad Azure Active Directory](https://docs.microsoft.com/en-us/windows/client-management/connect-to-remote-aadj-pc)


## <a name="understand-your-provisioning-options"></a>Identificazione delle opzioni di provisioning

È possibile effettuare il provisioning dell'aggiunta ad Azure AD tramite gli approcci seguenti:

- **Modalità self-service in Configurazione guidata/Impostazioni**: in modalità self-service gli utenti eseguono il processo di aggiunta ad Azure AD durante la Configurazione guidata o le impostazioni di Windows. Per altre informazioni, vedere [Aggiungere il dispositivo aziendale alla rete dell'organizzazione](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 

- **Windows Autopilot**: Windows Autopilot permette la preconfigurazione dei dispositivi per un'esperienza più uniforme in Configurazione guidata per l'esecuzione dell'aggiunta ad Azure AD. Per altre informazioni, vedere [Panoramica di Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 

- **Registrazione in blocco**: la registrazione in blocco permette un'aggiunta ad Azure AD eseguita da un amministratore tramite uno strumento di provisioning in blocco per configurare i dispositivi. Per altre informazioni, vedere [Registrazione in blocco per dispositivi Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
 


Ecco un confronto di questi tre approcci 

 
||Configurazione self-service|Windows Autopilot|Registrazione in blocco|
|---|---|---|---|
|Richiesta dell'interazione dell'utente per la configurazione|Yes|Sì|No |
|Richiesta di attività IT|No |Sì|Sì|
|Flussi applicabili|Configurazione guidata e impostazioni|Solo Configurazione guidata|Solo Configurazione guidata|
|Diritti di amministratore locale a un utente primario|Sì, per impostazione predefinita|Configurabile|No |
|Richiesta del supporto dell'OEM del dispositivo|No |Sì|No |
|Versioni supportate|1511+|1709+|1703+|
 
Scegliere l'approccio o gli approcci di distribuzione consultando la tabella precedente e le considerazioni seguenti per l'adozione di uno degli approcci:  

- Gli utenti sono sufficientemente esperti per completare personalmente la configurazione? 

    - La modalità self-service può essere l'approccio più adatto per questi utenti. Prendere in considerazione Windows Autopilot per migliorare l'esperienza utente.  

- Gli utenti sono remoti o si trovano in un ambiente aziendale locale? 

    - La modalità self-service o Windows Autopilot sono entrambi approcci adatti per gli utenti remoti per una configurazione senza problemi. 
 
- Si preferisce una configurazione eseguita dall'utente o gestita dall'amministratore? 

    - La registrazione in blocco è l'approccio più adatto per la distribuzione gestita dall'amministratore per configurare i dispositivi prima di consegnarli agli utenti.     

- Si acquistano dispositivi da 1 o 2 OEM o la distribuzione di dispositivi OEM è più estesa?  

    - Se si acquista da un numero limitato di OEM che supportano anche Autopilot, è possibile trarre vantaggio da un'integrazione maggiore con Autopilot. 
 

## <a name="configure-your-device-settings"></a>Configurare le impostazioni dei dispositivi

Il portale di Azure permette di controllare la distribuzione dei dispositivi aggiunti ad Azure AD nell'organizzazione. Per configurare le impostazioni correlate, nella pagina **Azure Active Directory** selezionare `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Gli utenti possono aggiungere dispositivi ad Azure AD

Impostare questa opzione su **Tutti** o **Selezionati** in base all'ambito della distribuzione e agli utenti cui si vuole consentire la configurazione di un dispositivo aggiunto ad Azure AD. 

![Gli utenti possono aggiungere dispositivi ad Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD

Scegliere **Selezionati** e selezionare gli utenti che si vuole aggiungere al gruppo degli amministratori locali in tutti i dispositivi aggiunti ad Azure AD. 

![Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD](./media/azureadjoin-plan/02.png)


### <a name="require-multi-factor-auth-to-join-devices"></a>Richiedi Multi-Factor Authentication per aggiungere i dispositivi

Selezionare **Sì** se gli utenti devono eseguire l'autenticazione a più fattori durante l'aggiunta dei dispositivi ad Azure AD. Per gli utenti che aggiungono dispositivi ad Azure AD tramite MFA, il dispositivo stesso diventa un secondo fattore.

![Richiedi Multi-Factor Authentication per aggiungere i dispositivi](./media/azureadjoin-plan/03.png)




## <a name="configure-your-mobility-settings"></a>Configurare le impostazioni di mobilità

Prima di poter configurare le impostazioni di mobilità, può essere necessario aggiungere un provider MDM.

**Per aggiungere un provider MDM**:

1. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su `Mobility (MDM and MAM)`. 

2. Fare clic su **Aggiungi applicazione**.

3. Selezionare il provider MDM nell'elenco.

    ![Aggiungere un'applicazione](./media/azureadjoin-plan/04.png)

Selezionare il provider MDM per configurare le impostazioni correlate. 

### <a name="mdm-user-scope"></a>Ambito utente MDM

Selezionare **In parte** o **Tutti** in base all'ambito della distribuzione. 

![Ambito utente MDM](./media/azureadjoin-plan/05.png)

A seconda dell'ambito, si verifica una delle situazioni seguenti: 

- **L'utente è incluso nell'ambito MDM**: se si ha una sottoscrizione Azure AD Premium, la registrazione MDM è un'operazione automatica che avviene durante l'aggiunta ad Azure AD. Tutti gli utenti con ambito devono avere una licenza appropriata per MDM. Se la registrazione MDM non riesce in questo scenario, viene eseguito il rollback anche dell'aggiunta ad Azure AD.
    
- **Gli utenti non sono inclusi nell'ambito MDM**: se gli utenti non sono inclusi nell'ambito MDM, l'aggiunta ad Azure AD viene completata senza alcuna registrazione MDM. Questa situazione comporta un dispositivo non gestito.


### <a name="mdm-urls"></a>URL MDM

Esistono tre URL correlati alla configurazione MDM:

- URL delle condizioni per l'utilizzo di MDM

- URL individuazione MDM 

- URL conformità MDM


![Aggiungere un'applicazione](./media/azureadjoin-plan/06.png)


Ogni URL ha un valore predefinito. Se questi campi sono vuoti, contattare il provider MDM per altre informazioni.

### <a name="mam-settings"></a>Impostazioni del software MAM

Il software MAM non si applica all'aggiunta ad Azure AD. 


## <a name="configure-enterprise-state-roaming"></a>Configurazione di Enterprise State Roaming

Se si vuole abilitare Enterprise State Roaming in Azure AD in modo che gli utenti possano sincronizzare le impostazioni tra dispositivi, vedere [Abilitare Enterprise State Roaming in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable). 

**Consiglio**: abilitare questa impostazione anche per i dispositivi aggiunti ad Azure AD ibrido.


## <a name="configure-conditional-access"></a>Configurazione dell'accesso condizionale

Se per i dispositivi aggiunti ad Azure AD è stato configurato un provider MDM, il provider contrassegna il dispositivo come conforme non appena questo è in fase di gestione. 

![Dispositivo conforme](./media/azureadjoin-plan/46.png)

È possibile usare questa implementazione per [richiedere dispositivi gestiti per l'accesso alle app cloud con l'accesso condizionale](../conditional-access/require-managed-devices.md).




## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un nuovo dispositivo Windows 10 con Azure AD in fase di completamento dell'installazione](azuread-joined-devices-frx.md)
> [Aggiungere il dispositivo aziendale alla rete dell'organizzazione](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)


<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
