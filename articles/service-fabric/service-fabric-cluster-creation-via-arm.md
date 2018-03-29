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
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: ebf4ebd563bab1395f32654bc71955a9416c7a5a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Creare un cluster di Service Fabric usando Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Questo articolo contiene una guida dettagliata che illustra la configurazione di un cluster di Azure Service Fabric sicuro in Azure tramite Azure Resource Manager. Anche se l'articolo è lungo, a meno che non si conosca già a fondo il contenuto, assicurarsi di seguire con attenzione ogni passaggio.

La guida illustra le procedure seguenti:

* Concetti principali da tenere in considerazione prima di distribuire un cluster di Service Fabric.
* Creazione di un cluster in Azure tramite moduli di Gestione risorse di Service Fabric.
* Configurazione di Azure Active Directory (Azure AD) per l'autenticazione degli utenti che eseguono operazioni di gestione nel cluster.
* Creazione di un modello personalizzato di Azure Resource Manager per il cluster e relativa distribuzione.

## <a name="key-concepts-to-be-aware-of"></a>Concetti principali da tenere in considerazione
In Azure, Service Fabric impone l'uso di un certificato x509 per la protezione del cluster e dei relativi endpoint. I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni. Per l'accesso client o l'esecuzione di operazioni di gestione nel cluster, come la distribuzione, l'aggiornamento e l'eliminazione di applicazioni, servizi e dati in essi contenuti, è possibile usare i certificati o le credenziali di Azure Active Directory. L'uso di Azure Active Directory è fortemente consigliato, trattandosi dell'unico modo per impedire la condivisione dei certificati nei client.  Per altre informazioni sull'uso dei certificati in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security].

Service Fabric usa certificati X.509 per proteggere un cluster e fornire le funzionalità di sicurezza dell'applicazione. Si usa [Key Vault][key-vault-get-started] per gestire i certificati dei cluster di Service Fabric in Azure. 


### <a name="cluster-and-server-certificate-required"></a>Cluster e certificato del server (obbligatorio)
Questi certificati (uno primario ed eventualmente uno secondario) sono necessari per proteggere un cluster e prevenire accessi non autorizzati. Il certificato fornisce protezione del cluster in due modi:

* **Autenticazione del cluster:** autentica la comunicazione da nodo a nodo per la federazione di cluster. Solo i nodi che possono dimostrare la propria identità con il certificato possono essere aggiunti al cluster.
* **Autenticazione del server:** autentica gli endpoint di gestione del cluster in un client di gestione, in modo che il client di gestione sappia con certezza di comunicare con il cluster reale e di non essere soggetto a un attacco "man in the middle". Questo certificato fornisce anche un certificato SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.

A tale scopo, il certificato deve soddisfare i requisiti seguenti:

* Il certificato deve includere una chiave privata. In genere questi certificati hanno l'estensione pfx o pem.  
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il **nome del soggetto del certificato deve corrispondere al dominio usato per accedere al cluster di Service Fabric**. Questa corrispondenza è necessaria per fornire un certificato SSL per l'endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'autorità di certificazione (CA) per il dominio *.cloudapp.azure.com. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Configurare Azure Active Directory per l'autenticazione client (facoltativo, ma consigliato)

Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso utenti alle applicazioni. Alcune applicazioni sono caratterizzate da un'interfaccia utente di accesso basata sul Web, altre invece da un'esperienza client nativa. In questo articolo si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory][active-directory-howto-tenant].

I cluster di Service Fabric offrono numerosi punti di ingresso alle relative funzionalità di gestione, tra cui [Service Fabric Explorer][service-fabric-visualizing-your-cluster], basato sul Web, e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Verranno quindi create due applicazioni Azure AD per controllare l'accesso al cluster, un'applicazione Web e un'applicazione nativa.

Più avanti in questo documento verranno fornite altre informazioni sulla configurazione.

### <a name="application-certificates-optional"></a>Certificati delle applicazioni (facoltativo)
Per motivi di sicurezza dell'applicazione, è possibile installare nel cluster numerosi certificati aggiuntivi. Prima di creare il cluster, considerare gli scenari di protezione delle applicazioni che richiedono l'installazione di un certificato sui nodi, ad esempio:

* Crittografia e decrittografia dei valori di configurazione dell'applicazione.
* Crittografia dei dati tra i nodi durante la replica.

Il concetto di creazione di cluster sicuri è lo stesso per i cluster sia Linux che Windows. 

### <a name="client-authentication-certificates-optional"></a>Certificati di autenticazione client (facoltativo)
Per le operazioni client degli utenti o degli amministratori è possibile specificare un numero qualsiasi di certificati aggiuntivi. Per impostazione predefinita, il certificato del cluster ha privilegi "Client amministratore". Questi certificati client aggiuntivi non devono essere installati nel cluster, ma devono essere semplicemente specificati come consentiti nella configurazione del cluster; tuttavia, è necessario che siano installati nei computer client per connettersi al cluster ed eseguire tutte le operazioni di gestione.


## <a name="prerequisites"></a>prerequisiti 
Il concetto di creazione di cluster sicuri è lo stesso per i cluster sia Linux che Windows. Questa guida illustra l'uso di Azure PowerShell o dell'interfaccia della riga di comando di Azure per creare nuovi cluster. Devono essere soddisfatti i prerequisiti seguenti: 

-  [Azure PowerShell 4.1 e versioni successive][azure-powershell] o [interfaccia della riga di comando di Azure 2.0 e versioni successive][azure-CLI].
-  Informazioni dettagliate sui moduli di Service Fabric sono disponibili negli argomenti relativi a [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) e al [modulo dell'interfaccia della riga di comando az SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest).


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Usare il modulo RM di Service Fabric per distribuire un cluster

In questo documento si usa il modulo RM di Service Fabric di PowerShell e dell'interfaccia della riga di comando per distribuire un cluster. Il modulo di PowerShell o dell'interfaccia della riga di comando consente più scenari, che verranno esaminati di seguito in dettaglio. Scegliere lo scenario più appropriato per le specifiche esigenze. 

- Creare un nuovo cluster con un certificato autofirmato generato dal sistema
    - Usare un modello di cluster predefinito
    - Usare un modello già disponibile
- Creare un nuovo cluster con un certificato già in proprio possesso
    - Usare un modello di cluster predefinito
    - Usare un modello già disponibile

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Creare un nuovo cluster con un certificato autofirmato generato dal sistema

Usare il comando seguente per creare un cluster, se si vuole che il sistema generi un certificato autofirmato da usare per la protezione del cluster. Questo comando imposta un certificato cluster primario che viene usato per la protezione del cluster e per configurare l'accesso amministratore per l'esecuzione di operazioni di gestione tramite il certificato.

### <a name="login-in-to-azure"></a>Accedere ad Azure

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Usare il modello predefinito a 5 nodi e 1 tipo di nodo disponibile nel modulo per configurare il cluster

Usare il comando seguente per creare un cluster rapidamente, specificando i parametri minimi.

Il modello usato è disponibile tra gli [esempi di modelli di Azure Service Fabric: modello di Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e nel [modello Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure).

I comandi seguenti consentono di creare cluster di Windows e Linux; è sufficiente specificare il sistema operativo appropriato. I comandi di PowerShell e dell'interfaccia della riga di comando restituiscono inoltre il certificato nella cartella CertificateOutputFolder specificata, tuttavia assicurarsi che la cartella del certificato sia già stata creata. Il comando accetta anche altri parametri come VM SKU.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Usare il modello personalizzato già disponibile 

Se è necessario creare un modello personalizzato specifico per le proprie esigenze, è consigliabile iniziare con uno di quelli disponibili tra gli [esempi di modelli di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Attenersi alle indicazioni e alle spiegazioni per [personalizzare il modello di cluster][customize-your-cluster-template] riportate nella sezione seguente.

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri correlati al certificato nel modello e il file dei parametri siano denominati come segue e che i valori siano Null come indicato di seguito.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Ecco il comando equivalente dell'interfaccia della riga di comando per eseguire la stessa operazione. Modificare i valori nelle istruzioni declare specificando i valori appropriati. L'interfaccia della riga di comando supporta tutti gli altri parametri supportati dal precedente comando di PowerShell.

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Creare un nuovo cluster con il certificato acquistato da un'autorità di certificazione o già in proprio possesso

Usare il comando seguente per creare il cluster, se si dispone già di un certificato da usare per la protezione del cluster.

Se si tratta di un certificato firmato da un'autorità di certificazione utilizzabile anche per altri scopi, è consigliabile fornire un gruppo di risorse distinto specifico per l'insieme di credenziali delle chiavi. È consigliabile inserire l'insieme di credenziali delle chiavi in un proprio gruppo di risorse. Questa azione consente di rimuovere i gruppi di risorse di calcolo e di archiviazione, incluso il gruppo di risorse contenente il cluster di Service Fabric, senza perdere le chiavi e i segreti. **Il gruppo di risorse che contiene l'insieme di credenziali delle chiavi _deve essere situato nella stessa area_ del cluster che lo usa.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Usare il modello predefinito a 5 nodi e 1 tipo di nodo fornito nel modulo
Il modello usato è disponibile tra gli [esempi di Azure: modello di Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e nel [modello Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure).

```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser

```

#### <a name="use-the-custom-template-that-you-have"></a>Usare il modello personalizzato disponibile 
Se è necessario creare un modello personalizzato specifico per le proprie esigenze, è consigliabile iniziare con uno di quelli disponibili tra gli [esempi di modelli di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Attenersi alle indicazioni e alle spiegazioni per [personalizzare il modello di cluster][customize-your-cluster-template] riportate nella sezione seguente.

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri correlati al certificato nel modello e il file dei parametri siano denominati come segue e che i valori siano Null come indicato di seguito.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword

```

Ecco il comando equivalente dell'interfaccia della riga di comando per eseguire la stessa operazione. Modificare i valori nelle istruzioni declare specificando i valori appropriati.

```CLI

declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>Usare un puntatore al segreto già caricato nell'insieme di credenziali delle chiavi

Per usare un insieme di credenziali delle chiavi esistente, è _necessario abilitarlo per la distribuzione_ per consentire al provider di risorse di calcolo di ottenere i certificati e installarli nei nodi del cluster:

```PowerShell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```
Ecco il comando equivalente dell'interfaccia della riga di comando per eseguire la stessa operazione. Modificare i valori nelle istruzioni declare specificando i valori appropriati.

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

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
4. Eseguire `SetupApplications.ps1` e indicare i parametri TenantId, ClusterName e WebApplicationReplyUrl. Ad esempio: 

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

È possibile trovare il TenantId eseguendo il comando di PowerShell `Get-AzureSubscription`. Eseguendo questo comando, viene visualizzato il TenantId per ogni sottoscrizione.

ClusterName viene usato come prefisso per le applicazioni Azure AD create dallo script. Non è necessario che corrisponda esattamente al nome effettivo del cluster. Ha solo lo scopo di facilitare il mapping degli elementi di Azure AD al cluster di Service Fabric con cui vengono usati.

WebApplicationReplyUrl è l'endpoint predefinito che Azure AD restituisce agli utenti dopo che hanno completato l'accesso. Impostare questo endpoint come endpoint di Service Fabric Explorer per il cluster, che per impostazione predefinita è:

https://&lt;cluster_domain&gt;:19080/Explorer

Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant Azure AD. Dopo avere eseguito l'accesso, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Tra le applicazioni del tenant nel [portale di Azure][azure-portal] dovrebbero essere visualizzate due nuove voci:

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

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Creare un modello di Cluster Resource Manager di Service Fabric
Questa sezione è per gli utenti che vogliono creare un modello personalizzato di Resource Manager per cluster di Service Fabric. Dopo avere creato un modello, è comunque possibile usare i moduli di PowerShell o dell'interfaccia della riga di comando per la distribuzione. 

Tra gli [esempi di Azure su GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) sono disponibili alcuni modelli di Resource Manager di esempio. Questi modelli possono essere usati come punto di partenza per il modello del cluster.

### <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager
Questa guida usa il modello di esempio di un [cluster protetto a 5 nodi][service-fabric-secure-cluster-5-node-1-nodetype] e i relativi parametri. Scaricare `azuredeploy.json` e `azuredeploy.parameters.json` sul computer e aprire entrambi i file in un editor di testo.

### <a name="add-certificates"></a>Aggiungere certificati
I certificati vengono aggiunti a un modello di Resource Manager per cluster facendo riferimento all'insieme di credenziali delle chiavi che contiene le chiavi del certificato. Aggiungere i parametri e i valori dell'insieme di credenziali delle chiavi in un file di parametri del modello di Resource Manager (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Aggiungere tutti i certificati all'elemento osProfile del set di scalabilità di macchine virtuali
Ogni certificato che viene installato nel cluster deve essere configurato nella sezione osProfile della risorsa del set di scalabilità (Microsoft.Compute/virtualMachineScaleSets). Questa azione indica al provider di risorse di installare il certificato nelle macchine virtuali, includendo sia il certificato del cluster che eventuali certificati di sicurezza dell'applicazione che si intende usare per le applicazioni:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
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

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Aggiungere le informazioni sul certificato alla risorsa del set di scalabilità di macchine virtuali:
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
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

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Aggiungere le informazioni sul certificato alla risorsa del cluster di Service Fabric:
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
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

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Aggiungere la configurazione di Azure AD per l'uso di Azure AD per l'accesso client

La configurazione di Azure AD viene aggiunta a un modello di Resource Manager per cluster facendo riferimento all'insieme di credenziali delle chiavi contenente le chiavi del certificato. Aggiungere i parametri e i valori di Azure AD in un file di parametri del modello di Resource Manager (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
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

### <a name="populate-the-parameter-file-with-the-values"></a>Compilare il file dei parametri con i valori
Infine, usare i valori di output dei comandi di PowerShell per Azure AD e dell'insieme di credenziali delle chiavi per compilare il file dei parametri:

Se si prevede di usare i moduli RM di PowerShell di Azure Service Fabric, non occorre compilare le informazioni sul certificato del cluster; se si vuole che il certificato autofirmato per la protezione del cluster venga generato automaticamente dal sistema, lasciarle Null. 

> [!NOTE]
> Se si vuole che i moduli RM selezionino e compilino i valori dei parametri vuoti, i nomi dei parametri devono corrispondere a quelli riportati di seguito.
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Se si usano certificati dell'applicazione o un cluster esistente caricato nell'insieme di credenziali delle chiavi, è necessario ottenere queste informazioni e specificarle nel file 

I moduli RM non hanno la possibilità di generare automaticamente la configurazione di Azure AD. Pertanto, se si prevede di usare Azure AD per l'accesso client, è necessario compilarlo.

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

### <a name="test-your-template"></a>Testare il modello  
Usare il comando PowerShell seguente per testare il modello di Resource Manager con un file di parametri:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Nel caso in cui si verifichino problemi e vengano visualizzati messaggi di difficile interpretazione, usare "-Debug" come opzione.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Il diagramma seguente illustra i punti in cui la configurazione dell'insieme di credenziali delle chiavi e di Azure AD rientra nel modello di Resource Manager.

![Mappa di dipendenza di Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Creare il cluster usando il modello di risorsa di Azure 

È ora possibile distribuire il cluster attenendosi alla procedura descritta in precedenza in questo documento o, se il file di parametri contiene valori, è possibile creare il cluster usando direttamente la [distribuzione del modello di risorsa di Azure][resource-group-template-deploy].

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Nel caso in cui si verifichino problemi e vengano visualizzati messaggi di difficile interpretazione, usare "-Debug" come opzione.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Assegnare utenti ai ruoli
Dopo aver creato le applicazioni per rappresentare il cluster, assegnare gli utenti ai ruoli supportati da Service Fabric: sola lettura e amministratore. È possibile assegnare i ruoli usando il [portale di Azure][azure-portal].

1. Nel portale di Azure selezionare il tenant nell'angolo in alto a destra.

    ![Pulsante per la selezione del tenant][select-tenant-button]
2. Selezionare **Azure Active Directory** nella scheda a sinistra e quindi selezionare "Applicazioni aziendali".
3. Selezionare "Tutte le applicazioni" e quindi individuare e selezionare l'applicazione Web, che ha un nome analogo a `myTestCluster_Cluster`.
4. Fare clic sulla scheda **Utenti e gruppi**.

    ![Scheda Utenti e gruppi][users-and-groups-tab]
5. Fare clic sul pulsante **Aggiungi utente** nella nuova pagina, selezionare un utente e il ruolo da assegnare e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della pagina.

    ![Pagina di assegnazione di utenti ai ruoli][assign-users-to-roles-page]
6. Fare clic sul pulsante **Assegna** nella parte inferiore della pagina.

    ![Conferma dell'aggiunta di un'assegnazione][assign-users-to-roles-confirm]

> [!NOTE]
> Per altre informazioni sui ruoli in Service Fabric, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Risoluzione dei problemi di configurazione di Azure Active Directory
La configurazione di Azure AD e il suo uso possono comportare difficoltà, pertanto di seguito verranno fornite alcune indicazioni per risolvere gli eventuali problemi.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer richiede di selezionare un certificato
#### <a name="problem"></a>Problema
Dopo avere eseguito l'accesso ad Azure AD in Service Fabric Explorer, il browser torna alla home page, ma un messaggio chiede di selezionare un certificato.

![Finestra di dialogo del certificato di SFX][sfx-select-certificate-dialog]

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
Selezionare "Registrazioni per l'app" nella pagina di Azure Active Directory, selezionare l'applicazione cluster e quindi fare clic sul pulsante **URL di risposta**. Nella pagina URL di risposta aggiungere l'URL di Service Fabric Explorer all'elenco o sostituire una delle voci dell'elenco. Al termine, salvare la modifica.

![URL di risposta dell'applicazione Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Connettere il cluster usando l'autenticazione di Azure AD tramite PowerShell
Per connettere il cluster di Service Fabric, usare il comando di PowerShell di esempio seguente:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Per informazioni sul cmdlet Connect-ServiceFabricCluster, vedere [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>È possibile usare di nuovo lo stesso tenant di Azure AD in più cluster?
Sì. Ricordarsi però di aggiungere l'URL di Service Fabric Explorer all'applicazione cluster (Web). In caso contrario, Service Fabric Explorer non funzionerà.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Perché è ancora necessario un certificato del server se Azure AD è abilitato?
FabricClient e FabricGateway eseguono un'autenticazione reciproca. Durante l'autenticazione di Azure AD, l'integrazione di Azure AD consente di assegnare un'identità del client al server e il certificato del server viene usato per verificare l'identità del server. Per altre informazioni sui certificati di Service Fabric, vedere [Certificati X.509 e Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Passaggi successivi
A questo punto, è stato creato un cluster con Azure Active Directory che fornisce l'autenticazione per la gestione. Successivamente, [connettersi al cluster](service-fabric-connect-to-secure-cluster.md) e scoprire come [gestire i segreti delle applicazioni](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

