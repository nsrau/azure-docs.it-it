<properties
   pageTitle="Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux | Microsoft Azure"
   description="Il documento fornisce una panoramica di Microsoft Azure Disk Encryption per le VM IaaS Windows e Linux."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="devtiw"/>


#Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux

Microsoft Azure è caratterizzato dal massimo impegno volto ad assicurare la privacy e la sovranità dei dati e a consentire il controllo dei dati ospitati in Azure con una gamma di tecnologie avanzate per crittografare, controllare e gestire le chiavi di crittografia e controllare e verificare l'accesso ai dati. I clienti di Azure hanno quindi la possibilità di scegliere la soluzione che meglio soddisfa le proprie esigenze aziendali. In questo documento viene introdotta una nuova soluzione tecnologica, "Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux", che facilita la protezione e la salvaguardia dei dati per rispettare gli impegni in termini di sicurezza e conformità dell'organizzazione. Il documento include informazioni dettagliate sull'uso delle funzionalità di crittografia del disco di Azure, compresi gli scenari supportati e le esperienze utente.

**NOTA**: alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione.

## Panoramica

Azure Disk Encryption è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Azure Disk Encryption sfrutta la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e il sistema operativo. La soluzione è integrata con l'[insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/services/key-vault/) per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi sui dischi delle macchine virtuali siano crittografati nell'archiviazione di Azure.

Crittografia dischi di Azure per le VM IaaS Windows è ora [disponibile a livello generale](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/15/azure-disk-encryption-for-windows-virtual-machines-reaches-general-availability/) nell'area Australia. La disponibilità a livello generale per le VM IaaS di Windows per altre aree sarà comunicata a breve.

### Scenari di crittografia

La soluzione Crittografia dischi di Azure supporta i tre scenari dei clienti descritti di seguito:

- Abilitare la crittografia delle nuove VM IaaS create da chiavi di crittografia e VHD pre-crittografati.
- Abilitare la crittografia di nuove VM IaaS create da immagini della Raccolta di Azure.
- Abilitare la crittografia delle VM IaaS esistenti già in esecuzione in Azure.
- Disabilitare la crittografia nelle VM IaaS Windows

Se abilitata in Microsoft Azure, la soluzione supporta le funzionalità seguenti per le VM IaaS:

- Integrazione dell'insieme di credenziali delle chiavi di Azure.

- [VM IaaS serie A, D e G](https://azure.microsoft.com/pricing/details/virtual-machines/) Standard.

- Abilitare la crittografia nelle VM IaaS Windows e Linux

- Disabilitare la crittografia nelle VM IaaS Windows

- Abilitare la crittografia in VM IaaS eseguite nel sistema operativo client Windows

- Abilitare la crittografia su volumi con percorsi di montaggio

- Tutte le aree pubbliche di Azure sono supportate nell'anteprima pubblica e l'area Australia di Azure è supportata nella versione disponibile a livello generale per le VM IaaS Windows

La soluzione non supporta gli scenari, le funzionalità e la tecnologia seguenti in questa versione:

- VM Basic e VM IaaS serie DS Standard (archiviazione Premium).

- VM IaaS create con il metodo classico di creazione delle VM.

- Abilitare la crittografia del disco del sistema operativo nelle VM IaaS Linux già in esecuzione in Azure

- Disabilitare la crittografia nelle VM IaaS Linux abilitate tramite Crittografia dischi di Azure

- Integrazione con il servizio di gestione delle chiavi locale.

- Windows Server 2016 Technical Preview 3 e successive

- File di Azure (condivisione file di Azure), file system di rete (NFS), volumi dinamici, sistemi RAID basati su software.


### Funzionalità di crittografia

Quando si abilita e si distribuisce la crittografia del disco di Azure per le VM IaaS di Azure, sono abilitate le funzionalità seguenti, a seconda della configurazione fornita:

- Crittografia del volume del sistema operativo per proteggere il volume di avvio inattivo nell'archiviazione del cliente.

	- La crittografia del volume del sistema operativo nella VM IaaS Linux già in esecuzione in Azure non è attualmente supportata. La crittografia del volume del sistema operativo per la VM IaaS Linux è supportata solo per uno scenario VHD pre-crittografato.

- Crittografia del volume o dei volumi dati per proteggere i volumi dati inattivi nell'archiviazione del cliente.

- Disabilitare la crittografia nelle VM IaaS Windows

- Salvaguardia delle chiavi di crittografia e dei segreti nella sottoscrizione dell'insieme di credenziali delle chiavi di Azure.

- Segnalazione dello stato di crittografia della VM IaaS crittografata.

- Rimozione delle impostazioni di configurazione della crittografia del disco dalla macchina virtuale IaaS.

La soluzione Azure Disk Encryption per le VM IaaS per Windows e Linux include l'estensione di crittografia del disco per Windows, l'estensione di crittografia del disco per Linux, i cmdlet di crittografia del disco di PowerShell, i cmdlet di crittografia del disco per l'interfaccia della riga di comando e i modelli di crittografia del disco di Gestione risorse di Azure. La soluzione Azure Disk Encryption è supportata nelle VM IaaS che eseguono il sistema operativo Windows o Linux. Per altri dettagli sui sistemi operativi supportati, vedere la sezione relativa ai prerequisiti più avanti.

**Nota:** non è previsto alcun addebito per la crittografia dei dischi delle macchine virtuali con Crittografia dischi di Azure.

### Proposta di valore

La soluzione di gestione di Azure Disk Encryption consente di soddisfare le esigenze aziendali seguenti nel cloud:

-   Le VM IaaS inattive sono protette con la tecnologia di crittografia standard, per soddisfare i requisiti di sicurezza e conformità dell'organizzazione.

-   Le VM IaaS vengono avviate con chiavi e criteri controllati dal cliente e il relativo utilizzo può essere controllato nell'insieme di credenziali delle chiavi.


### Flusso di lavoro della crittografia
Ecco la procedura generale richiesta per abilitare la crittografia del disco per le VM Windows e Linux:

1. Il cliente sceglie lo scenario di crittografia fra i tre precedenti.

2. Il cliente sceglie di abilitare la crittografia del disco con il modello ARM di crittografia del disco di Azure o i cmdlet PS o l'interfaccia della riga di comando e specifica la configurazione della crittografia.

    - Per lo scenario con VHD crittografato, il cliente carica il VHD crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi e infine fornisce la configurazione della crittografia per abilitarla nella nuova VM IaaS.

    - Per la nuova VM creata dalla raccolta di Azure e per le VM esistenti già in esecuzione in Azure, fornisce la configurazione della crittografia per abilitarla nella VM IaaS.

3. Il cliente concede l'accesso alla piattaforma Azure per la lettura del materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per i sistemi Windows e Passphrase per Linux, dall'insieme di credenziali delle chiavi locale per abilitare la crittografia nella VM IaaS.

4. Il cliente fornisce l'identità di Azure AD per la scrittura del materiale della chiave di crittografia nell'insieme di credenziali delle chiavi locale per abilitare la crittografia nella VM IaaS per gli scenari 2 e 3 precedenti.

5.  La funzionalità di gestione dei servizi di Azure aggiorna il modello di servizi della VM con la configurazione della crittografia e dell'insieme di credenziali delle chiavi ed effettua il provisioning automatico della VM crittografata.

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.JPG)

### Flusso di lavoro della decrittografia

Ecco la procedura generale richiesta per disabilitare la crittografia del disco per le VM Iaas Windows:

1. Il cliente sceglie di disabilitare la crittografia (decrittografia) in una VM IaaS Windows in esecuzione in Azure tramite il modello Crittografia dischi di Azure di Azure Resource Manager o i cmdlet di PowerShell e specifica la configurazione della decrittografia.

2. Il passaggio per disabilitare la crittografia è supportato solo in VM IaaS Windows, ma non nella VM IaaS Linux.

3. Il passaggio per disabilitare la crittografia agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione.

4. Azure Service Management aggiorna il modello di servizio della VM e la VM IaaS Windows viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato.

5. La disabilitazione della crittografia non elimina l'insieme di credenziali delle chiavi del cliente e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per Windows e la passphrase per Linux.

## Prerequisiti

Di seguito sono elencati i prerequisiti per abilitare Azure Disk Encryption nelle VM IaaS di Azure per gli scenari supportati indicati nella sezione Panoramica.

- Per creare le risorse in Azure nella aree geografiche supportate, l'utente deve avere una sottoscrizione di Azure attiva e valida.

- Azure Disk Encryption è supportata negli SKU di Windows Server seguenti: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. La soluzione non è supportata nel sistema operativo Windows Server 2008. Windows Server 2016 Technical Preview 3 non è supportato in questa versione.

Crittografia dischi di Azure è supportato negli SKU client di Windows seguenti: client Windows 8 e client Windows 10.

**Nota**: per Windows Server 2008 R2 è NECESSARIO installare .NET Framework 4.5 prima di abilitare la crittografia in Azure. È possibile installarlo da Windows Update tramite l'aggiornamento facoltativo "Microsoft .NET Framework 4.5.2 per i sistemi Windows Server 2008 R2 basati su x64 ([KB2901983](https://support.microsoft.com/kb/2901983))".

- Crittografia dischi di Azure è supportato negli SKU dei server Linux seguenti: Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.

- Tutte le risorse, (ad esempio, l’insieme di credenziali delle chiavi, l’account di archiviazione, la VM e così via), devono appartenere alla stessa area e alla stessa sottoscrizione di Azure.

**Nota** -Azure disk encryption richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area di Azure. Configurarli in un'area separata provocherà un errore nell'attivazione ella funzionalità di Azure disk encryption.

- Per impostare e configurare l'nsieme di credenziali delle chiavi di Azure per l'utilizzo di Crittografia dischi di Azure, vedere la sezione **Impostazione e configurazione di Insieme di credenziali delle chiavi di Azure per l'utilizzo di Crittografia dischi di Azure** nella sezione *Prerequisiti* di questo articolo.

- Per impostare e configurare l'applicazione Azure AD in Azure Active Directory per l'utilizzo di Crittografia dischi di Azure, vedere la sezione **Installare l'applicazione Azure AD in Azure Active Directory** nella sezione *Prerequisiti* di questo articolo.

- Per impostare e configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione Azure AD, vedere la sezione **Impostazione dei criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione Azure AD** nella sezione *Prerequisiti* di questo articolo.

- Per preparare -crittografato, vedere la sezione **Preparazione di un VHD Windows pre-crittografato** nell'appendice di questo articolo.

- Per preparare un disco rigido virtuale Linux pre-crittografato, vedere la sezione **Preparazione di un VHD Linux pre-crittografato** nell'appendice di questo articolo.

- La piattaforma Azure deve avere accesso ai segreti o alle chiavi di crittografia nell'insieme di credenziali delle chiavi di Azure del cliente per renderli disponibili alla VM perché possa avviare e decrittografare il volume del sistema operativo della macchina virtuale. Per concedere alla piattaforma Azure le autorizzazioni di accesso all'insieme di credenziali delle chiavi, è necessario impostare la proprietà **enabledForDiskEncryption** nell'insieme di credenziali delle chiavi per questo requisito. Per altre informazioni, fare riferimento alla sezione **Impostazione e configurazione dell'insieme di credenziali delle chiavi di Azure per l'utilizzo di Azure disk encryption** nell'appendice di questo articolo.

- Agli URL KEK (Key Encryption Key) e del segreto dell'insieme di credenziali delle chiavi deve essere applicato il controllo delle versioni. La funzionalità di gestione dei servizi di Azure applica questa restrizione relativa al controllo delle versioni. Ecco alcuni esempi di URL KEK e del segreto validi:

	- Esempio di URL del segreto valido:

		*https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Esempio di URL KEK valido:

		*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Azure Disk Encryption non supporta l'indicazione dei numeri di porta come parte degli URL KEK e del segreto dell'insieme di credenziali delle chiavi. Ecco alcuni esempi di URL dell'insieme di credenziali delle chiavi supportati:

 	- URL dell'insieme di credenziali delle chiavi non accettato:

		*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- URL dell'insieme di credenziali delle chiavi accettato:

		*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Per abilitare la funzionalità Azure Disk Encryption, le VM IaaS devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:

	- La VM IaaS deve potersi connettere all'endpoint di Azure Active Directory [Login.windows.net] per ottenere un token per la connessione all'insieme di credenziali delle chiavi di Azure.

	- La VM IaaS deve potersi connettere all'endpoint dell'insieme di credenziali delle chiavi di Azure per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi del cliente.

	- La VM IaaS deve potersi connettere all'endpoint dell'archiviazione di Azure che ospita l'archivio delle estensioni di Azure e all'account di archiviazione di Azure che ospita i file VHD.

**Nota:** se i criteri di protezione limitano l'accesso da macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente con il quale è necessaria la connettività e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP.

- Usare la versione più recente della versione di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell versione 1.3.0](https://github.com/Azure/azure-powershell/releases/download/v1.3.0-March2016/azure-powershell.1.3.0.msi) e successive

**Nota:** Crittografia dischi di Azure non è supportato in [Azure PowerShell SDK versione 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Se viene visualizzato un errore relativo all'uso di Azure PowerShell 1.1.0, vedere l'articolo [Azure Disk Encryption Error Related to Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx) (Errore di Crittografia dischi di Azure correlato a Azure PowerShell 1.1.0).

- Per eseguire uno qualsiasi dei comandi dell'interfaccia della riga di comando di Azure e associarla alla sottoscrizione di Azure, è necessario installare prima di tutto la versione dell'interfaccia della riga di comando di Azure:

	- Per installare l'interfaccia della riga di comando di Azure e associarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli-install.md).

	- Per l'uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure, vedere [qui](azure-cli-arm-commands.md).

- La soluzione Azure Disk Encryption usa la protezione con chiave esterna BitLocker per le VM IaaS Windows. Se le VM sono aggiunte a un dominio, non effettuare il push dei criteri di gruppo che applicano protezioni TPM. Per informazioni dettagliate sui criteri di gruppo per "consentire l'uso di BitLocker senza un TPM compatibile", vedere [questo articolo](https://technet.microsoft.com/library/ee706521).

- Lo script di PowerShell richiesto da Crittografia dischi di Azure per creare un'applicazione Azure AD, creare un nuovo insieme di credenziali delle chiavi o configurare l'insieme di credenziali delle chiavi esistente e abilitare la crittografia è disponibile [qui](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

### Impostazione e configurazione dell’insieme di credenziali delle chiavi di Azure per l'utilizzo con Azure Disk Encryption

Azure Disk Encryption protegge i segreti e le chiavi di crittografia del disco nell'insieme di credenziali delle chiavi di Azure. Seguire procedura in ognuna delle sezioni seguenti per impostare l'insieme di credenziali delle chiavi per l'utilizzo di Azure Disk Encryption.

#### Creare un nuovo insieme di credenziali delle chiavi
Per creare un nuovo insieme di credenziali delle chiavi, usare una delle opzioni elencate di seguito:

- Usare il modello ARM "101-Create-KeyVault" disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Usare i cmdlet dell'insieme di credenziali delle chiavi di Azure PowerShell.

**Nota:** se per la sottoscrizione è già configurato un insieme di credenziali delle chiavi, passare alla sezione successiva.

#### Provisioning di una chiave di crittografia della chiave (facoltativo)

Se si vuole usare una chiave di crittografia della chiave (KEK) per avere un livello di sicurezza aggiuntivo per eseguire il wrapping delle chiavi di crittografia BitLocker, aggiungere una KEK all'insieme di credenziali delle chiavi da usare nel processo di provisioning. Usare il cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) per creare una nuova chiave di crittografia delle chiavi nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere la [documentazione relativa all'insieme di credenziali delle chiavi](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

#### Impostare le autorizzazioni dell'insieme di credenziali delle chiavi per consentire alla piattaforma Azure di accedere a chiavi e segreti

La piattaforma Azure deve avere accesso ai segreti o alle chiavi di crittografia nell'insieme di credenziali delle chiavi di Azure per renderli disponibili alla VM perché possa avviare e decrittografare i volumi. Per concedere alla piattaforma Azure le autorizzazioni di accesso all'insieme di credenziali delle chiavi, è necessario impostare la proprietà *enabledForDiskEncryption* nell'insieme di credenziali delle chiavi. È possibile impostare la proprietà enabledForDiskEncryption nell'insieme di credenziali delle chiavi usando il cmdlet PS corrispondente:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

È necessario impostare la proprietà *enabledForDiskEncryption* nell'insieme di credenziali delle chiavi come accennato in precedenza. È possibile impostare la proprietà visitando la pagina https://resources.azure.com. Assicurarsi che le proprietà descritte sopra siano impostate correttamente, altrimenti la distribuzione non riuscirà.

#### Installare l'applicazione Azure AD in Azure Active Directory

Quando è necessario abilitare la crittografia in una VM in esecuzione in Azure, Azure Disk Encryption genera e scrive le chiavi di crittografia nell'insieme di credenziali delle chiavi. La gestione delle chiavi di crittografia nell'insieme di credenziali delle chiavi richiede l'autenticazione di Azure AD.

Per questo motivo, è necessario creare un'applicazione Azure AD. La procedura dettagliata per la registrazione di un'applicazione è disponibile nella sezione che descrive "come ottenere un'identità per l'applicazione" in questo [post di blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Questo post include anche diversi esempi utili sul provisioning e sulla configurazione dell'insieme di credenziali delle chiavi. Ai fini dell'autenticazione, è possibile usare l'autenticazione basata su segreto client o l'autenticazione di Azure AD basata su certificato client.

##### Autenticazione basata su segreto client per Azure AD

Le sezioni seguenti includono le procedure necessarie per configurare l'autenticazione basata su segreto client per Azure AD.

##### Creare una nuova app Azure AD con Azure PowerShell

Usare il cmdlet di PowerShell seguente per creare una nuova app Azure AD:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Nota:** $azureAdApplication.ApplicationId è l'ID client di Azure AD e $aadClientSecret è il segreto client che verrà usato in seguito per abilitare ADE. Il segreto client di Azure AD dovrà essere correttamente protetto.


##### Provisioning del segreto e dell'ID client di Azure AD dal portale di gestione dei servizi di Azure

È anche possibile effettuare il provisioning del segreto e dell'ID client di Azure AD tramite il portale di gestione dei servizi di Azure all'indirizzo https://manage.windowsazure.com. Per eseguire questa attività, usare questa procedura:

1\.Fare clic sulla scheda Active Directory illustrata nella figura seguente:

![Azure Disk Encryption](./media/azure-security-disk-encryption\disk-encryption-fig3.JPG)

2\.Fare clic su Aggiungi applicazione e digitare il nome dell'applicazione come illustrato di seguito:

![Azure Disk Encryption](./media/azure-security-disk-encryption\disk-encryption-fig4.JPG)

3\.Fare clic sul pulsante con la freccia e configurare le proprietà dell'app come illustrato di seguito:

![Azure Disk Encryption](./media/azure-security-disk-encryption\disk-encryption-fig5.JPG)

4\.Fare clic sul segno di spunta nell'angolo in basso a sinistra per completare l'operazione. Verrà visualizzata la pagina di configurazione dell'app. Notare che l'ID client di Azure AD si trova in basso nella pagina, come illustrato nella figura seguente.

![Azure Disk Encryption](./media/azure-security-disk-encryption\disk-encryption-fig6.JPG)

5\.Per salvare il segreto client di Azure AD usare il pulsante Salva. Fare clic sul pulsante Salva e prendere nota del segreto indicato nella casella di testo delle chiavi. Questo è il segreto client di Azure AD. Il segreto client di Azure AD dovrà essere correttamente protetto.

![Azure Disk Encryption](./media/azure-security-disk-encryption\disk-encryption-fig7.JPG)


**Nota:** il flusso di lavoro precedente non è supportato nel portale.

##### Usare un'app esistente

Per eseguire i comandi seguenti, è necessario il modulo Azure AD PowerShell, che è possibile ottenere [qui](https://technet.microsoft.com/library/jj151815.aspx).

**Nota:** i comandi seguenti devono essere eseguiti da una nuova finestra di PowerShell. NON usare Azure PowerShell o la finestra di Gestione risorse di Azure per eseguire questi comandi. Il motivo di questa indicazione è dovuto al fatto che questi cmdlet si trovano nel modulo MSOnline in Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Autenticazione basata su certificato per Azure AD

Le sezioni seguenti includono le procedure necessarie per configurare l'autenticazione basata su certificato per Azure AD.

##### Creare una nuova app Azure AD

Eseguire i cmdlet di PowerShell seguenti per creare una nuova app Azure AD:

**Nota:** sostituire la stringa “yourpassword” indicata di seguito con la password sicura e proteggere la password.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Dopo avere completato questo passaggio, caricare un file PFX nell'insieme di credenziali delle chiavi e abilitare i criteri di accesso necessari per distribuire il certificato a una VM.

##### Usare un'app Azure AD esistente
Se si configura l'autenticazione basata su certificato per un'app esistente, usare i cmdlet di PowerShell seguenti. Assicurarsi di eseguirli in una nuova finestra di PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Dopo avere completato questo passaggio, caricare un file PFX nell'insieme di credenziali delle chiavi e abilitare i criteri di accesso necessari per distribuire il certificato a una VM.

##### Caricare un file PFX nell'insieme di credenziali delle chiavi
Per una spiegazione dettagliata del funzionamento di questo processo, è possibile leggere questo [post di blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). I cmdlet di PowerShell seguenti sono comunque tutto ciò che serve per questa attività. Accertarsi di eseguirli dalla console di Azure PowerShell:

**Nota:** sostituire la stringa “yourpassword” indicata di seguito con la password sicura e proteggere la password.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### Distribuire un certificato nell'insieme di credenziali delle chiavi in una VM esistente
Dopo avere caricato il file PFX, seguire questa procedura per distribuire un certificato nell'insieme di credenziali delle chiavi in una VM esistente:

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName


#### Impostazione dei criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione Azure AD

L'applicazione Azure AD deve avere i diritti di accesso alle chiavi o ai segreti nell'insieme di credenziali. Usare il cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) per concedere le autorizzazioni all'applicazione con l'ID client, generato al momento della registrazione dell'applicazione, come valore del parametro ServicePrincipalName. Per alcuni esempi, è possibile leggere [questo post di blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Di seguito è disponibile anche un esempio dell'esecuzione di questa attività tramite PowerShell:

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

## Terminologia

Usare la tabella della terminologia come riferimento per comprendere alcuni dei termini comuni usati da questa tecnologia:


| Terminologia | Definizione |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD | Azure AD è l'abbreviazione di [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). L'account Azure AD è un prerequisito per le operazioni di autenticazione, archiviazione e recupero dei segreti dall'insieme di credenziali delle chiavi. |
| Insieme di credenziali delle chiavi di Azure | L'insieme di credenziali delle chiavi di Azure è un servizio di gestione delle chiavi crittografiche basato su moduli di protezione hardware convalidati in base a FIPS per proteggere le chiavi crittografiche e i segreti sensibili in modo sicuro. Per altri dettagli, vedere la documentazione dell'[Insieme di credenziali delle chiavi](https://azure.microsoft.com/services/key-vault/). |
| ARM | Gestione risorse di Azure |
| BitLocker | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta nel settore, usata per abilitare la crittografia del disco nelle VM IaaS Windows. |
| BEK | Le chiavi di crittografia BitLocker vengono usate per crittografare il volume di avvio del sistema operativo e i volumi dati. Le chiavi BitLocker sono protette nell'insieme di credenziali delle chiavi di Azure del cliente come segreti. |
| CLI | [Interfaccia della riga di comando di Azure](xplat-cli-install.md) |
| DM-Crypt | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) è il sottosistema di crittografia del disco trasparente basato su Linux usato per abilitare la crittografia del disco nelle VM IaaS Linux. |
| KEK | La chiave di crittografia della chiave (KEK, Key Encryption Key) è la chiave asimmetrica (RSA 2048) usata per proteggere o per eseguire il wrapping del segreto, se necessario. È possibile fornire una chiave protetta tramite HSM o una chiave protetta tramite software. Per altri dettagli, vedere la documentazione dell'[Insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/). |
| Cmdlet PS | [Cmdlet di Azure PowerShell](powershell-install-configure.md) |

## Scenari di distribuzione di Disk Encryption ed esperienze utente

La crittografia del disco può essere abilitata in molti scenari e la procedura può variare a seconda dello scenario. Le sezioni che seguono descrivono più dettagliatamente questi scenari.

### Abilitare la crittografia di nuove VM IaaS create dalla raccolta di Azure.

La crittografia del disco può essere abilitata in una nuova VM IaaS Windows dalla raccolta di Azure in Azure usando il modello ARM pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una nuova VM IaaS.

**Nota:** questo modello crea una nuova VM Windows crittografata usando l'immagine della raccolta di Windows Server 2012.

La tabella seguente include la descrizione dettagliata dei parametri del modello ARM per la nuova VM dello scenario relativo alla raccolta di Azure con l'ID client di Azure AD:

| Parametro | Descrizione|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | Nome utente dell'amministratore per la macchina virtuale. |
| adminPassword | Password utente dell'amministratore per la macchina virtuale. |
| newStorageAccountName | Nome dell'account di archiviazione per archiviare i VHD di dati e del sistema operativo. |
| vmSize | Dimensioni della macchina virtuale. Attualmente sono supportate solo le serie A, D e G Standard. |
| virtualNetworkName | Nome della rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| subnetName | Nome della subnet nella rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| AADClientID | ID client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultResourceID,ResourceID | Identifica la risorsa insieme di credenziali delle chiavi in ARM. È possibile ottenerla con il cmdlet di PowerShell: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId |
| keyVaultURL | URL dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo con il cmdlet: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. Facoltativo. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia.


**Nota:** KeyEncryptionKeyURL è un parametro opzionale. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per l'ulteriore protezione della chiave DEK (segreto passphrase) nell'insieme di credenziali delle chiavi.

### Abilitare la crittografia delle nuove VM IaaS create da chiavi di crittografia e VHD crittografati dei clienti.

In questo scenario è possibile abilitare la crittografia usando il modello ARM, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in dettaglio il modello ARM e i comandi dell'interfaccia della riga di comando.

#### Uso del modello ARM

La crittografia del disco può essere abilitata in un VHD crittografato del cliente usando il modello ARM pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia nella nuova VM IaaS.

La tabella seguente include la descrizione dettagliata dei parametri del modello ARM per lo scenario con VHD crittografati del cliente:

| Parametro | Descrizione|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Nome dell'account di archiviazione per archiviare il VHD del sistema operativo. È necessario che questo account di archiviazione sia già stato creato nello stesso gruppo di risorse e nello stesso percorso della VM. |
| osVhdUri | URI del VHD del sistema operativo dall'account di archiviazione. |
| osType | Tipo di prodotto del sistema operativo (Windows/Linux). |
| virtualNetworkName | Nome della rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. È necessario che sia già stata creata nello stesso gruppo di risorse e nello stesso percorso della VM. |
| subnetName | Nome della subnet nella rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| vmSize | Dimensioni della macchina virtuale. Attualmente sono supportate solo le serie A, D e G Standard. |
| keyVaultResourceID | ID della risorsa che identifica la risorsa insieme di credenziali delle chiavi in ARM. È possibile ottenerla con il cmdlet di PowerShell: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl | ​URL della chiave di crittografia del disco di cui è stato effettuato il provisioning nell'insieme di credenziali delle chiavi. |
| keyVaultKekUrl | URL della chiave di crittografia della chiave per crittografare la chiave di crittografia del disco generata. |
| ​vmName | ​Nome della VM IaaS.   



####Usare i cmdlet PowerShell

La crittografia del disco può essere abilitata in un VHD crittografato del cliente usando i cmdlet PS pubblicati [qui](https://msdn.microsoft.com/library/azure/mt603746.aspx).

####Uso dei comandi dell'interfaccia della riga di comando

Usare la procedura seguente per abilitare la crittografia del disco per questo scenario con i comandi dell'interfaccia della riga di comando:

1. Impostare i criteri di accesso nell'insieme di credenziali delle chiavi:
	- Impostare il flag "EnabledForDiskEncryption": "azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true"
	- Impostare le autorizzazioni dell'app Azure AD per la scrittura delle chiavi private nell'insieme di credenziali: "azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]"
2. Per abilitare la crittografia in una macchina virtuale esistente o in esecuzione, digitare: *azure vm enable-disk-encryption --resource-group <NomeGruppoRisorse> --name <NomeVM> --aad-client-id <IDClient AAD> --aad-client-secret <SegretoClientAAD> --disk-encryption-key-vault-url <URLInsiemeCredenzialiChiavi> --disk-encryption-key-vault-id <IDRisorsaInsiemeCredenzialiChiavi>*
3. Ottenere lo stato della crittografia: *"azure vm show-disk-encryption-status --resource-group <NomeGruppoRisorse> --name <NomeVM> --json"*
4. Per abilitare la crittografia in una nuova VM dal VHD crittografato del cliente, usare i parametri seguenti con il comando "azure vm create":
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Abilitare la crittografia in una VM IaaS Windows esistente o in esecuzione in Azure

In questo scenario è possibile abilitare la crittografia usando il modello ARM, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in dettaglio come abilitarla usando il modello ARM e i comandi dell'interfaccia della riga di comando.

#### Uso del modello ARM

La crittografia del disco può essere abilitata in una VM IaaS Windows esistente o in esecuzione in Azure usando il modello ARM pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una VM IaaS esistente o in esecuzione.

La tabella seguente include la descrizione dettagliata dei parametri del modello ARM per lo scenario con VM esistenti o in esecuzione usando l'ID client di Azure AD:

| Parametro | Descrizione|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​ID client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | ​Segreto client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo con il cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. È facoltativo se si seleziona "nokek" nell'elenco a discesa UseExistingKek. Se si seleziona "kek" nell'elenco a discesa UseExistingKek, è necessario inserire il valore keyEncryptionKeyURL |
| ​volumeType | ​Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono "OS", "Data", "All". |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia del disco nella stessa VM. |
| ​vmName | ​Nome della VM in cui deve essere eseguita l'operazione di crittografia.


**Nota:** KeyEncryptionKeyURL è un parametro opzionale. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per l'ulteriore protezione della chiave DEK (segreto di crittografia di BitLocker) nell'insieme di credenziali delle chiavi.

#### Usare i cmdlet PowerShell

Fare riferimento alle [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) del post di blog **Esplorare Azure disk encryption con Azure PowerShell** per informazioni dettagliate su come abilitare la crittografia mediante Azure disk encryption con i cmdlet di PS.

#### Uso dei comandi dell'interfaccia della riga di comando

Seguire questa procedura per abilitare la crittografia nella VM IaaS Windows esistente o in esecuzione in Azure tramite i comandi dell'interfaccia della riga di comando:

1. Impostare i criteri di accesso nell'insieme di credenziali delle chiavi:
	- Impostare il flag "EnabledForDiskEncryption": "azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true"
	- Impostare le autorizzazioni dell'app Azure AD per la scrittura delle chiavi private nell'insieme di credenziali: "azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]"
2. Per abilitare la crittografia in una macchina virtuale esistente o in esecuzione, digitare: *azure vm enable-disk-encryption --resource-group <NomeGruppoRisorse> --name <NomeVM> --aad-client-id <IDClient AAD> --aad-client-secret <SegretoClientAAD> --disk-encryption-key-vault-url <URLInsiemeCredenzialiChiavi> --disk-encryption-key-vault-id <IDRisorsaInsiemeCredenzialiChiavi>*
3. Ottenere lo stato della crittografia: *"azure vm show-disk-encryption-status --resource-group <NomeGruppoRisorse> --name <NomeVM> --json"*
4. Per abilitare la crittografia in una nuova VM dal VHD crittografato del cliente, usare i parametri seguenti con il comando "azure vm create":
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Abilitare la crittografia in una VM IaaS Linux esistente o in esecuzione in Azure

La crittografia del disco può essere abilitata in una VM IaaS Linux esistente o in esecuzione in Azure usando il modello ARM pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una VM IaaS esistente o in esecuzione.

La tabella seguente include la descrizione dettagliata dei parametri del modello ARM per lo scenario con VM esistenti o in esecuzione usando l'ID client di Azure AD:

| Parametro | Descrizione|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​ID client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | ​Segreto client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo con il cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. È facoltativo se si seleziona "nokek" nell'elenco a discesa UseExistingKek. Se si seleziona "kek" nell'elenco a discesa UseExistingKek, è necessario inserire il valore keyEncryptionKeyURL |
| ​volumeType | ​Tipo del volume in cui viene eseguita l'operazione di crittografia. Il valore valido supportato è "Data". La VM Linux non supporta l'abilitazione della crittografia del volume del sistema operativo nella VM Linux in esecuzione. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia del disco nella stessa VM. |
| ​vmName | ​Nome della VM in cui deve essere eseguita l'operazione di crittografia.
| passPhrase | Digitare una passphrase complessa come chiave DEK. |                                                                                                                                                                                                                                                      

**Nota:** KeyEncryptionKeyURL è un parametro opzionale. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per l'ulteriore protezione della chiave DEK (segreto passphrase) nell'insieme di credenziali delle chiavi.

#### Comandi dell'interfaccia della riga di comando

La crittografia del disco può essere abilitata in un VHD crittografato del cliente usando il comando dell'interfaccia della riga di comando installato da [qui](xplat-cli-install.md). Seguire questa procedura per abilitare la crittografia nella VM IaaS Linux esistente o in esecuzione in Azure tramite i comandi dell'interfaccia della riga di comando:

1. Impostare i criteri di accesso nell'insieme di credenziali delle chiavi:
	- Impostare il flag "EnabledForDiskEncryption": "azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true"
	- Impostare le autorizzazioni dell'app Azure AD per la scrittura delle chiavi private nell'insieme di credenziali: "azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]"
2. Per abilitare la crittografia in una macchina virtuale esistente o in esecuzione, digitare: *azure vm enable-disk-encryption --resource-group <NomeGruppoRisorse> --name <NomeVM> --aad-client-id <IDClient AAD> --aad-client-secret <SegretoClientAAD> --disk-encryption-key-vault-url <URLInsiemeCredenzialiChiavi> --disk-encryption-key-vault-id <IDRisorsaInsiemeCredenzialiChiavi>*
3. Ottenere lo stato della crittografia: “azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”
4. Per abilitare la crittografia in una nuova VM dal VHD crittografato del cliente, usare i parametri seguenti con il comando "azure vm create".
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

### Ottenere lo stato della crittografia di una VM IaaS crittografata

È possibile ottenere lo stato della crittografia usando il portale di gestione di Azure, i [cmdlet di PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti illustrano come usare il portale di Azure e i comandi dell'interfaccia della riga di comando per ottenere lo stato della crittografia.

#### Ottenere lo stato della crittografia di una VM IaaS crittografata usando il portale di gestione di Azure

È possibile ottenere lo stato della crittografia della VM IaaS dal portale di gestione di Azure. Accedere al portale di Azure all'indirizzo https://portal.azure.com/, fare clic sul collegamento Macchine virtuali nel menu a sinistra per passare alla visualizzazione di riepilogo della macchine virtuali disponibili nella sottoscrizione. È possibile filtrare la visualizzazione Macchine virtuali selezionando il nome della sottoscrizione dall'elenco a discesa Sottoscrizione. Fare clic sulle colonne che trovano in alto nel menu della pagina Macchine virtuali. Selezionare la colonna relativa alla crittografia del disco nel pannello per la scelta delle colonne e fare clic su Aggiorna. Verrà visualizzata la colonna relativa alla crittografia del disco con lo stato della crittografia "Abilitata" o "Non abilitata" per ogni VM, come illustrato nella figura seguente.

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.JPG)

#### Ottenere lo stato della crittografia di una VM IaaS crittografata usando il cmdlet PS per la crittografia del disco
È possibile ottenere lo stato della crittografia della VM IaaS dal cmdlet PS per la crittografia del disco "Get-AzureRmVMDiskEncryptionStatus". Per ottenere le impostazioni delle crittografia per la VM, nella sessione di Azure PowerShell digitare:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName <yourResourceGroupName> -VMName <yourVMName>

    OsVolumeEncrypted: True
    OsVolumeEncryptionSettings : {
      "DiskEncryptionKey": {
       SecretUrl":"https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "SourceVault": {
            "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxxx/providers/Mi                            crosoft.KeyVault/vaults/xxxxxxx"
                                }
                            },
                    "KeyEncryptionKey": null
                             }
    DataVolumesEncrypted: True

Il valore delle impostazioni OSVolumeEncrypted e DataVolumesEncrypted è "True" e mostra che entrambi i volumi sono crittografati tramite Azure Disk Encryption. Fare riferimento alle [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) del post di blog **Esplorare Azure disk encryption con Azure PowerShell** per informazioni dettagliate su come abilitare la crittografia mediante Azure disk encryption con i cmdlet di PS.

#### Ottenere lo stato della crittografia della VM IaaS usando il comando dell'interfaccia della riga di comando per la crittografia del disco

È possibile ottenere lo stato della crittografia della VM IaaS dal comando dell'interfaccia della riga di comando per la crittografia del disco *azure vm show-disk-encryption-status*. Per ottenere le impostazioni delle crittografia per la VM, nella sessione dell'interfaccia della riga di comando digitare:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### Disabilitare la crittografia nelle VM IaaS Windows in esecuzione

È possibile disabilitare la crittografia in una VM IaaS Windows in esecuzione tramite il modello Crittografia dischi di Azure di Azure Resource Manager o i cmdlet di PowerShell e specificare la configurazione della decrittografia. Il passaggio per disabilitare la crittografia è supportato solo in VM IaaS Windows, ma non nella VM IaaS Linux. Il passaggio per disabilitare la crittografia agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione. Non è possibile disabilitare il volume del sistema operativo e lasciare il volume dati crittografato. Dopo aver completato il passaggio per disabilitare la crittografia, Azure Service Management aggiorna il modello di servizio della VM e la VM IaaS Windows viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato. La disabilitazione della crittografia non elimina l'insieme di credenziali delle chiavi del cliente e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per Windows e la passphrase per Linux.

##### Disabilitare la crittografia in una VM IaaS Windows esistente o in esecuzione in Azure con il modello di Azure Resource Manager.

La crittografia del disco può essere disabilitata in una macchina virtuale IaaS Windows in esecuzione usando il modello di Azure Resource Manager pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Fare clic sul pulsante "Deploy to Azure" nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una nuova VM IaaS.

Dettagli dei parametri del modello di Azure Resource Manager per disabilitare la crittografia una VM IaaS Windows in esecuzione:

| ​vmName | ​Nome della VM in cui deve essere eseguita l'operazione di crittografia. |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​volumeType | ​Tipo del volume su cui viene eseguita l'operazione di crittografia. I valori validi sono "OS", "Data", "All".<br>**Nota**: non è possibile disabilitare la crittografia nel volume del sistema operativo o di avvio di una VM IaaS Windows in esecuzione, senza disabilitare la crittografia nel volume "Data". |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di decrittografia del disco nella stessa VM. |

##### Disabilitare la crittografia in una VM IaaS Windows esistente o in esecuzione in Azure con il cmdlet di PowerShell.

Con il cmdlet di PowerShell, [Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx), è possibile disabilitare la crittografia in una macchina virtuale di tipo infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service). Questo cmdlet è supportato solo nelle macchine virtuali Windows, ma non nelle macchine virtuali Linux. Il cmdlet installa un'estensione nella macchina virtuale per disabilitare la crittografia. Se il parametro Name viene omesso, viene creata un'estensione con il nome predefinito "AzureDiskEncryption for Windows VMs".

**Nota**: questo cmdlet riavvia la macchina virtuale.

## Appendice

### Eseguire la connessione alla sottoscrizione

Prima di procedere, verificare la sessione relativa ai requisiti di questo documento. Dopo essersi assicurati che tutti i requisiti sono stati soddisfatti, seguire questa procedura per connettersi alla sottoscrizione:

1\.Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

    Login-AzureRmAccount

2\.Se sono disponibili più sottoscrizioni e se ne vuole specificare una in particolare da usare, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:

    Get-AzureRmSubscription

3\.Per specificare la sottoscrizione che si vuole usare, digitare:

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\.Per verificare che la sottoscrizione configurata sia corretta, digitare:

    Get-AzureRmSubscription

5\.Per verificare che i cmdlet di Azure Disk Encryption siano installati, digitare:

    Get-command *diskencryption*

6\.Verrà visualizzato l'output seguente che conferma installazione di PowerShell per Azure Disk Encryption:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                               	 Version    Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus       1.1.0      AzureRM.Compute                                                    
    Cmdlet       Remove-AzureRmVMDiskEncryptionExtension 1.1.0      AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension    1.1.0      AzureRM.Compute                                                     

### Preparazione di un VHD Windows pre-crittografato
Le sezioni seguenti sono necessarie per preparare un VHD Windows pre-crittografato per la distribuzione come VHD crittografato in Azure IaaS. I passaggi vengono usati per preparare e avviare una nuova VM (VHD) Windows in Hyper-V o Azure.

#### Aggiornare i criteri di gruppo per consentire la protezione non TPM del sistema operativo
È necessario configurare l'impostazione di Criteri di gruppo BitLocker denominata Crittografia unità BitLocker, che si trova in Criteri del computer locale \\Configurazione computer\\Modelli amministrativi\\Componenti di Windows. Modificare questa impostazione in: *Unità del sistema operativo - Richiedi autenticazione aggiuntiva all'avvio - Consenti BitLocker senza un TPM compatibile* come illustrato nella figura seguente:

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.JPG)

#### Installare i componenti della funzionalità BitLocker
Per Windows Server 2012 e versioni successive usare il comando seguente:

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Per Windows Server 2008 R2 usare il comando seguente:

    ServerManagerCmd -install BitLockers

#### Preparare il volume del sistema operativo per BitLocker tramite bdehdcfg

Eseguire il comando seguente per comprimere la partizione del sistema operativo e preparare il computer per BitLocker.

    bdehdcfg -target c: shrink -quiet

#### Uso di BitLocker per proteggere il volume del sistema operativo
Usare il comando [manage-bde](https://technet.microsoft.com/library/ff829849.aspx) per abilitare la crittografia nel volume di avvio usando una protezione con chiave esterna e posizionare la chiave esterna (file BEK) nel volume o nell'unità esterna. La crittografia sarà abilitata nel volume di sistema/di avvio al riavvio successivo.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Nota:** la VM deve essere preparata con un VHD di dati o di risorse separato per ottenere la chiave esterna tramite BitLocker.

#### Preparazione di un VHD Linux pre-crittografato

##### Ubuntu 14.

1\.Creare un file in /usr/local/sbin/azure\_crypt\_key.sh con il contenuto dello script seguente. Prestare attenzione a KeyFileName, perché è il nome file della passphrase inserito da Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    sleep 2
    OPENED=0
    for SFS in /sys/block/sd*; do
        DEV=`basename $SFS`
        F=$SFS/${DEV}1/dev
        echo "> Trying device: $DEV ..." >&2
        mount /dev/${DEV}1 $MountPoint -t vfat -r >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\.Modificare la configurazione di crittografia in */etc/crypttab*. L'aspetto dovrebbe risultare simile al seguente:

    Sda5_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\.Se si modifica *azure\_crypt\_key.sh* in Windows e si copia il valore in Linux, non dimenticare di eseguire *dos2unix /usr/local/sbin/azure\_crypt\_key.sh*. 4.Eseguire *update-initramfs -u -k all* per aggiornare initramfs e rendere effettivo il keyscript.

##### openSUSE 13.2.

1\.Modificare /etc/dracut.conf add\_drivers+="vfat nls\_cp437 nls\_iso8859-1"

2\.Impostare come commento queste righe verso la fine del file "/usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #    inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator


3\.Aggiungere DRACUT\_SYSTEMD=0 all'inizio del file "/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh" e modificare tutto "if [ -z "$DRACUT\_SYSTEMD" ]; then" in "if [ 1 ]; then"

4\.Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere questo codice dopo "# Open LUKS device"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
       echo "> Trying device:$SFS..." >&2
       mount ${SFS}1 $MountPoint -t vfat -r >&2
       if [ -f $MountPoint/$KeyFileName ]; then
          echo "> keyfile got..." >&2
          luksfile=$MountPoint/$KeyFileName
          break
       fi
    done

5\.Eseguire "dracut –f -v" per aggiornare initrd

##### CentOS 7
1\.Modificare /etc/dracut.conf add\_drivers+=" vfat nls\_cp437 nls\_iso8859-1"

2\.Impostare come commento queste righe verso la fine del file "/usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\.Aggiungere DRACUT\_SYSTEMD=0 all'inizio del file "/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh" e modificare tutto "if [ -z "$DRACUT\_SYSTEMD" ]; then" in "if [ 1 ]; then"

4\.Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere questo codice dopo "# Open LUKS device"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        luksfile=$MountPoint/$KeyFileName
        break
    fi
    done


5\.Eseguire "/usr/sbin/dracut -f -v" per aggiornare initrd.

###Caricare il VHD crittografato in un account di archiviazione di Azure
Dopo aver abilitato la crittografia BitLocker o la crittografia DM-Crypt, il VHD crittografato locale dovrà essere caricato nell'account di archiviazione.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### Caricare il segreto di crittografia del disco per la VM pre-crittografata nell'insieme di credenziali delle chiavi
Il segreto di crittografia del disco ottenuto in precedenza deve essere caricato come segreto nell'insieme di credenziali delle chiavi.

#### Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave
Usare [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) per effettuare il provisioning del segreto nell'insieme di credenziali delle chiavi. Nel caso di una macchina virtuale Windows, il file BEK viene codificato come stringa Base 64 e quindi caricato nell'insieme di credenziali delle chiavi con il cmdlet Set-AzureKeyVaultSecret. Per Linux la passphrase viene codificata come stringa Base 64 e quindi caricata nell'insieme di credenziali delle chiavi. Assicurarsi anche che i tag seguenti siano impostati mentre si crea il segreto nell'insieme di credenziali delle chiavi.

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }

    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      )

    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"

    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"

    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"}

    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"

    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags


#### Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave

Il segreto può essere facoltativamente crittografato con una chiave di crittografia della chiave prima di essere caricato nell'insieme di credenziali delle chiavi. Usare l'[API](https://msdn.microsoft.com/library/azure/dn878066.aspx) WRAP per crittografare prima il segreto con la chiave di crittografia della chiave. L'output di questa operazione WRAP si basa su una stringa con codifica Base 64 dell'URL che viene quindi caricata come segreto con il cmdlet [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx).


##Scaricare questa guida
È possibile scaricare in questa guida dalla [Raccolta TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## Per altre informazioni
[Esplorare Azure Disk Encryption con Azure PowerShell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Esplorare Azure Disk Encryption con Azure PowerShell - Parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0803_2016-->