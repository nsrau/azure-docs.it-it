---
title: Introduzione all&quot;autenticazione basata su certificati in Android | Documentazione Microsoft
description: Informazioni su come configurare l&quot;autenticazione basata su certificati in soluzioni con dispositivi Android
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/16/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ce9474f2926a856673efbab5103a308d31001343
ms.openlocfilehash: ed1c66f72b09a14a14c6ecd0bf39cd92f2bd22b8


---
# <a name="get-started-with-certificate-based-authentication-on-android"></a>Introduzione all'autenticazione basata su certificati in Android
> [!div class="op_single_selector"]
> * [iOS](active-directory-certificate-based-authentication-ios.md)
> * [Android](active-directory-certificate-based-authentication-android.md)
> 
> 

Questo argomento descrive come configurare e usare l'autenticazione basata su certificati (Certificate Based Authentication o CBA) in un dispositivo Android per gli utenti dei tenant nei piani Office 365 Enterprise, Business ed Education. 

L'autenticazione basata su certificati consente di essere autenticati da Azure Active Directory con un certificato client in un dispositivo Android o iOS quando si connette l'account Exchange online a: 

* Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word   
* Client Exchange ActiveSync (EAS) 

La configurazione di questa funzionalità elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta e applicazioni Microsoft Office sul dispositivo mobile. 

## <a name="supported-scenarios-and-requirements"></a>Requisiti e scenari supportati
### <a name="general-requirements"></a>Requisiti generali
Per tutti gli scenari di questo argomento sono necessarie le seguenti attività:  

* Accesso alle autorità di certificazione per l'emissione dei certificati client.  
* Le autorità di certificazione devono essere configurate in Azure Active Directory. La procedura dettagliata su come completare la configurazione è riportata nella sezione [introduttiva](#getting-started) .  
* L'autorità di certificazione radice e tutte le autorità di certificazione intermedie devono essere configurate in Azure Active Directory.  
* Ogni autorità di certificazione deve avere un elenco di revoche di certificati (Certificate Revocation List o CRL) a cui si possa fare riferimento tramite un URL Internet.  
* Per l'autenticazione client deve essere emesso il certificato client.  
* Solo per i client Exchange ActiveSync, il certificato client deve avere l'indirizzo di posta elettronica dell'utente, instradabile in Exchange online, o nel nome dell'entità o nel valore del nome RFC822 del campo relativo al nome oggetto alternativo. Azure Active Directory esegue il mapping del valore RFC822 all'attributo dell'indirizzo Proxy nella directory.  

### <a name="office-mobile-applications-support"></a>Supporto delle applicazioni Office per dispositivi mobili
| App | Supporto |
| --- | --- |
| Word / Excel / PowerPoint |![Controllo][1] |
| OneNote |![Controllo][1] |
| OneDrive |![Controllo][1] |
| Outlook |![Controllo][1] |
| Yammer |![Controllo][1] |
| Skype for Business Online |![Controllo][1] |

### <a name="requirements"></a>Requisiti
La versione del sistema operativo del dispositivo deve essere Android 5.0 (Lollipop) o successiva. 

È necessario configurare un server federativo.  

Perché Azure Active Directory possa revocare un certificato client, il token ADFS deve avere le attestazioni seguenti:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
   (Il numero di serie del certificato client) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
   (La stringa per l'autorità emittente del certificato client) 

Azure Active Directory aggiunge queste attestazioni per il token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando il token di aggiornamento deve essere convalidato, queste informazioni vengono usate per controllare la revoca. 

Come procedura consigliata, è necessario aggiornare le pagine di errore di ADFS con le istruzioni su come ottenere un certificato utente.  
Per altre informazioni, vedere [Personalizzazione delle pagine di accesso ad AD FS](https://technet.microsoft.com/library/dn280950.aspx).  

Alcune app di Office, in cui non è abilitata l'autenticazione moderna, inviano "*prompt=login*" ad Azure AD nella richiesta. Per impostazione predefinita, Azure AD lo traduce in una richiesta ad AD FS di eseguire l'autorizzazione di nome utente e password, ovvero "*wauth=usernamepassworduri*", e di ignorare lo stato SSO ed eseguire una nuova autenticazione, ovvero "*wfresh=0*". Per abilitare l'autenticazione basata su certificati per queste applicazioni, è necessario modificare il comportamento predefinito di Azure AD. È sufficiente impostare "*PromptLoginBehavior*" tra le impostazioni del dominio federato su "*Disabled*" (Disabilitato). Per eseguire questa operazione è possibile usare il cmdlet [MSOLDomainFederationSettings](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msoldomainfederationsettings):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`



### <a name="exchange-activesync-clients-support"></a>Supporto dei client Exchange ActiveSync
Alcune applicazioni Exchange ActiveSync sono supportate in Android 5.0 (Lollipop) o versioni successive. Per determinare se l'applicazione di posta elettronica supporta questa funzionalità, contattare lo sviluppatore dell'applicazione. 

## <a name="getting-started"></a>introduttiva
Per iniziare, è necessario configurare le autorità di certificazione in Azure Active Directory. Per ogni autorità di certificazione caricare gli elementi seguenti: 

* La parte pubblica del certificato, nel formato *.cer* 
* Gli URL Internet in cui si trovano gli elenchi di revoche di certificati (Certificate Revocation List o CRL)

Di seguito è riportato lo schema per un'autorità di certificazione: 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Per caricare le informazioni, è possibile usare il modulo Azure AD tramite Windows PowerShell.  
Di seguito sono riportati esempi per l'aggiunta, la rimozione e la modifica di un'autorità di certificazione. 

### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Configurazione del tenant di Azure AD per l'autenticazione basata su certificati
1. Avviare Windows PowerShell con privilegi amministrativi. 
2. Installare il modulo Azure AD. È necessario installare la versione [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) o una versione successiva.  
   
        Install-Module -Name AzureADPreview –RequiredVersion 2.0.0.33 
3. Connettersi al tenant di destinazione: 
   
        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Aggiunta di una nuova autorità di certificazione
1. Impostare le varie proprietà dell'autorità di certificazione e aggiungerla ad Azure Active Directory: 
   
        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 
2. Ottenere le autorità di certificazione: 
   
        Get-AzureADTrustedCertificateAuthority 

### <a name="retrieving-the-list-certificate-authorities"></a>Recupero delle autorità di certificazione dell'elenco
Recuperare le autorità di certificazione attualmente archiviate in Azure Active Directory per il tenant: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Rimozione di un'autorità di certificazione
1. Recuperare le autorità di certificazione: 
   
     $c=Get-AzureADTrustedCertificateAuthority 
2. Rimuovere il certificato per l'autorità di certificazione: 
   
        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 

### <a name="modfiying-a-certificate-authority"></a>Modifica di un'autorità di certificazione
1. Recuperare le autorità di certificazione: 
   
     $c=Get-AzureADTrustedCertificateAuthority 
2. Modificare le proprietà dell'autorità di certificazione: 
   
        $c[0].AuthorityType=1 
3. Impostare l' **autorità di certificazione**: 
   
        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 

## <a name="testing-office-mobile-applications"></a>Test delle applicazioni Office per dispositivi mobili
Per testare l'autenticazione basata su certificati con un'applicazione Office per dispositivi mobili: 

1. Nel dispositivo di test installare un'applicazione Office per dispositivi mobili (come OneDrive) da Google Play Store.
2. Verificare che il certificato utente sia stato fornito al dispositivo di test. 
3. Avviare l'applicazione. 
4. Immettere il nome utente e quindi selezionare il certificato utente da usare. 

È necessario aver eseguito l'accesso. 

## <a name="testing-exchange-activesync-client-applications"></a>Test delle applicazioni client Exchange ActiveSync
Per accedere a Exchange ActiveSync tramite l'autenticazione basata su certificati, l'applicazione deve avere a disposizione un profilo EAS contenente il certificato client. Il profilo EAS deve contenere le informazioni seguenti:

* Il certificato utente da usare per l'autenticazione 
* L'endpoint EAS deve essere outlook.office365.com (in quanto la funzionalità è attualmente supportata solo nell'ambiente Exchange online multi-tenant)

Un profilo EAS può essere configurato e aggiunto al dispositivo tramite l'uso di un MDM come Intune o inserendo manualmente il certificato nel profilo EAS sul dispositivo.  

### <a name="testing-eas-client-applications-on-android"></a>Test delle applicazioni client EAS in Android
Per testare l'autenticazione basata su certificati con un'applicazione in Android 5.0 (Lollipop) o versioni successive, eseguire la procedura riportata di seguito:  

1. Configurare un profilo EAS nell'applicazione che soddisfi i requisiti riportati sopra.  
2. Dopo aver configurato correttamente il profilo, aprire l'applicazione e verificare che la posta sia sincronizzata. 

## <a name="revocation"></a>Revoca
Per revocare un certificato client, Azure Active Directory recupera l'elenco di revoche di certificati (Certificate Revocation List o CRL) dagli URL caricati come parte delle informazioni sull'autorità di certificazione e li memorizza nella cache. L'ultimo timestamp di pubblicazione, ovvero la proprietà**Effective Date** (Data di validità), in CRL viene usato per assicurare la validità di CRL. Il CRL viene referenziato periodicamente per revocare l'accesso ai certificati che fanno parte dell'elenco.

Se è necessaria una revoca più immediata (ad esempio in caso di smarrimento del dispositivo da parte di un utente), il token di autorizzazione dell'utente può essere annullato. Per annullare il token di autorizzazione, impostare il campo **StsRefreshTokenValidFrom** per questo particolare utente usando Windows PowerShell. È necessario aggiornare il campo **StsRefreshTokenValidFrom** per ogni utente a cui revocare l'accesso.

Per fare in modo che la revoca persista, è necessario impostare la proprietà **Effective Date** (Data di validità) di CRL su una data successiva al valore impostato da **StsRefreshTokenValidFrom** e assicurarsi che il certificato in questione sia in CRL.

I passaggi seguenti illustrano il processo per aggiornare e annullare il token di autorizzazione impostando il campo **StsRefreshTokenValidFrom** . 

1. Connettersi con credenziali amministrative al servizio MSOL: 
   
        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 
2. Recuperare il valore StsRefreshTokensValidFrom corrente per un utente: 
   
     $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`   $user.StsRefreshTokensValidFrom 
3. Configurare un nuovo valore StsRefreshTokensValidFrom per l'utente uguale al timestamp corrente: 
   
     Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

La data impostata deve essere futura. Se la data non è futura, la proprietà **StsRefreshTokensValidFrom** non viene impostata. Se la data è futura, la proprietà **StsRefreshTokensValidFrom** viene impostata sull'ora corrente, non sulla data indicata dal comando Set-MsolUser. 

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png



<!--HONumber=Jan17_HO1-->


