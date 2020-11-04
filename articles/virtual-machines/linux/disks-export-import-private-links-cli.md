---
title: Interfaccia della riga di comando di Azure - Limitare l'accesso di importazione/esportazione ai dischi gestiti con collegamenti privati
description: Abilitare collegamenti privati per i dischi gestiti con l'interfaccia della riga di comando di Azure. Consente di esportare e importare in modo sicuro i dischi solo all'interno della rete virtuale.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 20b2f74b33ae57016188ee66af29570f7ec1b529
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289269"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Interfaccia della riga di comando di Azure: limitare l'accesso di importazione/esportazione per i dischi gestiti con collegamenti privati

È possibile usare [endpoint privati](../../private-link/private-endpoint-overview.md) per limitare l'esportazione e l'importazione di dischi gestiti e accedere in modo sicuro ai dati tramite un [collegamento privato](../../private-link/private-link-overview.md) dai client nella rete virtuale di Azure. L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per il servizio Managed Disks. Il traffico di rete tra i client nella rete virtuale e i dischi gestiti passa soltanto attraverso la rete virtuale e un collegamento privato sulla rete backbone Microsoft, eliminando l'esposizione alla rete Internet pubblica.

Per usare i Collegamenti privati per esportare/importare i dischi gestiti, creare prima di tutto una risorsa di accesso al disco e collegarla a una rete virtuale nella stessa sottoscrizione creando un endpoint privato. Quindi, associare un disco o uno snapshot a un'istanza di accesso al disco. Infine, impostare la proprietà NetworkAccessPolicy del disco o dello snapshot su `AllowPrivate`. In questo modo si limiterà l'accesso alla rete virtuale. 

È possibile impostare la proprietà NetworkAccessPolicy su `DenyAll` per impedire a chiunque di esportare i dati di un disco o uno snapshot. Il valore predefinito per la proprietà NetworkAccessPolicy è `AllowAll`.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Accedere alla sottoscrizione e impostare le variabili

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Creare un accesso al disco usando l'interfaccia della riga di comando
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Creare una rete virtuale

I criteri di rete come i gruppi di sicurezza di rete (NSG) non sono supportati per gli endpoint privati. Per distribuire un endpoint privato in una determinata subnet, è necessaria un'impostazione di disabilitazione esplicita in tale subnet. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Disabilitare i criteri per gli endpoint privati della subnet

Azure distribuisce le risorse in una subnet all'interno di una rete virtuale, di conseguenza è necessario aggiornare la subnet per disabilitare i criteri di rete per gli endpoint privati. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Creare un endpoint privato per l'oggetto accesso al disco

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato

Creare una zona DNS privato per il dominio del BLOB del servizio di archiviazione, quindi creare un collegamento di associazione con la rete virtuale e creare un gruppo di zone DNS per associare l'endpoint privato con la zona DNS privato. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Creare un disco protetto con i collegamenti privati
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Creare uno snapshot di un disco protetto con i collegamenti privati
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti sui collegamenti privati](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Esportare/copiare snapshot gestiti come disco rigido virtuale in un account di archiviazione di un'area diversa con l'interfaccia della riga di comando](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md)
