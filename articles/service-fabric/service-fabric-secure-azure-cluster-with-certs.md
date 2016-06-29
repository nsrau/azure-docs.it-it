<properties
   pageTitle="Proteggere un cluster Service Fabric mediante certificati | Microsoft Azure"
   description="Come proteggere un cluster Service Fabric mediante certificati X.509."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Proteggere un cluster di Service Fabric in Azure con i certificati

Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari. Per impedire l'accesso non autorizzato alla risorsa, è necessario proteggerla, in particolare quando sono in esecuzione carichi di lavoro di produzione. Per configurare un cluster di Service Fabric protetto mediante certificati X.509, è necessario almeno un certificato X.509 del server da caricare nell'insieme di credenziali delle chiavi di Azure e usare durante il processo di creazione del cluster.

Questo articolo corrisponde al [Passaggio 3: Configurare la sicurezza](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) della procedura di creazione del cluster. Per altre informazioni su come Service Fabric usa i certificati X.509, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

Ci sono tre passaggi distinti:

1. Acquisire il certificato X.509.
2. Caricare il certificato nell'insieme di credenziali delle chiavi di Azure.
3. Fornire il percorso e i dettagli del certificato al processo di creazione del cluster di Service Fabric.

<a id="acquirecerts"></a>
## Passaggio 1: Acquisire il certificato X.509

Per proteggere i cluster che eseguono carichi di lavoro di produzione, è necessario usare un certificato X.509 firmato da un'[Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority). Per i dettagli su come ottenere questi certificati, vedere [Procedura: Ottenere un certificato](http://msdn.microsoft.com/library/aa702761.aspx).

Per i cluster usati solo a scopo di test, è possibile scegliere di usare un certificato autofirmato. Il passaggio 2.5 seguente illustra come eseguire questa operazione.

## Passaggio 2: Caricare il certificato X.509 nell'insieme di credenziali delle chiavi

Questo è un processo complesso che viene eseguito con un modulo PowerShell caricato in un repository Git.

### Passaggio 2.1
Assicurarsi che nel computer sia installato Azure PowerShell 1.0 o versione successiva. Se non è ancora installato, seguire la procedura descritta in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

### Passaggio 2.2
Copiare la cartella *ServiceFabricRPHelpers* da questo [repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) nel computer.

### Passaggio 2.3
Aprire una finestra di PowerShell e passare alla directory in cui è stato scaricato il modulo. Quindi importare il modulo usando il comando seguente.

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### Passaggio 2.4
Se si usa un certificato già acquisito, seguire la procedura descritta in questo passaggio. In caso contrario, andare al passaggio 2.5, che illustra come creare e distribuire un certificato autofirmato nell'insieme di credenziali delle chiavi.

È possibile usare un gruppo di risorse e un insieme di credenziali delle chiavi esistenti per archiviare il certificato oppure, se non sono disponibili, creare un nuovo gruppo di risorse e/o insieme di credenziali delle chiavi. Un insieme di credenziali delle chiavi esistente deve prima essere configurato con questo script per supportare la distribuzione.

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Per caricare il certificato nel gruppo di risorse e nell'insieme di credenziali delle chiavi, eseguire lo script seguente. Se il gruppo di risorse e l'insieme di credenziali delle chiavi non esistono, vengono creati.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Di seguito è fornito un esempio di script completo.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Al termine dell'esecuzione dello script si ottiene un output simile al seguente, che sarà necessario per il passaggio 3 (Configurare un cluster sicuro).

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

A questo punto sono disponibili le informazioni necessarie per configurare un cluster protetto. Andare al passaggio 3.

### Passaggio 2.5
Se *non* è disponibile un certificato e si vuole creare un nuovo certificato firmato per caricarlo nell'insieme di credenziali delle chiavi, seguire questa procedura. I certificati autofirmati devono essere usati solo per i cluster di test e non per i cluster di produzione.

È possibile usare un gruppo di risorse e un insieme di credenziali delle chiavi esistenti per archiviare il certificato oppure, se non sono disponibili, creare un nuovo gruppo di risorse e/o insieme di credenziali delle chiavi. Un insieme di credenziali delle chiavi esistente deve prima essere configurato con questo script per supportare la distribuzione.

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Lo script seguente creerà un nuovo gruppo di risorse e/o un insieme di credenziali delle chiavi se non sono presenti, creerà e caricherà un certificato autofirmato nell'insieme di credenziali delle chiavi e inserirà il nuovo certificato in *OutputPath*.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
La stringa *DnsName* specifica uno o più nomi DNS da inserire nell'estensione di nome alternativo del soggetto del certificato quando un certificato da copiare non viene specificato con il parametro CloneCert. Il primo nome DNS viene salvato anche come nome del soggetto. Se non viene specificato alcun certificato di firma, il primo nome DNS viene salvato anche come nome dell'autorità emittente. Il cmdlet *Invoke-AddCertToKeyVault* usa il [cmdlet New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633.aspx) per creare il certificato autofirmato.

Di seguito è fornito un esempio di script completo.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Al termine dell'esecuzione dello script si ottiene un output simile al seguente, che sarà necessario per il passaggio 3.

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## Passaggio 3: Configurare un cluster sicuro

Dopo che i certificati sono stati caricati in un insieme di credenziali delle chiavi di Azure, è possibile creare un cluster protetto con tali certificati. Questo passaggio corrisponde al [Passaggio 3: Configurare la sicurezza](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) della procedura di creazione del cluster e mostra come impostare le configurazioni di sicurezza.

>[AZURE.NOTE]
I certificati necessari sono specificati a livello di tipo di nodo nelle configurazioni di sicurezza. È necessario specificarli per ogni tipo di nodo presente nel cluster. Questo documento illustra come procedere usando il portale, ma è possibile fare lo stesso usando un modello di Gestione risorse di Azure.

![Schermata delle configurazioni di sicurezza nel portale di Azure][SecurityConfigurations_01]

### Parametri obbligatori

- **Modalità di sicurezza**: selezionare **Certificato X509** per configurare un cluster protetto con certificati X.509.
- **Livello di protezione cluster**: per comprendere il significato di ognuno di questi valori, vedere questo [documento sul livello di protezione](https://msdn.microsoft.com/library/aa347692.aspx). Anche se sono consentiti tre valori, EncryptAndSign, Sign e None, è consigliabile mantenere il valore predefinito EncryptAndSign, a meno di essere consapevoli delle conseguenze.
- **Source Vault** (Insieme di credenziali di origine): si riferisce all'ID risorsa dell'insieme di credenziali delle chiavi. Deve essere nel formato:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **URL del certificato**: si riferisce all'URL del percorso nell'insieme di credenziali delle chiavi in cui è stato caricato il certificato. Deve essere nel formato:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Identificazione digitale del certificato**: s riferisce all'identificazione personale del certificato disponibile nell'URL specificato prima.

### Parametri facoltativi

 È possibile specificare certificati aggiuntivi usati dai computer client per eseguire operazioni sul cluster. Per impostazione predefinita, l'identificazione personale specificata nei parametri obbligatori viene aggiunta all'elenco autorizzato di identificazioni personali a cui è consentito eseguire operazioni client.

**Client amministratore**: queste informazioni vengono usate per verificare che il client che si connette all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni amministrative e di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni amministrative.

- **Authorize By** (Autorizza in base a): indica a Service Fabric se cercare questo certificato usando il nome del soggetto o l'identificazione personale. L'uso del nome del soggetto per l'autorizzazione non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.
- **Nome soggetto**: è necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione personale autorità di certificazione**: rappresenta un livello di controllo aggiuntivo che il server può eseguire quando un client presenta le credenziali al server.

**Client di sola lettura**: queste informazioni vengono usate per verificare che il client che si connette all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni di sola lettura.

- **Authorize By** (Autorizza in base a): indica a Service Fabric se cercare questo certificato usando il nome del soggetto o l'identificazione personale. L'uso del nome del soggetto per l'autorizzazione non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.
- **Nome soggetto**: è necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione personale autorità di certificazione**: rappresenta un livello di controllo aggiuntivo che il server può eseguire quando un client presenta le credenziali al server.

## Passaggi successivi
Dopo aver configurato la sicurezza basata su certificati nel cluster, riprendere la procedura di creazione del cluster dal [Passaggio 4: Completare la creazione del cluster](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation).

Dopo aver configurato la sicurezza basata su certificati per il cluster, in un secondo momento è possibile [aggiornare un certificato](service-fabric-cluster-security-update-certs-azure.md).


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0615_2016-->