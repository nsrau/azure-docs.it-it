---
title: Revocare l'accesso utente per l'emergenza in Azure Active Directory | Microsoft Docs
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure AD in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 06/26/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5ca5f7c6032a69286da72d8ef3640f64038eb3a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027189"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Revocare l'accesso utente in Azure Active Directory

Tra gli scenari in cui potrebbe essere necessario che un amministratore revocare tutti gli accessi per un utente includa account compromessi, terminazione dei dipendenti e altre minacce interne. A seconda della complessità dell'ambiente, gli amministratori possono eseguire diversi passaggi per garantire la revoca dell'accesso. In alcuni scenari potrebbe verificarsi un periodo tra l'avvio della revoca di accesso e il momento in cui l'accesso viene revocato in modo efficace.

Per attenuare i rischi, è necessario comprendere il funzionamento dei token. Sono disponibili molti tipi di token, che rientrano in uno dei modelli descritti nelle sezioni seguenti.

## <a name="access-tokens-and-refresh-tokens"></a>Token di accesso e token di aggiornamento

I token di accesso e i token di aggiornamento vengono spesso usati con applicazioni client spesse e usati anche nelle applicazioni basate su browser, ad esempio le app a pagina singola.

- Quando gli utenti eseguono l'autenticazione a Azure AD, vengono valutati i criteri di autorizzazione per determinare se all'utente è possibile concedere l'accesso a una risorsa specifica.  

- Se autorizzato, Azure AD rilascia un token di accesso e un token di aggiornamento per la risorsa.  

- Per impostazione predefinita, i token di accesso emessi da Azure AD per l'ultima volta per 1 ora. Se il protocollo di autenticazione lo consente, l'app può riautenticare automaticamente l'utente passando il token di aggiornamento al Azure AD al momento della scadenza del token di accesso.

Azure AD quindi rivaluta i criteri di autorizzazione. Se l'utente è ancora autorizzato, Azure AD rilascia un nuovo token di accesso e un token di aggiornamento.

I token di accesso possono essere un problema di sicurezza se l'accesso deve essere revocato entro un periodo di tempo più breve rispetto alla durata del token, che in genere è di circa un'ora. Per questo motivo, Microsoft sta lavorando attivamente per offrire la [valutazione dell'accesso continuo](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation) alle applicazioni Office 365, che consente di garantire l'invalidamento dei token di accesso quasi in tempo reale.  

## <a name="session-tokens-cookies"></a>Token di sessione (cookie)

La maggior parte delle applicazioni basate su browser usa i token di sessione invece dei token di accesso e di aggiornamento.  

- Quando un utente apre un browser ed esegue l'autenticazione a un'applicazione tramite Azure AD, l'utente riceve due token di sessione. Una da Azure AD e un'altra dall'applicazione.  

- Quando un'applicazione rilascia il proprio token di sessione, l'accesso all'applicazione è regolato dalla sessione dell'applicazione. A questo punto, l'utente è interessato solo dai criteri di autorizzazione che l'applicazione è in grado di riconoscere.

- I criteri di autorizzazione di Azure AD vengono rivalutati con la stessa frequenza con cui l'applicazione invia nuovamente l'utente al Azure AD. La rivalutazione viene in genere eseguita in modo invisibile all'utente, anche se la frequenza dipende dalla modalità di configurazione dell'applicazione. È possibile che l'app non rimandi mai l'utente a Azure AD purché il token di sessione sia valido.

- Per revocare un token di sessione, l'applicazione deve revocare l'accesso in base ai propri criteri di autorizzazione. Azure AD non può revocare direttamente un token di sessione emesso da un'applicazione.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Revocare l'accesso per un utente nell'ambiente ibrido

Per un ambiente ibrido con Active Directory locale sincronizzato con Azure Active Directory, Microsoft consiglia agli amministratori IT di eseguire le azioni seguenti.  

### <a name="on-premises-active-directory-environment"></a>Ambiente Active Directory locale

Come amministratore nella Active Directory, connettersi alla rete locale, aprire PowerShell ed eseguire le azioni seguenti:

1. Disabilitare l'utente in Active Directory. Vedere [Disable-ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Reimpostare la password dell'utente due volte nel Active Directory. Vedere [set-ADAccountPassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

    > [!NOTE]
    > Il motivo per cui si modifica la password di un utente due volte consiste nel ridurre il rischio di pass-the-hash, soprattutto se sono presenti ritardi nella replica delle password locali. Se è possibile presupporre che questo account non è compromesso, è possibile reimpostare la password una sola volta.

    > [!IMPORTANT] 
    > Non usare le password di esempio nei cmdlet seguenti. Assicurarsi di modificare le password in una stringa casuale.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Ambiente di Azure Active Directory

Come amministratore in Azure Active Directory, aprire PowerShell, eseguire ``Connect-AzureAD`` ed eseguire le azioni seguenti:

1. Disabilitare l'utente in Azure AD. Vedere [set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Revocare i token di aggiornamento Azure AD dell'utente. Vedere [Revoke-AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Disabilitare i dispositivi dell'utente. Vedere [Get-AzureADUserRegisteredDevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Passaggi facoltativi

- [Cancellazione dei dati aziendali dalle applicazioni gestite da Intune](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe).

- [Cancellazione dei dispositivi di proprietà dell'azienda reimpostazione delle impostazioni predefinite del dispositivo](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Non è possibile recuperare i dati nel dispositivo dopo una cancellazione.

## <a name="when-access-is-revoked"></a>Quando viene revocato l'accesso

Quando gli amministratori hanno eseguito i passaggi precedenti, l'utente non può ottenere nuovi token per qualsiasi applicazione associata a Azure Active Directory. Il tempo trascorso tra la revoca e l'utente che perde l'accesso dipende dalla modalità di concessione dell'accesso da parte dell'applicazione:

- Per le **applicazioni che usano i token di accesso**, l'utente perde l'accesso alla scadenza del token di accesso.

- Per le **applicazioni che usano i token di sessione**, le sessioni esistenti terminano non appena il token scade. Se lo stato disabilitato dell'utente viene sincronizzato con l'applicazione, l'applicazione può revocare automaticamente le sessioni esistenti dell'utente se è configurata.  Il tempo necessario dipende dalla frequenza di sincronizzazione tra l'applicazione e Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Procedure di accesso sicuro per gli amministratori di Azure AD](directory-admin-roles-secure.md)
- [Aggiungere o aggiornare le informazioni sul profilo utente](../fundamentals/active-directory-users-profile-azure-portal.md)
