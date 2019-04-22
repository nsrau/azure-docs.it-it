---
title: Creare un cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare un cluster di Service Fabric protetto in Azure usando Azure Resource Manager.  È possibile creare un cluster usando un modello predefinito o il proprio modello di cluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 52623183139be2b8ac6b12d3adca64e72de932d3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050323"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Creare un cluster di Service Fabric usando Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portale di Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Un [cluster di Azure Service Fabric](service-fabric-deploy-anywhere.md) è un set di macchine virtuali connesse in rete, in cui vengono distribuiti e gestiti i microservizi.  Un cluster di Service Fabric in esecuzione in Azure è una risorsa di Azure e viene distribuito tramite Azure Resource Manager. Questo articolo descrive come distribuire un cluster di Service Fabric protetto in Azure usando Resource Manager. È possibile usare un modello di cluster predefinito o un modello personalizzato.  Se non si ha un modello personalizzato, consultare le [informazioni su come crearne uno](service-fabric-cluster-creation-create-template.md).

La sicurezza del cluster viene configurata durante la prima configurazione del cluster e non può essere modificata in un secondo momento. Prima di configurare un cluster, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security]. In Azure, Service Fabric usa un certificato x509 per proteggere il cluster e i relativi endpoint, autenticare i client e crittografare i dati. Per proteggere l'accesso agli endpoint di gestione, è anche consigliabile usare Azure Active Directory. Prima di creare il cluster, è necessario creare i tenant e gli utenti di Azure AD.  Per altre informazioni, vedere [Configurare Azure AD per autenticare i client](service-fabric-cluster-creation-setup-aad.md).

Per la creazione di un cluster di produzione per l'esecuzione di carichi di lavoro di produzione, si consiglia di leggere l'[Elenco di controllo per l'idoneità per la produzione](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti 
In questo articolo, è possibile usare powershell Resource Manager di Service Fabric o moduli dell’interfaccia della riga di comando di Azure per distribuire un cluster:

* [Azure PowerShell 4.1 e versioni successive][azure-powershell]
* [Interfaccia della riga di comando di Azure versione 2.0 e versioni successive][azure-CLI]

È possibile trovare la documentazione di riferimento per i moduli di Service Fabric di seguito:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [modulo della riga di comando az SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Accedi ad Azure

Prima di eseguire i comandi di questo articolo, accedere ad Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Creare un nuovo cluster con un certificato autofirmato generato dal sistema

Usare i comandi seguenti per creare un cluster protetto con un certificato autofirmato generato dal sistema. Questo comando imposta un certificato cluster primario che viene usato per la protezione del cluster e per configurare l'accesso amministratore per l'esecuzione di operazioni di gestione tramite il certificato.  I certificati autofirmati sono utili per la protezione dei cluster di test.  I cluster di produzione devono essere protetti con un certificato di un'entità di certificazione (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Usare il modello di cluster fornito nel modulo

Usare il comando seguente per creare un cluster rapidamente, specificando i parametri minimi usando il modello predefinito.

Il modello utilizzato è disponibile tra gli [esempi di modelli di Azure Service Fabric: modello di Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e nel [modello Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Il comando seguente può creare sia cluster Windows che cluster Linux, è necessario specificare il sistema operativo in base alle esigenze. I comandi di PowerShell e dell'interfaccia della riga di comando restituiscono inoltre il certificato nella cartella *CertificateOutputFolder* specificata, tuttavia assicurarsi che la cartella del certificato sia già stata creata. Il comando accetta anche altri parametri come VM SKU.

> [!NOTE]
> Il comando PowerShell seguente funziona solo con Azure PowerShell `Az` modulo. Per controllare la versione corrente di PowerShell per Azure Resource Manager, eseguire il comando seguente di PowerShell "Get-Module Az". Per aggiornare la versione di PowerShell per Azure Resource Manager, seguire [questo collegamento](/powershell/azure/install-Az-ps). 
>
>

Distribuire il cluster usando PowerShell:

```powershell
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

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Distribuire il cluster usando l’interfaccia della riga di comando di Azure:

```azurecli
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

### <a name="use-your-own-custom-template"></a>Usare il proprio modello personalizzato

Se è necessario creare un modello personalizzato specifico per le proprie esigenze, è consigliabile iniziare con uno di quelli disponibili tra gli [esempi di modelli di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Informazioni su come [personalizzare il modello di cluster][customize-your-cluster-template].

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri correlati al certificato nel modello e il file dei parametri siano denominati come segue e che i valori siano Null come indicato di seguito:

```json
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

Distribuire il cluster usando PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Distribuire il cluster usando l’interfaccia della riga di comando di Azure:

```azurecli
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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Creare un nuovo cluster usando il proprio certificato X.509

Usare il comando seguente per creare il cluster, se si dispone già di un certificato da usare per la protezione del cluster.

Se si tratta di un certificato firmato da un'autorità di certificazione utilizzabile anche per altri scopi, è consigliabile fornire un gruppo di risorse distinto specifico per l'insieme di credenziali delle chiavi. È consigliabile inserire l'insieme di credenziali delle chiavi in un proprio gruppo di risorse. Questa azione consente di rimuovere i gruppi di risorse di calcolo e di archiviazione, incluso il gruppo di risorse contenente il cluster di Service Fabric, senza perdere le chiavi e i segreti. **Il gruppo di risorse che contiene l'insieme di credenziali delle chiavi *deve essere situato nella stessa area* del cluster che lo usa.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Usare il modello predefinito a 5 nodi e 1 tipo di nodo fornito nel modulo
Il modello usato è disponibile negli [esempi di Azure: modello di Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e nel [modello Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Distribuire il cluster usando PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Distribuire il cluster usando l’interfaccia della riga di comando di Azure:

```azurecli
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

### <a name="use-your-own-custom-cluster-template"></a>Usare il proprio modello di cluster personalizzato
Se è necessario creare un modello personalizzato specifico per le proprie esigenze, è consigliabile iniziare con uno di quelli disponibili tra gli [esempi di modelli di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Informazioni su come [personalizzare il modello di cluster][customize-your-cluster-template].

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri correlati al certificato nel modello e il file dei parametri siano denominati come segue e che i valori siano Null come indicato di seguito.

```json
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

Distribuire il cluster usando PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Distribuire il cluster usando l’interfaccia della riga di comando di Azure:

```azurecli
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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Usare un puntatore a un segreto caricato in un insieme di credenziali delle chiavi

Per usare un insieme di credenziali delle chiavi esistente, è necessario [abilitarlo per la distribuzione](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) per consentire al provider di risorse di calcolo di ottenere i certificati e installarli nei nodi del cluster.

Distribuire il cluster usando PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Distribuire il cluster usando l’interfaccia della riga di comando di Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, si dispone di un cluster sicuro in esecuzione in Azure. Successivamente, [connettersi al cluster](service-fabric-connect-to-secure-cluster.md) e scoprire come [gestire i segreti delle applicazioni](service-fabric-application-secret-management.md).

Per la sintassi e le proprietà JSON da usare in un modello, vedere [Microsoft.ServiceFabric/clusters template reference](/azure/templates/microsoft.servicefabric/clusters) (Informazioni di riferimento sul modello di cluster Microsoft.ServiceFabric).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
