---
title: Create an Azure private endpoint using Azure CLI| Microsoft Docs
description: Learn about Azure private endpoint
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 467b2426ccd69a27adc9df7ee3ff0304886b8f4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224856"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Create a private endpoint using Azure CLI
Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link Resources. In this Quickstart, you will learn how to create a VM on a virtual network, a SQL Database Server with a Private Endpoint using Azure CLI. Then, you can access the VM to and securely access the private link resource (a private Azure SQL Database server in this example). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Before you can create any resource, you have to create a resource group to host the Virtual Network. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). This example creates a resource group named *myResourceGroup* in the *westcentralus* location:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
Create a Virtual Network with [az network vnet create](/cli/azure/network/vnet). This example creates a default Virtual Network named *myVirtualNetwork* with one subnet named *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Disable subnet private endpoint policies 
Azure deploys resources to a subnet within a virtual network, so you need to create or update the subnet to disable private endpoint network policies. Update a subnet configuration named *mySubnet* with [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Creare la VM 
Create a VM with az vm create. When prompted, provide a password to be used as the sign-in credentials for the VM. This example creates a VM named *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Note the public IP address of the VM. Questo indirizzo verrà usato per connettersi alla VM da Internet nel passaggio successivo.

## <a name="create-a-sql-database-server"></a>Create a SQL Database Server 
Create a SQL Database Server with the az sql server create command. Remember that the name of your SQL Server must be unique across Azure, so replace the placeholder value in brackets with your own unique value: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Note the SQL Server ID is similar to ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` You will use the SQL Server ID in the next step. 

## <a name="create-the-private-endpoint"></a>Create the Private Endpoint 
Create a private endpoint for the SQL Database server in your Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Configure the Private DNS Zone 
Create a Private DNS Zone for SQL Database server domain and create an association link with the Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connect to the VM *myVm* from the internet as follows:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione rdp scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server privately from the VM

In this section, you will connect to the SQL Database Server from the VM using the Private Endpoint.

 1. In the Remote Desktop of *myVM*, open PowerShell.
 2. Enter nslookup myserver.database.windows.net  You'll receive a message similar to this: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Install SQL Server Management Studio 
 4. In Connect to server, enter or select this information: Server type: Select Database Engine.
 Server name: Select myserver.database.windows.net Username: Enter a username provided during creation.
 Password: Enter a password provided during creation.
 Remember password: Select Yes.
 
 5. Selezionare **Connessione**.
 6. Browse **Databases** from left menu.
 7. (Optionally) Create or query information from *mydatabase*
 8. Close the remote desktop connection to *myVm*.

## <a name="clean-up-resources"></a>Pulire le risorse 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passaggi successivi
- Learn more about [Azure Private Link](private-link-overview.md)
 
