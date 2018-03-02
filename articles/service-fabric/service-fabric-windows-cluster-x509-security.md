---
title: Proteggere un cluster di Azure Service Fabric in Windows usando i certificati | Microsoft Docs
description: "Proteggere le comunicazioni all'interno del cluster autonomo o locale di Azure Service Fabric nonché tra i client e il cluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: ee1a2eeeda95b03b185090841cf93c4183c5fce2
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Proteggere un cluster autonomo in Windows usando i certificati X.509
Questo articolo descrive come proteggere la comunicazione tra i vari nodi del cluster di Windows autonomo. Descrive inoltre come autenticare i client che si connettono a questo cluster usando i certificati X.509. Questa autenticazione garantisce che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione. La sicurezza basata su certificati deve essere abilitata nel cluster durante la creazione del cluster.  

Per altre informazioni sulla sicurezza del cluster da nodo a nodo e da client a nodo e sul controllo degli accessi in base al ruolo, vedere [Scenari di sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Certificati necessari
Per iniziare, [scaricare il pacchetto Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) in uno dei nodi del cluster. Nel pacchetto scaricato è incluso un file ClusterConfig.X509.MultiMachine.json. Aprire il file ed esaminare la sezione security sotto la sezione properties:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
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
                "CertificateIssuerThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Questa sezione descrive i certificati necessari per proteggere il cluster Windows autonomo. Se si specifica un certificato di cluster, impostare il valore di ClusterCredentialType su _X509_. Per specificare un certificato del server per le connessioni esterne, impostare il valore della proprietà ServerCredentialType su _X509_. Benché non sia obbligatorio, è consigliabile disporre di entrambi questi certificati per un cluster adeguatamente protetto. Se si impostano questi valori su *X509*, sarà inoltre necessario specificare i certificati corrispondenti o Service Fabric genererà un'eccezione. In alcuni scenari può essere preferibile specificare solo _ClientCertificateThumbprints_ o _ReverseProxyCertificate_. In questi scenari non è necessario impostare _ClusterCredentialType_ o _ServerCredentialType_ su _X509_.


> [!NOTE]
> Un' [identificazione personale](https://en.wikipedia.org/wiki/Public_key_fingerprint) è l'identità primaria di un certificato. Per trovare l'identificazione personale dei certificati creati dall'utente, vedere [Procedura: recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

La tabella seguente include un elenco dei certificati necessari per la configurazione del cluster:

| **Impostazione CertificateInformation** | **Descrizione** |
| --- | --- |
| ClusterCertificate |Consigliato per un ambiente di test. Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster. È possibile usare due diversi certificati, uno primario e uno secondario per l'aggiornamento. Impostare l'identificazione personale del certificato primario nella sezione Identificazione personale e quella del certificato secondario nelle variabili ThumbprintSecondary. |
| ClusterCertificateCommonNames |Consigliato per un ambiente di produzione. Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster. È possibile usare uno o due nomi comuni del certificato del cluster. CertificateIssuerThumbprint corrisponde all'identificazione personale dell'autorità emittente del certificato. È possibile specificare più di un'identificazione personale dell'autorità emittente se si usa più di un certificato con lo stesso nome comune.|
| ClusterCertificateIssuerStores |Consigliato per un ambiente di produzione. Questo certificato corrisponde all'autorità di certificazione del certificato cluster. È possibile specificare il nome comune dell'autorità di certificazione e il nome dell'archivio corrispondente in questa sezione, anziché specificare l'identificazione personale dell'autorità di certificazione in ClusterCertificateCommonNames.  Ciò semplifica il rollover dei certificati dell'autorità di certificazione cluster. È possibile specificare più autorità di certificazione se viene usato più di un certificato cluster. Un valore IssuerCommonName vuoto consente di aggiungere all'elenco elementi consentiti tutti i certificati negli archivi corrispondenti specificati in X509StoreNames.|
| ServerCertificate |Consigliato per un ambiente di test. Questo certificato viene presentato al client quando tenta di connettersi al cluster. Per praticità, è possibile scegliere di usare lo stesso certificato per ClusterCertificate e ServerCertificate. È possibile usare due diversi certificati del server, uno primario e uno secondario per l'aggiornamento. Impostare l'identificazione personale del certificato primario nella sezione Identificazione personale e quella del certificato secondario nelle variabili ThumbprintSecondary. |
| ServerCertificateCommonNames |Consigliato per un ambiente di produzione. Questo certificato viene presentato al client quando tenta di connettersi al cluster. CertificateIssuerThumbprint corrisponde all'identificazione personale dell'autorità emittente del certificato. È possibile specificare più di un'identificazione personale dell'autorità emittente se si usa più di un certificato con lo stesso nome comune. Per praticità, è possibile scegliere di usare lo stesso certificato per ClusterCertificateCommonNames e ServerCertificateCommonNames. È possibile usare uno o due nomi comuni del certificato del server. |
| ServerCertificateIssuerStores |Consigliato per un ambiente di produzione. Questo certificato corrisponde all'autorità di certificazione del certificato server. È possibile specificare il nome comune dell'autorità di certificazione e il nome dell'archivio corrispondente in questa sezione, anziché specificare l'identificazione personale dell'autorità di certificazione in ServerCertificateCommonNames.  Ciò semplifica il rollover dei certificati dell'autorità di certificazione server. È possibile specificare più autorità di certificazione se viene usato più di un certificato server. Un valore IssuerCommonName vuoto consente di aggiungere all'elenco elementi consentiti tutti i certificati negli archivi corrispondenti specificati in X509StoreNames.|
| ClientCertificateThumbprints |Installare questo set di certificati nei client autenticati. È possibile avere diversi certificati client installati nei computer a cui si vuole consentire l'accesso al cluster. Impostare l'identificazione personale di ogni certificato nella variabile CertificateThumbprint. Se si imposta IsAdmin su *true*, il client in cui è installato questo certificato può eseguire attività di gestione degli amministratori sul cluster. Se IsAdmin è *false*, il client con questo certificato può eseguire solo le azioni consentite con diritti di accesso utente, in genere di sola lettura. Per altre informazioni sui ruoli, vedere [Controllo degli accessi in base al ruolo](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Impostare il nome comune del primo certificato client per CertificateCommonName. CertificateIssuerThumbprint è l'identificazione personale dell'autorità emittente del certificato. Per altre informazioni sui nomi comuni e sull'autorità emittente, vedere [Utilizzo dei certificati](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Consigliato per un ambiente di produzione. Questo certificato corrisponde all'autorità di certificazione del certificato client (ruoli di amministratore e non). È possibile specificare il nome comune dell'autorità di certificazione e il nome dell'archivio corrispondente in questa sezione, anziché specificare l'identificazione personale dell'autorità di certificazione in ClientCertificateCommonNames.  Ciò semplifica il rollover dei certificati dell'autorità di certificazione client. È possibile specificare più autorità di certificazione se viene usato più di un certificato client. Un valore IssuerCommonName vuoto consente di aggiungere all'elenco elementi consentiti tutti i certificati negli archivi corrispondenti specificati in X509StoreNames.|
| ReverseProxyCertificate |Consigliato per un ambiente di test. Si tratta di un certificato facoltativo che è possibile specificare se si vuole proteggere il [proxy inverso](service-fabric-reverseproxy.md). Se si usa questo certificato, assicurarsi che reverseProxyEndpointPort sia impostato in nodeTypes. |
| ReverseProxyCertificateCommonNames |Consigliato per un ambiente di produzione. Si tratta di un certificato facoltativo che è possibile specificare se si vuole proteggere il [proxy inverso](service-fabric-reverseproxy.md). Se si usa questo certificato, assicurarsi che reverseProxyEndpointPort sia impostato in nodeTypes. |

Ecco un esempio di una configurazione cluster in cui sono stati specificati certificati cluster, server e client. Si noti che per i certificati del cluster/server/reverseProxy non è consentito configurare insieme l'identificazione personale e il nome comune per lo stesso tipo di certificato.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
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
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
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

## <a name="certificate-rollover"></a>Rollover dei certificati
Quando si usa il nome comune del certificato al posto dell'identificazione personale, il rollover del certificato non richiede l'aggiornamento della configurazione del cluster. Per gli aggiornamenti dell'identificazione personale dell'autorità di certificazione, verificare che ci sia un'intersezione tra il nuovo elenco di identificazione personale e l'elenco precedente. Sarà necessario innanzitutto eseguire un aggiornamento della configurazione con la nuova identificazione personale dell'autorità di certificazione e quindi installare i nuovi certificati (sia il certificato cluster/server che i certificati dell'autorità di certificazione) nell'archivio. Mantenere il vecchio certificato dell'autorità di certificazione nell'archivio certificati per almeno due ore dopo aver installato il nuovo certificato dell'autorità di certificazione.
Se si usano archivi dell'autorità di certificazione, non deve essere eseguito alcun aggiornamento di configurazione per il rollover dei certificati dell'autorità di certificazione. Installare il nuovo certificato dell'autorità di certificazione con una data di scadenza più recente nell'archivio certificati corrispondente e rimuovere il certificato dell'autorità di certificazione precedente dopo alcune ore.

## <a name="acquire-the-x509-certificates"></a>Acquisire i certificati X.509
Per proteggere la comunicazione nel cluster, è prima necessario ottenere i certificati X.509 per i nodi del cluster. Per limitare inoltre la connessione al cluster a computer o utenti autorizzati, è necessario ottenere e installare i certificati per i computer client.

Per proteggere i cluster che eseguono carichi di lavoro di produzione, è necessario usare un certificato X.509 firmato da un' [Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority). Per altre informazioni su come ottenere questi certificati, vedere [Procedura: ottenere un certificato (WCF)](http://msdn.microsoft.com/library/aa702761.aspx).

Per i cluster usati solo a scopo di test, si può scegliere di usare un certificato autofirmato.

## <a name="optional-create-a-self-signed-certificate"></a>Facoltativo: creare un certificato autofirmato
Un modo per creare un certificato autofirmato che possa essere protetto correttamente consiste nell'usare lo script CertSetup.ps1 della cartella Service Fabric SDK nella directory C:\Programmi\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Modificare questo file per cambiare il nome predefinito del certificato (individuare il valore CN=ServiceFabricDevClusterCert). Eseguire questo script come `.\CertSetup.ps1 -Install`.

Esportare ora il certificato in un file con estensione pfx con una password protetta. Ottenere per prima cosa l'identificazione personale del certificato. 
1. Dal menu **Start** eseguire **Gestisci i certificati computer**. 

2. Passare alla cartella **Computer locale\Personale** e cercare il certificato appena creato. 

3. Fare doppio clic sul certificato per aprirlo, selezionare la scheda **Dettagli** e quindi scorrere verso il basso fino al campo **Identificazione personale**. 

4. Rimuovere gli spazi e copiare il valore dell'identificazione personale nel comando PowerShell di seguito. 

5. Modificare il valore `String` in una password sicura idonea a proteggerlo ed eseguire quanto segue in PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Per visualizzare i dettagli di un certificato installato nel computer, eseguire il comando PowerShell seguente:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

In alternativa, se si dispone di una sottoscrizione di Azure, seguire i passaggi in [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Installare i certificati
Dopo aver ottenuto i certificati, è possibile installarli nei nodi del cluster. È necessario che nei nodi sia già installata la versione più recente di Windows PowerShell 3.x. È necessario ripetere questi passaggi in ogni nodo, sia per i certificati cluster e server che per gli eventuali certificati secondari.

1. Copiare i file con estensione pfx nel nodo.

2. Aprire una finestra di PowerShell come amministratore e immettere i comandi seguenti. Sostituire *$pswd* con la password usata per creare il certificato. Sostituire *$PfxFilePath* con il percorso completo del file con estensione pfx copiato nel nodo.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Impostare ora il controllo di accesso per questo certificato in modo che possa essere usato dal processo di Service Fabric, eseguito con l'account Servizio di rete, con lo script seguente. Specificare l'identificazione personale del certificato e **NETWORK SERVICE** come account del servizio. È possibile controllare che gli ACL per il certificato siano corretti aprendo il certificato in **Start** > **Gestisci i certificati computer** ed esaminando **Tutte le attività** > **Gestisci chiavi private**.
   
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
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Ripetere i passaggi precedenti per ogni certificato del server. Questa procedura può essere usata anche per installare i certificati client nei computer a cui si vuole consentire l'accesso al cluster.

## <a name="create-the-secure-cluster"></a>Creare il cluster sicuro
Dopo aver configurato la sezione security del file ClusterConfig.X509.MultiMachine.json, passare alla sezione [Creare il cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) per configurare i nodi e creare il cluster autonomo. Ricordarsi di usare il file ClusterConfig.X509.MultiMachine.json mentre si crea il cluster. Ad esempio, il comando può essere simile al seguente:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Quando il cluster Windows autonomo sicuro è in esecuzione e sono stati configurati i client autenticati per la connessione al cluster, seguire la procedura riportata nella sezione [Connessione a un cluster mediante PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) per stabilire la connessione. Ad esempio: 

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
> La configurazione non corretta del certificato può impedire la visualizzazione del cluster durante la distribuzione. Per diagnosticare automaticamente i problemi di sicurezza, consultare il gruppo di Visualizzatore eventi **Registri applicazioni e servizi** > **Microsoft Service Fabric**.
> 
> 

