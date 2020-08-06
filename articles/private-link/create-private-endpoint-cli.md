---
title: Avvio rapido - Creare un endpoint privato di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni sull'endpoint privato di Azure - Avvio rapido
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502665"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Avvio rapido: Creare un endpoint privato con l'interfaccia della riga di comando di Azure

Un endpoint privato è il blocco predefinito fondamentale per il servizio Collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali, di comunicare privatamente con risorse Collegamento privato. Questa guida di avvio rapido illustrerà come creare una macchina virtuale in una rete virtuale e un server in database SQL con un endpoint privato tramite l'interfaccia della riga di comando di Azure. Sarà quindi possibile accedere alla macchina virtuale e accedere in modo sicuro alla risorsa Collegamento privato (in questo esempio, un server di database SQL).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare le risorse, è prima necessario creare un gruppo di risorse in cui ospitare la rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella località *westcentralus*:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
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

## <a name="create-a-server-in-sql-database"></a>Creare un server nel database SQL

Creare un server nel database SQL con il comando az sql server create. Tenere presente che il nome del server deve essere univoco in Azure, quindi sostituire il valore del segnaposto tra parentesi acute con il valore univoco personalizzato:

```azurecli-interactive
# Create a server in the resource group
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

L'ID del ID è simile a  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` L'ID del server verrà usato nel passaggio successivo.

## <a name="create-the-private-endpoint"></a>Creare l'endpoint privato

Creare un endpoint privato per il server SQL logico nella rete virtuale:

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato

Creare una zona DNS privato per il dominio del database SQL, quindi creare un collegamento di associazione con la rete virtuale e creare un gruppo di zone DNS per associare l'endpoint privato con la zona DNS privato. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

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

## <a name="access-sql-database-privately-from-the-vm"></a>Accedere al database SQL privatamente dalla macchina virtuale

In questa sezione si stabilirà la connessione al database SQL dalla macchina virtuale tramite l'endpoint privato.

1. Nel Desktop remoto di *myVm1* aprire PowerShell.
2. Immettere nslookup myserver.database.windows.net

   Verrà visualizzato un messaggio simile al seguente:

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. Installare SQL Server Management Studio
4. In Connetti al server immettere o selezionare queste informazioni:

   - Tipo di server: selezionare Motore di database.
   - Nome server: Selezionare myserver.database.windows.net
   - Nome utente: immettere il nome utente specificato durante la creazione.
   - Password: immettere la password specificata durante la creazione.
   - Memorizza password: selezionare Sì.

5. Selezionare **Connetti**.
6. Esplorare i **database** dal menu a sinistra.
7. Creare o eseguire query sulle informazioni di *mydatabase* (passaggio facoltativo)
8. Chiudere la connessione Desktop remoto a *myVm*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare il comando az group delete per rimuovere il gruppo di risorse e tutte le risorse in esso contenute:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Collegamento privato di Azure](private-link-overview.md)
