<properties
	pageTitle="Guida per il rinnovo del certificato per gli utenti di Office 365 e Azure AD. | Microsoft Azure"
	description="In questo articolo viene illustrato agli utenti di Office 365 come risolvere i problemi con i messaggi e-mail contenenti la notifica sul rinnovo di un certificato."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath"/>


# Rinnovo dei certificati di federazione per Office 365 e Azure AD

Se viene ricevuto un messaggio e-mail o una notifica tramite il portale in cui viene richiesto di rinnovare il certificato per Office 365, questo articolo è destinato a risolvere il problema ed evitare che si ripresenti. In questo articolo si presuppone che si utilizza AD FS come server federativo.

>[AZURE.IMPORTANT] Tenere presente che l'autenticazione tramite il proxy potrebbe non riuscire in Windows Server 2012 o Windows Server 2008 R2 dopo aver eseguito una delle seguenti operazioni:
>
- Rinnovo del token di protezione da parte del prozy dopo il rollover dei certificati in AD FS
- Sostituzione manuale dei certificati di AD FS
>
Un aggiornamento rapido è disponibile per risolvere il problema. Vedere [L'autenticazione tramite proxy ha esito negativo in Windows Server 2012 o Windows Server 2008 R2 SP1](http://support.microsoft.com/kb/3094446)

## Verificare se è necessario eseguire operazioni

Se si utilizza AD FS 2.0 o versioni successive, Office 365 e Azure AD aggiornerà automaticamente il certificato prima della scadenza. Non è necessario eseguire passaggi manuali o uno script come attività pianificata. A questo scopo, entrambe le seguenti impostazioni di configurazione predefinite di AD FS devono essere attive:

- La proprietà di AD FS AutoCertificateRollover deve essere impostata su True, indicante che AD FS genererà automaticamente nuovi certificati di decrittografia di token e firma di token prima della scadenza dei precedenti.
	- Se il valore è False, si utilizzano le impostazioni del certificato personalizzate. Visitare [questa pagina](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert) per le istruzioni complete.
- I metadati di federazione devono essere disponibili per la rete Internet pubblica.

	Di seguito viene illustrato come eseguire il controllo:

	- Verificare che l'installazione di AD FS utilizza il rollover automatico dei certificati eseguendo il comando seguente in una finestra di comando di PowerShell nel server federativo primario:

	`PS C:\> Get-ADFSProperties`

Si noti che se si utilizza AD FS 2.0, sarà necessario eseguire prima Add-Pssnapin Microsoft.Adfs.Powershell.

Verificare che i metadati di federazione siano accessibili pubblicamente, passare all'URL seguente da un computer sulla rete Internet pubblica (all'esterno della rete aziendale):


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

dove `(your_FS_name) ` viene sostituito con il nome host del servizio federativo usato dall'organizzazione, ad esempio fs.contoso.com. Se si è in grado di verificare entrambe le impostazioni correttamente, non occorre eseguire altre operazioni.

Esempio: https://fs.contos.com/federationmetadata/2007-06/federationmetadata.xml

## Se la proprietà AutoCertificateRollover è impostata su False

Se la proprietà AutoCertificateRollover è impostata su False, si utilizzano le impostazioni di certificati AD FS non predefiniti. Il motivo più comune è che l'organizzazione gestisce i certificati AD FS registrati da un'autorità di certificazione aziendale. In questo caso è necessario rinnovare e aggiornare manualmente i certificati. Usare le linee guida disponibili [qui](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

## Se i metadati non sono accessibili pubblicamente
Se AutocertificateRollover è impostato su True, ma i metadati di federazione non sono disponibili pubblicamente, utilizzare la procedura riportata di seguito per verificare che i certificati vengano aggiornati in locale e nel cloud:

### Verificare che il sistema di AD FS abbia generato un nuovo certificato.

- Verificare di essere connessi al server AD FS primario.
- Verificare i certificati di firma correnti in AD FS aprendo una finestra di comando di PowerShell ed eseguendo il comando seguente:

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

Si noti che se si utilizza AD FS 2.0, sarà necessario eseguire prima Add-Pssnapin Microsoft.Adfs.Powershell.


- Esaminare l'output del comando in tutti i certificati elencati. Se AD FS ha generato un nuovo certificato, sono presenti due certificati nell'output: uno per cui il valore IsPrimary è True e la data NotAfter è entro 5 giorni e uno per cui IsPrimary è False e NotAfter è circa un anno nel futuro.

- Se è presente un solo certificato e la data di NotAfter è entro 5 giorni, è necessario generare un nuovo certificato eseguendo i passaggi seguenti.

- Per generare un nuovo certificato, eseguire il comando seguente al prompt dei comandi di PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

- Verificare l'aggiornamento eseguendo di nuovo il comando seguente: PS C:\>Get-ADFSCertificate –CertificateType token-signing
- Quindi, per aggiornare manualmente le proprietà di attendibilità federazione di Office 365, attenersi alla seguente procedura.

A questo punto dovrebbero essere elencati due certificati, uno dei quali ha una data NotAfter di circa un anno nel futuro e il valore IsPrimary è False.


### Per aggiornare manualmente le proprietà di attendibilità federazione di Office 365, attenersi alla seguente procedura.

1.	Aprire il modulo di Microsoft Azure Active Directory per Windows PowerShell.
2.	Eseguire $cred=Get-Credential. Quando questo cmdlet richiede le credenziali, digitare le credenziali dell'account amministratore servizio cloud.
3.	Eseguire Connect-MsolService –Credential $cred. Questo cmdlet consente di connettersi al servizio cloud. La creazione di un contesto che consente di connettersi al servizio cloud è necessaria prima di eseguire i cmdlet aggiuntivi installati dallo strumento.
4.	Se si eseguono questi comandi in un computer che non è il server federativo primario di ADFS, eseguire Set-MSOLAdfscontext -Computer <AD FS primary server>, dove <AD FS primary server> è il nome FQDN interno del server ADFS primario. Questo cmdlet crea un contesto che consente la connessione ad AD FS.
5.	Eseguire Update-MSOLFederatedDomain –DomainName <domain>. Questo cmdlet aggiorna le impostazioni di AD FS nel servizio cloud e configura la relazione di trust tra i due.

>[AZURE.NOTE] Se è necessario supportare più domini di primo livello, ad esempio contoso.com e fabrikam.com, è necessario utilizzare l'opzione SupportMultipleDomain con tutti i cmdlet. Per ulteriori informazioni, vedere Supporto per più domini di primo livello. Infine, verificare che tutti i server proxy applicazione Web vengano aggiornati con l'aggiornamento cumulativo [Windows Server maggio 2014](http://support.microsoft.com/kb/2955164), in caso contrario i proxy potrebbero non essere aggiornati con il nuovo certificato, con una conseguente interruzione.

<!---HONumber=AcomDC_0128_2016-->