<properties
   pageTitle="Come proteggere un Service Fabric Cluster | Microsoft Azure"
   description="Come proteggere un Service Fabric Cluster. Quali sono le opzioni?"
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
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# Protezione di un Service Fabric Cluster

Un Service Fabric Cluster è una risorsa di cui si è proprietari e deve essere protetta da accessi non autorizzati, in particolare quando sono in esecuzione carichi di lavoro di produzione. Questo documento fornisce informazioni dettagliate su come procedere.

##  Scenari di sicurezza cluster da prendere in considerazione

L'infrastruttura di servizi fornisce la sicurezza per gli scenari seguenti:

1. **Sicurezza da nodo a nodo** o protezione di un cluster per le comunicazioni da nodo a nodo. Protegge le comunicazioni tra le macchine virtuali o i computer nel cluster. In questo modo, solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.

	![Da nodo a nodo][Node-to-Node]

2. **Sicurezza da client a nodo** o protezione di un client dell'infrastruttura che comunica con uno specifico nodo nel cluster. Autentica e protegge le comunicazioni client. In questo modo solo gli utenti autorizzati possono accedere al cluster e alle applicazioni distribuite in cluster Windows Fabric. I client vengono identificati in modo univoco con le credenziali di sicurezza di Windows o le credenziali di sicurezza del relativo certificato.

	![Da client a nodo][Client-to-Node]

	Per entrambi i tipi di scenario di comunicazione (da nodo a nodo o da client a nodo), Service Fabric fornisce supporto per l'uso del [certificato di sicurezza](https://msdn.microsoft.com/library/ff649801.aspx) o della [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx). Le opzioni per la sicurezza da nodo a nodo o da client a nodo sono indipendenti l'una dall'altra e potrebbero essere uguali o diverse per ogni elemento.

	Azure Service Fabric usa i certificati server x509 specificati durante le configurazioni del tipo di nodo quando si crea un cluster. Per una rapida panoramica di questi certificati e di come sia possibile acquisirli/crearli, scorrere fino alla fine di questa pagina.

3. **Controllo degli accessi in base al ruolo**: possibilità di limitare le operazioni amministrative dalle operazioni di sola lettura sul cluster a un set di certificati.

4. **Account del servizio e RunAs**: Service Fabric stesso viene eseguito come processo del servizio di Windows (Fabric.exe) e l'account di sicurezza in cui viene eseguito il processo Fabric.exe è configurabile. È possibile proteggere gli account di processo in cui viene eseguito Fabric.exe in ogni nodo del cluster nonché i processi host del servizio attivati per ogni servizio. Per altre informazioni, vedere l'articolo relativo a [sicurezza delle applicazioni e RunAs](service-fabric-application-runas-security.md).
  

## Come proteggere un cluster di infrastruttura di servizi con i certificati

Per configurare un cluster di infrastruttura di servizi, sarà necessario almeno un certificato server/x509, che viene quindi caricato nell'insieme di credenziali delle chiavi di Azure e usato nel processo di creazione del cluster.

Ci sono tre diversi passaggi:

1. Acquisire il certificato x509.
2. Caricare il certificato nell'insieme di credenziali delle chiavi di Azure.
3. Fornire la posizione e i dettagli del certificato al processo di creazione del cluster di infrastruttura di servizi.

 
## Passaggio 1: acquisire uno o più certificati x509

1. Per proteggere i cluster che eseguono carichi di lavoro di produzione, è necessario usare un certificato x509 firmato da un'[autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority). Per informazioni dettagliate su come acquisire questi certificati, andare all'indirizzo [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Per i cluster usati solo a scopo di test, è possibile scegliere di usare un certificato autofirmato. Il passaggio 2.5 illustra i passaggi della procedura.


## Passaggio 2: caricare il certificato x509 nell'insieme di credenziali delle chiavi

Questo è un processo complesso e viene eseguito da un modulo PowerShell caricato in un repository Git.

**Passaggio 2.1**: copiare la cartella nel computer da questo [repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Passaggio 2.2**: assicurarsi che Azure SDK 1.0+ sia installato nel computer.

**Passaggio 2.3**: aprire una finestra di PowerShell e importare il file ServiceFabricRPHelpers.psm.

```
Remove-Module ServiceFabricRPHelpers
```

Copiare quanto segue e modificare il percorso del file con estensione psm1 in quello del computer. Di seguito è fornito un esempio

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```
  

**Passaggio 2.4**: se si usa un certificato già acquisito, seguire questa procedura. In caso contrario andare al passaggio 2.5.


Accedere all'account Azure.

```
Login-AzureRmAccount
```

Lo script crea un nuovo gruppo di risorse e/o un insieme di credenziali se non sono già presenti.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file> 
```
Di seguito è fornito un esempio di script completo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx 
```

Al termine dell'esecuzione dello script si ottiene un output simile al seguente, necessario per il passaggio 3.

1. **Identificazione digitale del certificato**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **Insieme di credenziali di origine**/ID risorsa dell'insieme di credenziali delle chiavi: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL del certificato**/URL del percorso del certificato nell'insieme di credenziali delle chiavi: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

Si tratta delle informazioni necessarie per configurare un cluster protetto. Andare al passaggio 3.


**Passaggio 2.5**: per creare un nuovo certificato autofirmato e caricarlo nell'insieme di credenziali delle chiavi, procedere come indicato di seguito.

Accedere all'account Azure.

```
Login-AzureRmAccount
```

Lo script crea un nuovo gruppo di risorse e/o un insieme di credenziali se non sono già presenti.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file> 
```
Il percorso di output fornito allo script contiene il nuovo certificato autofirmato caricato nell'insieme di credenziali delle chiavi.


**Nota**: DnsName <stringa> specifica uno o più nomi DNS da inserire nell'estensione di nome alternativo del soggetto del certificato quando un certificato da copiare non viene specificato con il parametro CloneCert. Il primo nome DNS viene salvato anche come nome del soggetto. Se non viene specificato alcun certificato di firma, il primo nome DNS viene salvato anche come nome dell'autorità emittente.

Altre informazioni sulla creazione di un certificato autofirmato sono disponibili all'indirizzo [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Di seguito è fornito un esempio di script completo.```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Dal momento che si tratta di un certificato autofirmato, è necessario importarlo nell'archivio "persone attendibili" del computer prima di poterlo usare per la connessione a un cluster protetto. ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
``` ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Al termine dell'esecuzione dello script si ottiene un output simile al seguente, necessario per il passaggio 3.

1. **Identificazione digitale del certificato**: 64881409F4D86498C88EEC3697310C15F8F1540F
2. **Insieme di credenziali di origine**/ID risorsa dell'insieme di credenziali delle chiavi: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL del certificato**/URL del percorso del certificato nell'insieme di credenziali delle chiavi: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea 

##Passaggio 3: configurare un cluster protetto 

Seguire i passaggi descritti nell'articolo relativo al [processo di creazione di un Service Fabric Cluster](service-fabric-cluster-creation-via-portal.md) fino a visualizzare le configurazioni di sicurezza. Di seguito viene illustrato come impostare le configurazioni di sicurezza.

I certificati da usare vengono specificati a livello di tipo di nodo nelle configurazioni di sicurezza. È necessario specificarli per ogni tipo di nodo presente nel cluster. Questo documento illustra come procedere usando il portale, ma è possibile fare lo stesso usando un modello ARM.

![Configurazioni di sicurezza\_01][SecurityConfigurations_01]

Parametri obbligatori

- **Modalità di sicurezza**: assicurarsi di selezionare il certificato x509. Questo indica a Service Fabric che si intende configurare un cluster protetto. 
- **Livello di protezione cluster**: vedere questo [documento sul livello di protezione](https://msdn.microsoft.com/library/aa347692.aspx) per conoscere il significato di ogni valore. Qui sono consentiti tre valori: EncryptAndSign, Sign, None. Modificare il valore predefinito "EncryptAndSign", solo se si è perfettamente consapevoli delle conseguenze.
- **Insieme di credenziali di origine**: si riferisce all'ID risorsa dell'insieme di credenziali delle chiavi e deve essere nel formato ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **URL certificato**: si riferisce all'URL del percorso nell'insieme di credenziali delle chiavi in cui è stato caricato il certificato e deve essere nel formato ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **Identificazione digitale del certificato**: si riferisce all'identificazione digitale del certificato, disponibile nell'URL specificato prima.

Parametri facoltativi: è possibile specificare certificati aggiuntivi usati dai computer client per eseguire operazioni sul cluster. Per impostazione predefinita, l'identificazione personale specificata nei parametri obbligatori viene aggiunta all'elenco autorizzato di identificazioni personali a cui è consentito eseguire operazioni client.

Client amministratore: queste informazioni vengono usate per verificare che la connessione client all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni amministrative e di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni amministrative.


- **Autorizza in base a**: indica a Service Fabric se cercare questo certificato usando il nome del soggetto o l'identificazione digitale. L'uso del nome del soggetto da autorizzare non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.


- **Nome soggetto**: è necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione digitale autorità emittente**: consente un livello di controllo aggiuntivo che il server può eseguire quando un client presenta le credenziali al server.

Client di sola lettura: queste informazioni vengono usate per verificare che la connessione client all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni di sola lettura.


- **Autorizza in base a**: indica a Service Fabric se cercare questo certificato usando il nome del soggetto o l'identificazione digitale. L'uso del nome del soggetto da autorizzare non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.

- **Nome soggetto**: è necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione digitale autorità emittente**: consente un livello di controllo aggiuntivo che il server può eseguire quando un client presenta le credenziali al server.


## Come aggiornare i certificati nel cluster

L'infrastruttura di servizi consente di specificare due certificati, uno primario e uno secondario. Quello specificato in fase di creazione viene impostato come primario. Per aggiungere un altro certificato, è necessario distribuirlo nelle VM del cluster. Il passaggio 2 precedente illustra come caricare un nuovo certificato nell'insieme di credenziali delle chiavi. In questo caso è possibile usare lo stesso insieme di credenziali delle chiavi usato per il primo certificato.

Per informazioni, vedere il documento sulla [distribuzione di certificati nelle macchine virtuali dall'insieme di credenziali delle chiavi gestito dal cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Una volta completata l'operazione, andare al portale o usare un modello ARM e indicare all'infrastruttura di servizi che è disponibile un certificato secondario che è possibile usare. Tutto ciò che serve è un'identificazione personale.

Procedere nel modo seguente: nel portale cercare la risorsa cluster a cui si vuole aggiungere questo certificato, fare clic sull'impostazione del certificato, immettere l'identificazione personale del certificato secondario e fare clic su Salva. Verrà avviata una distribuzione e al termine sarà possibile usare sia il certificato primario che quello secondario per eseguire operazioni di gestione sul cluster.

![Configurazioni di sicurezza\_02][SecurityConfigurations_02]

Se necessario, è possibile rimuovere uno dei certificati. Assicurarsi di fare clic su Salva dopo averlo rimosso, per poter avviare una nuova distribuzione. Una volta completata la distribuzione, il certificato rimosso non può più essere usato per connettersi al cluster. Per un cluster sicuro, sarà sempre necessario almeno un certificato valido (non revocato o scaduto) distribuito. In caso contrario, non sarà possibile accedere al cluster.

Esiste un evento di diagnostica che consente di sapere se uno dei certificati è vicino alla scadenza.



## Che cosa sono i certificati X509?

I certificati digitali X509 vengono comunemente usati per autenticare client e server e per crittografare e firmare digitalmente i messaggi. Per altri dettagli su questi certificati, andare all'indirizzo [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx).

**Nota**

1. È consigliabile creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione usando un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'[autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approvata.
2. Non usare mai certificati temporanei o di test creati con strumenti come MakeCert.exe in fase di produzione.
3. Per i cluster usati solo a scopo di test, è possibile scegliere di usare un certificato autofirmato. 


## Che cosa sono i certificati del server e i certificati client?

**Certificati server/X509**

L'attività principale dei certificati del server è l'autenticazione del server (nodo) nei client o del server (nodo) nel server (nodo). Uno dei controlli iniziali quando un client o un nodo autentica un nodo verifica che il valore del nome comune nel campo del soggetto sia presente nell'elenco di nomi comuni consentiti configurato. Questo nome comune o uno dei nomi alternativi del soggetto dei certificati deve essere presente nell'elenco di nomi comuni consentiti.

L'articolo seguente descrive come generare certificati con nomi alternativi del soggetto: [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351).
 
**Nota:** il campo del soggetto può contenere diversi valori, tutti preceduti da un'inizializzazione per indicare il valore. L'inizializzazione è quasi sempre "CN" per il nome comune, ad esempio, "CN = www.contoso.com". Il campo del soggetto può anche essere vuoto. Si noti anche il campo facoltativo Nome alternativo soggetto. Se viene popolato, deve contenere sia il nome comune del certificato che una voce per ogni nome alternativo del soggetto. Queste voci vengono immesse come valori di nomi DNS.

Si noti anche che il valore del campo Scopi designati del certificato deve includere un valore appropriato, ad esempio "Autenticazione server" o "Autenticazione client".

**Certificati client**

I certificati client in genere non vengono rilasciati da un'autorità di certificazione di terze parti. Invece l'archivio personale della località utente corrente contiene in genere certificati inseriti da un'autorità radice, con lo scopo designato "Autenticazione client". Il client può usare tali certificati quando è necessaria l'autenticazione reciproca. Tutte le operazioni di gestione sul cluster di infrastruttura di servizi richiedono certificati server. Non devono essere usati certificati client.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introduzione al modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_1217_2015-->