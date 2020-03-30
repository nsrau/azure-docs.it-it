---
title: Collegamento privato - interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL - Singolo serverPrivate Link - Azure CLI - Azure Database for PostgreSQL - Single server
description: Informazioni su come configurare il collegamento privato per il database di Azure per PostgreSQL- Singolo server dall'interfaccia della riga di comando di AzureLearn how to configure private link for Azure Database for PostgreSQL- Single server from Azure CLI
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: d4288b901a0e6e132e32f8391d108e79861fc331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371036"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Creare e gestire Private Link for Azure Database for PostgreSQL - Singolo server tramite l'interfaccia della riga di comandoCreate and manage Private Link for Azure Database for PostgreSQL - Single server using CLI

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. In questo articolo si apprenderà come usare l'interfaccia della riga di comando di Azure per creare una macchina virtuale in una rete virtuale di Azure e un database di Azure per PostgreSQL singolo server con un endpoint privato di Azure.In this article, you will learn how to use the Azure CLI to create a VM in an Azure Virtual Network and an Azure Database for PostgreSQL Single server with an Azure private endpoint.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Database di Azure per PostgreSQL: singolo server supporta i livelli dei prezzi Scopo generale e Ottimizzato per la memoria.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- [Server e database di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare le risorse, è prima necessario creare un gruppo di risorse in cui ospitare la rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). In questo esempio viene creato un gruppo di risorse denominato myResourceGroup nella posizione *westeurope:This* example creates a resource group named *myResourceGroup* in the westeurope location:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
Creare una rete virtuale con il comando [az network vnet create](/cli/azure/network/vnet). Questo esempio crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Disabilitare i criteri per gli endpoint privati della subnet 
Azure distribuisce le risorse in una subnet all'interno di una rete virtuale, di conseguenza è necessario creare o aggiornare la subnet per disabilitare i criteri di rete per gli endpoint privati. Aggiornare una configurazione di subnet denominata *mySubnet* con [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Creare la VM 
Creare una macchina virtuale con il comando az vm create. Quando richiesto, specificare la password da usare come credenziali di accesso per la macchina virtuale. Questo esempio crea una macchina virtuale denominata *myVM*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Prendere nota dell'indirizzo IP pubblico della macchina virtuale. Questo indirizzo verrà usato per connettersi alla VM da Internet nel passaggio successivo.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Creare un database di Azure per PostgreSQL - Server singoloCreate an Azure Database for PostgreSQL - Single server 
Creare un database di Azure per PostgreSQL con il comando az postgres server create. Tenere presente che il nome di PostgreSQL Server deve essere univoco in Azure, quindi sostituire il valore segnaposto tra parentesi con il proprio valore univoco: 

```azurecli-interactive
# Create a logical server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Si noti che l'ID ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/servername.``` PostgreSQL Server è simile a Si utilizzerà l'ID PostgreSQL Server nel passaggio successivo. 

## <a name="create-the-private-endpoint"></a>Creare l'endpoint privato 
Creare un endpoint privato per il server PostgreSQL nella rete virtuale:Create a private endpoint for the PostgreSQL server in your Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<PostgreSQL Server ID>" \  
    --group-ids postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato 
Creare una zona DNS privata per il dominio server PostgreSQL e creare un collegamento di associazione con la rete virtuale. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Il nome di dominio completo nell'impostazione DNS del cliente non viene risolto nell'IP privato configurato. È necessario configurare una zona DNS per il nome di dominio completo configurato, come illustrato [di seguito.](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file *downloaded.rdp*.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un account diverso**per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Accedere al server PostgreSQL privatamente dalla macchina virtualeAccess the PostgreSQL server privately from the VM

1. Nel desktop remoto di  *myVM* aprire PowerShell.

2. Immettere  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Testare la connessione di collegamento privato per il server PostgreSQL utilizzando qualsiasi client disponibile. Nell'esempio seguente ho usato [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) per eseguire l'operazione.

4. In **Nuova connessione**immettere o selezionare le informazioni seguente:

    | Impostazione | valore |
    | ------- | ----- |
    | Tipo di server| Selezionare **PostgreSQL**.|
    | Nome server| Selezionare *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Nome utente | Immettere username@servername username come fornito durante la creazione del server PostgreSQL. |
    |Password |Immettere una password fornita durante la creazione del server PostgreSQL. |
    |SSL|Selezionare **Obbligatorio**.|
    ||

5. Selezionare Connetti.

6. Esplorare i database dal menu a sinistra.

7. (Opzionalmente) Creare o eseguire query sulle informazioni dal server postgreSQL.

8. Chiudere la connessione desktop remoto a myVm.

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non sono più necessari, è possibile usare il comando az group delete per rimuovere il gruppo di risorse e tutte le risorse in esso contenute: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni [sull'endpoint privato](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) di AzureLearn more about What is Azure private endpoint
