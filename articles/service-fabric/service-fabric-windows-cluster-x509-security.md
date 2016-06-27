<properties
   pageTitle="Connettersi a un cluster privato sicuro | Microsoft Azure"
   description="Questo articolo descrive come proteggere le comunicazioni all'interno del cluster autonomo o privato, nonché tra i client e il cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# Proteggere il cluster Windows autonomo con i certificati

Questo articolo descrive come proteggere la comunicazione tra i diversi nodi del cluster Windows autonomo e come autenticare i client che si connettono a questo cluster usando certificati X.509. Ciò garantisce che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione. La sicurezza basata su certificati deve essere attivata nel cluster durante la creazione del cluster stesso. Per altre informazioni sulla sicurezza da nodo a nodo e da client a nodo e sul controllo degli accessi in base al ruolo, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

## Certificati necessari

Per iniziare, [scaricare il pacchetto del cluster autonomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) in uno dei nodi del cluster. Nel pacchetto scaricato è incluso il file **ClusterConfig.X509.json**. Aprire il file in modalità di *modifica* e vedere la sezione **security** nella scheda **Proprietà**:

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

Questa sezione descrive tutti i certificati necessari per proteggere il cluster Windows autonomo. Abilitare la sicurezza basata sui certificati impostando i valori di **ClusterCredentialType** e **ServerCredentialType** su *X509*.

>[AZURE.NOTE] Un'[identificazione personale](https://en.wikipedia.org/wiki/Public_key_fingerprint) è l'identità primaria di un certificato. Per trovare l'identificazione personale dei certificati creati dall'utente, vedere [Procedura: Recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695(v=vs.110).aspx).

La tabella seguente include l'elenco dei certificati effettivi necessari per l'installazione del cluster:

|**CertificateInformation Setting**|**Descrizione**|
|-----------------------|--------------------------|
|ClusterCertificate|Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster. È possibile usare due diversi certificati, uno primario e uno secondario per il failover. Impostare l'identificazione personale del certificato primario nella sezione **Thumbprint** e quello del certificato secondario nelle variabili **ThumbprintSecondary**.|
|ServerCertificate|Questo certificato viene presentato al client quando tenta di connettersi al cluster. Per praticità, è possibile scegliere di usare lo stesso certificato per *ClusterCertificate* e *ServerCertificate*. È possibile usare due diversi certificati server, uno primario e uno secondario per il failover. Impostare l'identificazione personale del certificato primario nella sezione **Thumbprint** e quello del certificato secondario nelle variabili **ThumbprintSecondary**. |
|ClientCertificateThumbprints|Si tratta di un set di certificati che da installare nei client autenticati. È possibile avere diversi certificati client installati nei computer a cui si vuole consentire l'accesso al cluster e alle applicazioni in esecuzione nel cluster stesso. Impostare l'identificazione personale di ogni certificato nella variabile **CertificateThumbprint**. Se si imposta **IsAdmin** su *true*, il client con installato questo certificato può eseguire varie attività di gestione per il cluster. Se **IsAdmin** è *false*, è possibile accedere solo alle applicazioni in esecuzione nel cluster.|
|ClientCertificateCommonNames|Impostare il nome comune del primo certificato client per **CertificateCommonName**. **CertificateIssuerThumbprint** è l'identificazione personale per l'autorità emittente del certificato. Per altre informazioni sui nomi comuni e sull'autorità emittente, vedere [Utilizzo dei certificati](https://msdn.microsoft.com/library/ms731899(v=vs.110).aspx).|


## Installare i certificati

Per proteggere le comunicazioni tra i cluster, è necessario ottenere prima di tutto i certificati X.509 per i nodi del cluster. Per limitare inoltre la connessione al cluster a computer o utenti autorizzati, è necessario ottenere e installare i certificati per questi computer client potenziali. Per la procedura da eseguire per ottenere i certificati X.509, vedere [Procedura: Ottenere un certificato](https://msdn.microsoft.com/library/aa702761.aspx). Per poter archiviare la chiave privata, è necessario creare un certificato **PFX**. In alternativa, se è disponibile una sottoscrizione di Azure, per creare i certificati è possibile vedere la sezione [Acquisire uno o più certificati x509](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts). Dopo aver creato il certificato, è possibile installarlo nei nodi del cluster usando la procedura seguente. Si noti che la procedura presuppone che nei nodi sia già installata la versione più recente di Windows PowerShell 3.x. È necessario ripetere questi passaggi in ogni nodo, sia per i certificati server e cluster che per gli eventuali certificati secondari di ognuno.

- Copiare il file PFX nel nodo.

- Aprire una finestra di PowerShell come amministratore e immettere i comandi seguenti:
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

Sostituire il *$password* con la password usata per creare il certificato. Sostituire $PfxFilePath con il percorso completo del file PFX copiato in questo nodo.

- Impostare il controllo di accesso per questo certificato in modo che il processo di Service Fabric possa usarlo eseguendo questo script:

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

Usare la procedura per installare anche i certificati client nei computer a cui si vuole consentire l'accesso al cluster.


## Passaggi successivi

Dopo aver configurato la sezione **security** del file ClusterConfig.X509.json, è possibile passare alla sezione [Creare il cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) per configurare i nodi e creare il cluster autonomo. Il file **ClusterConfig.X509.json** deve essere usato mentre si crea il cluster. Ad esempio, il comando può essere simile al seguente:

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


Una volta che il cluster Windows autonomo sarà in esecuzione e sono stati configurati i client autenticati per la connessione al cluster, vedere la sezione [Connettersi a un cluster sicuro mediante PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) per stabilire la connessione.

<!---HONumber=AcomDC_0615_2016-->