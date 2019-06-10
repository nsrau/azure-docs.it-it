---
title: Configurare l'aggiunta ad Azure Active Directory ibrido per i domini gestiti | Microsoft Docs
description: Informazioni su come configurare l'aggiunta ad Azure Active Directory ibrido per i domini gestiti.
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
ms.openlocfilehash: efa653ecf306f5ac5eefaddd61d98e81f919876d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513296"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Esercitazione: Configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini gestiti

Analogamente agli utenti, i dispositivi sono un'altra identità di base da proteggere nonché da usare per proteggere le risorse ovunque e in qualsiasi momento. È possibile raggiungere questo obiettivo trasferendo e gestendo le identità dei dispositivi in Azure AD con uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti tramite il Single Sign-On (SSO) in tutte le risorse locali e nel cloud. Analogamente, è possibile proteggere l'accesso alle risorse locali e nel cloud con l'[accesso condizionale](../active-directory-conditional-access-azure-portal.md).

Questa esercitazione illustra come configurare l'aggiunta ad Azure AD ibrido per i dispositivi di computer aggiunti a un dominio di AD in un ambiente gestito. 

Un ambiente gestito può essere distribuito tramite la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) o l'[autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) con [Single Sign-On facile](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).
Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.

> [!div class="checklist"]
> * Configurare l'aggiunta ad Azure AD ibrido
> * Abilitare dispositivi Windows di livello inferiore
> * Verificare i dispositivi aggiunti
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che l'utente abbia familiarità con:

- [Introduzione alla gestione delle identità dei dispositivi in Azure Active Directory](../device-management-introduction.md)
- [Come pianificare l'implementazione dell'aggiunta all'identità ibrida di Azure Active Directory](hybrid-azuread-join-plan.md)
- [Come eseguire la convalida controllata dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD non supporta certificati o smart card nei domini gestiti.

Per configurare lo scenario di questo articolo, è necessario avere installato la [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 o versioni successive).

Verificare che Azure AD Connect abbia sincronizzato gli oggetti computer dei dispositivi che devono essere aggiunti ad Azure AD ibrido. Se gli oggetti computer appartengono a unità organizzative specifiche, queste unità organizzative devono essere configurate per la sincronizzazione anche in Azure AD Connect. Per altre informazioni sulla sincronizzazione di oggetti computer con Azure AD Connect, vedere l'articolo su come [configurare il filtro con Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

A partire dalla versione 1.1.819.0, in Azure AD Connect è presente una procedura guidata per configurare l'aggiunta ad Azure AD ibrido che semplifica enormemente il processo di configurazione. La configurazione guidata imposta i punti di connessione del servizio (SCP) per la registrazione dei dispositivi.

I passaggi di configurazione descritti in questo articolo si basano su questa procedura guidata.

L'aggiunta ad Azure AD ibrido richiede che i dispositivi abbiano accesso alle risorse Microsoft seguenti dall'interno della rete dell'organizzazione:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- [https://autologon.microsoftazuread-sso.com](`https://autologon.microsoftazuread-sso.com`) (Se si usa o si pensa di usare Seamless SSO)

Se l'organizzazione deve accedere a Internet tramite un proxy in uscita, è consigliabile [implementare WPAD (Web Proxy Auto-Discovery)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) per consentire ai computer Windows 10 di eseguire la registrazione di dispositivi con Azure AD. In caso di problemi nella configurazione e nella gestione di WPAD, vedere [Risoluzione dei problemi di rilevamento automatico](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10). 

Se non si usa WPAD ed è necessario configurare le impostazioni del proxy sul computer in uso, a partire da Windows 10 1709 è possibile [configurare le impostazioni WinHTTP usando un oggetto Criteri di gruppo](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se si configurano le impostazioni del proxy sul computer usando le impostazioni WinHTTP, i computer che non possono connettersi al proxy configurato non riusciranno a connettersi a Internet.

Se l'organizzazione richiede l'accesso a Internet attraverso un proxy in uscita autenticato, è necessario assicurarsi che i computer Windows 10 possano eseguire l'autenticazione al proxy in uscita. Poiché i computer Windows 10 eseguono la registrazione dei dispositivi usando il contesto del computer, è necessario configurare l'autenticazione del proxy in uscita usando il contesto del computer. Per i requisiti di configurazione, contattare il provider del proxy in uscita.

## <a name="configure-hybrid-azure-ad-join"></a>Configurare l'aggiunta ad Azure AD ibrido

Per configurare un'aggiunta ad Azure AD ibrido con Azure AD Connect, è necessario disporre degli elementi seguenti:

- Credenziali di un amministratore globale per il tenant di Azure AD.  
- Credenziali dell'amministratore dell'organizzazione per ognuna delle foreste.

**Per configurare un'aggiunta ad Azure AD ibrido con Azure AD Connect:**

1. Avviare Azure AD Connect, quindi fare clic su **Configura**.

   ![Schermata iniziale](./media/hybrid-azuread-join-managed-domains/11.png)

1. Nella pagina **Attività aggiuntive** selezionare **Configura le opzioni del dispositivo**, quindi fare clic su **Avanti**.

   ![Attività aggiuntive](./media/hybrid-azuread-join-managed-domains/12.png)

1. Nella pagina **Panoramica** fare clic su **Avanti**.

   ![Panoramica](./media/hybrid-azuread-join-managed-domains/13.png)

1. Nella pagina **Connessione ad Azure AD** immettere le credenziali di amministratore globale per il tenant di Azure AD.  

   ![Connessione ad Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Nella pagina **Opzioni dispositivo** selezionare **Configura l'aggiunta ad Azure AD ibrido**, quindi fare clic su **Avanti**.

   ![Opzioni del dispositivo](./media/hybrid-azuread-join-managed-domains/15.png)

1. Nella pagina **SCP**, per ogni foresta che si intende configurare a SCP tramite Azure AD Connect, attenersi ai passaggi seguenti e quindi fare clic su **Avanti**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Selezionare la foresta.
   1. Selezionare il servizio di autenticazione.
   1. Fare clic su **Aggiungi** per immettere le credenziali di amministratore aziendale.

1. Nella pagina **Sistemi operativi del dispositivo** selezionare i sistemi operativi usati dai dispositivi nell'ambiente Active Directory, quindi fare clic su **Avanti**.

   ![Sistema operativo del dispositivo](./media/hybrid-azuread-join-managed-domains/17.png)

1. Nella pagina **Pronto per la configurazione** fare clic su **Configura**.

   ![Pronto per la configurazione](./media/hybrid-azuread-join-managed-domains/19.png)

1. Nella pagina **Configurazione completata** fare clic su **Esci**.

   ![Configurazione completata](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Abilitare dispositivi Windows di livello inferiore

Se alcuni dei dispositivi aggiunti a un dominio sono dispositivi di livello inferiore di Windows, è necessario:

- Configurare le impostazioni intranet locali per la registrazione dei dispositivi
- Configurare l'accesso Single Sign-On facile (Seamless SSO)
- Installare Microsoft Workplace Join per i computer Windows di livello inferiore

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurare le impostazioni intranet locali per la registrazione dei dispositivi

Per completare l'aggiunta ad Azure AD ibrido dei dispositivi Windows di livello inferiore e per evitare le richieste dei certificati quando i dispositivi vengano autenticati in Azure AD, è possibile eseguire il push di criteri nei dispositivi aggiunti al dominio per aggiungere gli URL seguenti all'area Intranet locale in Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

È inoltre necessario abilitare **Consenti aggiornamenti barra di stato tramite script** nell'area Intranet locale dell'utente.

### <a name="configure-seamless-sso"></a>Configurare l'accesso Seamless SSO

Per completare correttamente l'aggiunta ad Azure AD ibrido dei dispositivi Windows di livello inferiore in un dominio gestito che usa la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) o l'[autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) come metodo di autenticazione cloud di Azure AD, è necessario anche [configurare l'accesso SSO facile](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installare Microsoft Workplace Join per i computer Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft Workplace Join per computer non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554), disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro quiet. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione con Azure AD.

## <a name="verify-the-registration"></a>Verificare la registrazione

Per verificare lo stato di registrazione del dispositivo nel tenant di Azure, è possibile usare il cmdlet **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** nel **[modulo PowerShell di Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Quando si usa il cmdlet **Get-MSolDevice** per controllare i dettagli del servizio:

- Deve essere presente un oggetto con **ID dispositivo** corrispondente all'ID nel client Windows.
- Il valore di **DeviceTrustType** deve essere **Aggiunto a un dominio**. Questo valore equivale allo stato **Aggiunto ad Azure AD ibrido** nella pagina dei dispositivi nel portale di Azure AD.
- Per i dispositivi usati nell'accesso condizionale, il valore **Abilitato** deve essere **True** e **DeviceTrustLevel** deve essere **Gestito**.

**Per controllare i dettagli del servizio:**

1. Aprire **Windows PowerShell** come amministratore.
1. Digitare `Connect-MsolService` per eseguire la connessione al tenant di Azure desiderato.  
1. Digitare `get-msoldevice -deviceId <deviceId>`.
1. Verificare che **Abilitato** sia impostato su **True**.

## <a name="troubleshoot-your-implementation"></a>Risolvere i problemi di implementazione

Se si verificano problemi durante il completamento dell'aggiunta ad Azure AD ibrido per dispositivi Windows aggiunti al dominio, vedere:

- [Risoluzione dei problemi relativi all'aggiunta ad Azure AD ibrido per dispositivi Windows correnti](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi all'aggiunta ad Azure AD ibrido per dispositivi Windows di livello inferiore](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla gestione delle identità dei dispositivi nel portale di Azure AD, vedere [Gestione delle identità dei dispositivi con il portale di Azure](device-management-azure-portal.md).
