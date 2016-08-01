<properties 
    pageTitle="Introduzione all'autenticazione basata su certificati in Android | Microsoft Azure" 
    description="Informazioni su come configurare l'autenticazione basata su certificati in soluzioni con dispositivi Android" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/13/2016" 
    ms.author="markvi" />



# Introduzione all'autenticazione basata su certificati nell'anteprima pubblica per Android  

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


Questo argomento descrive come configurare e usare l'autenticazione basata su certificati (Certificate Based Authentication o CBA) in un dispositivo Android per gli utenti dei tenant nei piani Office 365 Enterprise, Business ed Education.

L'autenticazione basata su certificati consente di essere autenticati da Azure Active Directory con un certificato client in un dispositivo Android o iOS quando si connette l'account Exchange online a:

- Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word
- Client Exchange ActiveSync (EAS)

La configurazione di questa funzionalità elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta e applicazioni Microsoft Office sul dispositivo mobile.
 

## Requisiti e scenari supportati  



### Requisiti generali 


Per tutti gli scenari di questo argomento sono necessarie le seguenti attività:

- Accesso alle autorità di certificazione per l'emissione dei certificati client.

- Le autorità di certificazione devono essere configurate in Azure Active Directory. La procedura dettagliata su come completare la configurazione è riportata nella sezione introduttiva.

- L'autorità di certificazione radice e tutte le autorità di certificazione intermedie devono essere configurate in Azure Active Directory.

- Ogni autorità di certificazione deve avere un elenco di revoche di certificati (Certificate Revocation List o CRL) a cui si possa fare riferimento tramite un URL Internet.

- Per l'autenticazione client deve essere emesso il certificato client.


- Solo per i client Exchange ActiveSync, il certificato client deve avere l'indirizzo di posta elettronica dell'utente, instradabile in Exchange online, o nel nome dell'entità o nel valore del nome RFC822 del campo relativo al nome oggetto alternativo. Azure Active Directory esegue il mapping del valore RFC822 all'attributo dell'indirizzo Proxy nella directory.



### Supporto delle applicazioni Office per dispositivi mobili 

| App | Supporto |
| ---                       | ---          |
| OneDrive | Sì |
| Outlook | Sì |
| Word / Excel / PowerPoint | Sì |
| Skype for Business Online | Sì |


### Requisiti  

La versione del sistema operativo del dispositivo deve essere Android 5.0 (Lollipop) o successiva

Per eseguire l'autenticazione basata su certificati con le applicazioni Office per dispositivi mobili è necessario configurare un server federativo.


Perché Azure Active Directory possa revocare un certificato client, il token ADFS deve avere le attestazioni seguenti:

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Il numero di serie del certificato client)

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (La stringa per l'autorità emittente del certificato client)

Azure Active Directory aggiunge queste attestazioni per il token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando il token di aggiornamento deve essere convalidato, queste informazioni vengono usate per controllare la revoca.

Come procedura consigliata, è necessario aggiornare le pagine di errore di ADFS con le istruzioni su come ottenere un certificato utente.

Per altre informazioni, vedere l'articolo sulla [personalizzazione delle pagine di accesso ad AD FS](https://technet.microsoft.com/library/dn280950.aspx).



### Supporto dei client Exchange ActiveSync 


Alcune applicazioni Exchange ActiveSync sono supportate in Android 5.0 (Lollipop) o versioni successive. Per determinare se l'applicazione di posta elettronica supporta questa funzionalità, contattare lo sviluppatore dell'applicazione.



## Introduzione 


Per iniziare, è necessario configurare le autorità di certificazione in Azure Active Directory. Per ogni autorità di certificazione caricare gli elementi seguenti:

- La parte pubblica del certificato, nel formato *.cer*

- Gli URL Internet in cui si trovano gli elenchi di revoche di certificati (Certificate Revocation List o CRL)
 

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


Per caricare le informazioni, è possibile usare il modulo Azure AD tramite Windows PowerShell. Di seguito sono riportati esempi per l'aggiunta, la rimozione e la modifica di un'autorità di certificazione.



### Configurazione del tenant di Azure AD per l'autenticazione basata su certificati 

1. Avviare Windows PowerShell con privilegi amministrativi.

2. Installare il modulo Azure AD. È necessario installare la versione [1\.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) o una versione successiva.

        Install-Module -Name AzureAD –RequiredVersion 1.1.143.0 

3. Connettersi al tenant di destinazione:

        Connect-AzureAD 

### Aggiunta di una nuova autorità di certificazione

1. Impostare le varie proprietà dell'autorità di certificazione e aggiungerla ad Azure Active Directory:

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Ottenere le autorità di certificazione:

        Get-AzureADTrustedCertificateAuthority 


### Recupero delle autorità di certificazione dell'elenco

Recuperare le autorità di certificazione attualmente archiviate in Azure Active Directory per il tenant:

        Get-AzureADTrustedCertificateAuthority 


### Rimozione di un'autorità di certificazione

1.	Recuperare le autorità di certificazione:

		$c=Get-AzureADTrustedCertificateAuthority 


2. Rimuovere il certificato per l'autorità di certificazione:

		Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### Modifica di un'autorità di certificazione 

1.	Recuperare le autorità di certificazione:

		$c=Get-AzureADTrustedCertificateAuthority 


2. Modificare le proprietà dell'autorità di certificazione:

		$c[0].AuthorityType=1 

3. Impostare l'**autorità di certificazione**:

		Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## Test delle applicazioni Office per dispositivi mobili  

Per testare l'autenticazione basata su certificati con un'applicazione Office per dispositivi mobili:

1.	Nel dispositivo di test installare un'applicazione Office per dispositivi mobili (come OneDrive) da Google Play Store.

2.	Verificare che il certificato utente sia stato fornito al dispositivo di test.

3.	Avviare l'applicazione.

4.	Immettere il nome utente e quindi selezionare il certificato utente da usare.

È necessario aver eseguito l'accesso.





## Test delle applicazioni client Exchange ActiveSync

Per accedere a Exchange ActiveSync tramite l'autenticazione basata su certificati, l'applicazione deve avere a disposizione un profilo EAS contenente il certificato client. Il profilo EAS deve contenere le informazioni seguenti:

- Il certificato utente da usare per l'autenticazione

- L'endpoint EAS deve essere outlook.office365.com (in quanto la funzionalità è attualmente supportata solo nell'ambiente Exchange online multi-tenant)

Un profilo EAS può essere configurato e aggiunto al dispositivo tramite l'uso di un MDM come Intune o inserendo manualmente il certificato nel profilo EAS sul dispositivo.


### Test delle applicazioni client EAS in Android 

Per testare l'autenticazione basata su certificati con un'applicazione in Android 5.0 (Lollipop) o versioni successive, eseguire la procedura riportata di seguito:

1. Configurare un profilo EAS nell'applicazione che soddisfi i requisiti riportati sopra.


2. Dopo aver configurato correttamente il profilo, aprire l'applicazione e verificare che la posta sia sincronizzata.




## Revoca

Per revocare un certificato client, Azure Active Directory recupera l'elenco di revoche di certificati (Certificate Revocation List o CRL) dagli URL caricati come parte delle informazioni sull'autorità di certificazione e li memorizza nella cache. L'ultimo timestamp di pubblicazione, ovvero la proprietà **Effective Date** (Data di validità), in CRL viene usato per assicurare la validità di CRL. Il CRL viene referenziato periodicamente per revocare l'accesso ai certificati che fanno parte dell'elenco.

Se è necessaria una revoca più immediata (ad esempio in caso di smarrimento del dispositivo da parte di un utente), il token di autorizzazione dell'utente può essere annullato. Per annullare il token di autorizzazione, impostare il campo **StsRefreshTokenValidFrom** per questo particolare utente usando Windows PowerShell. È necessario aggiornare il campo **StsRefreshTokenValidFrom** per ogni utente a cui revocare l'accesso.
 
Per fare in modo che la revoca persista, è necessario impostare la proprietà **Effective Date** (Data di validità) di CRL su una data successiva al valore impostato da **StsRefreshTokenValidFrom** e assicurarsi che il certificato in questione sia in CRL.
 
I passaggi seguenti illustrano il processo per aggiornare e annullare il token di autorizzazione impostando il campo **StsRefreshTokenValidFrom**.

1. Connettersi con credenziali amministrative al servizio MSOL:

		$msolcred = get-credential 
		connect-msolservice -credential $msolcred 

1.	Recuperare il valore StsRefreshTokensValidFrom corrente per un utente:

		$user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
		$user.StsRefreshTokensValidFrom 


1.	Configurare un nuovo valore StsRefreshTokensValidFrom per l'utente uguale al timestamp corrente:

		Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


La data impostata deve essere futura. Se la data non è futura, la proprietà **StsRefreshTokensValidFrom** non viene impostata. Se la data è futura, la proprietà **StsRefreshTokensValidFrom** viene impostata sull'ora corrente, non sulla data indicata dal comando Set-MsolUser.

<!---HONumber=AcomDC_0720_2016-->