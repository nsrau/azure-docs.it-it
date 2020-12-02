---
title: Usare il generatore di immagini di Azure per macchine virtuali Linux consentendo l'accesso a una VNET di Azure esistente (anteprima)
description: Creare immagini di VM Linux con Azure Image Builder per consentire l'accesso a una VNET di Azure esistente
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: cbff2358dcf8685298e31f18c430c35ec7de4948
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437338"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Usare il generatore di immagini di Azure per macchine virtuali Linux consentendo l'accesso a una VNET di Azure esistente

Questo articolo illustra come usare Azure Image Builder per creare un'immagine Linux personalizzata di base che ha accesso alle risorse esistenti in un VNET. La macchina virtuale di compilazione creata viene distribuita in un VNET nuovo o esistente specificato nella sottoscrizione. Quando si usa una VNET di Azure esistente, il servizio Generatore di immagini di Azure non richiede la connettività di rete pubblica.

> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrare le funzionalità

Per prima cosa, è necessario registrarsi per il servizio Generatore di immagini di Azure. La registrazione concede al servizio l'autorizzazione per creare, gestire ed eliminare un gruppo di risorse di staging. Il servizio dispone inoltre dei diritti per aggiungere risorse al gruppo necessario per la compilazione dell'immagine.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Impostare variabili e autorizzazioni 

Verranno utilizzate più volte le informazioni. Creare alcune variabili per archiviare tali informazioni.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Creare il gruppo di risorse.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Configurare le impostazioni di rete

Se non si dispone di un VNET\Subnet\NSG esistente, usare lo script seguente per crearne uno.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Aggiungere una regola del gruppo di sicurezza di rete

Questa regola consente la connettività dal servizio di bilanciamento del carico di Azure Image Builder alla VM del proxy. La porta 60001 è per OSs Linux e la porta 60000 è per i sistemi operativi Windows. La VM proxy si connette alla macchina virtuale di compilazione usando la porta 22 per OSs Linux o la porta 5986 per OSs di Windows.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Disabilitare i criteri del servizio privato nella subnet

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Per altre informazioni sulle funzionalità di rete di Image Builder, vedere [Opzioni di rete del servizio Azure Image Builder](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Modificare il modello di esempio e creare un ruolo

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Impostare le autorizzazioni per il gruppo di risorse

Image Builder userà l'[identità utente](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) specificata per inserire l'immagine nella raccolta immagini condivise di Azure. In questo esempio si creerà una definizione di ruolo di Azure che include azioni granulari per eseguire la distribuzione dell'immagine nella raccolta immagini condivise. La definizione del ruolo verrà quindi assegnata all'identità utente.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Anziché concedere a Image Builder una granularità più bassa e privilegi più elevati, è possibile creare due ruoli. Uno fornisce al generatore le autorizzazioni per creare un'immagine, l'altra consente di connettere la macchina virtuale di compilazione e il servizio di bilanciamento del carico a VNET.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Per altre informazioni sulle autorizzazioni, vedere Configurare le autorizzazioni del [servizio Generatore di immagini di Azure con l'interfaccia](image-builder-permissions-cli.md) della riga di comando di Azure o [configurare le autorizzazioni del servizio Azure Image Builder con PowerShell](image-builder-permissions-powershell.md)

## <a name="create-the-image"></a>Creare l'immagine

Inviare la configurazione dell'immagine al servizio Image Builder di Azure.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Avviare la compilazione dell'immagine.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

La creazione dell'immagine e la sua replica in entrambe le aree possono richiedere un certo tempo. Attendere il completamento di questa parte prima di procedere alla creazione di una macchina virtuale.


## <a name="create-the-vm"></a>Creare la VM

Creare una macchina virtuale dalla versione dell'immagine creata da Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Accedere tramite SSH alla macchina virtuale.

```bash
ssh aibuser@<publicIpAddress>
```

Si noterà che l'immagine viene personalizzata con un *messaggio del giorno* non appena viene stabilita la connessione SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole provare a ripersonalizzare la versione dell'immagine per creare una nuova versione della stessa immagine, ignorare i passaggi successivi e passare a [usare il generatore di immagini di Azure per creare un'altra versione](image-builder-gallery-update-image-version.md)dell'immagine.


Nell'esempio seguente viene eliminata l'immagine creata insieme a tutti gli altri file di risorse. Assicurarsi che la distribuzione sia stata completata prima di eliminare le risorse.

Quando si eliminano risorse della raccolta immagini, è necessario eliminare tutte le versioni dell'immagine prima di poter eliminare la definizione immagine usata per creare le risorse. Per eliminare una raccolta, è prima di tutto necessario eliminare tutte le definizioni immagine nella raccolta.

Eliminare il modello di generatore di immagini.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Eliminare assegnazioni di autorizzazioni, ruoli e identità
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Eliminare il gruppo di risorse.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Se è stato creato un VNET per questa Guida introduttiva, è possibile eliminare il VNET se non è più usato.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [raccolte di immagini condivise di Azure](shared-image-galleries.md).