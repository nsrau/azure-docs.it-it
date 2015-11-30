<properties
   pageTitle="Come rendere sicuro un cluster di infrastruttura di servizi | Microsoft Azure"
   description="Come rendere sicuro un cluster di infrastruttura di servizi. Quali sono le opzioni?"
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

# Come proteggere un cluster di infrastruttura di servizi con i certificati

Per configurare un cluster di infrastruttura di servizi, sarà necessario almeno un certificato server/x509, che viene quindi caricato nell'insieme di credenziali delle chiavi di Azure e usato nel processo di creazione del cluster descritto in [Processo di creazione del cluster di infrastruttura di servizi](service-fabric-cluster-creation-via-portal.md).

Ci sono tre diversi passaggi:

1. Acquisire il certificato x509.
2. Caricare il certificato nell'insieme di credenziali delle chiavi di Azure.
3. Fornire la posizione e i dettagli del certificato al processo di creazione del cluster di infrastruttura di servizi.

Prima di entrare troppo nel dettaglio, verranno illustrate alcune informazioni di base sugli scenari.

##  Quali scenari sono trattati?

L'infrastruttura di servizi fornisce la sicurezza per gli scenari seguenti:

1. Protezione di un cluster per la comunicazione da nodo a nodo.
2. Protezione di un client dell'infrastruttura che comunica con un determinato nodo del cluster.
3. Controllo degli accessi in base al ruolo: possibilità di limitare le operazioni amministrative dalle operazioni di sola lettura sul cluster a un set di certificati.   

L'infrastruttura di servizi usa i certificati del server X509 specificati durante le configurazioni del tipo di nodo quando si crea un cluster. Per una rapida panoramica di questi certificati e di come sia possibile acquisirli/crearli, scorrere fino alla fine di questa pagina.

 
## Acquisire uno o più certificati x509

1. Per i cluster che eseguono carichi di lavoro di produzione, è necessario usare un certificato x509 firmato da un'[autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) per proteggere il cluster. Per dettagli su come acquisire questi certificati, andare all'indirizzo [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Per i cluster usati solo a scopo di test, è possibile scegliere di usare un certificato autofirmato.


## Creazione di un certificato autofirmato a scopo di test

I dettagli sulla creazione di un certificato autofirmato sono disponibili all'indirizzo [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).
    
Ecco un comando di PowerShell che è possibile usare per creare certificati di test, ma è consigliabile leggere il documento precedente per essere certi che risponda alle proprie esigenze. ```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**Nota** DnsName <stringa> specifica uno o più nomi DNS da inserire nell'estensione di nome alternativo del soggetto del certificato quando un certificato da copiare non viene specificato con il parametro CloneCert. Il primo nome DNS viene salvato anche come nome del soggetto. Se non viene specificato alcun certificato di firma, il primo nome DNS viene salvato anche come nome dell'autorità emittente.

## Caricamento del certificato x509 nell'insieme di credenziali delle chiavi

Le istruzioni per caricare un certificato nell'insieme di credenziali delle chiavi sono disponibili nella [documentazione dell'insieme di credenziali delle chiavi](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).

Assicurarsi di prendere nota dell'URL dell'insieme di credenziali di origine, dell'URL del certificato e dell'identificazione personale del certificato. Queste informazioni saranno necessarie durante la configurazione del cluster di infrastruttura di servizi sicuro. I dati necessari saranno i seguenti:



1. **ID risorsa dell'URL dell'insieme di credenziali delle chiavi/insieme di credenziali di origine**: /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **URL del percorso del certificato nell'insieme di credenziali delle chiavi**: https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **Identificazione personale del certificato**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##Configurazione di un cluster sicuro 

I certificati da usare vengono specificati a livello di tipo di nodo nelle configurazioni di sicurezza. È necessario specificarli per ogni tipo di nodo presente nel cluster. Questo documento illustra come procedere usando il portale, ma è possibile fare lo stesso usando un modello ARM.

![Configurazioni di sicurezza\_01][SecurityConfigurations_01]

Parametri obbligatori

- **Modalità sicurezza**: assicurarsi di selezionare il certificato x509. Indica all'infrastruttura di servizi che si intende configurare un cluster sicuro. 
- **Livello di protezione cluster**: vedere questo [documento sul livello di protezione](https://msdn.microsoft.com/library/aa347692.aspx) per conoscere il significato di ogni valore. Qui sono consentiti tre valori: EncryptAndSign, Sign, None. Modificare il valore predefinito "EncryptAndSign", solo se si è perfettamente consapevoli delle conseguenze.
- **Insieme di credenziali di origine**: si riferisce all'ID risorsa dell'insieme di credenziali delle chiavi e deve essere nel formato ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **URL certificato**: si riferisce all'URL del percorso nell'insieme di credenziali delle chiavi in cui è stato caricato il certificato e deve essere nel formato ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **Identificazione personale certificato**: si riferisce all'identificazione personale del certificato, disponibile nell'URL specificato prima.

Parametri facoltativi: è possibile specificare certificati aggiuntivi usati dai computer client per eseguire operazioni sul cluster. Per impostazione predefinita, l'identificazione personale specificata nei parametri obbligatori viene aggiunta all'elenco autorizzato di identificazioni personali a cui è consentito eseguire operazioni client.

Client amministratore: queste informazioni vengono usate per verificare che la connessione client all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni amministrative e di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni amministrative.


- **Autorizza in base a**: indica all'infrastruttura di servizi se cercare questo certificato usando il nome del soggetto o l'identificazione personale. L'uso del nome del soggetto da autorizzare non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.


- **Nome soggetto**: è necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione personale autorità emittente**: consente un ulteriore livello di controllo che il server può eseguire quando un client presenta le credenziali al server.

Client di sola lettura: queste informazioni vengono usate per verificare che la connessione client all'endpoint di gestione cluster presenti le credenziali corrette per eseguire azioni di sola lettura sul cluster. È possibile specificare più di un certificato da autorizzare per le operazioni di sola lettura.


- **Autorizza in base a**: indica all'infrastruttura di servizi se cercare questo certificato usando il nome del soggetto o l'identificazione personale. L'uso del nome del soggetto da autorizzare non è una procedura di sicurezza consigliabile, anche se consente una maggiore flessibilità.

- **Nome soggetto**: è necessario solo se si è specificato che l'autorizzazione si basa sul nome del soggetto.
- **Identificazione personale autorità emittente**: consente un ulteriore livello di controllo che il server può eseguire quando un client presenta le credenziali al server.


## Come aggiornare i certificati nel cluster

L'infrastruttura di servizi consente di specificare due certificati, uno primario e uno secondario. Quello specificato in fase di creazione viene impostato come primario. Per aggiungere un altro certificato, è necessario distribuirlo nelle VM del cluster. Per informazioni, vedere il documento sulla [distribuzione di certificati nelle VM dall'insieme di credenziali delle chiavi gestito dal cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Una volta completata l'operazione, andare al portale o usare un modello ARM e indicare all'infrastruttura di servizi che è disponibile un certificato secondario che è possibile usare. Tutto ciò che serve è un'identificazione personale.

Nel portale cercare la risorsa cluster a cui si vuole aggiungere questo certificato, fare clic sull'impostazione del certificato, immettere l'identificazione personale del certificato secondario e fare clic su Salva. Verrà avviata una distribuzione e al termine sarà possibile usare sia il certificato primario che quello secondario per eseguire operazioni di gestione sul cluster.

![Configurazioni di sicurezza\_02][SecurityConfigurations_02]

Se necessario, è possibile rimuovere uno dei certificati. Assicurarsi di fare clic su Salva dopo averlo rimosso, per poter avviare una nuova distribuzione. Una volta completata la distribuzione, il certificato rimosso non può più essere usato per connettersi al cluster. Per un cluster sicuro, sarà sempre necessario almeno un certificato valido (non revocato o scaduto) distribuito. In caso contrario, non sarà possibile accedere al cluster.

Esiste un evento di diagnostica che consente di sapere se uno dei certificati è vicino alla scadenza.



## Che cosa sono i certificati X509?

I certificati digitali X509 vengono comunemente usati per autenticare client e server e per crittografare e firmare digitalmente i messaggi. Per altri dettagli su questi certificati, andare all'indirizzo [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx).

**Nota**

1. È consigliabile creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione usando un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'[autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approvata.
2. Non usare mai certificati temporanei o di test creati con strumenti come MakeCert.exe in fase di produzione.
3. Per i cluster usati solo a scopo di test, è possibile scegliere di usare un certificato autofirmato. 


##Che cosa sono i certificati del server e i certificati client?

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
- [Introduzione al modello di integrità dell'infrastruttura di servizi](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png

<!---HONumber=Nov15_HO4-->