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
ms.openlocfilehash: 251f7fc99f1c8d79f31118df11b7522930903c25
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Creare un cluster di Service Fabric usando Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure portal](service-fabric-cluster-creation-via-portal.md)
>
>

Questo articolo contiene una guida dettagliata che illustra la configurazione di un cluster di Azure Service Fabric sicuro in Azure tramite Azure Resource Manager. Anche se l'articolo è lungo, a meno che non si conosca già a fondo il contenuto, assicurarsi di seguire con attenzione ogni passaggio.

La guida illustra le procedure seguenti:

* Concetti principali che è necessario essere a conoscenza off prima di distribuire un cluster di service fabric.
* Creazione di un cluster in Azure utilizzando moduli di gestione delle risorse dell'infrastruttura del servizio.
* Configurazione di Azure Active Directory (Azure AD) per l'autenticazione degli utenti che eseguono operazioni di gestione nel cluster.
* Creazione di un modello personalizzato di gestione risorse di Azure per il cluster e la distribuzione.

## <a name="key-concepts-to-be-aware-of"></a>Concetti principali da tenere presenti
In Azure, Service fabric impone che è possibile utilizzare un x509 certificato per proteggere il cluster e dei relativi endpoint. I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni. Per i client di accesso o le attività di gestione nel cluster, inclusi la distribuzione, aggiornamento ed eliminazione di applicazioni, servizi e i dati in che essi contenuti, è possibile utilizzare certificati o le credenziali di Azure Active Directory. L'utilizzo di Azure Active Directory è vivamente consigliata, poiché è l'unico modo per impedire la condivisione dei certificati nei client.  Per altre informazioni sull'uso dei certificati in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security].

Service Fabric usa certificati X.509 per proteggere un cluster e fornire le funzionalità di sicurezza dell'applicazione. Utilizzare [insieme di credenziali chiave] [ key-vault-get-started] per gestire i certificati per i cluster di Service Fabric in Azure. 


### <a name="cluster-and-server-certificate-required"></a>Cluster e certificato del server (obbligatorio)
Questi certificati (una primaria e, facoltativamente, un database secondario) necessari per proteggere un cluster e prevenire accessi non autorizzati. Il certificato fornisce protezione del cluster in due modi:

* **Autenticazione del cluster:** autentica la comunicazione da nodo a nodo per la federazione di cluster. Solo i nodi che possono dimostrare la propria identità con il certificato possono essere aggiunti al cluster.
* **L'autenticazione del server:** autentica gli endpoint di gestione di cluster a un client di gestione, in modo che il client di gestione sa stia comunicando con il cluster reale e non un "man in the middle". Questo certificato fornisce anche un certificato SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.

A tale scopo, il certificato deve soddisfare i requisiti seguenti:

* Il certificato deve includere una chiave privata. Questi certificati sono in genere con estensione pfx estensioni o PEM  
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il **nome soggetto del certificato deve corrispondere il dominio utilizzato per accedere al cluster di Service Fabric**. Questo tipo di associazione è necessario specificare SSL per il cluster endpoint di gestione HTTPS e Service Fabric Explorer. È possibile ottenere un certificato SSL da un'autorità di certificazione (CA) per il *. dominio cloudapp.azure.com. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Configurare Azure Active Directory per l'autenticazione client (facoltativo ma consigliato)

Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso utenti alle applicazioni. Alcune applicazioni sono caratterizzate da un'interfaccia utente di accesso basata sul Web, altre invece da un'esperienza client nativa. In questo articolo si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory][active-directory-howto-tenant].

I cluster di Service Fabric offrono numerosi punti di ingresso alle relative funzionalità di gestione, tra cui [Service Fabric Explorer][service-fabric-visualizing-your-cluster], basato sul Web, e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Verranno quindi create due applicazioni Azure AD per controllare l'accesso al cluster, un'applicazione Web e un'applicazione nativa.

Altre informazioni su come configurarlo in un secondo momento nel documento.

### <a name="application-certificates-optional"></a>Certificati delle applicazioni (facoltativo)
Per motivi di sicurezza dell'applicazione, è possibile installare nel cluster numerosi certificati aggiuntivi. Prima di creare il cluster, considerare gli scenari di protezione delle applicazioni che richiedono l'installazione di un certificato sui nodi, ad esempio:

* Crittografia e decrittografia dei valori di configurazione dell'applicazione.
* Crittografia dei dati tra i nodi durante la replica.

Il concetto di creazione di cluster sicuri è lo stesso per i cluster sia Linux che Windows. 

### <a name="client-authentication-certificates-optional"></a>Certificati di autenticazione client (facoltativi)
Per le operazioni client amministratore o utente, è possibile specificare qualsiasi numero di certificati aggiuntivi. Per impostazione predefinita il certificato del cluster ha privilegi di amministratore client. Questi certificati client aggiuntivo non devono essere installati nel cluster, deve essere specificato come consentite nella configurazione del cluster, tuttavia è necessario che sia installato nel computer client per connettersi al cluster ed eseguire qualsiasi sistema di gestione operazioni.


## <a name="prerequisites"></a>Prerequisiti 
Il concetto di creazione di cluster sicuri è lo stesso per i cluster sia Linux che Windows. Questa guida illustra l'uso di azure powershell o CLI di azure per creare nuovi cluster. I prerequisiti sono 

-  [Azure PowerShell 4.1 e versioni successive] [ azure-powershell] o [CLI di Azure 2.0 e versioni successive][azure-CLI].
-  è possibile trovare informazioni dettagliate su, i moduli di fabric di service [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) e [modulo CLI az SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Usare il modulo di fabric RM servizio per distribuire un cluster

In questo documento, si utilizzerebbe di powershell di service fabric RM e modulo CLI per distribuire un cluster, di powershell o il comando modulo CLI consente di più scenari. Segnalare il problema passare tramite ognuno di essi il. Scegliere lo scenario che si ritiene migliore soddisfa le proprie esigenze. 

- Creare un nuovo cluster: utilizzo di un sistema generato certificato autofirmato
    - Utilizzare un modello di cluster predefinito
    - Utilizzare un modello che si dispone già di
- Creare un nuovo cluster, utilizzando un certificato che già in proprio possesso
    - Utilizzare un modello di cluster predefinito
    - Utilizzare un modello che si dispone già di

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Creare il nuovo cluster: utilizza un sistema generato certificato autofirmato

Utilizzare il comando seguente per creare cluster, nel caso desidera che il sistema per generare un certificato autofirmato e usarlo per proteggere il cluster. Questo comando imposta un certificato di cluster primario utilizzato per la sicurezza del cluster e per configurare l'accesso di amministratore per eseguire operazioni di gestione tramite il certificato.

### <a name="login-in-to-azure"></a>Accedi ad Azure.

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Utilizzare il modello di nodetype 1 nodo valore predefinito 5 fornito nel modulo per configurare il cluster

Usare il comando seguente per creare un cluster rapidamente, specificando i parametri minimi

Modello utilizzato è disponibile nel [esempi di modelli di azure service fabric: modello di windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modello Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

I comandi riportati di seguito può essere usato per la creazione di cluster di Windows e Linux, è sufficiente specificare il sistema operativo, di conseguenza. Il comando powershell / comandi CLI restituisce inoltre il certificato del certificato nel theCertificateOutputFolder specificato in. Il comando accetta anche altri parametri come VM SKU.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!1"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Utilizzare il modello personalizzato che si dispone già di 

Se è necessario creare un modello personalizzato in base alle esigenze, è consigliabile iniziare con uno dei modelli disponibili nel [esempi di modelli di azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Attenersi alla Guida e le spiegazioni di [personalizzare il modello di cluster] [ customize-your-cluster-template] sezione riportata di seguito.

Se è già disponibile un modello personalizzato, quindi verificare che per verificare che tutti i tre certificati correlati i parametri nel modello e il file dei parametri sono denominati come segue e i valori sono null come indicato di seguito.

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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Ecco il comando CLI equivalente per eseguire la stessa. Modificare i valori nelle istruzioni declare ai valori appropriati. L'interfaccia CLI supporta tutti gli altri parametri che supporta il comando powershell riportato sopra.

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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Crea nuovo cluster, utilizzando il certificato è stato acquistato da un'autorità di certificazione o si dispone già di.

Utilizzare il comando seguente per creare cluster, se si dispone di un certificato che si desidera utilizzare per proteggere il cluster con.

Se si tratta di un certificato CA firmato che si finirà con per altri scopi oltre, è consigliabile fornire un gruppo di risorse distinto in particolare di credenziali delle chiavi. È consigliabile inserire l'insieme di credenziali delle chiavi in un proprio gruppo di risorse. Questa azione consente di rimuovere i gruppi di risorse di calcolo e di archiviazione, incluso il gruppo di risorse contenente il cluster di Service Fabric, senza perdere le chiavi e i segreti. **Il gruppo di risorse che contiene l'insieme di credenziali chiave _deve essere nella stessa area_ del cluster di cui viene utilizzato.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Utilizzare il modello di nodetype 1 nodo valore predefinito 5 fornito nel modulo
Modello utilizzato è disponibile nel [esempi di azure: modello di windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modello Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!1" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

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

#### <a name="use-the-custom-template-that-you-have"></a>Utilizzare il modello personalizzato che è necessario 
Se è necessario creare un modello personalizzato in base alle esigenze, è consigliabile iniziare con uno dei modelli disponibili nel [esempi di modelli di azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Attenersi alla Guida e le spiegazioni di [personalizzare il modello di cluster] [ customize-your-cluster-template] sezione riportata di seguito.

Se è già disponibile un modello personalizzato, quindi verificare che per verificare che tutti i tre certificati correlati i parametri nel modello e il file dei parametri sono denominati come segue e i valori sono null come indicato di seguito.

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


```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword #certPassword

```

Ecco il comando CLI equivalente per eseguire la stessa. Modificare i valori nelle istruzioni declare ai valori appropriati.

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

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-keyvault"></a>Usare un puntatore per il segreto che è già stato caricato nel keyvault

Per usare un insieme di credenziali delle chiavi esistente, è _necessario abilitarlo per la distribuzione_ per consentire al provider di risorse di calcolo di ottenere i certificati e installarli nei nodi del cluster:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretID -TemplateFile $templateFile -ParameterFile $templateParmfile 

```
Ecco il comando CLI equivalente per eseguire la stessa. Modificare i valori nelle istruzioni declare ai valori appropriati.

```cli

declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifieraz $secretID  \
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

Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant Azure AD. Dopo avere eseguito l'accesso, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Se si esamina le applicazioni del tenant di [portale di Azure][azure-portal], verranno visualizzate due voci di nuovo:

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
In questa sezione è per gli utenti che desiderano personalizzato creano un modello di gestione risorse di cluster di Service Fabric. Dopo aver creato un modello, è possibile tornare indietro e utilizzare di powershell o i moduli di interfaccia CLI di distribuirlo. 

Sono disponibili in modelli di gestione risorse di esempio di [esempi di Azure su GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Questi modelli possono essere usati come punto di partenza per il modello del cluster.

### <a name="create-the-resource-manager-template"></a>Creare il modello di Azure Resource Manager
Questa guida usa il modello di esempio di un [cluster protetto a 5 nodi][service-fabric-secure-cluster-5-node-1-nodetype] e i relativi parametri. Scaricare `azuredeploy.json` e `azuredeploy.parameters.json` sul computer e aprire entrambi i file in un editor di testo.

### <a name="add-certificates"></a>Aggiungere certificati
I certificati vengono aggiunti a un modello di Resource Manager per cluster facendo riferimento all'insieme di credenziali delle chiavi che contiene le chiavi del certificato. Aggiungere i parametri di insieme di credenziali chiave e i valori in un file di parametri di modello di gestione risorse (azuredeploy.parameters.json). 

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

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Aggiungere che le informazioni sul certificato di scalabilità della macchina virtuale dell'insieme di risorse:
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

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Aggiungere le informazioni sul certificato per la risorsa cluster di Service Fabric:
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

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Aggiungere la configurazione di Azure AD per l'utilizzo di Azure AD per l'accesso client

Per aggiungere la configurazione di Azure AD s a un modello di gestione risorse di cluster, che fa riferimento la chiave dell'insieme di credenziali contenente le chiavi del certificato. Aggiungere i parametri di Azure AD e i valori in un file di parametri di modello di gestione risorse (azuredeploy.parameters.json).

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

### <a name="populate-the-parameter-file-with-the-values"></a>Compilare il file con i valori dei parametri.
Infine, è possibile usare i valori di output dall'insieme di credenziali delle chiavi e i comandi di powershell di Azure AD per popolare il file dei parametri:

Se si prevede di usare i moduli di powershell Azure service fabric RM, non occorre compilare le informazioni sul certificato del cluster, se si desidera al sistema di generare self firma certificato per la sicurezza del cluster, tenere come null. 

> [!NOTE]
> Per i moduli di RM prelevare e popolare questi valori di parametri vuoto, i nomi dei parametri devono corrispondere i nomi
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

Se si utilizzano i certificati di applicazione o si utilizza un cluster esistente di keyvault caricati, è necessario ottenere queste informazioni e popolarlo 

I moduli di RM non sono la possibilità di geneate la configurazione di Azure AD per l'utente. Pertanto, se si prevede di usare Azure AD per l'accesso client, è necessario compilarlo.

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
Utilizzare il comando PowerShell seguente per testare il modello di gestione delle risorse con un file di parametro:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Nel caso in cui si verifichino problemi e visualizzati messaggi di difficile interpretazione, utilizzare "-Debug" come opzione.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Il diagramma seguente illustra i punti in cui la configurazione dell'insieme di credenziali delle chiavi e di Azure AD rientra nel modello di Resource Manager.

![Mappa di dipendenza di Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Creare il cluster utilizzando il modello di risorse di Azure 

È ora possibile distribuire cluster utilizzando la procedura descritta in precedenza nel documento o se sono disponibili i valori nel file di parametri, popolato, si è ora pronto per creare il cluster usando [la distribuzione dei modelli di risorse di Azure] [ resource-group-template-deploy] direttamente.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Nel caso in cui si verifichino problemi e visualizzati messaggi di difficile interpretazione, utilizzare "-Debug" come opzione.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Assegnare utenti ai ruoli
Dopo aver creato le applicazioni per rappresentare il cluster, assegnare gli utenti ai ruoli supportati da Service Fabric: sola lettura e amministratore. È possibile assegnare i ruoli utilizzando la [portale di Azure][azure-portal].

1. Nel portale di Azure, selezionare il tenant nell'angolo superiore destro.

    ![Selezionare il pulsante di tenant][select-tenant-button]
2. Selezionare **Azure Active Directory** nel riquadro a sinistra e quindi selezionare "applicazioni aziendali".
3. Selezionare "Tutte le applicazioni", quindi individuare e selezionare l'applicazione web, che ha un nome come `myTestCluster_Cluster`.
4. Fare clic su di **utenti e gruppi** scheda.

    ![Scheda gruppi e utenti][users-and-groups-tab]
5. Fare clic su di **Aggiungi utente** pulsante nella nuova pagina, selezionare un utente e il ruolo da assegnare e quindi fare clic su di **selezionare** nella parte inferiore della pagina.

    ![Assegnare utenti a pagina ruoli][assign-users-to-roles-page]
6. Fare clic su di **assegnare** nella parte inferiore della pagina.

    ![Aggiungere una conferma di assegnazione][assign-users-to-roles-confirm]

> [!NOTE]
> Per altre informazioni sui ruoli in Service Fabric, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Risoluzione dei problemi nella configurazione di Azure Active Directory
Configurazione di Azure AD e utilizzandolo, può essere difficile o modificate, in modo di seguito sono elencati alcuni puntatori alle operazioni eseguite per eseguire il debug del problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer richiede di selezionare un certificato
#### <a name="problem"></a>Problema
Dopo avere eseguito l'accesso ad Azure AD in Service Fabric Explorer, il browser torna alla home page, ma un messaggio chiede di selezionare un certificato.

![Finestra di dialogo certificato SFX][sfx-select-certificate-dialog]

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
Selezionare "Registrazioni di App" nella pagina AAD, selezionare l'applicazione del cluster e quindi selezionare il **gli URL di risposta** pulsante. Nella pagina "URL di risposta", aggiungere all'elenco di URL di Service Fabric Explorer o sostituire uno degli elementi nell'elenco. Al termine, salvare la modifica.

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

## <a name="next-steps"></a>Passaggi successivi
A questo punto, è stato creato un cluster con Azure Active Directory che fornisce l'autenticazione per la gestione. Successivamente, [connettersi al cluster](service-fabric-connect-to-secure-cluster.md) e scoprire come [gestire i segreti delle applicazioni](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
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

