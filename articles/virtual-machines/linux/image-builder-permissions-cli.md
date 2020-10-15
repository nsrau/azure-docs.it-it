---
title: Configurare le autorizzazioni del servizio Azure Image Builder usando l'interfaccia della riga di comando
description: Configurare i requisiti per il servizio Generatore di immagini di VM di Azure, inclusi i privilegi e le autorizzazioni
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 58bbe01c8de0bbe606f4fc428032cd213f05d386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88068240"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Configurare le autorizzazioni del servizio Azure Image Builder usando l'interfaccia della riga di comando

Il servizio Generatore di immagini di Azure richiede la configurazione di autorizzazioni e privilegi prima di creare un'immagine. Le sezioni seguenti illustrano in dettaglio come configurare possibili scenari usando l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrare le funzionalità

Per prima cosa, è necessario registrarsi per il servizio Generatore di immagini di Azure. La registrazione concede al servizio l'autorizzazione per creare, gestire ed eliminare un gruppo di risorse di staging. Il servizio dispone inoltre dei diritti per aggiungere risorse al gruppo necessario per la compilazione dell'immagine.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente di Azure

Azure Image Builder richiede la creazione di un' [identità gestita assegnata dall'utente di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Azure Image Builder usa l'identità gestita assegnata dall'utente per leggere immagini, scrivere immagini e accedere agli account di archiviazione di Azure. Si concede l'autorizzazione di identità per eseguire azioni specifiche nella sottoscrizione.

> [!NOTE]
> In precedenza, generatore di immagini di Azure usava il nome dell'entità servizio (SPN) di Azure Image Builder per concedere le autorizzazioni ai gruppi di risorse immagine. L'utilizzo del nome SPN verrà deprecato. Usare invece un'identità gestita assegnata dall'utente.

L'esempio seguente illustra come creare un'identità gestita assegnata dall'utente di Azure. Sostituire le impostazioni segnaposto per impostare le variabili.

| Impostazione | Descrizione |
|---------|-------------|
| \<Resource group\> | Gruppo di risorse in cui creare l'identità gestita assegnata dall'utente. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Per altre informazioni sulle identità assegnate dall'utente di Azure, vedere la documentazione di [identità gestita assegnata dall'utente di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) per informazioni su come creare un'identità.

## <a name="allow-image-builder-to-distribute-images"></a>Consenti a Generatore di immagini di distribuire immagini

Per consentire a Generatore immagini di Azure di distribuire immagini (immagini gestite/raccolta immagini condivise), il servizio Generatore di immagini di Azure deve essere autorizzato a inserire le immagini in questi gruppi di risorse. Per concedere le autorizzazioni necessarie, è necessario creare un'identità gestita assegnata dall'utente e concedere i diritti per il gruppo di risorse in cui viene compilata l'immagine. Il generatore di immagini di **Azure non è** autorizzato ad accedere alle risorse di altri gruppi di risorse nella sottoscrizione. È necessario eseguire azioni esplicite per consentire l'accesso per evitare che le compilazioni abbiano esito negativo.

Non è necessario concedere i diritti di collaboratore identità gestita assegnato dall'utente nel gruppo di risorse per distribuire le immagini. Tuttavia, l'identità gestita assegnata dall'utente deve disporre delle seguenti `Actions` autorizzazioni di Azure nel gruppo di risorse di distribuzione:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Se si esegue la distribuzione in una raccolta di immagini condivise, è necessario anche:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Autorizzazione per personalizzare le immagini esistenti

Per consentire a Generatore immagini di Azure di creare immagini da immagini personalizzate di origine (immagini gestite/raccolta immagini condivise), è necessario che il servizio Azure Image Builder possa leggere le immagini in questi gruppi di risorse. Per concedere le autorizzazioni necessarie, è necessario creare un'identità gestita assegnata dall'utente e concedere i diritti per il gruppo di risorse in cui si trova l'immagine.

Compilare da un'immagine personalizzata esistente:

```Actions
Microsoft.Compute/galleries/read
```

Compilare da una versione della raccolta immagini condivisa esistente:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Autorizzazione per personalizzare le immagini nel reti virtuali

Il generatore di immagini di Azure è in grado di distribuire e usare un VNET esistente nella sottoscrizione, consentendo così alle personalizzazioni di accedere alle risorse connesse.

Non è necessario concedere i diritti di collaboratore identità gestite assegnati dall'utente nel gruppo di risorse per distribuire una macchina virtuale in una VNET esistente. Tuttavia, l'identità gestita assegnata dall'utente deve disporre delle seguenti `Actions` autorizzazioni di Azure per il gruppo di risorse VNET:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Creare una definizione di ruolo di Azure

Gli esempi seguenti creano una definizione di ruolo di Azure dalle azioni descritte nelle sezioni precedenti. Gli esempi vengono applicati a livello di gruppo di risorse. Valutare e verificare se gli esempi sono sufficientemente granulari per le proprie esigenze. Per lo scenario in uso, potrebbe essere necessario affinarlo in una raccolta di immagini condivise specifica.

Le azioni dell'immagine consentono le operazioni di lettura e scrittura. Decidere cosa è appropriato per l'ambiente in uso. Ad esempio, creare un ruolo per consentire a Generatore di immagini di Azure di leggere immagini dal gruppo di risorse *esempio-RG-1* e scrivere immagini nell'esempio di gruppo di risorse *-RG-2*.

### <a name="custom-image-azure-role-example"></a>Esempio di ruolo di Azure per immagini personalizzate

Nell'esempio seguente viene creato un ruolo di Azure per l'uso e la distribuzione di un'immagine personalizzata di origine. Si concede quindi il ruolo personalizzato all'identità gestita assegnata dall'utente per il generatore di immagini di Azure.

Per semplificare la sostituzione dei valori nell'esempio, impostare prima le variabili seguenti. Sostituire le impostazioni segnaposto per impostare le variabili.

| Impostazione | Descrizione |
|---------|-------------|
| \<Subscription ID\> | L'ID sottoscrizione di Azure |
| \<Resource group\> | Gruppo di risorse per l'immagine personalizzata |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Esempio di ruolo di Azure VNET esistente

Nell'esempio seguente viene creato un ruolo di Azure per l'uso e la distribuzione di un'immagine VNET esistente. Si concede quindi il ruolo personalizzato all'identità gestita assegnata dall'utente per il generatore di immagini di Azure.

Per semplificare la sostituzione dei valori nell'esempio, impostare prima le variabili seguenti. Sostituire le impostazioni segnaposto per impostare le variabili.

| Impostazione | Descrizione |
|---------|-------------|
| \<Subscription ID\> | L'ID sottoscrizione di Azure |
| \<Resource group\> | Gruppo di risorse VNET |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Uso dell'identità gestita per l'accesso ad archiviazione di Azure

Se si vuole facilmente l'autenticazione con archiviazione di Azure e si usano contenitori privati, il generatore di immagini di Azure per Azure necessita di un'identità gestita assegnata dall'utente. Azure Image Builder usa l'identità per l'autenticazione con archiviazione di Azure.

> [!NOTE]
> Azure Image Builder usa solo l'identità al momento dell'invio del modello di immagine. La macchina virtuale di compilazione non ha accesso all'identità durante la compilazione dell'immagine.

Usare l'interfaccia della riga di comando di Azure per creare l'identità gestita assegnata dall'utente.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

Nel modello Image Builder è necessario fornire l'identità gestita assegnata dall'utente.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Sostituire le seguenti impostazioni segnaposto:

| Impostazione | Descrizione |
|---------|-------------|
| \<Region\> | Area del modello |
| \<Resource group\> | Resource group |
| \<Storage account container\> | Nome del contenitore dell'account di archiviazione |
| \<Subscription ID\> | Sottoscrizione di Azure |

Per altre informazioni sull'uso di un'identità gestita assegnata dall'utente, vedere la pagina relativa alla [creazione di un'immagine personalizzata che userà un'identità gestita di azure User-Assigned per accedere ai file di archiviazione di Azure in facilmente](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage). Questa Guida introduttiva illustra come creare e configurare l'identità gestita assegnata dall'utente per accedere a un account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Panoramica di Azure Image Builder](image-builder-overview.md).