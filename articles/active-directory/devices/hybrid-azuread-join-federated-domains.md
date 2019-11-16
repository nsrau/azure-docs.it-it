---
title: Configurare l'aggiunta ad Azure Active Directory ibrido per i domini federati | Microsoft Docs
description: Informazioni su come configurare l'aggiunta ad Azure Active Directory ibrido per i domini federati.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14565c7e499b04b9c41184111d6ddcc88fffac80
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883001"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Esercitazione: configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini federati

Come gli utenti dell'organizzazione, anche i dispositivi rappresentano identità importanti da proteggere. È possibile usare l'identità di un dispositivo per proteggere le risorse in qualsiasi momento e da qualunque posizione. È possibile raggiungere questo obiettivo trasferendo e gestendo le identità dei dispositivi in Azure Active Directory (Azure AD) con uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti grazie all'accesso Single Sign-On (SSO) a tutte le risorse locali e cloud. Contemporaneamente, è possibile proteggere l'accesso alle risorse locali e cloud con l'[accesso condizionale](../active-directory-conditional-access-azure-portal.md).

Un ambiente federato deve includere un provider di identità che supporta i requisiti riportati di seguito. Se l'ambiente federato usa Active Directory Federation Services (AD FS), i requisiti seguenti sono già supportati.

- **Attestazione WIAORMULTIAUTHN:** questa attestazione è necessaria per eseguire l'aggiunta ad Azure AD ibrido per dispositivi Windows di livello inferiore.
- **Protocollo WS-Trust:** questo protocollo è necessario per l'autenticazione con Azure AD degli attuali dispositivi Windows aggiunti ad Azure AD ibrido.
  Quando si usa AD FS, è necessario abilitare gli endpoint WS-Trust seguenti: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Sia **adfs/services/trust/2005/windowstransport** che **adfs/services/trust/13/windowstransport** devono essere abilitati solo come endpoint per Intranet e NON devono essere esposti come endpoint per Extranet tramite Proxy applicazione Web. Per altre informazioni su come disabilitare gli endpoint Windows WS-Trust, vedere [Disabilitare gli endpoint Windows WS-Trust sul proxy](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). È possibile verificare gli endpoint abilitati nella console di gestione di AD FS, in **Servizio** > **Endpoint**.

Questa esercitazione illustra come configurare l'aggiunta ad Azure AD ibrido per i dispositivi di computer aggiunti a un dominio di Active Directory in un ambiente federato con AD FS.

Si apprenderà come:

> [!div class="checklist"]
> * Configurare l'aggiunta ad Azure AD ibrido
> * Abilitare dispositivi Windows di livello inferiore
> * Verificare la registrazione
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che l'utente abbia familiarità con gli articoli seguenti:

- [Informazioni sulle identità dei dispositivi](overview.md)
- [Pianificare l'implementazione dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-plan.md)
- [Come eseguire la convalida controllata dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-control.md)

Per configurare lo scenario in questa esercitazione, sono necessari gli elementi seguenti:

- Windows Server 2012 R2 con AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versione 1.1.819.0 o successiva

A partire dalla versione 1.1.819.0, Azure AD Connect include una procedura guidata per configurare l'aggiunta ad Azure AD ibrido. Tale procedura semplifica notevolmente il processo di configurazione. La procedura guidata correlata:

- Configura i punti di connessione del servizio per la registrazione dei dispositivi
- esegue il backup del trust della relying party esistente di Azure AD;
- aggiorna le regole attestazioni nel trust di Azure AD.

I passaggi di configurazione descritti in questo articolo sono basati sull'uso della procedura guidata di Azure AD Connect. Se è installata una versione precedente di Azure AD Connect, è necessario eseguire l'aggiornamento alla versione 1.1.819 o successiva per usare la procedura guidata. Se non è possibile installare l'ultima versione di Azure AD Connect, vedere [Configurare manualmente l'aggiunta ad Azure AD ibrido](hybrid-azuread-join-manual.md).

Per l'aggiunta ad Azure AD ibrido i dispositivi devono avere accesso alle risorse Microsoft seguenti dalla rete dell'organizzazione:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Servizio Token di sicurezza (STS) dell'organizzazione (per domini federati)
- `https://autologon.microsoftazuread-sso.com` (se si usa o si prevede di usare Seamless SSO)

A partire da Windows 10 1803, se l'aggiunta istantanea ad Azure AD ibrido per un ambiente federato con AD FS non riesce, è possibile usare Azure AD Connect per sincronizzare l'oggetto computer in Azure AD che verrà successivamente usato per completare la registrazione del dispositivo per l'aggiunta ad Azure AD ibrido. Verificare che Azure AD Connect abbia sincronizzato gli oggetti computer dei dispositivi che devono essere aggiunti ad Azure AD ibrido. Se gli oggetti computer appartengono a unità organizzative specifiche, è necessario configurare anche le unità organizzative per la sincronizzazione in Azure AD Connect. Per altre informazioni su come sincronizzare oggetti computer con Azure AD Connect, vedere [Configurare il filtro con Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Se l'organizzazione deve accedere a Internet tramite un proxy in uscita, è consigliabile [implementare WPAD (Web Proxy Auto-Discovery)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) per consentire ai computer Windows 10 di eseguire la registrazione di dispositivi con Azure AD. In caso di problemi nella configurazione e nella gestione di WPAD, vedere [Risolvere i problemi di rilevamento automatico](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se non si non usa WPAD e si vuole configurare le impostazioni proxy nel computer in uso, è possibile farlo a partire da Windows 10 1709. Per altre informazioni, vedere [Configurare le impostazioni WinHTTP tramite un oggetto Criteri di gruppo (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se si configurano le impostazioni proxy nel computer usando le impostazioni WinHTTP, i computer che non possono connettersi al proxy configurato non riusciranno a connettersi a Internet.

Se l'organizzazione richiede l'accesso a Internet attraverso un proxy in uscita autenticato, è necessario assicurarsi che i computer Windows 10 possano eseguire l'autenticazione nel proxy in uscita. Poiché i computer Windows 10 eseguono la registrazione dei dispositivi usando il contesto del computer, è necessario configurare l'autenticazione proxy in uscita usando il contesto del computer. Per i requisiti di configurazione, contattare il provider del proxy in uscita.

Per verificare se il dispositivo può accedere alle risorse Microsoft sopra riportate con l'account di sistema, è possibile usare lo script disponibile per [testare la connettività durante la registrazione dei dispositivi](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0).

## <a name="configure-hybrid-azure-ad-join"></a>Configurare l'aggiunta ad Azure AD ibrido

Per configurare un'aggiunta ad Azure AD ibrido con Azure AD Connect, è necessario disporre di quanto segue:

- Credenziali di un amministratore globale per il tenant di Azure AD  
- Credenziali di amministratore dell'organizzazione per ognuna delle foreste
- Credenziali dell'amministratore AD FS

**Per configurare l'aggiunta ad Azure AD ibrido con Azure AD Connect**:

1. Avviare Azure AD Connect e quindi selezionare **Configura**.

   ![Schermata iniziale](./media/hybrid-azuread-join-federated-domains/11.png)

1. Nella pagina **Attività aggiuntive** selezionare **Configura le opzioni del dispositivo** e quindi **Avanti**.

   ![Attività aggiuntive](./media/hybrid-azuread-join-federated-domains/12.png)

1. Nella pagina **Panoramica** selezionare **Avanti**.

   ![Panoramica](./media/hybrid-azuread-join-federated-domains/13.png)

1. Nella pagina **Connessione ad Azure AD** immettere le credenziali di un amministratore globale per il tenant di Azure AD e quindi selezionare **Avanti**.

   ![Connessione ad Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Nella pagina **Opzioni dispositivo** selezionare **Configura l'aggiunta ad Azure AD ibrido** e quindi **Avanti**.

   ![Opzioni del dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

1. Nella pagina **Punto di connessione del servizio** completare la procedura seguente e quindi selezionare **Avanti**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selezionare la foresta.
   1. Selezionare il servizio di autenticazione. È necessario selezionare **Server AD FS**, a meno che all'interno dell'organizzazione non siano presenti esclusivamente client Windows 10 e non sia stata configurata la sincronizzazione computer/dispositivo o non si usi Seamless SSO.
   1. Selezionare **Aggiungi** per immettere le credenziali di amministratore aziendale.

1. Nella pagina **Sistemi operativi del dispositivo** selezionare i sistemi operativi usati dai dispositivi nell'ambiente Active Directory e quindi selezionare **Avanti**.

   ![Sistema operativo del dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

1. Nella pagina **Configurazione della federazione** immettere le credenziali di amministratore AD FS e quindi selezionare **Avanti**.

   ![Configurazione della federazione](./media/hybrid-azuread-join-federated-domains/18.png)

1. Nella pagina **Pronto per la configurazione** selezionare **Configura**.

   ![Pronto per la configurazione](./media/hybrid-azuread-join-federated-domains/19.png)

1. Nella pagina **La configurazione è stata completata** selezionare **Esci**.

   ![Configurazione completata](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Abilitare dispositivi Windows di livello inferiore

Se alcuni dei dispositivi aggiunti a un dominio sono dispositivi Windows di livello inferiore, è necessario:

- Configurare le impostazioni intranet locali per la registrazione dei dispositivi
- Installare Microsoft Workplace Join for Windows per i computer di livello inferiore

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurare le impostazioni intranet locali per la registrazione dei dispositivi

Per completare l'aggiunta ad Azure AD ibrido dei dispositivi Windows di livello inferiore e per evitare le richieste di certificati quando i dispositivi vengono autenticati in Azure AD, è possibile eseguire il push di criteri nei dispositivi aggiunti al dominio per aggiungere gli URL seguenti all'area Intranet locale in Internet Explorer:

- `https://device.login.microsoftonline.com`
- Servizio token di sicurezza dell'organizzazione (per domini federati)
- `https://autologon.microsoftazuread-sso.com` (per Seamless SSO)

È anche necessario abilitare **Consenti aggiornamenti barra di stato tramite script** nell'area Intranet locale dell'utente.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installare Microsoft Workplace Join for Windows per i computer di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join for non-Windows 10 computers è disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro `quiet`. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo ad Azure AD usando le credenziali utente dopo l'autenticazione con Azure AD.

## <a name="verify-the-registration"></a>Verificare la registrazione

Per verificare lo stato di registrazione del dispositivo nel tenant di Azure, è possibile usare il cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** nel [modulo PowerShell di Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando si usa il cmdlet **Get-MSolDevice** per controllare i dettagli del servizio:

- Deve essere presente un oggetto con **ID dispositivo** corrispondente all'ID nel client Windows.
- Il valore di **DeviceTrustType** deve essere **Aggiunto a un dominio**. Questa impostazione equivale allo stato **Aggiunto ad Azure AD ibrido** in **Dispositivi** nel portale di Azure AD.
- Per i dispositivi usati nell'accesso condizionale, il valore di **Enabled** deve essere **True** e quello di **DeviceTrustLevel** deve essere **Managed**.

**Per controllare i dettagli del servizio**:

1. Aprire Windows PowerShell come amministratore.
1. Immettere `Connect-MsolService` per stabilire la connessione al tenant di Azure.  
1. Immettere `get-msoldevice -deviceId <deviceId>`.
1. Verificare che **Abilitato** sia impostato su **True**.

## <a name="troubleshoot-your-implementation"></a>Risolvere i problemi di implementazione

Se si verificano problemi durante il completamento dell'aggiunta ad Azure AD ibrido per dispositivi Windows aggiunti al dominio, vedere:

- [Risolvere i problemi dei dispositivi Windows correnti aggiunti ad Azure AD ibrido](troubleshoot-hybrid-join-windows-current.md)
- [Risolvere i problemi dei dispositivi Windows di livello inferiore aggiunti ad Azure AD ibrido](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come [gestire le identità dei dispositivi usando il portale di Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
