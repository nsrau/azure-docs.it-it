---
title: Creare un endpoint privato di Azure usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni sull'endpoint privato di Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 46c08f48efc00c1e4d88ceccb680c0cfd1671b2f
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203571"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Creare un endpoint privato usando l'interfaccia della riga di comando
L'endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse di collegamento privato. In questa Guida introduttiva si apprenderà come creare una VM in una rete virtuale, un server di database SQL con un endpoint privato usando l'interfaccia della riga di comando di Azure. Quindi, è possibile accedere alla macchina virtuale e accedere in modo sicuro alla risorsa di collegamento privato (un server di database SQL di Azure privato in questo esempio). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare una risorsa, è necessario creare un gruppo di risorse per ospitare la rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella località *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale con [AZ Network VNET create](/cli/azure/network/vnet). Questo esempio crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *Subnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Disabilitare i criteri dell'endpoint privato della subnet 
Azure distribuisce le risorse in una subnet all'interno di una rete virtuale, pertanto è necessario creare o aggiornare la subnet per disabilitare i criteri di rete degli endpoint privati. Aggiornare una configurazione di subnet denominata * subnet * * con [AZ Network VNET subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Creare la VM 
Creare una macchina virtuale con AZ VM create. Quando richiesto, specificare una password da usare come credenziali di accesso per la macchina virtuale. Questo esempio crea una macchina virtuale denominata *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Prendere nota dell'indirizzo IP pubblico della macchina virtuale. Questo indirizzo verrà usato per connettersi alla VM da Internet nel passaggio successivo.

## <a name="create-a-sql-database-server"></a>Creare un server di database SQL 
Creare un server di database SQL con il comando AZ SQL Server create. Tenere presente che il nome del SQL Server deve essere univoco in Azure, quindi sostituire il valore del segnaposto tra parentesi quadre con il proprio valore univoco: 

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

Si noti che l'ID del SQL Server ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` è simile a quello che verrà SQL Server usato nel passaggio successivo. 

## <a name="create-the-private-endpoint"></a>Creare l'endpoint privato 
Creare un endpoint privato per il server di database SQL nella rete virtuale: 
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
## <a name="configure-the-private-dns-zone"></a>Configurare la zona di DNS privato 
Creare una zona DNS privato per il dominio del server di database SQL e creare un collegamento di associazione con la rete virtuale. 
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

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione RDP scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Scegliere **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-dql-database-server-privately-from-the-vm"></a>Accedere al server di database DQL privatamente dalla macchina virtuale

In questa sezione si effettuerà la connessione al server di database SQL dalla macchina virtuale usando l'endpoint privato.

 1. Nel Desktop remoto di *myVM*aprire PowerShell.
 2. Immettere nslookup MyServer.database.Windows.NET  verrà visualizzato un messaggio simile al seguente: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Installa SQL Server Management Studio 
 4. In Connetti al server immettere o selezionare queste informazioni: Tipo di server: Selezionare motore di database.
 Nome server: Selezionare il nome utente myserver.database.windows.net: Immettere un nome utente specificato durante la creazione.
 Password: Immettere una password specificata durante la creazione.
 Ricorda password: Selezionare Sì.
 
 5. Selezionare **Connetti**.
 6. Esplorare i **database** dal menu a sinistra.
 7. Facoltativamente Creare o eseguire query sulle informazioni da *database*
 8. Chiudere la connessione Desktop remoto a *myVm*.

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non è più necessario, è possibile usare il comando AZ Group Delete per rimuovere il gruppo di risorse e tutte le risorse disponibili: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passaggi successivi
- Scopri di più sul [collegamento privato di Azure](private-link-overview.md)
 
