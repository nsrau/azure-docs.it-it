<properties
	pageTitle="Proteggere le risorse del cloud e locali mediante server Azure Multi-Factor Authentication con AD FS per Windows Server 2012 R2 | Microsoft Azure"
	description="Questo articolo offre una guida introduttiva ad Azure Multi-Factor Authentication e AD FS in Windows Server 2012 R2."
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
	ms.date="08/04/2016"
	ms.author="billmath"/>


# Proteggere le risorse del cloud e locali mediante server Azure Multi-Factor Authentication con AD FS di Windows Server 2012 R2

Se l'organizzazione usa Active Directory Federation Services (AD FS), per proteggere le risorse del cloud o locali è possibile distribuire e configurare il server Azure Multi-Factor Authentication per l'uso con AD FS. Questa configurazione attiva l'autenticazione a più fattori per gli endpoint di alto valore.

Questo articolo illustra l'uso del server Azure Multi-Factor Authentication con AD FS in Windows Server 2012 R2. Per altre informazioni, vedere [Proteggere le risorse del cloud e locali mediante il server Azure Multi-Factor Authentication con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Proteggere AD FS per Windows Server 2012 R2 con il server Azure Multi-Factor Authentication

Quando si installa il server Multi-Factor Authentication sono disponibili le opzioni seguenti:

- Installare il server Azure Multi-Factor Authentication in locale nello stesso server di AD FS
- Installare l'adapter di Azure Multi-Factor Authentication in locale nel server AD FS e quindi installare il server Multi-Factor Authentication in un altro computer

Prima di iniziare, tenere presente le seguenti informazioni:

- Non è necessario installare il server Azure Multi-Factor Authentication nel server AD FS. È tuttavia necessario installare l'adapter Multi-Factor Authentication per AD FS in un computer Windows Server 2012 R2 che esegue AD FS. È possibile installare il server in un computer diverso, se si tratta di una versione supportata, e installare separatamente l'adapter AD FS nel server federativo AD FS. Vedere le procedure seguenti per informazioni su come installare l'adapter separatamente.
- Quando è stato progettato l'adapter AD FS del server Multi-Factor Authentication, è stato previsto per AD FS il passaggio del nome della relying party all'adapter, che può essere usato come nome di un'applicazione. Tuttavia, questo non si è verificato. Se l'organizzazione usa metodi di autenticazione tramite app per dispositivi mobili o SMS, le stringhe definite in Impostazioni società contengono un segnaposto <$*application\_name*$>. Questo segnaposto non viene sostituito automaticamente quando si usa l'adapter AD FS. È consigliabile rimuovere il segnaposto dalle stringhe appropriate per la protezione di AD FS.

- L'account usato per accedere deve avere diritti utente per la creazione di gruppi di sicurezza nel servizio Active Directory.

- L'installazione guidata dell'adapter AD FS di Multi-Factor Authentication crea un gruppo di sicurezza denominato PhoneFactor Admins nell'istanza di Active Directory e quindi aggiunge l'account del servizio AD FS del servizio federativo a questo gruppo. È consigliabile verificare nel controller di dominio che il gruppo PhoneFactor Admins sia stato effettivamente creato e che l'account del servizio AD FS sia un membro di questo gruppo. Se necessario, aggiungere l'account del servizio AD FS manualmente al gruppo PhoneFactor Admins nel controller di dominio.
- Per informazioni sull'installazione dell'SDK del servizio Web con il portale utenti, vedere [Distribuzione del portale utenti per il server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-portal.md).


### Installare il server Azure Multi-Factor Authentication in locale nel server AD FS

1. Scaricare e installare il server Azure Multi-Factor Authentication nel server federativo AD FS. Per informazioni sull'installazione, vedere [Introduzione al server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
2. Nella console di gestione del server Azure Multi-Factor Authentication, fare clic sull'icona **AD FS** e quindi selezionare le opzioni **Consenti registrazione utente** e **Consenti agli utenti di selezionare il metodo**.
3. Selezionare eventuali opzioni aggiuntive per l'organizzazione.
4. Fare clic su **Installa scheda ADFS**.
<center>![Cloud](. / media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se il computer è unito a un dominio e la configurazione di Active Directory per proteggere la comunicazione tra l'adapter AD FS e il servizio Multi-Factor Authentication è incompleta, verrà visualizzata la finestra **Active Directory**. Fare clic su **Avanti** per completare la configurazione automaticamente oppure selezionare la casella di controllo **Ignora la configurazione automatica di Active Directory e configura le impostazioni manualmente** e quindi fare clic su **Avanti**.
6. Se il computer non è unito a un dominio e la configurazione del gruppo locale per proteggere la comunicazione tra l'adapter AD FS e il servizio Multi-Factor Authentication è incompleta, verrà visualizzata la finestra **Gruppo locale**. Fare clic su **Avanti** per completare la configurazione automaticamente oppure selezionare la casella di controllo **Ignora la configurazione automatica di Gruppo locale e configura le impostazioni manualmente** e quindi fare clic su **Avanti**.
7. Nell'installazione guidata fare clic su **Avanti**. Il server Azure Multi-Factor Authentication creerà il gruppo PhoneFactor Admins e aggiungerà l'account del servizio AD FS al gruppo PhoneFactor Admins.
<center>![Cloud](. / media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Nella pagina **Avvia programma di installazione** fare clic su **Avanti**.
9. Nel programma di installazione dell'adapter AD FS di Multi-Factor Authentication fare clic su **Avanti**.
10. Al termine dell'installazione, fare clic su **Chiudi**.
11. Dopo aver installato l'adapter è necessario registrarlo con AD FS. Aprire Windows PowerShell ed eseguire il comando seguente:<br>`C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](. / media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Modificare i criteri di autenticazione globali in AD FS per usare l'adapter appena registrato. Nella console di gestione di AD FS, passare al nodo **Criteri di autenticazione**. Nella sezione **Autenticazione a più fattori** fare clic sul collegamento **Modifica** accanto alla sezione **Impostazioni globali**. Nella finestra di dialogo **Modifica criteri di autenticazione globali** selezionare **Autenticazione a più fattori** come metodo di autenticazione aggiuntivo e quindi fare clic su **OK**. L'adapter viene registrata come WindowsAzureMultiFactorAuthentication. È necessario riavviare il servizio ADFS rendere effettiva la registrazione.

<center>![Cloud](. / media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

A questo punto, il server Multi-Factor Authentication è configurato per essere un provider di autenticazione aggiuntivo per l'uso con AD FS.

## Installare un'istanza autonoma dell'adapter AD FS usando l'SDK del servizio Web
1. Installare l'SDK del servizio Web nel server che esegue il server Multi-Factor Authentication.
2. Copiare i file seguenti dalla directory \\Programmi\\Multi-Factor Authentication Server al server in cui si prevede di installare l'adapter AD FS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Eseguire il file di installazione MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Nel programma di installazione dell'adapter AD FS di Multi-Factor Authentication, fare clic su **Avanti** per eseguire l'installazione.
5. Al termine dell'installazione, fare clic su **Chiudi**.
6. Modificare il file Multifactorauthenticationadfsadapter effettuando le operazioni seguenti:

|Passaggio Multifactorauthenticationadfsadapter| Passaggio secondario|
|:------------- | :------------- |
|Impostare il nodo **UseWebServiceSdk** su **true**.||
|Impostare il valore di **WebServiceSdkUrl** sull'URL dell'SDK del servizio Web Multi-Factor Authentication.||
|Configurare l'SDK del servizio Web.<br><br>*Opzione 1*: con un nome utente e una password|<ol type="a"><li>Impostare il valore di **WebServiceSdkUsername** su un account membro del gruppo di sicurezza PhoneFactor Admins. Usare il formato &lt;dominio&gt;&#92;&lt;nomeutente&gt;.<li>Impostare il valore di **WebServiceSdkPassword** sulla password dell'account.</li></ol>
|Configurare l'SDK del servizio Web, *continua*<br><br>*Opzione 2*: con un certificato client|<ol type="a"><li>Ottenere un certificato client da un'autorità di certificazione per il server che esegue l'SDK del servizio Web. Informazioni su come [ottenere certificati client](https://technet.microsoft.com/library/cc770328.aspx).</li><li>Importare il certificato client nell'archivio certificati personali del computer locale nel server che esegue l'SDK del servizio Web. Nota: verificare che il certificato pubblico dell'autorità di certificazione si trovi nell'archivio dei certificati radice trusted.</li><li>Esportare le chiavi pubbliche e private del certificato client in un file PFX.</li><li>Esportare la chiave pubblica con codifica Base64 in un file CER.</li><li>In Server Manager verificare che sia installata la funzionalità Autenticazione mapping certificati client IIS in Server Web (IIS)\\Server Web\\Sicurezza. Se non è installata, selezionare **Aggiungi ruoli e funzionalità** per aggiungerla.</li><li>In Gestione IIS fare doppio clic sull'**editor di configurazione** nel sito Web che contiene la directory virtuale dell'SDK del servizio Web. Nota: è molto importante eseguire questa operazione a livello di sito Web e non a livello di directory virtuale.</li><li>Passare alla sezione **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.</li><li>Impostare **enabled** su **true**.</li><li>Impostare **oneToOneCertificateMappingsEnabled** su **true**.</li><li>Fare clic sul pulsante **...** accanto a **oneToOneMappings** e quindi fare clic sul collegamento **Aggiungi**.</li><li>Aprire il file CER Base64 esportato in precedenza. Rimuovere *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* e tutte le interruzioni di riga. Copiare la stringa risultante.</li><li>Impostare il **certificato** sulla stringa copiata nel passaggio precedente.</li><li>Impostare **enabled** su **true**.</li><li>Impostare **userName** su un account membro del gruppo di sicurezza PhoneFactor Admins. Usare il formato &lt;dominio&gt;&#92;&lt;nomeutente&gt;</li><li>Impostare la password sulla password dell'account e quindi chiudere l'editor di configurazione.</li><li>Fare clic sul collegamento **Applica**.</li><li>Nella directory virtuale dell'SKD del servizio Web fare doppio clic su **Autenticazione**.</li><li>Verificare che **Rappresentazione ASP.NET** e **Autenticazione di base** siano impostate su **Abilitato** e tutti gli altri elementi su **Disabilitato**.</li><li>Nella directory virtuale dell'SDK del servizio Web fare doppio clic su **Impostazioni SSL**.</li>Impostare <li>**Certificati client** su **Accetta** e quindi fare clic su **Applica**.</li><li>Copiare il file PFX esportato in precedenza per il server che esegue l'adapter AD FS.</li><li>Importare il file PFX nell'archivio di certificati personali del computer locale.</li><li>Fare clic con il pulsante destro del mouse, scegliere **Gestisci chiavi private** e concedere l'accesso in lettura all'account usato per accedere al servizio AD FS.</li><li>Aprire il certificato client e copiare l'identificazione personale dalla scheda **Dettagli**.</li><li>Nel file MultiFactorAuthenticationAdfsAdapter.config impostare **WebServiceSdkCertificateThumbprint** sulla stringa copiata nel passaggio precedente.</li></ol>
| Modificare lo script MultiFactorAuthenticationAdfsAdapter.ps1 aggiungendo *-ConfigurationFilePath &lt;percorso&gt;* alla fine del comando `Register-AdfsAuthenticationProvider`, dove *&lt;percorso&gt;* è il percorso completo del file MultiFactorAuthenticationAdfsAdapter.config.||

Per registrare l'adapter, eseguire lo script Register-MultiFactorAuthenticationAdfsAdapter.ps1 in \\Programmi\\Multi-Factor Authentication Server\\ in PowerShell. L'adapter viene registrata come WindowsAzureMultiFactorAuthentication. È necessario riavviare il servizio ADFS rendere effettiva la registrazione.

<!---HONumber=AcomDC_0810_2016-->