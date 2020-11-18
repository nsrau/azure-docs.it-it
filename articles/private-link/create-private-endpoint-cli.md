---
title: "Avvio rapido: Creare un endpoint privato di Azure con l'interfaccia della riga di comando di Azure"
description: Questa guida di avvio rapido illustra come creare un endpoint privato con l'interfaccia della riga di comando di Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: bba912930a9dff0a79e0b0d81025b7524c238db0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368679"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Avvio rapido: Creare un endpoint privato con l'interfaccia della riga di comando di Azure

Introduzione al collegamento privato di Azure, che consente di connettersi in modo sicuro a un'app Web di Azure tramite un endpoint privato.

In questa guida di avvio rapido si creerà un endpoint privato per un'app Web di Azure e si distribuirà una macchina virtuale per testare la connessione privata.  

È possibile creare endpoint privati per diversi tipi di servizi di Azure, ad esempio Azure SQL e Archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un'app Web di Azure con un piano di servizio app **PremiumV2-tier** o superiore distribuita nella sottoscrizione di Azure.  
    * Per altre informazioni e un esempio, vedere [Avvio rapido: Creare un'app Web ASP.NET Core in Azure](../app-service/quickstart-dotnetcore.md). 
    * Per un'esercitazione dettagliata sulla creazione di un'app Web e di un endpoint, vedere [Esercitazione: Connettersi a un'app Web con un endpoint privato di Azure](tutorial-private-endpoint-webapp-portal.md).
* Accedere al portale di Azure e verificare che la sottoscrizione sia attiva eseguendo `az login`.
* Controllare la versione dell'interfaccia della riga di comando di Azure in una finestra terminale o di comando eseguendo `az --version`. Per la versione più recente, vedere le [ultime note sulla versione](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se non si dispone della versione più recente, aggiornare l'installazione seguendo la [guida all'installazione per il sistema operativo o la piattaforma in uso](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](/cli/azure/group#az_group_create) un gruppo di risorse:

* Assegnare il nome **CreatePrivateEndpointQS-rg**. 
* Nella posizione **eastus**.

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Creare una rete virtuale e un host bastion

In questa sezione si creeranno una rete virtuale, una subnet e un host bastion. 

L'host bastion verrà usato per connettersi in modo sicuro alla macchina virtuale per testare l'endpoint privato.

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Denominata **myVNet**.
* Prefisso indirizzo **10.0.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.0.0.0/24**.
* Nel gruppo di risorse **CreatePrivateEndpointQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Aggiornare la subnet per disabilitare i criteri di rete per l'endpoint privato con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico per l'host bastion:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myBastionIP**.
* In **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Usare [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) per creare una subnet bastion:

* Denominata **AzureBastionSubnet**.
* Prefisso indirizzo **10.0.1.0/24**.
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Usare [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) per creare un host bastion:

* **myBastionHost** denominato.
* In **CreatePrivateEndpointQS-rg**.
* Associato all'IP pubblico **myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella località **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
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
* In **CreatePrivateEndpointQS-rg**.
* Nella rete **myVNet**.
* Nella subnet **myBackendSubnet**.
* Immagine del server **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>Creare un endpoint privato

In questa sezione si creerà l'endpoint privato.

Usare [az webapp list](/cli/azure/webapp#az_webapp_list) per inserire l'ID risorsa dell'app Web creata in precedenza in una variabile della shell.

Usare [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) per creare l'endpoint e la connessione:

* Endpoint denominato **myPrivateEndpoint**.
* Nel gruppo di risorse **CreatePrivateEndpointQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Connessione denominata **myConnection**.
* L'app Web **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato

In questa sezione si creerà e si configurerà la zona DNS privato con il comando [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Si userà [az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) per creare il collegamento della rete virtuale alla zona DNS.

Si creerà un gruppo di zone DNS con [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zona denominata **privatelink.azurewebsites.net**
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreatePrivateEndpointQS-rg**.
* Collegamento a DNS denominato **myDNSLink**.
* Associato a **myPrivateEndpoint**.
* Gruppo di zone denominato **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Testare la connettività con l'endpoint privato

In questa sezione si userà la macchina virtuale creata nel passaggio precedente per connettersi al server SQL tramite l'endpoint privato.

1. Accedere al [portale di Azure](https://portal.azure.com) 
 
2. Selezionare **Gruppi di risorse** nel riquadro di spostamento sinistro.

3. Selezionare **CreatePrivateEndpointQS-rg**.

4. Selezionare **myVM**.

5. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

6. Selezionare il pulsante blu **Usa Bastion**.

7. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

8. Aprire Windows PowerShell nel server dopo la connessione.

9. Immettere `nslookup <your-webapp-name>.azurewebsites.net`. Sostituire **\<your-webapp-name>** con il nome dell'app Web creata nei passaggi precedenti.  Si riceverà un messaggio simile al seguente:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Per il nome dell'app Web viene restituito l'indirizzo IP privato **10.0.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.

10. Nella connessione bastion a **myVM** aprire Internet Explorer.

11. Immettere l'URL dell'app Web **https://\<your-webapp-name>.azurewebsites.net**.

12. Se l'applicazione non è stata distribuita, si riceverà la pagina dell'app Web predefinita:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Pagina dell'app Web predefinita." border="true":::

13. Chiudere la connessione a **myVM**.

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non si ha più bisogno dell'endpoint privato e della macchina virtuale, usare [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse e tutte le risorse che contiene:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:

* Una rete virtuale e un host bastion.
* Una macchina virtuale.
* Un endpoint privato per un'app Web di Azure.

La macchina virtuale è stata usata per testare la connettività in modo sicuro all'app Web tramite l'endpoint privato.

Per altre informazioni sui servizi che supportano un endpoint privato, vedere:
> [!div class="nextstepaction"]
> [Disponibilità del collegamento privato](private-link-overview.md#availability)
