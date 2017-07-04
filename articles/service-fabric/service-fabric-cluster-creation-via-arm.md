---
title: Creare un cluster di Azure Service Fabric da un modello | Documentazione Microsoft
description: Questo articolo descrive come configurare un cluster di Service Fabric sicuro in Azure usando Azure Resource Manager, Azure Key Vault e Azure Active Directory (Azure AD) per l'autenticazione client.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8d876a0f2168ee9375a3905d5d5a562ab1194cf3
ms.openlocfilehash: 9159f40fed17e52e6576efa1ea7e8a2dee98728e
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Creare un cluster di Service Fabric usando Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Questo articolo contiene una guida dettagliata che illustra la configurazione di un cluster di Azure Service Fabric sicuro in Azure tramite Azure Resource Manager. Anche se l'articolo è lungo, a meno che non si conosca già a fondo il contenuto, assicurarsi di seguire con attenzione ogni passaggio.

La guida illustra le procedure seguenti:

* Configurazione di un insieme di credenziali delle chiavi di Azure per caricare certificati per la sicurezza di cluster e applicazioni
* Creazione di un cluster sicuro in Azure con Azure Resource Manager
* Autenticazione degli utenti con Azure Active Directory (Azure AD) per la gestione dei cluster

Un cluster sicuro impedisce l'accesso non autorizzato alle operazioni di gestione, tra cui distribuzione, aggiornamento ed eliminazione di applicazioni e servizi e dei dati contenuti. Un cluster non protetto è un cluster a cui tutti gli utenti possono connettersi in qualsiasi momento ed eseguire operazioni di gestione. Anche se è possibile creare un cluster non sicuro, è consigliabile creare un cluster sicuro fin dall'inizio. Poiché un cluster non sicuro non può essere protetto in un secondo momento, è necessario crearne uno nuovo.

Il concetto di creazione di cluster sicuri è lo stesso per i cluster sia Linux che Windows. Per altre informazioni e script helper per la creazione di cluster Linux sicuri, vedere [Creare cluster protetti in Linux](#secure-linux-clusters).

## <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure
Questa guida usa [Azure PowerShell][azure-powershell]. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.

Accedere all'account Azure:

```powershell
Login-AzureRmAccount
```

Selezionare la propria sottoscrizione:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Configurare un insieme di credenziali delle chiavi
Questa sezione illustra la creazione di un insieme di credenziali delle chiavi per un cluster di Service Fabric in Azure e per le applicazioni di Service Fabric. Per una guida completa su Azure Key Vault, vedere la [guida introduttiva a Key Vault][key-vault-get-started].

Service Fabric usa certificati X.509 per proteggere un cluster e fornire le funzionalità di sicurezza dell'applicazione. Si usa Key Vault per gestire i certificati dei cluster di Service Fabric in Azure. Quando viene distribuito un cluster in Azure, il provider di risorse di Azure responsabile della creazione di cluster Service Fabric estrae i certificati da Key Vault e li installa nelle macchine virtuali del cluster.

Nel diagramma seguente viene illustrata la relazione tra Azure Key Vault, un cluster di Service Fabric e il provider di risorse di Azure che usa i certificati archiviati in un insieme di credenziali delle chiavi durante la creazione di un cluster:

![Diagramma dell'installazione del certificato][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Il primo passaggio consiste nel creare un gruppo di risorse specifico per l'insieme di credenziali delle chiavi. È consigliabile inserire l'insieme di credenziali delle chiavi in un proprio gruppo di risorse. Questa azione consente di rimuovere i gruppi di risorse di calcolo e di archiviazione, incluso il gruppo di risorse contenente il cluster di Service Fabric, senza perdere le chiavi e i segreti. Il gruppo di risorse che contiene l'insieme di credenziali delle chiavi _deve essere situato nella stessa area_ del cluster che lo usa.

Se si prevede di distribuire i cluster in più aree, è consigliabile assegnare al gruppo di risorse e all'insieme di credenziali delle chiavi un nome indicante a quale area appartiene.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
L'output dovrebbe essere simile al seguente:

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>Creare un insieme di credenziali delle chiavi nel nuovo gruppo di risorse
L'insieme di credenziali delle chiavi _deve essere abilitato per la distribuzione_ per consentire al provider di risorse di calcolo di ottenere i certificati e installarli nelle istanze delle macchine virtuali:

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

L'output dovrebbe essere simile al seguente:

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>Usare un insieme di credenziali delle chiavi esistente

Per usare un insieme di credenziali delle chiavi esistente, è _necessario abilitarlo per la distribuzione_ per consentire al provider di risorse di calcolo di ottenere i certificati e installarli nei nodi del cluster:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Aggiungere i certificati all'insieme di credenziali delle chiavi

I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni. Per altre informazioni sull'uso dei certificati in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Cluster e certificato del server (obbligatorio)
Questo certificato è richiesto per proteggere un cluster e impedirne accessi non autorizzati. Il certificato fornisce protezione del cluster in due modi:

* Autenticazione del cluster: autentica la comunicazione da nodo a nodo per la federazione di cluster. Solo i nodi che possono dimostrare la propria identità con il certificato possono essere aggiunti al cluster.
* Autenticazione del server: autentica gli endpoint di gestione del cluster in un client di gestione, in modo che il client di gestione sappia con certezza di comunicare con il cluster reale. Questo certificato fornisce anche un certificato SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.

A tale scopo, il certificato deve soddisfare i requisiti seguenti:

* Il certificato deve includere una chiave privata.
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al cluster di Service Fabric. Questa corrispondenza è necessaria per fornire un certificato SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio .cloudapp.azure.com. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

### <a name="application-certificates-optional"></a>Certificati delle applicazioni (facoltativo)
Per motivi di sicurezza dell'applicazione, è possibile installare nel cluster numerosi certificati aggiuntivi. Prima di creare il cluster, considerare gli scenari di protezione delle applicazioni che richiedono l'installazione di un certificato sui nodi, ad esempio:

* Crittografia e decrittografia dei valori di configurazione dell'applicazione.
* Crittografia dei dati tra i nodi durante la replica.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formattazione dei certificati per l'uso di provider di risorse di Azure
È possibile aggiungere e usare file di chiave privata (PFX) direttamente tramite l'insieme di credenziali delle chiavi. Il provider di risorse di calcolo di Azure richiede tuttavia che le chiavi vengano archiviate in uno speciale formato JSON (JavaScript Object Notation). Il formato include il file PFX come stringa con codifica Base 64 e la password della chiave privata. Per soddisfare questi requisiti, le chiavi devono essere inserite in una stringa JSON e quindi archiviate come "segreti" nell'insieme di credenziali delle chiavi.

Per semplificare questo processo, è [disponibile su GitHub un modulo di PowerShell][service-fabric-rp-helpers]. Per usare il modulo, seguire questa procedura:

1. Scaricare l'intero contenuto del repository in una directory locale.
2. Andare alla directory locale.
2. Importare il modulo ServiceFabricRPHelpers nella finestra di PowerShell:

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

Il comando `Invoke-AddCertToKeyVault` in questo modulo di PowerShell formatta in modo automatico una chiave privata del certificato in una stringa JSON e la carica nell'insieme di credenziali delle chiavi. Usare il comando per aggiungere il certificato del cluster ed eventuali altri certificati delle applicazioni all'insieme di credenziali delle chiavi. Ripetere questo passaggio per tutti i certificati aggiuntivi che si vuole installare nel cluster.

#### <a name="uploading-an-existing-certificate"></a>Caricamento di un certificato esistente

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Se si verifica un errore, come quello illustrato qui, in genere significa che è presente un conflitto tra URL di risorsa. Per risolvere il conflitto, modificare il nome dell'insieme di credenziali delle chiavi.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Dopo avere risolto il conflitto, l'output sarà il seguente:

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>Sono necessarie le tre stringhe precedenti, CertificateThumbprint, SourceVault e CertificateURL, per configurare un cluster di Service Fabric sicuro e per ottenere i certificati dell'applicazione che potrebbero essere usati per la sicurezza dell'applicazione. Se non si salvano le stringhe, può essere difficile recuperarle in seguito effettuando una query dell'insieme di credenziali delle chiavi.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>Creazione di un certificato autofirmato e caricamento nell'insieme di credenziali delle chiavi

Se i certificati sono già stati caricati nell'insieme di credenziali delle chiavi, saltare questo passaggio che serve a generare un nuovo certificato autofirmato e a caricarlo nell'insieme di credenziali delle chiavi. Dopo avere modificato i parametri nello script seguente e averlo eseguito, verrà chiesta una password per il certificato.  

```powershell

$ResouceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Se si verifica un errore, come quello illustrato qui, in genere significa che è presente un conflitto tra URL di risorsa. Per risolvere il conflitto, modificare il nome dell'insieme di credenziali delle chiavi, il nome del gruppo di risorse e così via.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Dopo avere risolto il conflitto, l'output sarà il seguente:

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>Sono necessarie le tre stringhe precedenti, CertificateThumbprint, SourceVault e CertificateURL, per configurare un cluster di Service Fabric sicuro e per ottenere i certificati dell'applicazione che potrebbero essere usati per la sicurezza dell'applicazione. Se non si salvano le stringhe, può essere difficile recuperarle in seguito effettuando una query dell'insieme di credenziali delle chiavi.

 A questo punto, dovrebbero essere visualizzati gli elementi seguenti:

* Gruppo di risorse dell'insieme di credenziali delle chiavi.
* Insieme di credenziali delle chiavi e URL (denominato SourceVault nell'output di PowerShell precedente).
* Certificato di autenticazione server del cluster e URL nell'insieme di credenziali delle chiavi.
* Certificati dell'applicazione e URL nell'insieme di credenziali delle chiavi.


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurare Azure Active Directory per l'autenticazione client

Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso utenti alle applicazioni. Alcune applicazioni sono caratterizzate da un'interfaccia utente di accesso basata sul Web, altre invece da un'esperienza client nativa. In questo articolo si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory][active-directory-howto-tenant].

I cluster di Service Fabric offrono numerosi punti di ingresso alle relative funzionalità di gestione, tra cui [Service Fabric Explorer][service-fabric-visualizing-your-cluster], basato sul Web, e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Verranno quindi create due applicazioni Azure AD per controllare l'accesso al cluster, un'applicazione Web e un'applicazione nativa.

Per semplificare alcuni dei passaggi richiesti per la configurazione di Azure AD con un cluster di Service Fabric è stato creato un set di script di Windows PowerShell.

> [!NOTE]
> È necessario completare i passaggi seguenti prima di creare il cluster. Poiché per gli script sono previsti nomi ed endpoint dei cluster, i valori devono essere pianificati e non quelli già creati.

1. [Scaricare gli script][sf-aad-ps-script-download] nel computer.
2. Fare clic con il pulsante destro del mouse sul file ZIP, scegliere **Proprietà**, selezionare la casella di controllo **Sblocca** e quindi fare clic su **Applica**.
3. Estrarre il file con estensione zip.
4. Eseguire `SetupApplications.ps1` e indicare i parametri TenantId, ClusterName e WebApplicationReplyUrl. ad esempio:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    È possibile trovare il TenantId eseguendo il comando di PowerShell `Get-AzureSubscription`. Eseguendo questo comando, viene visualizzato il TenantId per ogni sottoscrizione.

    ClusterName viene usato come prefisso per le applicazioni Azure AD create dallo script. Non è necessario che corrisponda esattamente al nome effettivo del cluster. Ha solo lo scopo di facilitare il mapping degli elementi di Azure AD al cluster di Service Fabric con cui vengono usati.

    WebApplicationReplyUrl è l'endpoint predefinito che Azure AD restituisce agli utenti dopo che hanno completato l'accesso. Impostare questo endpoint come endpoint di Service Fabric Explorer per il cluster, che per impostazione predefinita è:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant Azure AD. Dopo avere eseguito l'accesso, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Tra le applicazioni del tenant nel [portale di Azure classico][azure-classic-portal] dovrebbero essere visualizzate due nuove voci:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

   È consigliabile tenere aperta la finestra di PowerShell perché, durante la creazione del cluster nella sezione successiva, lo script stamperà il codice JSON richiesto dal modello di Azure Resource Manager.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Creare un modello di Cluster Resource Manager di Service Fabric
In questa sezione, gli output dei comandi di PowerShell precedenti verranno usati in un modello di Resource Manager per cluster di Service Fabric.

Nella [raccolta di modelli di avvio rapido di Azure in GitHub][azure-quickstart-templates] sono disponibili alcuni modelli di Resource Manager di esempio. Questi modelli possono essere usati come punto di partenza per il modello del cluster.

### <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager
Questa guida usa il modello di esempio di un [cluster protetto a&5; nodi][service-fabric-secure-cluster-5-node-1-nodetype] e i relativi parametri. Scaricare `azuredeploy.json` e `azuredeploy.parameters.json` sul computer e aprire entrambi i file in un editor di testo.

### <a name="add-certificates"></a>Aggiungere certificati
I certificati vengono aggiunti a un modello di Resource Manager per cluster facendo riferimento all'insieme di credenziali delle chiavi che contiene le chiavi del certificato. È consigliabile inserire i valori dell'insieme di credenziali delle chiavi in un file di parametri del modello di Resource Manager. In questo modo, il file di modello di Resource Manager è riutilizzabile e non contiene valori specifici di una distribuzione.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Aggiungere tutti i certificati all'elemento osProfile del set di scalabilità di macchine virtuali
Ogni certificato che viene installato nel cluster deve essere configurato nella sezione osProfile della risorsa del set di scalabilità (Microsoft.Compute/virtualMachineScaleSets). Questa azione indica al provider di risorse di installare il certificato nelle macchine virtuali, includendo sia il certificato del cluster che eventuali certificati di sicurezza dell'applicazione che si intende usare per le applicazioni:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurare il certificato del cluster di Service Fabric
Il certificato di autenticazione del cluster deve essere configurato sia nella risorsa del cluster di Service Fabric (Microsoft.ServiceFabric/clusters) che nell'estensione di Service Fabric per i set di scalabilità di macchine virtuali nella risorsa del set di scalabilità di macchine virtuali. Questa disposizione consente al provider di risorse di Service Fabric di configurarlo per l'uso dell'autenticazione del cluster e del server per gli endpoint di gestione.

##### <a name="virtual-machine-scale-set-resource"></a>Risorsa del set di scalabilità di macchine virtuali:
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Risorsa di Service Fabric:
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Inserire la configurazione di Azure AD
La configurazione di Azure AD creata prima può essere inserita nel modello di Resource Manager. È tuttavia consigliabile estrarre prima i valori in un file di parametri in modo che il modello di Resource Manager sia riutilizzabile e non contenga valori specifici di una distribuzione.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a "configure-arm" ></a>Configurare i parametri del modello di Resource Manager
Infine, usare i valori di output dei comandi di PowerShell per Azure AD e dell'insieme di credenziali delle chiavi per compilare il file dei parametri:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
A questo punto, dovrebbero essere visualizzati gli elementi seguenti:

* Gruppo di risorse dell'insieme di credenziali delle chiavi
  * Insieme di credenziali delle chiavi
  * Certificato di autenticazione del server del cluster
  * Certificato di crittografia dei dati
* Tenant di Azure Active Directory
  * Applicazione Azure AD per la gestione basata su Web e Service Fabric Explorer
  * Applicazione Azure AD per la gestione dei client nativi
  * Utenti e ruoli assegnati
* Modello di Cluster Resource Manager di Service Fabric
  * Certificati configurati tramite l'insieme di credenziali delle chiavi
  * Azure Active Directory configurato

Il diagramma seguente illustra i punti in cui la configurazione dell'insieme di credenziali delle chiavi e di Azure AD rientra nel modello di Resource Manager.

![Mappa di dipendenza di Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Creare il cluster
A questo punto si è pronti per creare il cluster usando la [distribuzione del modello di risorsa di Azure][resource-group-template-deploy].

#### <a name="test-it"></a>Eseguirne il test
Usare il comando PowerShell seguente per testare il modello di Resource Manager con un file di parametri:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Distribuirlo
Se viene superato il test del modello di Resource Manager, usare il comando PowerShell seguente per distribuire il modello di Resource Manager con un file di parametri:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Assegnare utenti ai ruoli
Dopo aver creato le applicazioni per rappresentare il cluster, assegnare gli utenti ai ruoli supportati da Service Fabric: sola lettura e amministratore. È possibile assegnare i ruoli usando il [portale di Azure classico][azure-classic-portal].

1. Nel portale di Azure andare al tenant e quindi selezionare **Applicazioni**.
2. Selezionare l'applicazione Web, che avrà un nome simile a `myTestCluster_Cluster`.
3. Fare clic sulla scheda **Utenti** .
4. Selezionare un utente per l'assegnazione e quindi fare clic sul pulsante **Assegna** nella parte inferiore della schermata.

    ![Pulsante di assegnazione di utenti ai ruoli][assign-users-to-roles-button]
5. Selezionare il ruolo da assegnare all'utente.

    ![Finestra di dialogo "Assegna utenti"][assign-users-to-roles-dialog]

> [!NOTE]
> Per altre informazioni sui ruoli in Service Fabric, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric](service-fabric-cluster-security-roles.md).
>
>

 <a name="secure-linux-cluster"></a>

## <a name="create-secure-clusters-on-linux"></a>Creare cluster protetti in Linux
Per facilitare il processo, è disponibile uno [script helper](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Prima di usare questo script helper, assicurarsi che sia già installata un'interfaccia della riga di comando di Azure e che sia nel percorso in uso. Verificare che lo script abbia le autorizzazioni necessarie per l'esecuzione eseguendo `chmod +x cert_helper.py` al termine del download. Il primo passaggio consiste nell'accedere al proprio account Azure usando il comando `azure login` nell'interfaccia della riga di comando. Dopo aver eseguito l'accesso all'account Azure, usare lo script helper con il certificato firmato di una CA, come illustrato dai comandi seguenti:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

Il parametro -ifile può accettare un file PFX o un file PEM come input, con il tipo di certificato (pfx o pem oppure ss se è un certificato autofirmato).
Il parametro -h visualizza il testo della guida.


Questo comando restituisce come output le tre stringhe seguenti:

* SourceVaultID, che è l'ID del nuovo gruppo di risorse dell'insieme di credenziali delle chiavi creato.
* CertificateUrl per l'accesso al certificato.
* CertificateThumbprint, usata per l'autenticazione.

L'esempio seguente illustra come usare il comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Eseguendo il comando precedente si ottengono le tre stringhe come segue:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al cluster di Service Fabric. Questa corrispondenza è necessaria per fornire un certificato SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da una CA per il dominio `.cloudapp.azure.com`. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

Questi nomi di soggetto sono le voci necessarie per creare un cluster di Service Fabric sicuro (senza Azure AD), come descritto in [Configurare i parametri del modello di Resource Manager](#configure-arm). È possibile connettersi al cluster sicuro seguendo le istruzioni per [autenticare l'accesso client a un cluster](service-fabric-connect-to-secure-cluster.md). I cluster di anteprima Linux non supportano l'autenticazione di Azure AD. È possibile assegnare ruoli amministratore e client come descritto nella sezione [Assegnare utenti ai ruoli](#assign-roles). Quando si specificano ruoli amministrativi e client per un cluster di anteprima Linux, è necessario fornire le identificazioni personali del certificato per l'autenticazione. Non si specifica il nome del soggetto perché non vengono eseguite convalide o revoche della catena in questa versione di anteprima.

Per usare un certificato autofirmato per il test, è possibile usare lo stesso script per generarne uno. È quindi possibile caricare il certificato nell'insieme di credenziali delle chiavi specificando il flag `ss` invece del percorso e del nome del certificato. Per la creazione e il caricamento di un certificato autofirmato, vedere ad esempio il comando seguente:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Questo comando restituisce le stesse tre stringhe: SourceVault, CertificateUrl e CertificateThumbprint. È quindi possibile usare le stringhe per creare sia un cluster Linux sicuro che una posizione in cui inserire il certificato autofirmato. Per connettersi al cluster, è necessario il certificato autofirmato. È possibile connettersi al cluster sicuro seguendo le istruzioni per [autenticare l'accesso client a un cluster](service-fabric-connect-to-secure-cluster.md).

Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al cluster di Service Fabric. Questa corrispondenza è necessaria per fornire un certificato SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da una CA per il dominio `.cloudapp.azure.com`. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

È possibile immettere i parametri dello script helper nel portale di Azure, come descritto nella sezione [Creare un cluster nel portale di Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi
A questo punto, è stato creato un cluster con Azure Active Directory che fornisce l'autenticazione per la gestione. Successivamente, [connettersi al cluster](service-fabric-connect-to-secure-cluster.md) e scoprire come [gestire i segreti delle applicazioni](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Risoluzione dei problemi di configurazione di Azure Active Directory per l'autenticazione client
Se si verifica un problema durante la configurazione di Azure AD per l'autenticazione client, vedere le potenziali soluzioni in questa sezione.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer richiede di selezionare un certificato
#### <a name="problem"></a>Problema
Dopo avere eseguito l'accesso ad Azure AD in Service Fabric Explorer, il browser torna alla home page, ma un messaggio chiede di selezionare un certificato.

![Finestra di dialogo di selezione certificato in SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
All'utente non è assegnato un ruolo nell'applicazione cluster Azure AD. Di conseguenza, l'autenticazione di Azure AD non riesce nel cluster di Service Fabric. Service Fabric Explorer ritorna all'autenticazione del certificato.

#### <a name="solution"></a>Soluzione
Seguire le istruzioni per la configurazione di Azure AD e assegnare i ruoli utente. È anche consigliabile attivare "Assegnazione utente obbligatoria per l'accesso all'app", come in `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>La connessione a PowerShell non riesce con un errore: "Le credenziali specificate non sono valide"
#### <a name="problem"></a>Problema
Quando si usa PowerShell per connettersi al cluster con la modalità di sicurezza "AzureActiveDirectory", eseguito l'accesso ad Azure AD, la connessione ha esito negativo con un errore: "Le credenziali specificate non sono valide".

#### <a name="solution"></a>Soluzione
Questa soluzione è identica a quella precedente.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer restituisce un errore durante l'accesso: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando si prova a eseguire l'accesso ad Azure AD in Service Fabric Explorer, la pagina restituisce l'errore AADSTS50011, che indica che l'&lt;URL&gt; dell'indirizzo di risposta non corrisponde agli indirizzi configurati per l'applicazione: &lt;GUID&gt;.

![Indirizzo di risposta di SFX non corrispondente][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
L'applicazione cluster (Web) che rappresenta Service Fabric Explorer prova a eseguire l'autenticazione per Azure AD e come parte della richiesta indica l'URL di reindirizzamento restituito. L'URL non è presente nell'elenco degli **URL DI RISPOSTA** dell'applicazione Azure AD.

#### <a name="solution"></a>Soluzione
Nella scheda **Configura** dell'applicazione cluster (Web) aggiungere l'URL di Service Fabric Explorer all'elenco **URL DI RISPOSTA** o sostituire una delle voci dell'elenco. Al termine, salvare la modifica.

![URL di risposta dell'applicazione Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Connettere il cluster usando l'autenticazione di Azure AD tramite PowerShell
Per connettere il cluster di Service Fabric, usare il comando di PowerShell di esempio seguente:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Per informazioni sul cmdlet Connect-ServiceFabricCluster, vedere [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>È possibile usare di nuovo lo stesso tenant di Azure AD in più cluster?
Sì. Ricordarsi però di aggiungere l'URL di Service Fabric Explorer all'applicazione cluster (Web). In caso contrario, Service Fabric Explorer non funzionerà.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Perché è ancora necessario un certificato del server se Azure AD è abilitato?
FabricClient e FabricGateway eseguono un'autenticazione reciproca. Durante l'autenticazione di Azure AD, l'integrazione di Azure AD consente di assegnare un'identità del client al server e il certificato del server viene usato per verificare l'identità del server. Per altre informazioni sui certificati di Service Fabric, vedere [Certificati X.509 e Service Fabric][x509-certificates-and-service-fabric].

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

