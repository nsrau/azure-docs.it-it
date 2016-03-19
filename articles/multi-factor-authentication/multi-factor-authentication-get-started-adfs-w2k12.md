<properties 
	pageTitle="Proteggere le risorse locali e cloud con Azure multi-factor Authentication Server ADFS di Windows Server 2012 R2" 
	description="Questa è la pagina di autenticazione a più fattori di Azure che descrive come iniziare a utilizzare l'autenticazione a più fattori Azure e ADFS in Windows Server 2012 R2." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article"
	ms.date="03/04/2016"" 
	ms.author="billmath"/>


# Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con ADFS di Windows Server 2012 R2

Se l'organizzazione è federata con Azure Active Directory e si hanno risorse locali o nel cloud da proteggere, è possibile usare il server Azure Multi-Factor Authentication e configurarlo per il funzionamento con ADFS in modo da attivare l'autenticazione a più fattori per endpoint di particolare importanza.

In questa documentazione viene illustrato l'utilizzo di Server Azure multi-Factor Authentication con ADFS in Windows Server 2012 R2. Per informazioni sull'utilizzo di Azure multi-Factor Authentication con AD FS 2.0 vedere [proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Protezione di Windows Server 2012 R2 AD ADFS con Azure multi-Factor Authentication Server

Quando si installa il Server Azure multi-Factor Authentication hai due opzioni seguenti:

- Installare il Server Azure multi-Factor Authentication in locale nello stesso server ADFS 
- Installare l'Adapter di Azure multi-Factor Authentication in locale nel Server AD FS e installare il Server di autenticazione a più fattori in un altro computer

Prima di iniziare, tenere presente le seguenti informazioni:

- Non è un requisito che il Server Azure multi-Factor Authentication essere installata nel server federativo ADFS tuttavia l'Adapter per l'autenticazione a più fattori per ADFS deve essere installato in un'esecuzione di ADFS di Windows Server 2012 R2. È possibile installare il server in un computer diverso, purché è una versione supportata e installare separatamente la scheda ADFS nel server federativo ADFS. Vedere la procedura seguente per istruzioni sull'installazione dell'adapter separatamente.
- Quando è stata progettata la scheda AD FS del server Multi-Factor Authentication, è stato previsto per AD FS il passaggio del nome della relying party alla scheda che può essere usata come nome di un'applicazione. Tuttavia, questo non si è verificato. Se si usano metodi di autenticazione tramite app per dispositivi mobili o SMS, le stringhe definite nelle impostazioni società contengono un segnaposto "<$application\_name$>" che non viene sostituito quando si usa la scheda AD FS. Per questo motivo, è consigliabile rimuovere il segnaposto dalle stringhe appropriate per la protezione di AD FS.

- L'account che effettua necessario disporre dei privilegi per creare gruppi di protezione in Active Directory.

- L'installazione guidata scheda ADFS di multi-Factor Authentication consente di creare un gruppo di sicurezza denominato PhoneFactor Admins in Active Directory e quindi aggiunge l'account del servizio ADFS del servizio federativo a questo gruppo. È consigliabile verificare nel controller di dominio che effettivamente creato il gruppo PhoneFactor Admins e account del servizio AD FS è un membro di questo gruppo. Se necessario, aggiungere l'account del servizio ADFS manualmente al gruppo PhoneFactor Admins sul controller di dominio.
  

### Per installare il Server Azure multi-Factor Authentication in locale nello stesso server ADFS

1. Scaricare e installare il Server Azure multi-Factor Authentication nel server federativo ADFS. Per informazioni sull'installazione del server Azure Multi-Factor Authentication, vedere[Introduzione al server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md)
2. Nell'interfaccia utente Server Azure multi-Factor Authentication, selezionare l'icona di ADFS e selezionare le opzioni per Consenti utente enrollement e consentire agli utenti di selezionare il metodo.
3. Selezionare le opzioni aggiuntive.
4. Fare clic su Installa scheda ADFS.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se il computer è unito a un dominio e la configurazione di Active Directory per proteggere la comunicazione tra la scheda ADFS e il servizio multi-Factor Authentication è incompleta, verrà visualizzato il passaggio Active Directory. Fare clic sul pulsante Avanti per completare la configurazione automaticamente o controllare la configurazione di Active Directory automatic Skip e configurare le impostazioni manualmente casella di controllo e fare clic su Avanti.
6. Se il computer non è unito a un dominio e la configurazione del gruppo locale per proteggere la comunicazione tra la scheda ADFS e il servizio multi-Factor Authentication è incompleta, verrà visualizzato il passaggio di gruppo locali. Fare clic sul pulsante Avanti per completare la configurazione automaticamente o controllare la configurazione gruppo locale automatic Skip e configurare le impostazioni manualmente casella di controllo e fare clic su Avanti.
7. Questo verrà visualizzata l'installazione guidata, fare clic su Avanti per consentire il Server Azure multi-Factor Authentication creare il gruppo PhoneFactor Admins e aggiungere l'account del servizio ADFS al gruppo PhoneFactor Admins.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Nel passaggio Avvia programma di installazione, fare clic su Avanti.
9. Nel programma di installazione scheda ADFS di multi-Factor Authentication, fare clic su Avanti.
10. Al termine dell'installazione, fare clic su Chiudi.
11. Ora che è stato installato l'adapter, deve essere registrato con AD FS. Aprire Windows PowerShell ed eseguire il comando seguente: 
	c:\\Programmi\\Microsoft Programmi\\multi-factor \\programmi\\multi-factor Authentication Server\\register-multifactorauthenticationadfsadapter.ps1
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. A questo punto è necessario modificare i criteri di autenticazione globali in ADFS utilizzare l'adattatore appena registrato. Nella Console di gestione AD FS, passare al nodo Criteri di autenticazione e nella sezione multi-factor Authentication, fare clic sul collegamento di modifica accanto alla sottosezione impostazioni globali. Nella finestra di dialogo Modifica criteri di autenticazione globali, selezionare multi-Factor Authentication come metodo di autenticazione aggiuntivo e quindi fare clic su OK. L'adapter viene registrata come WindowsAzureMultiFactorAuthentication. È necessario riavviare il servizio ADFS rendere effettiva la registrazione.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

A questo punto, il Server multi-Factor Authentication è configurato per essere un provider di autenticazione aggiuntivi per l'utilizzo con AD FS.

## Per installare la scheda ADFS autonoma tramite il SDK del servizio Web
1. Installare l'SDK del servizio Web sul server che esegue il server Multi-Factor Authentication.
2. Copiare i file multifactorauthenticationadfsadaptersetup64. msi, Register-multifactorauthenticationadfsadapter. Ps1, Unregister-multifactorauthenticationadfsadapter. Ps1 e multifactorauthenticationadfsadapter da \\programmi\\multi-factor-Factor Authentication Server directory al server che si prevede di installare la scheda ADFS in.
3. Eseguire il file multifactorauthenticationadfsadaptersetup64. msi.
4. Nel programma di installazione scheda ADFS di multi-Factor Authentication, fare clic su Avanti per eseguire l'installazione.
5. Al termine dell'installazione, fare clic sul pulsante chiudere.
6. Modificare il file Multifactorauthenticationadfsadapter effettuando le operazioni seguenti:

Passaggio Multifactorauthenticationadfsadapter| Passaggio secondario
:------------- | :------------- |
Impostare il nodo UseWebServiceSdk su true.||
Impostare WebServiceSdkUrl sull'URL del servizio Web multi-Factor Authentication SDK.||
Opzione 1: configurare SDK del servizio Web con un nome utente e una password.|<ol><li>Impostare WebServiceSdkUsername su un account membro del gruppo di sicurezza PhoneFactor Admins. Usare <domain>formato <nomeutente>.<li>Impostare WebServiceSdkPassword sulla password dell'account appropriata.</li></ol>
Opzione 2: configurare il SDK del servizio Web con un certificato client.|<ol><li>Ottenere un certificato client da un'autorità di certificazione per il server che esegue l'SDK del servizio Web.</li><li>Importare il certificato client per l'archivio certificati personali del computer locale del server che esegue il SDK del servizio Web. Nota: assicurarsi che il certificato pubblico dell'autorità di certificazione sia tra i certificati radice attendibili.</li><li>Esportare le chiavi pubbliche e private del certificato client in un file PFX.</li><li>Esportare la chiave pubblica con codifica base 64 in un file CER.</li><li>In Server Manager verificare che sia installata la funzionalità di autenticazione mapping certificati client IIS in Server Web (IIS)\\Server Web\\Sicurezza.</li><li>Se non è installata, scegliere Aggiungi ruoli e funzionalità per aggiungerla.</li><li>In Gestione IIS fare doppio clic sull'editor di configurazione nel sito Web che contiene la directory virtuale dell'SDK del servizio Web. Nota: È molto importante eseguire questa operazione a livello di sito web e non a livello di directory virtuale.</li><li>Passare alla sezione system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Impostare enabled su true.</li><li>Impostare oneToOneCertificateMappingsEnabled su true.</li><li>Scegliere il... accanto a oneToOneMappings.</li><li>Fare clic sul collegamento Aggiungi.</li><li>Aprire il file con estensione cer base 64 esportato in precedenza. Rimuovere---inizio certificato------fine certificato--- e tutte le interruzioni di riga. Copiare la stringa risultante.</li><li>Impostare il certificato sulla stringa copiata nel passaggio precedente.</li><li>Impostare enabled su true.</li><li>Impostare userName su un account membro del gruppo di sicurezza PhoneFactor Admins. Utilizzare <domain>Formato < nomeutente >.</li><li>Imposta password per la password dell'account appropriato.</li><li>Chiudere l'Editor della raccolta.</li><li>Fare clic sul collegamento applica.</li><li>Passare alla directory virtuale Web Service SDK.</li><li>Fare doppio clic su autenticazione.</li><li>Verificare che siano abilitate rappresentazione ASP.NET e l'autenticazione di base e tutti gli altri elementi sono disabilitate.</li><li>Passare alla directory virtuale Web Service SDK di nuovo.</li><li>Fare doppio clic su impostazioni SSL.</li><li>Impostare i certificati Client su Accetta e fare clic su Applica.</li><li>Copia il file PFX esportato in precedenza per il server che esegue la scheda ADFS.</li><li>Importare il file pfx nell'archivio di certificati personali del computer locale.</li><li>Scegliere Gestisci chiavi Private dal menu di scelta rapida e concedere l'accesso in lettura all'account di Active Directory Federation Services servizio ha effettuato l'accesso con nome</li><li>Aprire il certificato client e copiare l'identificazione digitale dalla scheda Dettagli</li><li>Nel file Multifactorauthenticationadfsadapter config impostare WebServiceSdkCertificateThumbprint sulla stringa copiata nel passaggio precedente.</li></ol>
Modificare lo script Register-multifactorauthenticationadfsadapter.ps1 aggiungendo -ConfigurationFilePath <path> alla fine del comando Register-AdfsAuthenticationProvider, dove <path> è il percorso completo del file MultiFactorAuthenticationAdfsAdapter.config.|


Eseguire lo script \\Programmi\\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 in PowerShell per registrare la scheda. L'adapter viene registrata come WindowsAzureMultiFactorAuthentication. È necessario riavviare il servizio ADFS rendere effettiva la registrazione.




























 

 


 

 


 





 


 

























































































 


 

 






 

<!------HONumber=AcomDC_0309_2016-->