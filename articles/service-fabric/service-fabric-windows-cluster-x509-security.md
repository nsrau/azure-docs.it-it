---
title: Proteggere un cluster di Azure Service Fabric in Windows usando i certificati | Microsoft Docs
description: "Questo articolo descrive come proteggere le comunicazioni all&quot;interno del cluster autonomo o privato, nonché tra i client e il cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 51ed17ab8f036f00b285232500dc9f606f2a7e2f
ms.lasthandoff: 04/27/2017


---
# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Proteggere un cluster autonomo in Windows con certificati X.509
Questo articolo descrive come proteggere la comunicazione tra i diversi nodi del cluster Windows autonomo e come autenticare i client che si connettono a questo cluster usando certificati X.509. In questo modo, solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione.  La sicurezza basata su certificati deve essere abilitata nel cluster durante la creazione del cluster.  

Per altre informazioni sulla sicurezza del cluster da nodo a nodo e da client a nodo e sul controllo degli accessi in base al ruolo, vedere [Scenari di sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Certificati necessari
Per iniziare, [scaricare il pacchetto del cluster autonomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) in uno dei nodi del cluster. Nel pacchetto scaricato è incluso il file **ClusterConfig.X509.MultiMachine.json** . Aprire il file ed esaminare la sezione **security** nella sezione **properties**:

```JSON
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
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, 
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }
        ]
        "ReverseProxyCertificate":{
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        }
    }
}
```

Questa sezione descrive i certificati necessari per proteggere il cluster Windows autonomo. Se si specifica un certificato del cluster, impostare il valore di **ClusterCredentialType** su _**X509**_. Se si specifica un certificato del server per le connessioni esterne, impostare il valore di **ServerCredentialType** su _**X509**_. Benché non sia obbligatorio, è consigliabile disporre di entrambi questi certificati per un cluster adeguatamente protetto. Se si impostano questi valori su *X509*, sarà inoltre necessario specificare i certificati corrispondenti o Service Fabric genererà un'eccezione. In alcuni scenari può essere preferibile specificare solo _ClientCertificateThumbprints_ o _ReverseProxyCertificate_. In questi scenari non è necessario impostare _ClusterCredentialType_ o _ServerCredentialType_ su _X509_.


> [!NOTE]
> Un' [identificazione personale](https://en.wikipedia.org/wiki/Public_key_fingerprint) è l'identità primaria di un certificato. Per trovare l'identificazione personale dei certificati creati dall'utente, vedere [Procedura: Recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx) .
> 
> 

La tabella seguente include un elenco dei certificati necessari per la configurazione del cluster:

| **CertificateInformation Setting** | **Descrizione** |
| --- | --- |
| ClusterCertificate |Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster. È possibile usare due diversi certificati, uno primario e uno secondario per l'aggiornamento. Impostare l'identificazione personale del certificato primario nella sezione **Thumbprint** e quella del certificato secondario nelle variabili **ThumbprintSecondary**. |
| ServerCertificate |Questo certificato viene presentato al client quando tenta di connettersi al cluster. Per praticità, è possibile scegliere di usare lo stesso certificato per *ClusterCertificate* e *ServerCertificate*. È possibile usare due diversi certificati del server, uno primario e uno secondario per l'aggiornamento. Impostare l'identificazione personale del certificato primario nella sezione **Thumbprint** e quella del certificato secondario nelle variabili **ThumbprintSecondary**. |
| ClientCertificateThumbprints |Si tratta di un set di certificati che da installare nei client autenticati. È possibile avere diversi certificati client installati nei computer a cui si vuole consentire l'accesso al cluster. Impostare l'identificazione personale di ogni certificato nella variabile **CertificateThumbprint** . Se si imposta **IsAdmin** su *true*, il client in cui è installato questo certificato può eseguire attività di gestione degli amministratori per il cluster. Se **IsAdmin** è *false*, il client con questo certificato può eseguire solo le azioni consentite con diritti di accesso utente, in genere di sola lettura. Per altre informazioni sui ruoli, vedere [Controllo degli accessi in base al ruolo (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac) |
| ClientCertificateCommonNames |Impostare il nome comune del primo certificato client per **CertificateCommonName**. **CertificateIssuerThumbprint** è l'identificazione personale dell'autorità emittente del certificato. Per altre informazioni sui nomi comuni e sull'autorità emittente, vedere [Utilizzo dei certificati](https://msdn.microsoft.com/library/ms731899.aspx) . |
| ReverseProxyCertificate |Si tratta di un certificato facoltativo che è possibile specificare se si vuole proteggere il [proxy inverso](service-fabric-reverseproxy.md). Se si usa questo certificato, assicurarsi che reverseProxyEndpointPort sia impostato in nodeTypes. |

Ecco un esempio di una configurazione cluster in cui sono stati specificati certificati cluster, server e client.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="acquire-the-x509-certificates"></a>Acquisire i certificati X.509
Per proteggere la comunicazione nel cluster, è prima necessario ottenere i certificati X.509 per i nodi del cluster. Per limitare inoltre la connessione al cluster a computer o utenti autorizzati, è necessario ottenere e installare i certificati per i computer client.

Per proteggere i cluster che eseguono carichi di lavoro di produzione, è consigliabile usare un certificato X.509 firmato da un' [autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) . Per informazioni dettagliate su come ottenere questi certificati, vedere [Procedura: ottenere un certificato (WCF)](http://msdn.microsoft.com/library/aa702761.aspx).

Per i cluster usati solo a scopo di test, si può scegliere di usare un certificato autofirmato.

## <a name="optional-create-a-self-signed-certificate"></a>Facoltativo: creare un certificato autofirmato
Un modo per creare un certificato autofirmato che possa essere protetto correttamente consiste nell'usare lo script *CertSetup.ps1* della cartella Service Fabric SDK nella directory *C:\Programmi\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Modificare questo file per cambiare il nome predefinito del certificato (cercare il valore *CN = ServiceFabricDevClusterCert*). Eseguire questo script come `.\CertSetup.ps1 -Install`.

Esportare ora il certificato in un file PFX con una password protetta. Ottenere per prima cosa l'identificazione personale del certificato. Dal menu *Start* eseguire *Gestisci i certificati computer*. Passare alla cartella **Computer locale\Personale** e cercare il certificato appena creato. Fare doppio clic sul certificato per aprirlo, selezionare la scheda *Dettagli* e quindi scorrere verso il basso fino al campo *Identificazione personale*. Copiare il valore dell'identificazione personale nel comando PowerShell di seguito, dopo aver rimosso gli spazi.  Modificare il valore `String` in una password sicura idonea di protezione ed eseguire quanto segue in PowerShell:

```powershell   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Per visualizzare i dettagli di un certificato installato nel computer, è possibile eseguire il comando di PowerShell seguente:

```powershell
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

In alternativa, se si dispone di una sottoscrizione di Azure, vedere la sezione [Aggiungere i certificati all'insieme di credenziali delle chiavi](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Installare i certificati
Dopo aver ottenuto i certificati, è possibile installarli nei nodi del cluster. È necessario che nei nodi sia già installata la versione più recente di Windows PowerShell 3.x. È necessario ripetere questi passaggi in ogni nodo, sia per i certificati cluster e server che per gli eventuali certificati secondari.

1. Copiare i file PFX nel nodo.
2. Aprire una finestra di PowerShell come amministratore e immettere i comandi seguenti. Sostituire *$pswd* con la password usata per creare il certificato. Sostituire *$PfxFilePath* con il percorso completo del file PFX copiato nel nodo.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Impostare ora il controllo di accesso per questo certificato in modo che possa essere usato dal processo di Service Fabric, eseguito con l'account Servizio di rete, con lo script seguente. Specificare l'identificazione personale del certificato e "NETWORK SERVICE" come account del servizio. È possibile controllare che gli ACL per il certificato siano corretti aprendo il certificato in *Start* > *Gestisci i certificati computer* ed esaminando *Tutte le attività* > *Gestisci chiavi private*.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current acl of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ace to the acl of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new acl
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate.
    get-acl $keyFullPath| fl
    ```
4. Ripetere i passaggi precedenti per ogni certificato del server. Questa procedura può essere usata anche per installare i certificati client nei computer a cui si vuole consentire l'accesso al cluster.

## <a name="create-the-secure-cluster"></a>Creare il cluster sicuro
Dopo aver configurato la sezione **sicurezza** del file **ClusterConfig.X509.MultiMachine.json**, passare alla sezione [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) (Crea il cluster) per configurare i nodi e creare il cluster autonomo. Ricordarsi di usare il file **ClusterConfig.X509.MultiMachine.json** mentre si crea il cluster. Ad esempio, il comando può essere simile al seguente:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Quando il cluster Windows autonomo sicuro è in esecuzione e sono stati configurati i client autenticati per la connessione al cluster, vedere la sezione [Connect to a secure cluster using PowerShell (Connettersi a un cluster sicuro mediante PowerShell)](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) per stabilire la connessione. Ad esempio:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

È quindi possibile eseguire altri comandi di PowerShell per usare questo cluster. È possibile, ad esempio, usare il comando [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) per visualizzare un elenco di nodi nel cluster protetto.


Per rimuovere il cluster, connettersi al nodo del cluster in cui è stato scaricato il pacchetto di Service Fabric, aprire una riga di comando e passare alla cartella del pacchetto. Eseguire ora il comando seguente:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> La configurazione non corretta del certificato può impedire la visualizzazione del cluster durante la distribuzione. Per diagnosticare automaticamente problemi di sicurezza, consultare il gruppo di Visualizzatore eventi *Registri applicazioni e servizi* > *Microsoft Service Fabric*.
> 
> 


