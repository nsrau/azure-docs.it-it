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
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081950"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Esercitazione: Configurare l'aggiunta ad Azure Active Directory ibrido per domini gestiti

Questa esercitazione illustra come configurare un join ibrido di Azure Active Directory (Azure AD) per Active Directory dispositivi aggiunti a un dominio. Questo metodo supporta un ambiente gestito che include sia la Active Directory locale che la Azure AD.

Come gli utenti dell'organizzazione, anche i dispositivi rappresentano identità importanti da proteggere. È possibile usare l'identità di un dispositivo per proteggere le risorse in qualsiasi momento e da qualunque posizione. È possibile raggiungere questo obiettivo gestendo le identità dei dispositivi in Azure AD. Utilizzare una delle seguenti modalità:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Questo articolo è incentrato sul join Azure AD ibrido.

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti grazie all'accesso Single Sign-On (SSO) a tutte le risorse locali e cloud. Contemporaneamente, è possibile proteggere l'accesso alle risorse locali e cloud con l'[accesso condizionale](../active-directory-conditional-access-azure-portal.md).

È possibile distribuire un ambiente gestito usando la [sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md) o [l'autenticazione pass-through (PTA)](../hybrid/how-to-connect-pta.md) con [Single Sign-on senza](../hybrid/how-to-connect-sso.md)problemi. Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare l'aggiunta ad Azure AD ibrido
> * Abilitare dispositivi Windows di livello inferiore
> * Verificare i dispositivi aggiunti
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisites

- Il [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 o versione successiva)
- Credenziali di un amministratore globale per il tenant di Azure AD
- Credenziali di amministratore dell'organizzazione per ognuna delle foreste

Acquisire familiarità con questi articoli:

- [Informazioni sulle identità dei dispositivi](overview.md)
- [Procedura: pianificare l'implementazione ibrida di Azure Active Directory join](hybrid-azuread-join-plan.md)
- [Convalida controllata del join Azure AD ibrido](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD non supporta certificati o smart card nei domini gestiti.

Verificare che Azure AD Connect abbia sincronizzato gli oggetti computer dei dispositivi che devono essere aggiunti ad Azure AD ibrido. Se gli oggetti computer appartengono a unità organizzative (OU) specifiche, configurare le unità organizzative per la sincronizzazione in Azure AD Connect. Per ulteriori informazioni su come sincronizzare gli oggetti computer utilizzando Azure AD Connect, vedere [filtro basato su unità organizzative](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

A partire dalla versione 1.1.819.0, Azure AD Connect include una procedura guidata per configurare il join Azure AD ibrido. Tale procedura semplifica notevolmente il processo di configurazione. La procedura guidata configura i punti di connessione del servizio per la registrazione dei dispositivi.

I passaggi di configurazione descritti in questo articolo sono basati sull'uso della procedura guidata in Azure AD Connect.

Per l'aggiunta ad Azure AD ibrido i dispositivi devono avere accesso alle risorse Microsoft seguenti dalla rete dell'organizzazione:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (se si usa o si prevede di usare Seamless SSO)

Se l'organizzazione richiede l'accesso a Internet tramite un proxy in uscita, è consigliabile [implementare il protocollo WPAD (Web Proxy Auto-Discovery)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) per abilitare i computer Windows 10 per la registrazione dei dispositivi con Azure ad. Per risolvere i problemi relativi alla configurazione e alla gestione di WPAD, vedere [risoluzione dei problemi di rilevamento automatico](/previous-versions/tn-archive/cc302643(v=technet.10)).

Se non si utilizza WPAD, è possibile configurare le impostazioni proxy nel computer a partire da Windows 10 1709. Per ulteriori informazioni, vedere [impostazioni proxy WinHTTP distribuite dall'oggetto Criteri di](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)gruppo.

> [!NOTE]
> Se si configurano le impostazioni proxy nel computer usando le impostazioni WinHTTP, i computer che non possono connettersi al proxy configurato non riusciranno a connettersi a Internet.

Se l'organizzazione richiede l'accesso a Internet tramite un proxy in uscita autenticato, assicurarsi che i computer Windows 10 siano in grado di eseguire correttamente l'autenticazione al proxy in uscita. Poiché i computer Windows 10 eseguono la registrazione del dispositivo usando il contesto del computer, configurare l'autenticazione del proxy in uscita usando il contesto del computer. Per i requisiti di configurazione, contattare il provider del proxy in uscita.

Verificare che il dispositivo possa accedere alle risorse Microsoft precedenti con l'account di sistema usando lo script di connettività per la [registrazione del dispositivo di test](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Configurare l'aggiunta ad Azure AD ibrido

Per configurare un join di Azure AD ibrido usando Azure AD Connect:

1. Avviare Azure AD Connect e quindi selezionare **Configura**.

   ![Schermata iniziale](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. In **attività aggiuntive**selezionare **Configura opzioni dispositivo**e quindi fare clic su **Avanti**.

   ![Attività aggiuntive](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. In **Panoramica**selezionare **Avanti**.

   ![Panoramica](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. In **Connetti a Azure ad**immettere le credenziali di un amministratore globale per il tenant di Azure ad.  

   ![Connessione ad Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. In **Opzioni dispositivo**selezionare **Configura Azure ad ibrido join**e quindi fare clic su **Avanti**.

   ![Opzioni del dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. In **configurazione SCP**, per ogni foresta in cui si vuole Azure ad Connect configurare SCP, completare la procedura seguente e quindi fare clic su **Avanti**.

   1. Selezionare l' **insieme di strutture**.
   1. Selezionare un **servizio di autenticazione**.
   1. Selezionare **Aggiungi** per immettere le credenziali di amministratore aziendale.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. In **sistemi operativi del dispositivo**selezionare i sistemi operativi usati dai dispositivi nell'ambiente Active Directory e quindi fare clic su **Avanti**.

   ![Sistema operativo del dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. In **pronto per la configurazione**selezionare **Configura**.

   ![Pronto per la configurazione](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. In **Configurazione completata**selezionare **Esci**.

   ![Configurazione completata](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Abilitare dispositivi Windows di livello inferiore

Se alcuni dispositivi aggiunti a un dominio sono dispositivi Windows di livello inferiore, è necessario:

- Configurare le impostazioni intranet locali per la registrazione dei dispositivi
- Configurare Seamless SSO
- Installare Microsoft Workplace Join per i computer Windows di livello inferiore

> [!NOTE]
> Il supporto per Windows 7 è terminato il 14 gennaio 2020. Per ulteriori informazioni, vedere la pagina relativa al [supporto di Windows 7 terminato](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurare le impostazioni intranet locali per la registrazione dei dispositivi

Per completare il join Azure AD ibrido dei dispositivi Windows di livello inferiore ed evitare richieste di certificati quando i dispositivi eseguono l'autenticazione a Azure AD, è possibile effettuare il push di un criterio ai dispositivi aggiunti a un dominio per aggiungere gli URL seguenti all'area Intranet locale in Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

È anche necessario abilitare **Consenti aggiornamenti alla barra di stato tramite script** nell'area Intranet locale dell'utente.

### <a name="configure-seamless-sso"></a>Configurare Seamless SSO

Per completare il join Azure AD ibrido dei dispositivi Windows di livello inferiore in un dominio gestito che usa la [sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md) o [l'autenticazione pass-through](../hybrid/how-to-connect-pta.md) come metodo di autenticazione Azure ad cloud, è necessario anche configurare l'accesso [SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)facile.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installare Microsoft Workplace Join per i computer Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft workplace join per computer non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join for non-Windows 10 computers è disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [Microsoft Endpoint Configuration Manager](/configmgr/). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro `quiet`. La versione corrente di Configuration Manager offre vantaggi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo ad Azure AD usando le credenziali utente dopo l'autenticazione con Azure AD.

## <a name="verify-the-registration"></a>Verificare la registrazione

Verificare lo stato di registrazione del dispositivo nel tenant di Azure usando **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** . Questo cmdlet si trova nel [modulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando si usa il cmdlet **Get-MSolDevice** per controllare i dettagli del servizio:

- Deve essere presente un oggetto con **ID dispositivo** corrispondente all'ID nel client Windows.
- Il valore di **DeviceTrustType** è **aggiunto**a un dominio. Questa impostazione equivale allo stato **Aggiunto ad Azure AD ibrido** nella pagina **Dispositivi** nel portale di Azure AD.
- Per i dispositivi usati nell'accesso condizionale, il valore per **Enabled** è **true** e **DeviceTrustLevel** è **gestito**.

Per verificare i dettagli del servizio:

1. Aprire Windows PowerShell come amministratore.
1. Immettere `Connect-MsolService` per stabilire la connessione al tenant di Azure.  
1. Immettere `get-msoldevice -deviceId <deviceId>`.
1. Verificare che **Abilitato** sia impostato su **True**.

## <a name="troubleshoot-your-implementation"></a>Risolvere i problemi di implementazione

Se si verificano problemi durante il completamento di ibrido Azure AD join per i dispositivi Windows aggiunti a un dominio, vedere:

- [Risoluzione dei problemi relativi ai dispositivi ibridi Azure Active Directory aggiunti](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come gestire le identità dei dispositivi usando il portale di Azure.
> [!div class="nextstepaction"]
> [Gestire le identità dei dispositivi](device-management-azure-portal.md)
