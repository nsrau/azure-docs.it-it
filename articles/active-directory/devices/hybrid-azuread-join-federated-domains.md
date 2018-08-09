---
title: Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 1aa5c0f259fdb5fce449bc86f9cf293801b4770a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368669"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Esercitazione: configurare un'aggiunta ad Azure Active Directory ibrido per domini federati

Analogamente agli utenti, i dispositivi stanno diventando un'altra identità da proteggere e da usare per proteggere le risorse in qualsiasi momento e ovunque. Questo obiettivo si raggiunge trasferendo le identità dei dispositivi in Azure AD usando uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti tramite il Single Sign-On (SSO) in tutte le risorse locali e nel cloud. Analogamente, è possibile proteggere l'accesso alle risorse locali e nel cloud con l'[accesso condizionale](../active-directory-conditional-access-azure-portal.md).

Questa esercitazione illustra come configurare l'aggiunta ad Azure AD ibrido per dispositivi federati usando Active Directory Federation Services.

> [!div class="checklist"]
> * Configurare l'aggiunta ad Azure AD ibrido
> * Abilitare dispositivi Windows di livello inferiore
> * Verificare la registrazione
> * Risolvere problemi


## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che l'utente abbia familiarità con:

-  [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)

-  [Come pianificare l'implementazione dell'aggiunta all'identità ibrida di Azure Active Directory](hybrid-azuread-join-plan.md)



Per configurare lo scenario in questa esercitazione, sono necessari gli elementi seguenti:

- Windows Server 2012 R2 con AD FS

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versione 1.1.819.0 o successiva. 
 

A partire dalla versione 1.1.819.0, in Azure AD Connect è presente una procedura guidata per configurare l'aggiunta ad Azure AD ibrido che semplifica in modo significativo il processo di configurazione. La procedura guidata correlata:

- configura i punti di connessione del servizio (SCP) per la registrazione dei dispositivi;

- esegue il backup del trust della relying party esistente di Azure AD;

- aggiorna le regole attestazioni nel trust di Azure AD.

I passaggi di configurazione descritti in questo articolo si basano su questa procedura guidata. Se si dispone di una versione precedente di Azure AD Connect installata, è necessario aggiornarla alla versione 1.1.819 o successive. Se non è possibile installare la versione più recente di Azure AD Connect, vedere [come configurare manualmente la registrazione del dispositivo](../device-management-hybrid-azuread-joined-devices-setup.md).

Per l'aggiunta ad Azure AD ibrido i dispositivi devono avere accesso alle risorse Microsoft seguenti dalla rete dell'organizzazione:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- Servizio token di sicurezza dell'organizzazione (domini federati)
- https://autologon.microsoftazuread-sso.com (Se si usa o si pensa di usare Seamless SSO)

Se l'organizzazione richiede l'accesso a Internet attraverso un proxy in uscita, a partire da Windows 10 1709 è possibile configurare le impostazioni proxy nel computer usando un oggetto Criteri di gruppo (GPO). Se nel computer è in esecuzione una versione precedente a Windows 10 1709, è necessario implementare Web Proxy Auto-Discovery (WPAD) per consentire ai computer Windows 10 di registrare i dispositivi con Azure AD. 

Se l'organizzazione richiede l'accesso a Internet attraverso un proxy in uscita autenticato, è necessario assicurarsi che i computer Windows 10 possano eseguire l'autenticazione al proxy in uscita. Poiché i computer Windows 10 eseguono la registrazione dei dispositivi usando il contesto del computer, è necessario configurare l'autenticazione del proxy in uscita usando il contesto del computer. Per i requisiti di configurazione, contattare il provider del proxy in uscita. 


## <a name="configure-hybrid-azure-ad-join"></a>Configurare l'aggiunta ad Azure AD ibrido

Per configurare un'aggiunta ad Azure AD ibrido con Azure AD Connect, è necessario disporre degli elementi seguenti:

- Credenziali di un amministratore globale per il tenant di Azure AD.  

- Credenziali di amministratore dell'organizzazione per ognuna delle foreste.

- Credenziali dell'amministratore AD FS. 


**Per configurare un'aggiunta ad Azure AD ibrido con Azure AD Connect:**

1. Avviare Azure AD Connect, quindi fare clic su **Configura**.

    ![Schermata iniziale](./media/hybrid-azuread-join-federated-domains/11.png)

2. Nella pagina **Attività aggiuntive** selezionare **Configura le opzioni del dispositivo**, quindi fare clic su **Avanti**. 

    ![Attività aggiuntive](./media/hybrid-azuread-join-federated-domains/12.png)

3. Nella pagina **Panoramica** fare clic su **Avanti**. 

    ![Panoramica](./media/hybrid-azuread-join-federated-domains/13.png)

4. Nella pagina **Connessione ad Azure AD** immettere le credenziali di amministratore globale per il tenant di Azure AD, quindi fare clic su **Avanti**.   

    ![Connessione ad Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

5. Nella pagina **Opzioni dispositivo** selezionare **Configura l'aggiunta ad Azure AD ibrido**, quindi fare clic su **Avanti**. 

    ![Opzioni del dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

6. Nella pagina **SCP** eseguire la procedura seguente, quindi fare clic su **Avanti**: 

    ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

    a. Selezionare la foresta.

    b. Selezionare il servizio di autenticazione.

    c. Fare clic su **Aggiungi** per immettere le credenziali di amministratore aziendale.


7. Nella pagina **Sistemi operativi del dispositivo** selezionare i sistemi operativi usati dai dispositivi nell'ambiente Active Directory, quindi fare clic su **Avanti**. 

    ![Sistema operativo del dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

8. Nella pagina **Configurazione della federazione** immettere le credenziali di amministratore AD FS, quindi fare clic su **Avanti**. 

    ![Configurazione della federazione](./media/hybrid-azuread-join-federated-domains/18.png)

9. Nella pagina **Pronto per la configurazione** fare clic su **Configura**. 

    ![Pronto per la configurazione](./media/hybrid-azuread-join-federated-domains/19.png)

10. Nella pagina **Configurazione completata** fare clic su **Esci**. 

    ![Configurazione completata](./media/hybrid-azuread-join-federated-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Abilitare dispositivi Windows di livello inferiore

Se alcuni dei dispositivi aggiunti a un dominio sono dispositivi di livello inferiore di Windows, è necessario:

- Aggiornare le impostazioni dei dispositivi
 
- Configurare le impostazioni intranet locali per la registrazione dei dispositivi


### <a name="update-device-settings"></a>Aggiornare le impostazioni dei dispositivi 

Per registrare i dispositivi Windows di livello inferiore, è necessario assicurarsi che le impostazioni dei dispositivi che consentono agli utenti di registrare i dispositivi in Azure AD siano configurate. Nel portale di Azure queste impostazioni sono disponibili in:

`Home > [Name of your tenant] > Devices - Device settings`  


    
Il criterio **Gli utenti possono registrare i propri dispositivi in Azure AD** deve essere impostato su **Tutti**.

![Registrare i dispositivi](./media/hybrid-azuread-join-federated-domains/23.png)


### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurare le impostazioni intranet locali per la registrazione dei dispositivi

Per completare l'aggiunta ad Azure AD ibrido dei dispositivi Windows di livello inferiore e per evitare le richieste dei certificati quando i dispositivi vengano autenticati in Azure AD, è possibile eseguire il push di criteri nei dispositivi aggiunti al dominio per aggiungere gli URL seguenti all'area Intranet locale in Internet Explorer:

- `https://device.login.microsoftonline.com`

- `https://device.login.microsoftonline.com`

- Servizio Token di sicurezza dell'organizzazione (STS - domini federati)

- `https://autologon.microsoftazuread-sso.com` (per Seamless SSO).

È inoltre necessario abilitare **Consenti aggiornamenti barra di stato tramite script** nell'area Intranet locale dell'utente.



## <a name="verify-the-registration"></a>Verificare la registrazione

Per verificare lo stato di registrazione del dispositivo nel tenant di Azure, è possibile usare il cmdlet **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** nel **[modulo PowerShell di Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

Quando si usa il cmdlet **Get-MSolDevice** per controllare i dettagli del servizio:

- Deve essere presente un oggetto con **ID dispositivo** corrispondente all'ID nel client Windows.
- Il valore di **DeviceTrustType** deve essere **Aggiunto a un dominio**. Questo valore equivale allo stato **Aggiunto ad Azure AD ibrido** nella pagina dei dispositivi nel portale di Azure AD.
- Il valore di **Abilitato** deve essere **True** per i dispositivi che vengono usati nell'accesso condizionale. 


**Per controllare i dettagli del servizio:**

1. Aprire **Windows PowerShell** come amministratore.

2. Digitare `Connect-MsolService` per eseguire la connessione al tenant di Azure desiderato.  

3. Digitare `get-msoldevice -deviceId <deviceId>`.

6. Verificare che **Abilitato** sia impostato su **True**.





## <a name="troubleshoot-your-implementation"></a>Risolvere i problemi di implementazione

Se si verificano problemi durante il completamento dell'aggiunta ad Azure AD ibrido per dispositivi Windows aggiunti al dominio, vedere:

- [Risoluzione dei problemi relativi all'aggiunta ad Azure AD ibrido per dispositivi Windows correnti](../device-management-troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi all'aggiunta ad Azure AD ibrido per dispositivi Windows di livello inferiore](../device-management-troubleshoot-hybrid-join-windows-legacy.md)



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'aggiunta ad Azure Active Directory ibrido per i domini gestiti](hybrid-azuread-join-managed-domains.md)
> [Configurare manualmente l'aggiunta ad Azure Active Directory ibrido](../device-management-hybrid-azuread-joined-devices-setup.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
