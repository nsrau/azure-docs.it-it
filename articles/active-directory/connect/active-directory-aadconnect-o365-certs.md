---
title: Rinnovo del certificato per gli utenti di Office 365 e Azure AD. | Documentazione Microsoft
description: In questo articolo viene illustrato agli utenti di Office 365 come risolvere i problemi con i messaggi e-mail contenenti la notifica sul rinnovo di un certificato.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: billmath
ms.openlocfilehash: a0e3b65c108f8d839b8107e98a5cd59df78e1ab0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Rinnovare i certificati di federazione per Office 365 e Azure Active Directory
## <a name="overview"></a>Panoramica
Perché una federazione tra Azure Active Directory (Azure AD) e Active Directory Federation Services (AD FS) riesca, è necessario che i certificati usati da AD FS per la firma dei token di sicurezza in Azure AD corrispondano alle informazioni configurate in Azure AD. Eventuali informazioni non corrispondenti possono comportare l'interruzione del trust. Azure AD assicura che queste informazioni rimangano sincronizzate durante la distribuzione di AD FS e Proxy applicazione Web (per l'accesso Extranet).

Questo articolo fornisce informazioni aggiuntive per gestire i certificati per la firma di token e mantenerli sincronizzati con Azure AD nei casi seguenti:

* Non si distribuisce Proxy applicazione Web e quindi i metadati della federazione non sono disponibili nella Extranet.
* Non si usa la configurazione predefinita di AD FS per i certificati per la firma di token.
* Si usa un provider di identità di terze parti.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configurazione predefinita di AD FS per i certificati per la firma di token
I certificati di decrittografia token e per la firma di token sono in genere certificati autofirmati e sono validi per un anno. Per impostazione predefinita, AD FS include un processo di rinnovo automatico denominato **AutoCertificateRollover**. Se si usa AD FS 2.0 o versione successiva, Office 365 e Azure AD aggiornano automaticamente il certificato prima della scadenza.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notifica di rinnovo dal portale di Office 365 o in un messaggio di posta elettronica
> [!NOTE]
> Se si è ricevuta una notifica di posta elettronica o un avviso nel portale con la richiesta di rinnovare il certificato per Office, vedere la sezione sulla [gestione delle modifiche ai certificati per la firma di token](#managecerts) per verificare se è necessario intraprendere un'azione. Microsoft è a conoscenza di un possibile problema che può comportare l'invio di notifiche per il rinnovo dei certificati, anche quando non è richiesta alcuna azione.
>
>

Azure AD prova a monitorare i metadati della federazione e ad aggiornare i certificati per la firma di token come indicato dai metadati. 30 giorni prima della scadenza dei certificati per la firma di token, Azure AD verifica se i nuovi certificati sono disponibili eseguendo il poll dei metadati della federazione.

* Se riesce a eseguire il poll dei metadati della federazione e a recuperare i nuovi certificati, l'utente non riceverà alcuna notifica di posta elettronica né un avviso nel portale di Office 365.
* Se non riesce a recuperare i nuovi certificati per la firma di token, perché i metadati della federazione non sono raggiungibili o perché il rollover automatico dei certificati non è abilitato, Azure AD genera una notifica di posta elettronica e visualizza un avviso nel portale di Office 365.

![Notifica del portale di Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Se si usa AD FS, per garantire la continuità aziendale, verificare che nei server siano stati applicati gli aggiornamenti seguenti, in modo da evitare che si verifichino errori di autenticazione per problemi noti. Si riducono in tal modo i problemi noti relativi al server proxy di AD FS per questo periodo di rinnovo e per quelli futuri:
>
> Server 2012 R2 - [Aggiornamento cumulativo per Windows Server: maggio 2014](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 and 2012 - [L'autenticazione tramite proxy ha esito negativo in Windows Server 2012 o Windows Server 2008 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Verificare se è necessario aggiornare i certificati <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Passaggio 1: Verificare lo stato di AutoCertificateRollover
Nel server AD FS aprire Powershell. Verificare che il valore AutoCertificateRollover sia impostato su True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Se si usa AD FS 2.0, eseguire prima Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Passaggio 2: Verificare che AD FS e Azure AD siano sincronizzati
Nel server AD FS aprire il prompt di Azure AD PowerShell e connettersi ad Azure AD.

> [!NOTE]
> È possibile scaricare Azure AD PowerShell [qui](https://technet.microsoft.com/library/jj151815.aspx).
>
>

    Connect-MsolService

Verificare i certificati configurati in AD FS e le proprietà del trust di Azure AD per il dominio specificato.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Se le identificazioni personali in entrambi gli output corrispondono, i certificati sono sincronizzati con Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Passaggio 3: Verificare se il certificato sta per scadere
Nell'output di Get-MsolFederationProperty o Get-AdfsCertificate verificare la data in "Not after". Se mancano meno di 30 giorni alla scadenza, è consigliabile intervenire.

| AutoCertificateRollover | Certificati sincronizzati con Azure AD | I metadati della federazione sono accessibili pubblicamente | Validità | Azione |
|:---:|:---:|:---:|:---:|:---:|
| Sì |Sì |Sì |- |Non è richiesta alcuna azione. Vedere [Rinnovare automaticamente il certificato per la firma di token](#autorenew). |
| Sì |No  |- |Meno di 15 giorni |Rinnovare immediatamente. Vedere [Rinnovare manualmente il certificato per la firma di token](#manualrenew). |
| No  |- |- |Meno di 30 giorni |Rinnovare immediatamente. Vedere [Rinnovare manualmente il certificato per la firma di token](#manualrenew). |

\[-] Non è rilevante

## Rinnovare automaticamente il certificato per la firma di token (scelta consigliata) <a name="autorenew"></a>
Non è necessario eseguire passaggi manuali se vengono soddisfatte entrambe le condizioni seguenti:

* È stato distribuito Proxy applicazione Web che può abilitare l'accesso ai metadati della federazione dalla Extranet.
* Si usa la configurazione predefinita di AD FS (AutoCertificateRollover è abilitata).

Verificare quanto segue per assicurarsi che il certificato possa essere aggiornato.

**1. La proprietà AutoCertificateRollover di AD FS deve essere impostata su True.** Ciò indica che AD FS genererà automaticamente nuovi certificati per la firma di token e certificati di decrittografia token prima della scadenza di quelli precedenti.

**2. I metadati della federazione di AD FS sono accessibili pubblicamente.** Verificare che i metadati di federazione siano accessibili pubblicamente, passare all'URL seguente da un computer sulla rete Internet pubblica (all'esterno della rete aziendale):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

dove `(your_FS_name) `viene sostituito con il nome host del servizio federativo usato dall'organizzazione, ad esempio fs.contoso.com.  Se si è in grado di verificare entrambe le impostazioni correttamente, non occorre eseguire altre operazioni.  

Esempio: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Rinnovare manualmente il certificato per la firma di token <a name="manualrenew"></a>
Si può scegliere di rinnovare manualmente i certificati per la firma di token. Ad esempio, per gli scenari seguenti potrebbe funzionare meglio il rinnovo manuale:

* I certificati per la firma di token non sono certificati autofirmati. Il motivo più comune è che l'organizzazione gestisce i certificati AD FS registrati da un'autorità di certificazione aziendale.
* La sicurezza di rete non consente la disponibilità pubblica dei metadati della federazione.

In questi scenari, ogni volta che si aggiornano i certificati per la firma di token, è necessario aggiornare anche il dominio di Office 365 con il comando di PowerShell Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Passaggio 1: Verificare che in ADFS siamo disponibili nuovi certificati per la firma di token
**Configurazione non predefinita**

Se si usa una configurazione non predefinita di AD FS in cui **AutoCertificateRollover** è impostata su **False**, è probabile che siano in uso certificati personalizzati, ovvero non autofirmati. Per altre informazioni sul rinnovo dei certificati per la firma di token di AD FS, vedere [Linee guida per i clienti che non usano certificati autofirmati di AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**I metadati della federazione non sono disponibili pubblicamente**

D'altra parte, se **AutoCertificateRollover** è impostata su **True**, ma i metadati della federazione non sono accessibili pubblicamente, assicurarsi prima di tutto che i nuovi certificati per la firma di token siano stati generati da AD FS. Per verificare la disponibilità di nuovi certificati per la firma di token, seguire questa procedura:

1. Verificare di essere connessi al server AD FS primario.
2. Verificare i certificati di firma correnti in AD FS aprendo una finestra di comando di PowerShell ed eseguendo questo comando:

    PS C:\>Get-ADFSCertificate –CertificateType token-signing

   > [!NOTE]
   > Se si usa AD FS 2.0, è consigliabile eseguire prima Add-Pssnapin Microsoft.Adfs.Powershell.
   >
   >
3. Esaminare l'output del comando in tutti i certificati elencati. Se AD FS ha generato un nuovo certificato, saranno presenti due certificati nell'output: uno per cui il valore **IsPrimary** è **True** e la data **NotAfter** è entro 5 giorni e uno per cui **IsPrimary** è **False** e **NotAfter** è una data successiva di circa un anno.
4. Se è presente un solo certificato e la data **NotAfter** è entro 5 giorni, è necessario generare un nuovo certificato.
5. Per generare un nuovo certificato, eseguire il comando seguente al prompt dei comandi di PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Verificare l'aggiornamento eseguendo di nuovo il comando seguente: PS C:\>Get-ADFSCertificate –CertificateType token-signing

A questo punto verranno elencati due certificati, uno dei quali ha una data **NotAfter** successiva di circa un anno e il valore **IsPrimary** è **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Passaggio 2: Aggiornare i nuovi certificati per la firma di token per il trust di Office 365
Aggiornare Office 365 con i nuovi certificati per la firma di token da usare per il trust come indicato di seguito.

1. Aprire il modulo di Microsoft Azure Active Directory per Windows PowerShell.
2. Eseguire $cred=Get-Credential. Quando questo cmdlet richiede le credenziali, digitare le credenziali dell'account amministratore servizio cloud.
3. Eseguire Connect-MsolService –Credential $cred. Questo cmdlet consente di connettersi al servizio cloud. La creazione di un contesto che consente di connettersi al servizio cloud è necessaria prima di eseguire i cmdlet aggiuntivi installati dallo strumento.
4. Se si eseguono questi comandi in un computer che non è il server federativo primario di ADFS, eseguire Set-MSOLAdfscontext -Computer <AD FS primary server>, dove <AD FS primary server> è il nome FQDN interno del server ADFS primario. Questo cmdlet crea un contesto che consente la connessione ad AD FS.
5. Eseguire Update-MSOLFederatedDomain –DomainName <domain>. Questo cmdlet aggiorna le impostazioni di AD FS nel servizio cloud e configura la relazione di trust tra i due.

> [!NOTE]
> Se occorre supportare più domini di primo livello, ad esempio contoso.com e fabrikam.com, è necessario usare l'opzione **SupportMultipleDomain** con tutti i cmdlet. Per altre informazioni, vedere [Supporto di più domini per la federazione con Azure AD](active-directory-aadconnect-multiple-domains.md).
>


## Ripristinare il trust di Azure AD con AD Connect <a name="connectrenew"></a>
Se la farm AD FS e il trust di Azure AD sono stati configurati con Azure AD Connect, è possibile usare quest'ultimo per rilevare se occorre intraprendere un'azione per i certificati per la firma di token. Se è necessario rinnovare i certificati, è possibile usare Azure AD Connect a questo scopo.

Per altre informazioni, vedere [Ripristino del trust](active-directory-aadconnect-federation-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Passaggi per l'aggiornamento dei certificati AD FS e Azure AD
I certificati per la firma di token sono certificati X509 standard usati per firmare in modo sicuro tutti i token rilasciati dal server federativo. I certificati di decrittografia token sono certificati X509 standard usati per decrittografare i token in ingresso. 

Per impostazione predefinita, AD FS è configurato per generare automaticamente i certificati per la firma di token e i certificati di decrittografia token, sia in fase di configurazione iniziale, sia quando i certificati hanno quasi raggiunto la data di scadenza.

Azure AD tenta di recuperare un nuovo certificato dai metadati del servizio federativo 30 giorni prima della scadenza del certificato corrente. Se un nuovo certificato non è disponibile in quel momento, Azure AD continuerà a monitorare i metadati ogni giorno a intervalli regolari. Non appena il nuovo certificato è disponibile nei metadati, le impostazioni di federazione del dominio vengono aggiornate con le nuove informazioni del certificato. È possibile usare `Get-MsolDomainFederationSettings` per verificare se il nuovo certificato è presente nel NextSigningCertificate / SigningCertificate.

Per altre informazioni sui certificati per la firma di token in AD FS, vedere [Obtain and Configure Token Signing and Token Decryption Certificates for AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs) (Ottenere e configurare i certificati per la firma di token e i certificati di decrittografia token per AD FS)