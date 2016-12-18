---
title: Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio| Documentazione Microsoft
description: "L&quot;amministratore IT può scegliere di registrare con Azure Active Directory (Azure AD) i dispositivi Windows aggiunti a un dominio in modo automatico e invisibile all&quot;utente."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2d717f906bd4aad35cb46852baf84c4e088b953e


---
# <a name="automatic-device-registration-with-azure-active-directory-for-windows-domain-joined-devices"></a>Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio
L'amministratore IT può scegliere di registrare in modo automatico e invisibile all'utente i dispositivi Windows aggiunti a un dominio con Azure Active Directory (Azure AD). Questa operazione può risultare utile se sono stati configurati criteri di accesso condizionale basati sul dispositivo per applicazioni di Office 365 o applicazioni gestite in locale da AD FS. Per altre informazioni sugli scenari di registrazione dei dispositivi, vedere [Panoramica di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md).

> AZURE.NOTA Per le istruzioni più recenti su come configurare la registrazione automatica dei dispositivi, vedere [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> 

La registrazione automatica dei dispositivi con Azure Active Directory è disponibile per computer Windows 7 e Windows 8.1 aggiunti a un dominio di Active Directory. Si tratta in genere di computer aziendali assegnati agli Information Worker.

Per iniziare a registrare i dispositivi Windows aggiunti a un dominio con Azure AD, osservare i prerequisiti seguenti. Dopo aver completato i prerequisiti, configurare la registrazione automatica dei dispositivi Windows aggiunti al dominio.

## <a name="prerequisites-for-automatic-device-registration-of-domain-joined-windows-devices-with-azure-active-directory"></a>Prerequisiti per la registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio
## <a name="deploy-ad-fs-and-connect-to-azure-active-directory-using-azure-active-directory-connect"></a>Distribuire AD FS e connettersi ad Azure Active Directory con Azure Active Directory Connect
1. Usare Azure Active Directory Connect per distribuire Active Directory Federation Services (AD FS) con Windows Server 2012 R2 e configurare una relazione federativa con Azure Active Directory.
2. Configurare le regole attestazioni del trust della relying party di Azure Active Directory.
3. Aprire la console di gestione di AD FS e passare ad **AD FS**>**Relazioni di attendibilità>Trust relying party**. Fare clic con il pulsante destro del mouse sull'oggetto trust della relying party della Piattaforma delle identità di Microsoft Office 365 e scegliere **Modifica regole attestazione**
4. Nella scheda **Regole di trasformazione rilascio** selezionare **Aggiungi regola**.
5. Nella casella di riepilogo a discesa **Modello di regola attestazione** selezionare **Inviare attestazioni mediante una regola personalizzata**. Selezionare **Avanti**.
6. Digitare *Regola attestazioni Metodo di autenticazione* nella casella di testo **Nome regola attestazione** .
7. Digitare la regola attestazioni seguente nella casella di testo **Regola attestazioni** :
   
        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);
8. Fare clic su **OK** due volte per completare la finestra di dialogo.

## <a name="configure-an-additional-azure-active-directory-relying-party-trust-authentication-class-reference"></a>Configurare un riferimento aggiuntivo alla classe di autenticazione del trust della relying party di Azure Active Directory
Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare:

  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

Dove <RPObjectName> è il nome dell'oggetto relying party per l'oggetto trust della relying party di Azure Active Directory. Tale oggetto in genere è denominato Piattaforma delle identità di Microsoft Office 365.

## <a name="ad-fs-global-authentication-policy"></a>Criteri di autenticazione globali di AD FS
Configurare i criteri di autenticazione globali di AD FS per consentire l'autenticazione integrata di Windows per la rete Intranet (impostazione predefinita).

## <a name="internet-explorer-configuration"></a>Configurazione di Internet Explorer
Configurare le impostazioni seguenti in Internet Explorer sui dispositivi Windows per l'area di protezione Intranet locale:

* Non richiedere la selezione del certificato client quando esiste solo un certificato: **Attiva**
* Consenti esecuzione script: **Attiva**
* Accesso automatico solo nell'area Intranet: **Selezionata**

Queste sono le impostazioni predefinite per l'area di protezione Intranet locale di Internet Explorer. È possibile visualizzare o gestire queste impostazioni in Internet Explorer passando a **Opzioni Internet** > **Sicurezza** > Intranet locale > Livello personalizzato. È anche possibile configurare queste impostazioni con Criteri di gruppo di Active Directory.

## <a name="network-connectivity"></a>Connettività di rete
I dispositivi Windows aggiunti a un dominio devono essere connessi ad AD FS e a un controller di dominio di Active Directory per eseguire automaticamente la registrazione con Azure AD. In genere, ciò significa che il computer deve essere connesso alla rete aziendale. Può essere una connessione cablata, una connessione Wi-Fi, DirectAccess o VPN.

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurare l'individuazione di Registrazione dispositivo di Azure Active Directory
I dispositivi client Windows 7 e Windows 8.1 individueranno il server di registrazione dispositivo combinando il nome dell'account utente con il nome di un server di registrazione dispositivo noto. È necessario creare un record DNS CNAME che punti al record A associato al servizio Registrazione dispositivo di Azure Active Directory. Il record CNAME deve usare il prefisso noto **enterpriseregistration** seguito dal suffisso UPN usato per gli account utente nell'organizzazione. Se l'organizzazione usa più suffissi UPN, è necessario creare più record CNAME nel DNS.

Ad esempio, se nell'organizzazione si usano due suffissi UPN denominati @contoso.com e @region.contoso.com,, si creeranno i record DNS seguenti.

| Voce | Tipo | Indirizzo |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="configure-automatic-device-registration-for-windows-7-and-windows-81-domain-joined-devices"></a>Configurare la registrazione automatica per i dispositivi Windows 7 e Windows 8.1 aggiunti a un dominio
Configurare la registrazione automatica per i dispositivi Windows 7 e Windows 8.1 aggiunti a un dominio usando i collegamenti seguenti. Assicurarsi di aver completato i prerequisiti precedenti prima di continuare.

* [Configurare la registrazione automatica per i dispositivi Windows 8.1 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
* [Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)
* [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows 10 aggiunti a un dominio](active-directory-azureadjoin-devices-group-policy.md)

## <a name="additional-notes"></a>Note aggiuntive
La registrazione del dispositivo con Azure AD offre un'ampia gamma di funzionalità. Con Registrazione dispositivo di Azure AD è possibile registrare i dispositivi mobili personali (BYOD) e i dispositivi aziendali aggiunti a un dominio. I dispositivi possono essere usati con servizi ospitati come Office365 e con servizi gestiti in locale con AD FS.

Le aziende che usano dispositivi mobili e tradizionali o che usano Office365, Azure AD o altri servizi Microsoft devono registrare i dispositivi in Azure AD con il servizio Registrazione dispositivo di Azure AD. Se l'azienda non usa né dispositivi mobili né servizi Microsoft come Office365, Azure AD o Intune e ospita invece solo applicazioni locali, è possibile scegliere di registrare i dispositivi in Active Directory tramite AD FS.

Per altre informazioni sulla distribuzione della registrazione dei dispositivi con AD FS, fare clic [qui](https://technet.microsoft.com/library/dn486831.aspx).

## <a name="additional-topics"></a>Argomenti aggiuntivi
* [Panoramica di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)
* [Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)
* [Configurazione della registrazione automatica per i dispositivi appartenenti a un dominio di Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
* [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows 10 aggiunti a un dominio](active-directory-azureadjoin-devices-group-policy.md)




<!--HONumber=Nov16_HO3-->


