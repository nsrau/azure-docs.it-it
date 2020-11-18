---
title: 'Esercitazione: Connettersi a un server di Azure SQL con un endpoint privato di Azure - Interfaccia della riga di comando di Azure'
description: Questa esercitazione illustra come creare un server di Azure SQL con un endpoint privato usando l'interfaccia della riga di comando di Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.openlocfilehash: d28a3a304a42ec82ff18ad7f5e72518e2b77ec29
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280636"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Esercitazione: Connettersi a un server di Azure SQL con un endpoint privato di Azure - Interfaccia della riga di comando di Azure

Un endpoint privato di Azure è il blocco costitutivo fondamentale del collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con le risorse collegamento privato.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale e un host bastion.
> * Creare una macchina virtuale.
> * Creare un server di Azure SQL e un endpoint privato.
> * Testare la connettività con l'endpoint privato del server SQL.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Accedere al portale di Azure e verificare che la sottoscrizione sia attiva eseguendo `az login`.
* Controllare la versione dell'interfaccia della riga di comando di Azure in una finestra terminale o di comando eseguendo `az --version`. Per la versione più recente, vedere le [ultime note sulla versione](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se non si dispone della versione più recente, aggiornare l'installazione seguendo la [guida all'installazione per il sistema operativo o la piattaforma in uso](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](/cli/azure/group#az_group_create) un gruppo di risorse:

* Denominato **CreateSQLEndpointTutorial-rg**. 
* Nella posizione **eastus**.

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Creare una rete virtuale e un host bastion

In questa sezione si creeranno una rete virtuale, una subnet e un host bastion. 

L'host bastion verrà usato per connettersi in modo sicuro alla macchina virtuale per testare l'endpoint privato.

Creare una rete virtuale con il comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Denominata **myVNet**.
* Prefisso indirizzo **10.0.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.0.0.0/24**.
* Nel gruppo di risorse **CreateSQLEndpointTutorial-rg**.
* Posizione **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Aggiornare la subnet per disabilitare i criteri di rete degli endpoint privati per l'endpoint privato con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico per l'host bastion:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myBastionIP**.
* In **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Usare [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) per creare una subnet Bastion:

* Denominata **AzureBastionSubnet**.
* Prefisso indirizzo **10.0.1.0/24**.
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Usare [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) per creare un host bastion:

* **myBastionHost** denominato.
* In **CreateSQLEndpointTutorial-rg**.
* Associato all'IP pubblico **myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella località **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

## <a name="create-test-virtual-machine"></a>Creare una macchina virtuale di test

In questa sezione si creerà una macchina virtuale che verrà usata per testare l'endpoint privato.

Creare una macchina virtuale con il comando  [az vm create](/cli/azure/vm#az_vm_create). Quando richiesto, specificare la password da usare come credenziali per la macchina virtuale:

* Denominata **myVM**.
* In **CreateSQLEndpointTutorial-rg**.
* Nella rete **myVNet**.
* Nella subnet **myBackendSubnet**.
* Immagine del server **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>Creare un server SQL di Azure

In questa sezione si creerà un server e un database SQL.

Usare [AZ SQL Server create](/cli/azure/sql/server#az_sql_server_create) per creare un server SQL:

* Sostituire **\<sql-server-name>** con il nome del server univoco.
* Sostituire **\<your-password>** con la password.
* In **CreateSQLEndpointTutorial-rg**.
* Nell'area **eastus**.

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Usare [az sql db create](/cli/azure/sql/db#az_sql_db_create) per creare un database:

* Denominato **myDataBase**.
* In **CreateSQLEndpointTutorial-rg**.
* Sostituire **\<sql-server-name>** con il nome del server univoco.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà l'endpoint privato.

Usare [az sql server list](/cli/azure/sql/server#az_sql_server_list) per inserire l'ID risorsa del server SQL in una variabile della shell.

Usare [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) per creare l'endpoint e la connessione:

* Denominato **myPrivateEndpoint**.
* Nel gruppo di risorse **CreateSQLEndpointTutorial-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Connessione denominata **myConnection**.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato

In questa sezione si creerà e si configurerà la zona DNS privata con il comando [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Si userà [az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) per creare il collegamento della rete virtuale alla zona DNS.

Verrà creato un gruppo di zone DNS con [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zona denominata **privatelink.database.windows.net**
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreateSQLEndpointTutorial-rg**.
* Collegamento a DNS denominato **myDNSLink**.
* Associato a **myPrivateEndpoint**.
* Gruppo di zone denominato **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Testare la connettività con l'endpoint privato

In questa sezione si userà la macchina virtuale creata nel passaggio precedente per connettersi al server SQL tramite l'endpoint privato.

1. Accedere al [portale di Azure](https://portal.azure.com) 
 
2. Selezionare **Gruppi di risorse** nel riquadro di spostamento sinistro.

3. Selezionare **CreateSQLEndpointTutorial-rg**.

4. Selezionare **myVM**.

5. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

6. Selezionare il pulsante blu **Usa Bastion**.

7. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

8. Aprire Windows PowerShell nel server dopo la connessione.

9. Immettere `nslookup <sqlserver-name>.database.windows.net`. Sostituire **\<sqlserver-name>** con il nome del server SQL creato nei passaggi precedenti.  Si riceverà un messaggio simile al seguente:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Per il nome del server SQL viene restituito l'indirizzo IP privato **10.0.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.


10. Installare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017&preserve-view=true) in **myVM**.

11. Aprire **SQL Server Management Studio**.

12. In **Connetti a server** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Tipo di server | Selezionare **Motore di database**.|
    | Nome server | Immettere **\<sql-server-name>.database.windows.net** |
    | Autenticazione | Selezionare **Autenticazione di SQL Server**. |
    | Nome utente | Immettere il nome utente specificato durante la creazione del server |
    | Password | Immettere la password specificata durante la creazione del server |
    | Memorizza password | Selezionare **Sì**. |

13. Selezionare **Connetti**.

14. Esplorare i database dal menu a sinistra.

15. (Facoltativo) Creare o eseguire query sulle informazioni di **mysqldatabase**.

16. Chiudere la connessione bastion a **myVM**. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Quando non si ha più bisogno dell'endpoint privato, del server SQL e della macchina virtuale, rimuovere il gruppo di risorse e tutte le risorse che contiene: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati i componenti seguenti:

* Una rete virtuale e un host bastion.
* Una macchina virtuale.
* Un server SQL con un endpoint privato.

La macchina virtuale è stata usata per testare la connettività in modo sicuro al server SQL tramite l'endpoint privato.

Informazioni su come creare un servizio Collegamento privato:
> [!div class="nextstepaction"]
> [Creare un servizio Collegamento privato](create-private-link-service-portal.md)
