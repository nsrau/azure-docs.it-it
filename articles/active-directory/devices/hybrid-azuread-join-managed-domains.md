---
title: Configurare l'aggiunta ad Azure Active Directory ibrido per i domini gestiti | Microsoft Docs
description: Informazioni su come configurare l'aggiunta ad Azure Active Directory ibrido per i domini gestiti.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f3ea5f1810b5ca80e096b19e1dcf230e21eabcc
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85317641"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Esercitazione: Configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini gestiti

Questa esercitazione illustra come configurare l'aggiunta ad Azure Active Directory (Azure AD) ibrido per i dispositivi aggiunti a un dominio di Active Directory. Questo metodo supporta un ambiente gestito che include sia Active Directory locale che Azure AD.

Come gli utenti dell'organizzazione, anche i dispositivi rappresentano identità importanti da proteggere. È possibile usare l'identità di un dispositivo per proteggere le risorse in qualsiasi momento e da qualunque posizione. È possibile raggiungere questo obiettivo gestendo le identità dei dispositivi in Azure AD. Utilizzare una delle seguenti modalità:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Questo articolo è incentrato sull'aggiunta ad Azure AD ibrido.

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti grazie all'accesso Single Sign-On (SSO) a tutte le risorse locali e cloud. Contemporaneamente, è possibile proteggere l'accesso alle risorse locali e cloud con l'[accesso condizionale](../conditional-access/howto-conditional-access-policy-compliant-device.md).

Un ambiente gestito può essere distribuito tramite [sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md) o [autenticazione pass-through](../hybrid/how-to-connect-pta.md) con [Seamless SSO](../hybrid/how-to-connect-sso.md). Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare l'aggiunta ad Azure AD ibrido
> * Abilitare dispositivi Windows di livello inferiore
> * Verificare i dispositivi aggiunti
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisiti

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 o versione successiva)
- Credenziali di un amministratore globale per il tenant di Azure AD
- Credenziali di amministratore dell'organizzazione per ognuna delle foreste

Acquisire familiarità con questi articoli:

- [Informazioni sulle identità dei dispositivi](overview.md)
- [Procedura: Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](hybrid-azuread-join-plan.md)
- [Convalida controllata dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD non supporta certificati o smart card nei domini gestiti.

Verificare che Azure AD Connect abbia sincronizzato gli oggetti computer dei dispositivi che devono essere aggiunti ad Azure AD ibrido. Se gli oggetti computer appartengono a unità organizzative specifiche, configurare le unità organizzative per la sincronizzazione in Azure AD Connect. Per altre informazioni su come sincronizzare oggetti computer con Azure AD Connect, vedere [Filtro basato su unità organizzative](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

A partire dalla versione 1.1.819.0, Azure AD Connect include una procedura guidata per configurare l'aggiunta ad Azure AD ibrido. Tale procedura semplifica notevolmente il processo di configurazione. La procedura guidata configura i punti di connessione del servizio per la registrazione dei dispositivi.

I passaggi di configurazione descritti in questo articolo sono basati sull'uso della procedura guidata in Azure AD Connect.

Per l'aggiunta ad Azure AD ibrido i dispositivi devono avere accesso alle risorse Microsoft seguenti dalla rete dell'organizzazione:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (se si usa o si prevede di usare Seamless SSO)

Se l'organizzazione deve accedere a Internet tramite un proxy in uscita, è consigliabile [implementare WPAD (Web Proxy Auto-Discovery)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) per consentire ai computer Windows 10 di eseguire la registrazione di dispositivi con Azure AD. Per risolvere i problemi durante la configurazione e la gestione di WPAD, vedere [Risoluzione dei problemi relativi al rilevamento automatico](/previous-versions/tn-archive/cc302643(v=technet.10)). Nei dispositivi Windows 10 precedenti all'aggiornamento 1709, WPAD è l'unica opzione disponibile per configurare un proxy da usare con l'aggiunta ad Azure AD ibrido. 

Se non si usa WPAD, è possibile configurare le impostazioni proxy WinHTTP nel computer in uso a partire da Windows 10 1709. Per altre informazioni, vedere [Impostazioni del proxy WinHTTP distribuite dall'oggetto Criteri di gruppo](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se si configurano le impostazioni proxy nel computer usando le impostazioni WinHTTP, i computer che non possono connettersi al proxy configurato non riusciranno a connettersi a Internet.

Se l'organizzazione richiede l'accesso a Internet attraverso un proxy in uscita autenticato,assicurarsi che i computer Windows 10 possano eseguire l'autenticazione nel proxy in uscita. Poiché i computer Windows 10 eseguono la registrazione dei dispositivi usando il contesto del computer, configurare l'autenticazione proxy in uscita usando il contesto del computer. Per i requisiti di configurazione, contattare il provider del proxy in uscita.

Per verificare se il dispositivo può accedere alle risorse Microsoft sopra riportate con l'account di sistema, è possibile usare lo script disponibile per [testare la connettività durante la registrazione dei dispositivi](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0).

## <a name="configure-hybrid-azure-ad-join"></a>Configurare l'aggiunta ad Azure AD ibrido

Per configurare l'aggiunta ad Azure AD ibrido con Azure AD Connect:

1. Avviare Azure AD Connect e quindi selezionare **Configura**.

   ![Schermata iniziale](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. In **Attività aggiuntive** selezionare **Configura le opzioni del dispositivo** e quindi **Avanti**.

   ![Attività aggiuntive](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. In **Panoramica** selezionare **Avanti**.

   ![Panoramica](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. In **Connessione ad Azure AD** immettere le credenziali di amministratore globale per il tenant di Azure AD.  

   ![Connessione ad Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. In **Opzioni dispositivo** selezionare **Configura l'aggiunta ad Azure AD ibrido** e quindi **Avanti**.

   ![Opzioni del dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. In **Configurazione del punto di connessione del servizio** per ogni foresta in cui si vuole che Azure AD Connect configuri il punto di connessione del servizio eseguire i passaggi seguenti e quindi selezionare **Avanti**.

   1. Selezionare la **Foresta**.
   1. Selezionare **Servizio di autenticazione**.
   1. Selezionare **Aggiungi** per immettere le credenziali di amministratore aziendale.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. In **Sistemi operativi del dispositivo** selezionare i sistemi operativi usati dai dispositivi nell'ambiente Active Directory e quindi selezionare **Avanti**.

   ![Sistema operativo del dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. In **Pronto per la configurazione** selezionare **Configura**.

   ![Pronto per la configurazione](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. In **La configurazione è stata completata** selezionare **Esci**.

   ![Configurazione completata](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Abilitare dispositivi Windows di livello inferiore

Se alcuni dei dispositivi aggiunti a un dominio sono dispositivi di livello inferiore di Windows, è necessario:

- Configurare le impostazioni intranet locali per la registrazione dei dispositivi
- Configurare Seamless SSO
- Installare Microsoft Workplace Join per i computer Windows di livello inferiore

> [!NOTE]
> Il supporto per Windows 7 è terminato il 14 gennaio 2020. Per altre informazioni, vedere [Il supporto per Windows 7 è terminato](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurare le impostazioni intranet locali per la registrazione dei dispositivi

Per completare l'aggiunta ad Azure AD ibrido dei dispositivi Windows di livello inferiore e per evitare le richieste dei certificati quando i dispositivi vengono autenticati in Azure AD, è possibile eseguire il push di criteri nei dispositivi aggiunti al dominio per aggiungere gli URL seguenti all'area Intranet locale in Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

È anche necessario abilitare **Consenti aggiornamenti barra di stato tramite script** nell'area Intranet locale dell'utente.

### <a name="configure-seamless-sso"></a>Configurare Seamless SSO

Per completare l'aggiunta ad Azure AD ibrido dei dispositivi Windows di livello inferiore in un dominio gestito che usa la [sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md) o l'[autenticazione pass-through](../hybrid/how-to-connect-pta.md) come metodo di autenticazione cloud di Azure AD, è necessario anche [configurare l'accesso SSO facile](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installare Microsoft Workplace Join per i computer Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join for non-Windows 10 computers è disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [Microsoft Endpoint Configuration Manager](/configmgr/). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro `quiet`. La versione corrente di Configuration Manager offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo ad Azure AD usando le credenziali utente dopo l'autenticazione con Azure AD.

## <a name="verify-the-registration"></a>Verificare la registrazione

Ecco tre modi per individuare e verificare lo stato del dispositivo:

### <a name="locally-on-the-device"></a>In locale nel dispositivo

1. Aprire Windows PowerShell.
2. Immettere `dsregcmd /status`.
3. Verificare che **AzureAdJoined** e **DomainJoined** siano impostati su **SÌ**.
4. È possibile usare **DeviceId** e confrontare lo stato nel servizio usando il portale di Azure o PowerShell.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

1. Passare alla pagina di dispositivi usando un [collegamento diretto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Per informazioni su come individuare un dispositivo, vedere [Come gestire le identità dei dispositivi con il portale di Microsoft Azure](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#locate-devices).
3. Se la colonna **Registrazione completata** indica **In sospeso**, l'operazione di Aggiunta ad Azure AD ibrido non è stata completata.
4. Se la colonna **Registrazione completata** contiene una **data/ora**, l'operazione di Aggiunta ad Azure AD ibrido è stata completata.

### <a name="using-powershell"></a>Utilizzo di PowerShell

Verificare lo stato di registrazione del dispositivo nel tenant di Azure con **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** . Questo cmdlet si trova nel [modulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando si usa il cmdlet **Get-MSolDevice** per controllare i dettagli del servizio:

- Deve essere presente un oggetto con **ID dispositivo** corrispondente all'ID nel client Windows.
- Il valore di **DeviceTrustType** è **Domain Joined**. Questa impostazione equivale allo stato **Aggiunto ad Azure AD ibrido** nella pagina **Dispositivi** nel portale di Azure AD.
- Per i dispositivi usati nell'accesso condizionale, il valore di **Enabled** è **True** e quello di **DeviceTrustLevel** è **Managed**.

1. Aprire Windows PowerShell come amministratore.
2. Immettere `Connect-MsolService` per stabilire la connessione al tenant di Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Contare tutti i dispositivi aggiunti ad Azure AD ibrido (tranne quelli con lo stato **In sospeso**)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Contare tutti i dispositivi aggiunti ad Azure AD ibrido con lo stato **In sospeso**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Elencare tutti i dispositivi aggiunti ad Azure AD ibrido

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Elencare tutti i dispositivi aggiunti ad Azure AD ibrido con lo stato **In sospeso**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Elencare i dettagli di un singolo dispositivo:

1. Immettere `get-msoldevice -deviceId <deviceId>` (il valore di **DeviceId** ottenuta in locale nel dispositivo).
2. Verificare che **Abilitato** sia impostato su **True**.

## <a name="troubleshoot-your-implementation"></a>Risolvere i problemi di implementazione

Se si verificano problemi durante il completamento dell'aggiunta ad Azure AD ibrido per dispositivi Windows aggiunti al dominio, vedere:

- [Risoluzione dei problemi dei dispositivi con il comando dsregcmd](https://docs.microsoft.com/azure/active-directory/devices/troubleshoot-device-dsregcmd)
- [Risoluzione dei problemi relativi a dispositivi aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come gestire le identità dei dispositivi usando il portale di Azure.
> [!div class="nextstepaction"]
> [Gestire le identità dei dispositivi](device-management-azure-portal.md)
