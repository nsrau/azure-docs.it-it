<properties
   pageTitle="Proteggere un cluster di Service Fabric | Microsoft Azure"
   description="Come proteggere un cluster di Service Fabric. Ecco le opzioni"
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
   ms.date="05/02/2016"
   ms.author="chackdan"/>

# Proteggere un cluster di Service Fabric

Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari. Per impedire l'accesso non autorizzato alla risorsa, è necessario proteggerla, in particolare quando sono in esecuzione carichi di lavoro di produzione. Questo articolo illustra il processo di protezione di un cluster di Service Fabric.

## Scenari di sicurezza del cluster

Service Fabric fornisce la sicurezza per gli scenari seguenti:

1. **Sicurezza da nodo a nodo:** protegge la comunicazione tra le macchine virtuali e i computer del cluster. Assicura che solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.

	![Diagramma della comunicazione da nodo a nodo][Node-to-Node]

2. **Sicurezza da client a nodo:** protegge la comunicazione tra un client di Service Fabric e i singoli nodi del cluster. Questo tipo di sicurezza autentica e protegge le comunicazioni client, garantendo che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite nel cluster. I client vengono identificati in modo univoco con le credenziali di sicurezza di Windows o le credenziali di sicurezza del relativo certificato.

	![Diagramma della comunicazione da client a nodo][Client-to-Node]

	Per la sicurezza da nodo a nodo o da client a nodo è possibile usare la [sicurezza dei certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx). Le opzioni per la sicurezza da nodo a nodo o da client a nodo sono indipendenti l'una dall'altra e possono essere uguali o diverse.

	Azure Service Fabric usa i certificati server x.509 specificati durante le configurazioni del tipo di nodo quando si crea un cluster. Alla fine di questo articolo viene fornita una rapida panoramica di questi certificati e di come è possibile acquisirli o crearli.

3. **Controllo degli accessi in base al ruolo:** limita le operazioni di amministrazione del cluster a un particolare set di certificati.

## Proteggere un cluster di Service Fabric con i certificati.

Per configurare un cluster di Service Fabric protetto, è necessario almeno un certificato del server x. 509 da caricare nell'insieme di credenziali delle chiavi di Azure e usare durante il processo di creazione del cluster.

Ci sono tre passaggi distinti:

1. Acquisire il certificato X.509.
2. Caricare il certificato nell'insieme di credenziali delle chiavi di Azure.
3. Fornire il percorso e i dettagli del certificato al processo di creazione del cluster di Service Fabric.

### Passaggio 1: Acquisire il certificato X.509

1. Per proteggere i cluster che eseguono carichi di lavoro di produzione, è necessario usare un certificato X.509 firmato da un'[Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority). Per i dettagli su come ottenere questi certificati, vedere [Procedura: Ottenere un certificato](http://msdn.microsoft.com/library/aa702761.aspx).
2. Per i cluster usati solo a scopo di test, è possibile scegliere di usare un certificato autofirmato. Il passaggio 2.5 seguente illustra come eseguire questa operazione.

### Passaggio 2: Caricare il certificato X.509 nell'insieme di credenziali delle chiavi

Questo è un processo complesso che viene eseguito con un modulo PowerShell caricato in un repository Git.

**Passaggio 2.1**: copiare la cartella nel computer da questo [repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Passaggio 2.2**: Assicurarsi che nel computer sia installato Azure PowerShell 1.0 o versione successiva. Se non è ancora installato, è consigliabile seguire la procedura descritta in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

**Passaggio 2.3**: Aprire una finestra di PowerShell e importare il file ServiceFabricRPHelpers.psm. Si tratta del modulo scaricato durante il passaggio 2.1.

```
Remove-Module ServiceFabricRPHelpers
```

Copiare l'esempio seguente e modificare il percorso del file con estensione psm1 nel percorso corrispondente del computer.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**Passaggio 2.4**: Se si usa un certificato già acquisito, seguire la procedura descritta in questo passaggio. In caso contrario, andare al passaggio 2.5, che illustra come creare e distribuire un certificato autofirmato nell'insieme di credenziali delle chiavi.

Accedere all'account Azure. Se per qualche motivo questo comando PowerShell genera un errore, verificare che Azure PowerShell sia installato correttamente.

```
Login-AzureRmAccount
```

Lo script seguente crea un nuovo gruppo di risorse e/o un insieme di credenziali delle chiavi, se non sono già presenti. **Nota: se si utilizza un insieme di credenziali delle chiavi esistente, deve essere configurato con questo script per supportare la distribuzione.**

```
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Di seguito è fornito un esempio di script completo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Al termine dell'esecuzione dello script si ottiene un output simile al seguente, che sarà necessario per il passaggio 3 (Configurare un cluster sicuro).

- **Identificazione digitale del certificato**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

- **Insieme di credenziali di origine**/ID risorsa dell'insieme di credenziali delle chiavi: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **URL del certificato** /URL del percorso del certificato nell'insieme di credenziali delle chiavi: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea

A questo punto sono disponibili le informazioni necessarie per configurare un cluster protetto. Andare al passaggio 3.

**Passaggio 2.5**: se *non* si dispone di un certificato e si vuole creare un nuovo certificato firmato per caricarlo dell'insieme di credenziali delle chiavi, seguire questa procedura.

Accedere all'account Azure. Se per qualche motivo questo comando PowerShell genera un errore, verificare che Azure PowerShell sia installato correttamente.

```
Login-AzureRmAccount
```

Lo script seguente crea un nuovo gruppo di risorse e/o un insieme di credenziali delle chiavi, se non sono già presenti.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

Il valore OutputPath fornito allo script contiene il nuovo certificato autofirmato caricato dallo script nell'insieme di credenziali delle chiavi.

>[AZURE.NOTE] La stringa DnsName specifica uno o più nomi DNS da inserire nell'estensione di nome alternativo del soggetto del certificato quando un certificato da copiare non viene specificato con il parametro CloneCert. Il primo nome DNS viene salvato anche come nome del soggetto. Se non viene specificato alcun certificato di firma, il primo nome DNS viene salvato anche come nome dell'autorità emittente.

Altre informazioni sulla creazione di un certificato autofirmato sono disponibili all'indirizzo [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Di seguito è fornito un esempio di script completo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Dal momento che si tratta di un certificato autofirmato, è necessario importarlo nell'archivio "Persone attendibili" del computer prima di poterlo usare per la connessione a un cluster protetto.

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Al termine dell'esecuzione dello script si ottiene un output simile al seguente, che sarà necessario per il passaggio 3.

- **Identificazione digitale del certificato**: 64881409F4D86498C88EEC3697310C15F8F1540F

- **Insieme di credenziali di origine**/ID risorsa dell'insieme di credenziali delle chiavi: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **URL del certificato** /URL del percorso del certificato nell'insieme di credenziali delle chiavi: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### Passaggio 3: Configurare un cluster sicuro

Seguire i passaggi descritti nell'articolo [Configurazione di cluster di Service Fabric dal portale di Azure](service-fabric-cluster-creation-via-portal.md) fino a visualizzare la sezione Configurazioni di sicurezza. Passare quindi alle istruzioni illustrate qui per impostare le configurazioni di sicurezza:

>[AZURE.NOTE]
I certificati necessari sono specificati a livello di tipo di nodo nelle configurazioni di sicurezza. È necessario specificarli per ogni tipo di nodo presente nel cluster. Questo documento illustra come procedere usando il portale, ma è possibile fare lo stesso usando un modello di Gestione risorse di Azure.

![Schermata delle configurazioni di sicurezza nel portale di Azure][SecurityConfigurations_01]

Parametri obbligatori:

- **Modalità di sicurezza.** Selezionare **Certificato X.509**. Indica a Service Fabric che si vuole configurare un cluster protetto.
- **Livello di protezione del cluster.** Per comprendere il significato di ognuno di questi valori, vedere questo [documento sul livello di protezione](https://msdn.microsoft.com/library/aa347692.aspx). Anche se sono consentiti tre valori, EncryptAndSign, Sign e None, è consigliabile mantenere il valore predefinito EncryptAndSign, a meno di essere consapevoli delle conseguenze.
- **Insieme di credenziali di origine.** Si riferisce all'ID risorsa dell'insieme di credenziali delle chiavi. Deve essere nel formato:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **URL del certificato.** Si riferisce all'URL del percorso nell'insieme di credenziali delle chiavi in cui è stato caricato il certificato. Deve essere nel formato:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Identificazione personale del certificato.** Si riferisce all'identificazione personale del certificato disponibile nell'URL specificato prima.

Parametri facoltativi:

 - È possibile specificare certificati aggiuntivi usati dai computer client per eseguire operazioni sul cluster. Per impostazione predefinita, l'identificazione personale specificata nei parametri obbligatori viene aggiunta all'elenco autorizzato di identificazioni personali a cui è consentito eseguire operazioni client.

Client amministratore: queste informazioni vengono usate per verificare che il client che si connette all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni amministrative e di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni amministrative.

- **Autorizza in base a.** Indica a Service Fabric se cercare questo certificato usando il nome del soggetto o l'identificazione personale. L'uso del nome del soggetto per l'autorizzazione non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.
- **Nome soggetto.** È necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione personale autorità di certificazione**. Rappresenta un livello di controllo aggiuntivo che il server può eseguire quando un client presenta le credenziali al server.

Client di sola lettura: queste informazioni vengono usate per verificare che il client che si connette all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni di sola lettura.

- **Autorizza in base a.** Indica a Service Fabric se cercare questo certificato usando il nome del soggetto o l'identificazione personale. L'uso del nome del soggetto per l'autorizzazione non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.
- **Nome soggetto.** È necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione personale autorità di certificazione.** Consente un livello di controllo aggiuntivo che il server può eseguire quando un client presenta le credenziali al server.

## Aggiornare i certificati nel cluster

Service Fabric consente di specificare due certificati, uno primario e uno secondario. Per impostazione predefinita, quello specificato in fase di creazione è il certificato primario. Per aggiungere un altro certificato, è necessario distribuirlo nelle macchine virtuali del cluster. Il passaggio 2, descritto in precedenza, illustra come caricare un nuovo certificato nell'insieme di credenziali delle chiavi. È possibile usare lo stesso insieme di credenziali delle chiavi per questa operazione, come è stato fatto per il primo certificato. Per altre informazioni, vedere il blog relativo alla [distribuzione di certificati nelle macchine virtuali dall'insieme di credenziali delle chiavi gestito dal cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Una volta completata l'operazione, usare il portale o Gestione risorse per indicare a Service Fabric che è disponibile per l'uso anche un certificato secondario. Tutto ciò che serve è un'identificazione personale.

Questo è il processo per l'aggiunta di un certificato secondario:

1. Accedere al portale e passare alla risorsa del cluster a cui si vuole aggiungere questo certificato.
2. In **Impostazioni** fare clic sull'impostazione relativa ai certificati e immettere l'identificazione personale del certificato secondario.
3. Fare clic su **Save**. Verrà avviata una distribuzione. Se questa viene completata correttamente, sarà possibile usare il certificato primario o quello secondario per eseguire operazioni di gestione sul cluster.

![Schermata delle identificazioni personali del certificato nel portale][SecurityConfigurations_02]

Ecco la procedura per rimuovere un certificato precedente in modo che il cluster non lo usi:

1. Accedere al portale e passare alle impostazioni di sicurezza del cluster.
2. Rimuovere uno dei certificati.
3. Fare clic su **Salva**. Verrà avviata una nuova distribuzione. Una volta completata la nuova distribuzione, il certificato rimosso non potrà più essere usato per connettersi al cluster.

>[AZURE.NOTE] Per un cluster sicuro, sarà sempre necessario almeno un certificato (primario o secondario) valido (non revocato né scaduto) distribuito. In caso contrario, non sarà possibile accedere al cluster.


## Tipi di certificato usati da Service Fabric.

### Certificati X.509

I certificati digitali X509 vengono comunemente usati per autenticare client e server e per crittografare e firmare digitalmente i messaggi. Per altri dettagli su questi certificati, vedere la pagina relativa all'[uso dei certificati](http://msdn.microsoft.com/library/ms731899.aspx) in MSDN Library.

>[AZURE.NOTE]
- È consigliabile creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione con un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'[Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approvata.
- Non usare mai certificati temporanei o di test creati con strumenti come MakeCert.exe in fase di produzione.
- Per i cluster usati solo a scopo di test, è possibile scegliere di usare un certificato autofirmato.

### Certificati server e certificati client

#### Certificati server X.509

L'attività principale dei certificati del server è l'autenticazione di un server (nodo) nei client o di un server (nodo) in un server (nodo). Uno dei primi controlli eseguiti quando un client o un nodo autentica un nodo consiste nel controllare il valore del nome comune nel campo Oggetto. Questo nome comune o uno dei nomi alternativi del soggetto dei certificati deve essere presente nell'elenco di nomi comuni consentiti.

L'articolo seguente descrive come generare certificati con nomi alternativi del soggetto: [Come aggiungere un nome alternativo soggetto a un certificato LDAP sicuro](http://support.microsoft.com/kb/931351).

>[AZURE.NOTE] Il campo del soggetto può contenere diversi valori, tutti preceduti da un'inizializzazione per indicare il valore. L'inizializzazione è quasi sempre "CN" per il nome comune, ad esempio, "CN = www.contoso.com". Il campo del soggetto può anche essere vuoto. Se il campo facoltativo Nome alternativo soggetto è popolato, deve contenere sia il nome comune del certificato sia una voce per ogni nome alternativo del soggetto. Queste voci vengono immesse come valori di nomi DNS.

Il valore del campo Scopi designati del certificato deve includere un valore appropriato, ad esempio "Autenticazione server" o "Autenticazione client".

#### Certificati client

I certificati client in genere non vengono rilasciati da un'autorità di certificazione di terze parti. In genere l'archivio personale della località utente corrente contiene invece certificati client inseriti da un'autorità radice, con lo scopo designato "Autenticazione client". Il client può usare tali certificati quando è necessaria l'autenticazione reciproca.

>[AZURE.NOTE] Tutte le operazioni di gestione in un cluster di Service Fabric richiedono certificati server. I certificati client non possono essere usati per la gestione.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Passaggi successivi

- [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introduzione al modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md)
- [Sicurezza delle applicazioni e RunAs](service-fabric-application-runas-security.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0511_2016-->