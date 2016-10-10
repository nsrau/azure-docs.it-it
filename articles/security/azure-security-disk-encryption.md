<properties
   pageTitle="Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux | Microsoft Azure"
   description="Il documento offre una panoramica di Crittografia dischi di Microsoft Azure per le VM IaaS Windows e Linux."
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
   ms.date="09/26/2016"
   ms.author="krkhan"/>


#Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux

Microsoft Azure è caratterizzato dal massimo impegno volto ad assicurare la privacy e la sovranità dei dati e a consentire il controllo dei dati ospitati in Azure con una gamma di tecnologie avanzate per crittografare, controllare e gestire le chiavi di crittografia e controllare e verificare l'accesso ai dati. I clienti di Azure hanno quindi la possibilità di scegliere la soluzione che meglio soddisfa le proprie esigenze aziendali. In questo documento viene introdotta una nuova soluzione tecnologica, "Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux", che facilita la protezione e la salvaguardia dei dati per rispettare gli impegni in termini di sicurezza e conformità dell'organizzazione. Il documento include informazioni dettagliate sull'uso delle funzionalità di crittografia del disco di Azure, compresi gli scenari supportati e le esperienze utente.

**NOTA**: alcune indicazioni contenute in questo documento possono comportare un maggiore utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione.

## Panoramica

Azure Disk Encryption è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Azure Disk Encryption sfrutta la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e il sistema operativo. La soluzione è integrata con l'[insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/documentation/services/key-vault/) per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi sui dischi delle macchine virtuali siano crittografati nell'archiviazione di Azure.

Crittografia dischi di Azure per VM IaaS Windows e Linux ha ora **disponibilità generale** in tutte le aree pubbliche di Azure per VM Standard e VM con Archiviazione Premium.

### Scenari di crittografia

La soluzione Crittografia dischi di Azure supporta i tre scenari dei clienti descritti di seguito:

- Abilitare la crittografia nelle nuove VM IaaS create da chiavi di crittografia e VHD pre-crittografati
- Abilitare la crittografia in nuove VM IaaS create da immagini della Raccolta di Azure
- Abilitare la crittografia in VM IaaS esistenti in esecuzione in Azure
- Disabilitare la crittografia nelle VM IaaS Windows
- Disabilitare la crittografia nelle unità dati per le VM IaaS Linux

Se abilitata in Microsoft Azure, la soluzione supporta le funzionalità seguenti per le VM IaaS:

- Integrazione dell'insieme di credenziali delle chiavi di Azure.
- VM del piano Standard - [VM IaaS serie A, D, DS, G, GS e così via](https://azure.microsoft.com/pricing/details/virtual-machines/)
- Abilitare la crittografia nelle VM IaaS Windows e Linux
- Disabilitare la crittografia nel sistema operativo e nelle unità dati per le VM IaaS Windows
- Disabilitare la crittografia nelle unità dati per le VM IaaS Linux
- Abilitare la crittografia in VM IaaS eseguite nel sistema operativo client Windows
- Abilitare la crittografia su volumi con percorsi di montaggio
- Abilitare la crittografia nelle VM Linux configurate con sistema RAID basato su software
- Abilitare la crittografia nelle VM Windows configurate con spazi di archiviazione
- Sono supportate tutte le aree geografiche pubbliche di Azure

La soluzione non supporta gli scenari, le funzionalità e la tecnologia seguenti in questa versione:

- VM IaaS del piano Basic
- Disabilitare la crittografia nell'unità del sistema operativo per le VM IaaS Linux
- VM IaaS create con il metodo classico di creazione delle VM.
- Integrazione con il servizio di gestione delle chiavi locale.
- Windows Server 2016 Technical Preview non è supportato in questa versione
- File di Azure (condivisione file di Azure), file system di rete (NFS), volumi dinamici, VM Windows configurate con sistemi RAID basati su software


### Funzionalità di crittografia

Quando si abilita e si distribuisce la crittografia del disco di Azure per le VM IaaS di Azure, sono abilitate le funzionalità seguenti, a seconda della configurazione fornita:

- Crittografia del volume del sistema operativo per proteggere il volume di avvio inattivo nell'archiviazione del cliente.
- Crittografia del volume o dei volumi dati per proteggere i volumi dati inattivi nell'archiviazione del cliente.
- Disabilitare la crittografia nel sistema operativo e nelle unità dati per le VM IaaS Windows
- Disabilitare la crittografia nelle unità dati per le VM IaaS Linux
- Salvaguardia delle chiavi di crittografia e dei segreti nella sottoscrizione dell'insieme di credenziali delle chiavi di Azure.
- Segnalazione dello stato di crittografia della VM IaaS crittografata.
- Rimozione delle impostazioni di configurazione della crittografia del disco dalla macchina virtuale IaaS.

La soluzione Azure Disk Encryption per le VM IaaS per Windows e Linux include l'estensione di crittografia del disco per Windows, l'estensione di crittografia del disco per Linux, i cmdlet di crittografia del disco di PowerShell, i cmdlet di crittografia del disco per l'interfaccia della riga di comando e i modelli di crittografia del disco di Gestione risorse di Azure. La soluzione Azure Disk Encryption è supportata nelle VM IaaS che eseguono il sistema operativo Windows o Linux. Per altri dettagli sui sistemi operativi supportati, vedere la sezione relativa ai prerequisiti più avanti.

**NOTA **: non è previsto alcun addebito aggiuntivo per la crittografia dei dischi delle VM con Crittografia dischi di Azure.

### Proposta di valore

La soluzione di gestione di Azure Disk Encryption consente di soddisfare le esigenze aziendali seguenti nel cloud:

-   Le VM IaaS inattive sono protette con la tecnologia di crittografia standard, per soddisfare i requisiti di sicurezza e conformità dell'organizzazione.
-   Le VM IaaS vengono avviate con chiavi e criteri controllati dal cliente e il relativo utilizzo può essere controllato nell'insieme di credenziali delle chiavi.


### Flusso di lavoro della crittografia

Ecco la procedura generale richiesta per abilitare la crittografia del disco per le VM Windows e Linux:

1. Il cliente sceglie uno scenario di crittografia tra quelli elencati in precedenza
2. Il cliente sceglie di abilitare la crittografia del disco con il modello Crittografia dischi di Azure di Azure Resource Manager, i cmdlet di PowerShell o l'interfaccia della riga di comando e specifica la configurazione della crittografia

    - Per lo scenario con VHD crittografato, il cliente carica il VHD crittografato nell'account di archiviazione e il materiale della chiave di crittografia nell'insieme di credenziali delle chiavi e infine fornisce la configurazione della crittografia per abilitarla nella nuova VM IaaS.
    - Per la nuova VM creata dalla raccolta di Azure e per le VM esistenti già in esecuzione in Azure, fornisce la configurazione della crittografia per abilitarla nella VM IaaS.

3. Il cliente concede l'accesso alla piattaforma Azure per la lettura del materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per i sistemi Windows e Passphrase per Linux, dall'insieme di credenziali delle chiavi locale per abilitare la crittografia nella VM IaaS.
4. Il cliente specifica l'identità dell'applicazione di Azure AD per la scrittura del materiale della chiave di crittografia nell'insieme di credenziali delle chiavi locale per abilitare la crittografia nella VM IaaS per gli scenari indicati nel punto 2 descritto in precedenza
5.  Azure aggiorna il modello di servizi della VM con la configurazione della crittografia e dell'insieme di credenziali delle chiavi ed effettua il provisioning automatico della VM crittografata

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)  

### Flusso di lavoro della decrittografia

La procedura generale richiesta per disabilitare la crittografia del disco per le VM IaaS è la seguente:

1. Il cliente sceglie di disabilitare la crittografia (decrittografia) in una VM IaaS in esecuzione in Azure tramite il modello Crittografia dischi di Azure di Azure Resource Manager o i cmdlet di PowerShell e specifica la configurazione della decrittografia.
2. Il passaggio per disabilitare la crittografia agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione. La disabilitazione della crittografia del disco del sistema operativo per Linux non è tuttavia supportata, come indicato nella documentazione illustrata in precedenza. La disabilitazione è consentita solo per le unità dati nelle macchine virtuali Linux.
4. Azure aggiorna il modello di servizi della VM e la VM IaaS viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato.
5. La disabilitazione della crittografia non elimina l'insieme di credenziali delle chiavi del cliente e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per Windows o Passphrase per Linux.

## Prerequisiti

Di seguito sono elencati i prerequisiti per abilitare Azure Disk Encryption nelle VM IaaS di Azure per gli scenari supportati indicati nella sezione Panoramica.

- Per creare le risorse in Azure nella aree geografiche supportate, l'utente deve avere una sottoscrizione di Azure attiva e valida.
- Crittografia dischi di Azure è supportata negli SKU di Windows Server seguenti: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Windows Server 2016 Technical Preview non è supportato in questa versione.
- Crittografia dischi di Azure è supportato negli SKU client di Windows seguenti: client Windows 8 e client Windows 10.

**Nota**: per Windows Server 2008 R2 è NECESSARIO installare .NET Framework 4.5 prima di abilitare la crittografia in Azure. È possibile installarlo da Windows Update tramite l'aggiornamento facoltativo "Microsoft .NET Framework 4.5.2 per i sistemi Windows Server 2008 R2 basati su x64 ([KB2901983](https://support.microsoft.com/kb/2901983))".

- Crittografia dischi di Azure è supportato negli SKU dei server Linux seguenti: Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.

**Nota**: la crittografia del disco del sistema operativo Linux è attualmente supportata nelle distribuzioni Linux RHEL 7.2, CentOS 7.2, Ubuntu 16.04

- Tutte le risorse, ad esempio l'insieme di credenziali delle chiavi, l'account di archiviazione, la VM e così via, devono appartenere alla stessa area e alla stessa sottoscrizione di Azure.

**Nota**: Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area di Azure. Configurarli in un'area separata provocherà un errore nell'attivazione ella funzionalità di Azure disk encryption.

- Per impostare e configurare l'nsieme di credenziali delle chiavi di Azure per l'utilizzo di Crittografia dischi di Azure, vedere la sezione **Impostazione e configurazione di Insieme di credenziali delle chiavi di Azure per l'utilizzo di Crittografia dischi di Azure** nella sezione *Prerequisiti* di questo articolo.
- Per impostare e configurare l'applicazione Azure AD in Azure Active Directory per l'utilizzo di Crittografia dischi di Azure, vedere la sezione **Installare l'applicazione Azure AD in Azure Active Directory** nella sezione *Prerequisiti* di questo articolo.
- Per impostare e configurare i criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione Azure AD, vedere la sezione **Impostazione dei criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione Azure AD** nella sezione *Prerequisiti* di questo articolo.
- Per preparare -crittografato, vedere la sezione **Preparazione di un VHD Windows pre-crittografato** nell'appendice di questo articolo.
- Per preparare un disco rigido virtuale Linux pre-crittografato, vedere la sezione **Preparazione di un VHD Linux pre-crittografato** nell'appendice di questo articolo.
- La piattaforma Azure deve avere accesso ai segreti o alle chiavi di crittografia nell'insieme di credenziali delle chiavi di Azure del cliente per renderli disponibili alla macchina virtuale perché possa avviare e decrittografare il volume del sistema operativo della macchina virtuale. Per concedere alla piattaforma Azure le autorizzazioni di accesso all'insieme di credenziali delle chiavi del cliente, è necessario impostare la proprietà **enabledForDiskEncryption** nell'insieme di credenziali delle chiavi per questo requisito. Per altre informazioni, fare riferimento alla sezione **Impostazione e configurazione dell'insieme di credenziali delle chiavi di Azure per l'utilizzo di Azure disk encryption** nell'appendice di questo articolo.
- Agli URL KEK (Key Encryption Key) e del segreto dell'insieme di credenziali delle chiavi deve essere applicato il controllo delle versioni. Azure applica questa restrizione relativa al controllo delle versioni. Ecco alcuni esempi di URL KEK e del segreto validi:
	- Esempio di URL del segreto valido: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
	- Esempio di URL KEK valido: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Azure Disk Encryption non supporta l'indicazione dei numeri di porta come parte degli URL KEK e del segreto dell'insieme di credenziali delle chiavi. Ecco alcuni esempi di URL dell'insieme di credenziali delle chiavi supportati:
 	- URL dell'insieme di credenziali delle chiavi non accettato: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
	- URL dell'insieme di credenziali delle chiavi accettato: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Per abilitare la funzionalità Azure Disk Encryption, le VM IaaS devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:
	- La VM IaaS deve potersi connettere all'endpoint di Azure Active Directory [Login.windows.net] per ottenere un token per la connessione all'insieme di credenziali delle chiavi di Azure.
	- La VM IaaS deve potersi connettere all'endpoint dell'insieme di credenziali delle chiavi di Azure per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi del cliente.
	- La VM IaaS deve potersi connettere all'endpoint dell'archiviazione di Azure che ospita l'archivio delle estensioni di Azure e all'account di archiviazione di Azure che ospita i file VHD.

**Nota:** se i criteri di protezione limitano l'accesso da macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente con il quale è necessaria la connettività e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP.

- Usare la versione più recente della versione di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

**Nota:** Crittografia dischi di Azure non è supportato in [Azure PowerShell SDK versione 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Se viene visualizzato un errore relativo all'uso di Azure PowerShell 1.1.0, vedere l'articolo dedicato all'[errore di Crittografia dischi di Azure correlato a Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

- Per eseguire uno qualsiasi dei comandi dell'interfaccia della riga di comando di Azure e associarla alla sottoscrizione di Azure, è necessario installare prima di tutto la versione dell'interfaccia della riga di comando di Azure:
	- Per installare l'interfaccia della riga di comando di Azure e associarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
	- Per l'uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure, vedere [qui](azure-cli-arm-commands.md).
- La soluzione Azure Disk Encryption usa la protezione con chiave esterna BitLocker per le VM IaaS Windows. Se le VM sono aggiunte a un dominio, non effettuare il push dei criteri di gruppo che applicano protezioni TPM. Per informazioni dettagliate sui criteri di gruppo per "consentire l'uso di BitLocker senza un TPM compatibile", vedere [questo articolo](https://technet.microsoft.com/library/ee706521).
- Lo script di PowerShell richiesto da Crittografia dischi di Azure per creare un'applicazione Azure AD, creare un nuovo insieme di credenziali delle chiavi o configurare l'insieme di credenziali delle chiavi esistente e abilitare la crittografia è disponibile [qui](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

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


##### Provisioning del segreto e dell'ID client di Azure AD dal portale del modello di distribuzione classica di Azure

È anche possibile effettuare il provisioning del segreto e dell'ID client di Azure AD tramite il portale del modello di distribuzione classica di Azure all'indirizzo https://manage.windowsazure.com. Per eseguire questa attività, usare questa procedura:

1\.Fare clic sulla scheda Active Directory illustrata nella figura seguente:

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2\.Fare clic su Aggiungi applicazione e digitare il nome dell'applicazione come illustrato di seguito:

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3\.Fare clic sul pulsante con la freccia e configurare le proprietà dell'app come illustrato di seguito:

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4\.Fare clic sul segno di spunta nell'angolo in basso a sinistra per completare l'operazione. Verrà visualizzata la pagina di configurazione dell'app. Notare che l'ID client di Azure AD si trova in basso nella pagina, come illustrato nella figura seguente.

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5\.Per salvare il segreto client di Azure AD usare il pulsante Salva. Fare clic sul pulsante Salva e prendere nota del segreto indicato nella casella di testo delle chiavi. Questo è il segreto client di Azure AD. Il segreto client di Azure AD dovrà essere correttamente protetto.

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


**Nota:** il flusso di lavoro precedente non è supportato nel portale.

##### Usare un'app esistente

Per eseguire i comandi seguenti, è necessario il modulo Azure AD PowerShell, che è possibile ottenere [qui](https://technet.microsoft.com/library/jj151815.aspx).

**Nota:** i comandi seguenti devono essere eseguiti da una nuova finestra di PowerShell. NON usare Azure PowerShell o la finestra di Gestione risorse di Azure per eseguire questi comandi. Il motivo di questa indicazione è dovuto al fatto che questi cmdlet si trovano nel modulo MSOnline in Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Autenticazione basata su certificato per Azure AD

> [AZURE.NOTE] L'autenticazione basata su certificato per AAD non è attualmente supportata nelle VM Linux.

Le sezioni seguenti includono le procedure necessarie per configurare l'autenticazione basata su certificato per Azure AD.

##### Creare una nuova app Azure AD

Eseguire i cmdlet di PowerShell seguenti per creare una nuova app Azure AD:

**Nota:** sostituire la stringa `yourpassword` indicata di seguito con la password sicura e proteggere la password.

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

**Nota:** sostituire la stringa `yourpassword` indicata di seguito con la password sicura e proteggere la password.

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
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### Impostazione dei criteri di accesso dell'insieme di credenziali delle chiavi per l'applicazione Azure AD

L'applicazione Azure AD deve avere i diritti di accesso alle chiavi o ai segreti nell'insieme di credenziali. Usare il cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) per concedere le autorizzazioni all'applicazione con l'ID client, generato al momento della registrazione dell'applicazione, come valore del parametro ServicePrincipalName. Per alcuni esempi, è possibile leggere [questo post di blog](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Di seguito è disponibile anche un esempio dell'esecuzione di questa attività tramite PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**Nota**: Crittografia dischi di Azure richiede la configurazione di criteri di accesso all'applicazione client AAD, ovvero le autorizzazioni 'WrapKey' e 'Set'

## Terminologia

Usare la tabella della terminologia come riferimento per comprendere alcuni dei termini comuni usati da questa tecnologia:

| Terminologia | Definizione |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD | Azure AD è l'abbreviazione di [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). L'account Azure AD è un prerequisito per le operazioni di autenticazione, archiviazione e recupero dei segreti dall'insieme di credenziali delle chiavi. |
| Insieme di credenziali delle chiavi di Azure | L'insieme di credenziali delle chiavi di Azure è un servizio di gestione delle chiavi crittografiche basato su moduli di protezione hardware convalidati in base a FIPS per proteggere le chiavi crittografiche e i segreti sensibili in modo sicuro. Per altri dettagli, vedere la documentazione dell'[Insieme di credenziali delle chiavi](https://azure.microsoft.com/services/key-vault/). |
| ARM | Gestione risorse di Azure |
| BitLocker | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta nel settore, usata per abilitare la crittografia del disco nelle VM IaaS Windows. |
| BEK | Le chiavi di crittografia BitLocker vengono usate per crittografare il volume di avvio del sistema operativo e i volumi dati. Le chiavi BitLocker sono protette nell'insieme di credenziali delle chiavi di Azure del cliente come segreti. |
| CLI | [Interfaccia della riga di comando di Azure](../xplat-cli-install.md) |
| DM-Crypt | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) è il sottosistema di crittografia del disco trasparente basato su Linux usato per abilitare la crittografia del disco nelle VM IaaS Linux. |
| KEK | La chiave di crittografia della chiave (KEK, Key Encryption Key) è la chiave asimmetrica (RSA 2048) usata per proteggere o per eseguire il wrapping del segreto, se necessario. È possibile fornire una chiave protetta tramite HSM o una chiave protetta tramite software. Per altri dettagli, vedere la documentazione dell'[Insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/). |
| Cmdlet PS | [Cmdlet di Azure PowerShell](powershell-install-configure.md) |

### Impostazione e configurazione dell’insieme di credenziali delle chiavi di Azure per l'utilizzo con Azure Disk Encryption

Azure Disk Encryption protegge i segreti e le chiavi di crittografia del disco nell'insieme di credenziali delle chiavi di Azure. Seguire procedura in ognuna delle sezioni seguenti per impostare l'insieme di credenziali delle chiavi per l'utilizzo di Azure Disk Encryption.

#### Creare un nuovo insieme di credenziali delle chiavi
Per creare un nuovo insieme di credenziali delle chiavi, usare una delle opzioni elencate di seguito:

- Usare il modello di Resource Manager "101-Create-KeyVault" disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Usare i [cmdlet dell'insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/dn868052.aspx) di Azure PowerShell.
- Usare il portale di Azure Resource Manager.

**Nota:** se per la sottoscrizione è già configurato un insieme di credenziali delle chiavi, passare alla sezione successiva.

![Insieme di credenziali chiave Azure](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)  

#### Provisioning di una chiave di crittografia della chiave (facoltativo)

Se si vuole usare una chiave di crittografia della chiave (KEK) per avere un livello di sicurezza aggiuntivo per eseguire il wrapping delle chiavi di crittografia BitLocker, aggiungere una KEK all'insieme di credenziali delle chiavi da usare nel processo di provisioning. Usare il cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) per creare una nuova chiave di crittografia delle chiavi nell'insieme di credenziali delle chiavi. È anche possibile importare la chiave di crittografia della chiave dal modulo di protezione hardware di gestione delle chiavi locale. Per altre informazioni, vedere la [documentazione relativa all'insieme di credenziali delle chiavi](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

La chiave di crittografia della chiave può essere aggiunta dal portale di Azure Resource Manager e con l'esperienza utente dell'insieme di credenziali delle chiavi di Azure.

![Insieme di credenziali chiave Azure](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)  

#### Impostare le autorizzazioni dell'insieme di credenziali delle chiavi per consentire alla piattaforma Azure di accedere a chiavi e segreti

La piattaforma Azure deve avere accesso ai segreti o alle chiavi di crittografia nell'insieme di credenziali delle chiavi di Azure per renderli disponibili alla VM perché possa avviare e decrittografare i volumi. Per concedere alla piattaforma Azure le autorizzazioni di accesso all'insieme di credenziali delle chiavi, è necessario impostare la proprietà *enabledForDiskEncryption* nell'insieme di credenziali delle chiavi. È possibile impostare la proprietà enabledForDiskEncryption nell'insieme di credenziali delle chiavi usando il cmdlet PS corrispondente:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

È anche possibile impostare la proprietà *enabledForDiskEncryption* visitando https://resources.azure.com. È necessario impostare la proprietà *enabledForDiskEncryption* nell'insieme di credenziali delle chiavi come accennato in precedenza. In caso contrario, la distribuzione avrà esito negativo.

È possibile configurare i criteri di accesso per l'applicazione AAD dall'esperienza utente dell'insieme di credenziali delle chiavi:

![Insieme di credenziali chiave Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)  

![Insieme di credenziali chiave Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)  

Verificare che l'insieme di credenziali delle chiavi sia abilitato per la crittografia del disco in "Criteri di accesso avanzati":

![Insieme di credenziali chiave Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)  

## Scenari di distribuzione di Disk Encryption ed esperienze utente

La crittografia del disco può essere abilitata in molti scenari e la procedura può variare a seconda dello scenario. Le sezioni che seguono descrivono più dettagliatamente questi scenari.

### Abilitare la crittografia di nuove VM IaaS create dalla raccolta di Azure.

La crittografia del disco può essere abilitata in una nuova VM IaaS Windows dalla raccolta di Azure in Azure usando il modello di Resource Manager pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una nuova VM IaaS.

**Nota:** questo modello crea una nuova VM Windows crittografata usando l'immagine della raccolta di Windows Server 2012.

La crittografia del disco può essere abilitata in una nuova VM IaaS RedHat Linux 7.2 con una matrice RAID 0 da 200 GB usando [questo](https://aka.ms/fde-rhel) modello di Resource Manager. Dopo aver distribuito il modello, verificare lo stato di crittografia della VM tramite il cmdlet `Get-AzureRmVmDiskEncryptionStatus`, come descritto nella sezione "[Crittografia dell'unità del sistema operativo in una VM Linux in esecuzione](#encrypting-os-drive-on-a-running-linux-vm)". Quando la macchina restituisce lo stato `VMRestartPending`, riavviare la VM.

La tabella seguente descrive i parametri del modello di Resource Manager per la nuova VM per lo scenario relativo alla raccolta di Azure con l'ID client di Azure AD:

| Parametro | Descrizione|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | Nome utente dell'amministratore per la macchina virtuale. |
| adminPassword | Password utente dell'amministratore per la macchina virtuale. |
| newStorageAccountName | Nome dell'account di archiviazione per archiviare i VHD di dati e del sistema operativo |
| vmSize | Dimensioni della macchina virtuale. Attualmente sono supportate solo le serie A, D e G Standard. |
| virtualNetworkName | Nome della rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| subnetName | Nome della subnet nella rete virtuale a cui deve appartenere la scheda di interfaccia di rete della VM. |
| AADClientID | ID client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultURL | URL dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo con il cmdlet: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. Facoltativo. |
| keyVaultResourceGroup | Gruppo di risorse dell'insieme di credenziali delle chiavi |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia.


**Nota:** KeyEncryptionKeyURL è un parametro opzionale. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per l'ulteriore protezione della chiave DEK (segreto passphrase) nell'insieme di credenziali delle chiavi.

### Abilitare la crittografia delle nuove VM IaaS create da chiavi di crittografia e VHD crittografati dei clienti.

In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in dettaglio il modello di Resource Manager e i comandi dell'interfaccia della riga di comando.

Seguire le istruzioni di una di queste sezioni per la preparazione di immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM Azure crittografata.

- [Preparazione di un VHD Windows pre-crittografato](#preparing-a-pre-encrypted-windows-vhd)
- [Preparazione di un VHD Linux pre-crittografato](#preparing-a-pre-encrypted-linux-vhd)

#### Con il modello di Azure Resource Manager

La crittografia del disco può essere abilitata in un VHD crittografato del cliente usando il modello di Resource Manager pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia nella nuova VM IaaS.

La tabella seguente descrive i parametri del modello di Resource Manager per lo scenario con VHD crittografati del cliente:

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
| ​vmName | ​Nome della VM IaaS. |


#### Usare i cmdlet PowerShell

La crittografia del disco può essere abilitata in un VHD crittografato del cliente usando i cmdlet PS pubblicati [qui](https://msdn.microsoft.com/library/azure/mt603746.aspx).

#### Uso dei comandi dell'interfaccia della riga di comando

Usare la procedura seguente per abilitare la crittografia del disco per questo scenario con i comandi dell'interfaccia della riga di comando:

1. Impostare i criteri di accesso nell'insieme di credenziali delle chiavi:
	- Impostare il flag ‘EnabledForDiskEncryption’: `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
	- Impostare le autorizzazioni dell'app Azure AD per la scrittura di segreti nell'insieme di credenziali delle chiavi: `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]`
2. Per abilitare la crittografia in una macchina virtuale esistente o in esecuzione, digitare: *azure vm enable-disk-encryption --resource-group <NomeGruppoRisorse> --name <NomeVM> --aad-client-id <IDClient AAD> --aad-client-secret <SegretoClientAAD> --disk-encryption-key-vault-url <URLInsiemeCredenzialiChiavi> --disk-encryption-key-vault-id <IDRisorsaInsiemeCredenzialiChiavi>*
3. Ottenere lo stato della crittografia: *"azure vm show-disk-encryption-status --resource-group <NomeGruppoRisorse> --name <NomeVM> --json"*
4. Per abilitare la crittografia in una nuova VM dal VHD crittografato del cliente, usare i parametri seguenti con il comando "azure vm create":
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Abilitare la crittografia in una VM IaaS Windows esistente o in esecuzione in Azure

In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in dettaglio come abilitarla usando il modello di Resource Manager e i comandi dell'interfaccia della riga di comando.

#### Con il modello di Azure Resource Manager

La crittografia del disco può essere abilitata in una VM IaaS Windows esistente o in esecuzione in Azure usando il modello di Resource Manager pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una VM IaaS esistente o in esecuzione.

La tabella seguente descrive i parametri del modello di Resource Manager per lo scenario con VM esistenti o in esecuzione usando l'ID client di Azure AD:

| Parametro | Descrizione|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​ID client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | ​Segreto client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo con il cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. È facoltativo se si seleziona `nokek` nell'elenco a discesa UseExistingKek. Se si seleziona `kek` nell'elenco a discesa UseExistingKek, è necessario inserire il valore keyEncryptionKeyURL |
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

La crittografia del disco può essere abilitata in una VM IaaS Linux esistente o in esecuzione in Azure usando il modello di Resource Manager pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Fare clic sul pulsante di distribuzione in Azure nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una VM IaaS esistente o in esecuzione.

La tabella seguente descrive i parametri del modello di Resource Manager per lo scenario con VM esistenti o in esecuzione usando l'ID client di Azure AD:

| Parametro | Descrizione|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​ID client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | ​Segreto client dell'app Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo con il cmdlet: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. È facoltativo se si seleziona "nokek" nell'elenco a discesa UseExistingKek. Se si seleziona "kek" nell'elenco a discesa UseExistingKek, è necessario inserire il valore keyEncryptionKeyURL |
| ​volumeType | ​Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori supportati validi sono "OS"/"All" per RHEL 7.2, CentOS 7.2 e Ubuntu 16.04 e "Data" per tutte le altre distribuzioni. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia del disco nella stessa VM. |
| ​vmName | ​Nome della VM in cui deve essere eseguita l'operazione di crittografia.
| passPhrase | Digitare una passphrase complessa come chiave DEK. |                                                                                                                                                                                                                                                      

**Nota:** KeyEncryptionKeyURL è un parametro opzionale. È possibile specificare la chiave di crittografia della chiave (KEK) personalizzata per l'ulteriore protezione della chiave DEK (segreto passphrase) nell'insieme di credenziali delle chiavi.

#### Comandi dell'interfaccia della riga di comando

La crittografia del disco può essere abilitata in un VHD crittografato del cliente usando il comando dell'interfaccia della riga di comando installato da [qui](../xplat-cli-install.md). Seguire questa procedura per abilitare la crittografia nella VM IaaS Linux esistente o in esecuzione in Azure tramite i comandi dell'interfaccia della riga di comando:

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

È possibile ottenere lo stato della crittografia usando il portale di Azure Resource Manager, i [cmdlet di PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti illustrano come usare il portale di Azure e i comandi dell'interfaccia della riga di comando per ottenere lo stato della crittografia.

#### Ottenere lo stato della crittografia di una VM Windows crittografata usando il portale di Azure Resource Manager

È possibile ottenere lo stato della crittografia della VM IaaS dal portale di Azure Resource Manager. Accedere al portale di Azure all'indirizzo https://portal.azure.com/, fare clic sul collegamento Macchine virtuali nel menu a sinistra per passare alla visualizzazione di riepilogo della macchine virtuali disponibili nella sottoscrizione. È possibile filtrare la visualizzazione Macchine virtuali selezionando il nome della sottoscrizione dall'elenco a discesa Sottoscrizione. Fare clic sulle colonne che trovano in alto nel menu della pagina Macchine virtuali. Selezionare la colonna relativa alla crittografia del disco nel pannello per la scelta delle colonne e fare clic su Aggiorna. Verrà visualizzata la colonna relativa alla crittografia del disco con lo stato della crittografia "Abilitata" o "Non abilitata" per ogni VM, come illustrato nella figura seguente.

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)  

#### Ottenere lo stato della crittografia di una VM IaaS crittografata Windows o Linux usando il cmdlet di PowerShell per la crittografia del disco
È possibile ottenere lo stato della crittografia della VM IaaS dal cmdlet PS per la crittografia del disco "Get-AzureRmVMDiskEncryptionStatus". Per ottenere le impostazioni delle crittografia per la VM, nella sessione di Azure PowerShell digitare:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

È possibile esaminare l'output di Get-AzureRmVMDiskEncryptionStatus per gli URL della chiave di crittografia.
    
    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey
    
    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Il valore delle impostazioni OSVolumeEncrypted e DataVolumesEncrypted è "Encrypted" per indicare che entrambi i volumi sono crittografati tramite Crittografia dischi di Azure. Fare riferimento alle [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) del post di blog **Esplorare Azure disk encryption con Azure PowerShell** per informazioni dettagliate su come abilitare la crittografia mediante Azure disk encryption con i cmdlet di PS.

**NOTA**: nelle VM Linux, il cmdlet `Get-AzureRmVMDiskEncryptionStatus` richiede 3-4 minuti per segnalare lo stato della crittografia.

#### Ottenere lo stato della crittografia della VM IaaS usando il comando dell'interfaccia della riga di comando per la crittografia del disco

È possibile ottenere lo stato della crittografia della VM IaaS dal comando dell'interfaccia della riga di comando per la crittografia del disco *azure vm show-disk-encryption-status*. Per ottenere le impostazioni delle crittografia per la VM, nella sessione dell'interfaccia della riga di comando digitare:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### Disabilitare la crittografia nelle VM IaaS Windows in esecuzione

È possibile disabilitare la crittografia in una VM IaaS Windows o Linux in esecuzione tramite il modello Crittografia dischi di Azure di Azure Resource Manager o i cmdlet di PowerShell e specificare la configurazione della decrittografia.


##### Macchina virtuale Windows

Il passaggio per disabilitare la crittografia agisce sul volume dati o sul volume del sistema operativo o entrambi nella VM IaaS Windows in esecuzione. Non è possibile disabilitare il volume del sistema operativo e lasciare il volume dati crittografato. Dopo aver completato il passaggio per disabilitare la crittografia, il modello di distribuzione classica di Azure aggiorna il modello di servizi della VM e la VM IaaS Windows viene contrassegnata come decrittografata. Il contenuto inattivo della VM non viene più crittografato. La disabilitazione della crittografia non elimina l'insieme di credenziali delle chiavi del cliente e il materiale della chiave di crittografia, ovvero le chiavi di crittografia BitLocker per Windows e la passphrase per Linux.

##### VM Linux

Il passaggio per disabilitare la crittografia agisce sul volume dati nella VM IaaS Linux in esecuzione

**NOTA**: la disabilitazione della crittografia del disco del sistema operativo non è consentita nelle VM Linux.

##### Disabilitare la crittografia in una VM IaaS esistente o in esecuzione in Azure con il modello di Azure Resource Manager

La crittografia del disco può essere disabilitata in una macchina virtuale IaaS Windows in esecuzione usando il modello di Azure Resource Manager pubblicato [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Fare clic sul pulsante "Deploy to Azure" nel modello di avvio rapido di Azure, immettere la configurazione della crittografia nel pannello dei parametri e fare clic su OK. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, le condizioni legali e il contratto e fare clic sul pulsante Crea per abilitare la crittografia in una nuova VM IaaS.

Per VM Linux è possibile usare [questo](https://aka.ms/decrypt-linuxvm) modello per disabilitare la crittografia.

Dettagli dei parametri del modello di Resource Manager per disabilitare la crittografia in una VM IaaS in esecuzione:

| ​vmName | ​Nome della VM in cui deve essere eseguita l'operazione di crittografia. |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​volumeType | ​Tipo del volume su cui viene eseguita l'operazione di crittografia. I valori validi sono "OS", "Data", "All". **Nota:** non è possibile disabilitare la crittografia sul volume del sistema operativo o di avvio di una VM IaaS Windows in esecuzione senza disabilitare la crittografia sul volume "Data". **NOTA**: la disabilitazione della crittografia del disco del sistema operativo non è consentita nelle VM Linux. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di decrittografia del disco nella stessa VM. |

##### Disabilitare la crittografia in una VM IaaS esistente o in esecuzione in Azure con il cmdlet di PowerShell

Con il cmdlet di PowerShell, [Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx), è possibile disabilitare la crittografia in una macchina virtuale di tipo infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service). Questo cmdlet supporta sia le VM Windows che le VM Linux. Il cmdlet installa un'estensione nella macchina virtuale per disabilitare la crittografia. Se il parametro Name viene omesso, viene creata un'estensione con il nome predefinito "AzureDiskEncryption for Windows VMs".

Nelle VM Linux viene usata l'estensione "AzureDiskEncryptionForLinux".

**Nota**: questo cmdlet riavvia la macchina virtuale.

## Appendice

### Eseguire la connessione alla sottoscrizione

Prima di procedere, vedere la sezione *Prerequisiti* di questo documento. Dopo essersi assicurati che tutti i requisiti sono stati soddisfatti, seguire questa procedura per connettersi alla sottoscrizione:

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
    CommandType  Name                               	      Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### Preparazione di un VHD Windows pre-crittografato
Le sezioni seguenti sono necessarie per preparare un VHD Windows pre-crittografato per la distribuzione come VHD crittografato in Azure IaaS. I passaggi vengono usati per preparare e avviare una nuova VM (VHD) Windows in Hyper-V o Azure.

#### Aggiornare i criteri di gruppo per consentire la protezione non TPM del sistema operativo
È necessario configurare l'impostazione di Criteri di gruppo BitLocker denominata Crittografia unità BitLocker, che si trova in Criteri del computer locale \\Configurazione computer\\Modelli amministrativi\\Componenti di Windows. Modificare questa impostazione in: *Unità del sistema operativo - Richiedi autenticazione aggiuntiva all'avvio - Consenti BitLocker senza un TPM compatibile* come illustrato nella figura seguente:

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### Installare i componenti della funzionalità BitLocker
Per Windows Server 2012 e versioni successive usare il comando seguente:

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Per Windows Server 2008 R2 usare il comando seguente:

    ServerManagerCmd -install BitLockers

#### Preparare il volume del sistema operativo per BitLocker tramite `bdehdcfg`

Eseguire il comando seguente per comprimere la partizione del sistema operativo e preparare il computer per BitLocker.

    bdehdcfg -target c: shrink -quiet

#### Uso di BitLocker per proteggere il volume del sistema operativo
Usare il comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) per abilitare la crittografia nel volume di avvio usando una protezione con chiave esterna e posizionare la chiave esterna (file con estensione bek) nel volume o nell'unità esterna. La crittografia sarà abilitata nel volume di sistema/di avvio al riavvio successivo.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Nota:** la VM deve essere preparata con un VHD di dati o di risorse separato per ottenere la chiave esterna tramite BitLocker.

#### Crittografia dell'unità del sistema operativo in una VM Linux in esecuzione

La crittografia dell'unità del sistema operativo in una VM Linux in esecuzione è supportata nelle distribuzioni seguenti:

- RHEL 7.2
- CentOS 7.2
- Ubuntu 16.04

Prerequisiti per la crittografia del disco del sistema operativo:

- La macchina virtuale deve essere creata dall'immagine della raccolta di Azure nel portale di Azure Resource Manager.
- VM di Azure con almeno 4 GB di RAM (7 GB consigliati).
- Per RHEL e CentOS: è necessario [disabilitare](https://access.redhat.com/documentation/it-IT/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) SELinux nella macchina virtuale. La VM deve essere riavviata almeno una volta dopo la disabilitazione di SELinux.


##### Passi

1\. Creare una VM usando una delle distribuzioni specificate in precedenza.

Per CentOS 7.2, la crittografia del disco del sistema operativo è supportata tramite un'immagine speciale. Per usare questa immagine, specificare "7.2n" come SKU durante la creazione della VM:

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2\. Configurare la VM in base alle esigenze. Se si intende crittografare tutte le unità (sistema operativo e dati), le unità dati dovranno essere specificate e montabili da /etc/fstab.

> [AZURE.NOTE] È necessario usare UUID=... per specificare le unità di dati in /etc/fstab anziché specificare il nome del dispositivo a blocchi, ad esempio /dev/sdb1. L'ordine delle unità verrà modificato nella macchina virtuale durante la crittografia. Se la VM si basa su un ordine specifico di dispositivi a blocchi, questi dispositivi non potranno essere montati dopo la crittografia.

3\. Chiudere le sessioni SSH.

4\. Per crittografare il sistema operativo, specificare volumeType come "All" o "OS" quando si [abilita la crittografia](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

> [AZURE.NOTE] Tutti i processi dello spazio dell'utente non in esecuzione come servizi `systemd` verranno terminati con `SIGKILL`. La VM verrà riavviata. Prevedere un tempo di inattività della VM durante l'abilitazione della crittografia del disco del sistema operativo in una VM in esecuzione.

5\. Monitorare periodicamente lo stato della crittografia tramite le istruzioni indicate nella [sezione successiva](#monitoring-os-encryption-progress).

6\. Quando Get-AzureRmVmDiskEncryptionStatus indica "VMRestartPending", riavviare la VM accedendo alla VM stessa oppure tramite Portal/PowerShell/CLI.

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

È consigliabile salvare la [diagnostica di avvio](https://azure.microsoft.com/it-IT/blog/boot-diagnostics-for-virtual-machines-v2/) della macchina virtuale *prima* del riavvio.

#### Monitoraggio dello stato della crittografia del sistema operativo

Esistono tre modi per monitorare lo stato della crittografia del sistema operativo.

1\. Usare il cmdlet Get-AzureRmVmDiskEncryptionStatus ed esaminare il campo ProgressMessage:
 
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

Quando la VM raggiunge lo stato "OS disk encryption started", saranno necessari circa 40-50 minuti in una VM con archiviazione Premium.

A causa dell'[errore 388](https://github.com/Azure/WALinuxAgent/issues/388) in WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` vengono visualizzati come `Unknown` in alcune distribuzioni. Con WALinuxAgent 2.1.5 e versioni successive, l'errore verrà risolto automaticamente. Se viene visualizzato `Unknown` nell'output, è possibile verificare lo stato della crittografia del disco usando Esplora risorse di Azure.

Passare a [Esplora risorse di Azure](https://resources.azure.com/), quindi espandere questa gerarchia nel pannello di selezione a sinistra:

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

In InstanceView scorrere verso il basso per visualizzare lo stato della crittografia delle unità.

![Visualizzazione dell'istanza della VM](./media/azure-security-disk-encryption/vm-instanceview.png)  

2\. Esaminare la [diagnostica di avvio](https://azure.microsoft.com/it-IT/blog/boot-diagnostics-for-virtual-machines-v2/). I messaggi provenienti dall'estensione ADE avranno il prefisso `[AzureDiskEncryption]`.

3\. Accedere alla VM tramite SSH e ottenere il log di estensione da

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

Non è consigliabile accedere alla VM mentre è in corso la crittografia del sistema operativo. I log devono essere quindi copiati solo quando altri due metodi hanno esito negativo.

#### Preparazione di un VHD Linux pre-crittografato

##### Ubuntu 16

###### Configurare la crittografia durante l'installazione della distribuzione

1\. Selezionare "Configure encrypted volumes" (Configura volumi crittografati) durante il partizionamento dei dischi.

![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)  

2\. Creare un'unità di avvio separata che non deve essere crittografata. Crittografare l'unità radice.

![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)  

3\. Specificare una passphrase. Si tratta della passphrase che verrà caricata nell'insieme di credenziali delle chiavi.

![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)  

4\. Terminare il partizionamento.

![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)  

5\. Quando si avvia la VM, verrà richiesta una passphrase. Usare la passphrase immessa nel passaggio 3.

![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)  

6\. Preparare la VM per il caricamento in Azure seguendo [queste istruzioni](https://azure.microsoft.com/it-IT/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

###### Configurare la crittografia per l'uso di Azure

1\.Creare un file in /usr/local/sbin/azure\_crypt\_key.sh con il contenuto dello script seguente. Prestare attenzione a KeyFileName, perché è il nome file della passphrase inserito da Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
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

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\.Se si modifica *azure\_crypt\_key.sh* in Windows e si copia il valore in Linux, non dimenticare di eseguire *dos2unix /usr/local/sbin/azure\_crypt\_key.sh*.

4\. Aggiungere autorizzazioni di esecuzione allo script:

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4\.Modificare */etc/initramfs-tools/modules* accodando le righe:

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5\. Eseguire `update-initramfs -u -k all` per aggiornare initramfs e rendere operativo il `keyscript`. 6. È ora possibile effettuare il deprovisioning della VM.

![Configurazione di Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)  

7\. Continuare con il passaggio successivo e [caricare il VHD](#upload-encrypted-vhd-to-an-azure-storage-account) in Azure.

##### openSUSE 13.2

###### Configurare la crittografia durante l'installazione della distribuzione

1\. Selezionare "Encrypt Volume Group" (Crittografa gruppi di volumi) durante il partizionamento dei dischi. Specificare una passphrase. Si tratta della passphrase che verrà caricata nell'insieme di credenziali delle chiavi.

![Configurazione di openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)  

2\. Avviare la VM usando la passphrase.

![Configurazione di openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)  

3\. Preparare la VM per il caricamento in Azure seguendo [queste istruzioni](https://azure.microsoft.com/it-IT/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

###### Configurare la crittografia per l'uso di Azure

1\. Modificare il file /etc/dracut.conf e aggiungere la riga seguente:

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

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



3\. Aggiungere la riga seguente all'inizio del file "/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh"

    DRACUT_SYSTEMD=0

e modificare tutte le occorrenze di

    if [ -z "$DRACUT_SYSTEMD" ]; then

to

    if [ 1 ]; then

4\.Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere questo codice dopo "# Open LUKS device"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5\.Eseguire "/usr/sbin/dracut -f -v" per aggiornare initrd.

6\. È ora possibile effettuare il deprovisioning della VM e [caricare il VHD](#upload-encrypted-vhd-to-an-azure-storage-account) in Azure.

##### CentOS 7

###### Configurare la crittografia durante l'installazione della distribuzione

1\. Selezionare "Encrypt my data" (Crittografa dati personali) durante il partizionamento dei dischi.

![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)  

2\. Assicurarsi che "Encrypt" (Crittografa) sia selezionato per la partizione radice.

![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)  

3\. Specificare una passphrase. Si tratta della passphrase che verrà caricata nell'insieme di credenziali delle chiavi.

![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)  

4\. Quando si avvia la VM, verrà richiesta una passphrase. Usare la passphrase immessa nel passaggio 3.

![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)  

5\. Preparare la VM per il caricamento in Azure seguendo [queste istruzioni](https://azure.microsoft.com/it-IT/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Non eseguire ancora l'ultimo passaggio, ovvero il deprovisioning della VM.

6\. È ora possibile effettuare il deprovisioning della VM e [caricare il VHD](#upload-encrypted-vhd-to-an-azure-storage-account) in Azure.

###### Configurare la crittografia per l'uso di Azure

1\. Modificare il file /etc/dracut.conf e aggiungere la riga seguente:

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

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



3\. Aggiungere la riga seguente all'inizio del file "/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh"

    DRACUT_SYSTEMD=0

e modificare tutte le occorrenze di

    if [ -z "$DRACUT_SYSTEMD" ]; then

to

    if [ 1 ]; then

4\.Modificare /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e aggiungere questo codice dopo "# Open LUKS device"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5\.Eseguire "/usr/sbin/dracut -f -v" per aggiornare initrd.

![Configurazione di CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)  

### Caricare il VHD crittografato in un account di archiviazione di Azure
Dopo aver abilitato la crittografia BitLocker o la crittografia DM-Crypt, il VHD crittografato locale dovrà essere caricato nell'account di archiviazione.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### Caricare il segreto di crittografia del disco per la VM pre-crittografata nell'insieme di credenziali delle chiavi
Il segreto di crittografia del disco ottenuto in precedenza deve essere caricato come segreto nell'insieme di credenziali delle chiavi. L'insieme di credenziali delle chiavi deve avere autorizzazioni per il client AAD e la crittografia del disco.


    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave
Usare [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) per effettuare il provisioning del segreto nell'insieme di credenziali delle chiavi. Nel caso di una macchina virtuale Windows, il file BEK viene codificato come stringa Base 64 e quindi caricato nell'insieme di credenziali delle chiavi con il cmdlet Set-AzureKeyVaultSecret. Per Linux la passphrase viene codificata come stringa Base 64 e quindi caricata nell'insieme di credenziali delle chiavi. Assicurarsi anche che i tag seguenti siano impostati mentre si crea il segreto nell'insieme di credenziali delle chiavi.

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

`$secretUrl` verrà usato nel passaggio successivo per [collegare il disco del sistema operativo senza usare una chiave di crittografia della chiave (KEK)](#without-using-a-kek).

#### Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave

Il segreto può essere facoltativamente crittografato con una chiave di crittografia della chiave prima di essere caricato nell'insieme di credenziali delle chiavi. Usare l'[API](https://msdn.microsoft.com/library/azure/dn878066.aspx) WRAP per crittografare prima il segreto con la chiave di crittografia della chiave. L'output di questa operazione WRAP si basa su una stringa con codifica Base 64 dell'URL che viene quindi caricata come segreto con il cmdlet [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx).

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

`$KeyEncryptionKey` e `$secretUrl` verranno usati nel passaggio successivo per [collegare il disco del sistema operativo usando una chiave di crittografia della chiave (KEK)](#using-a-kek).

### Specificare l'URL del segreto quando si collega il disco del sistema operativo

#### Senza l'uso di una chiave di crittografia della chiave (KEK)

Quando si collega il disco del sistema operativo è necessario passare `$secretUrl`. L'URL è stato generato nella sezione ["Segreto di crittografia del disco non crittografato con una chiave di crittografia della chiave"](#disk-encryption-secret-not-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### Uso di una chiave di crittografia della chiave (KEK)

Quando si collega il disco del sistema operativo è necessario passare `$KeyEncryptionKey` e `$secretUrl`. L'URL è stato generato nella sezione ["Segreto di crittografia del disco crittografato con una chiave di crittografia della chiave"](#disk-encryption-secret-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## Scaricare questa guida
È possibile scaricare in questa guida dalla [Raccolta TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## Per altre informazioni
[Esplorare Azure Disk Encryption con Azure PowerShell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Esplorare Azure Disk Encryption con Azure PowerShell - Parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0928_2016-->