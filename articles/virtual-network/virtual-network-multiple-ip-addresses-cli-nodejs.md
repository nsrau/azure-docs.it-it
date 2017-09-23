---
title: "Creare una VM con più indirizzi IP usando l'interfaccia della riga di comando 1.0 di Azure | Microsoft Docs"
description: "Informazioni su come assegnare più indirizzi IP a una macchina virtuale usando l'interfaccia della riga di comando 1.0 di Azure | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.contentlocale: it-it
ms.lasthandoff: 03/28/2017

---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Assegnare più indirizzi IP alle macchine virtuali usando l'interfaccia della riga di comando 1.0 di Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Questo articolo spiega come creare una macchina virtuale (VM) tramite il modello di distribuzione Azure Resource Manager usando l'interfaccia della riga di comando 1.0 di Azure. Non è possibile a assegnare più indirizzi IP alle risorse create tramite il modello di distribuzione classica. Per altre informazioni sui modelli di distribuzione di Azure, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Creare una macchina virtuale con più indirizzi IP

È possibile completare questa attività usando l'interfaccia della riga di comando di Azure 1.0 (questo articolo) o l'[interfaccia della riga di comando di Azure 2.0](virtual-network-multiple-ip-addresses-cli.md). La procedura seguente illustra come creare una macchina virtuale di esempio con più indirizzi IP, come descritto nello scenario. Modificare i nomi delle variabili e i tipi di indirizzi IP come richiesto per l'implementazione.

1. Per installare e configurare l'interfaccia della riga di comando di Azure 1.0, seguire la procedura riportata nell'articolo [Installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e accedere all'account Azure con il comando `azure-login`.

2. Creare un gruppo di risorse:
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. Creare una rete virtuale:

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. Creare una subnet nella rete virtuale:

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. Creare un account di archiviazione per la macchina virtuale. Prima di eseguire il comando seguente, sostituire *mystorageaccount* con un nome univoco. Il nome deve essere univoco in Azure:

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. Creare le configurazioni IP, una scheda di interfaccia di rete e assegnare le configurazioni IP alla scheda creata. È possibile aggiungere, rimuovere o modificare le configurazioni in base alle esigenze. Nello scenario vengono descritte le configurazioni seguenti:

    **IPConfig-1**

    Immettere i comandi seguenti per creare:

    - Una risorsa indirizzo IP pubblico con un indirizzo IP pubblico statico
    - Una scheda di interfaccia di rete alla quale assegnare l'indirizzo IP pubblico e un indirizzo IP privato.
    
    Sostituire *mypublicdns* con un nome che sia univoco nella località di Azure.

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits).

    **IPConfig-2**

     Immettere i comandi seguenti per creare una nuova risorsa indirizzo IP pubblico e una nuova configurazione IP con un indirizzo IP pubblico e un indirizzo IP privato statico:
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    Immettere i comandi seguenti per creare una configurazione IP con un indirizzo IP privato statico e nessun indirizzo IP pubblico:

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >Sebbene questo articolo assegna tutte le configurazioni IP a una singola scheda di interfaccia di rete, è possibile anche assegnare più configurazioni IP a qualsiasi scheda di interfaccia di rete in una macchina virtuale. Per informazioni su come creare una VM con più interfacce di rete, leggere l'articolo Creare una macchina virtuale con più schede di interfaccia di rete usando PowerShell.

7. Creare una macchina virtuale Linux 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    Per modificare le dimensioni della macchina virtuale alla versione 2 DS2 Standard, ad esempio, è sufficiente aggiungere la seguente proprietà `--vm-size Standard_DS3_v2` al comando `azure vm create` nel passaggio 6.

8. Immettere il comando seguente per visualizzare la scheda di interfaccia di rete e le configurazioni IP associate:

    ```azurecli
    azure network nic show --resource-group $RgName --name myNic1
    ```
9. Aggiungere gli indirizzi IP privati al sistema operativo della macchina virtuale seguendo la procedura per il proprio sistema operativo riportata nella sezione [Aggiungere indirizzi IP a una macchina virtuale](#os-config) di questo articolo.

## <a name="add"></a>Aggiungere indirizzi IP a una macchina virtuale

È possibile aggiungere indirizzi IP privati e pubblici a una scheda di interfaccia di rete esistente completando la procedura seguente. Gli esempi si basano sullo [scenario](#Scenario) descritto in questo articolo.

1. Aprire l'interfaccia della riga di comando di Azure e completare i passaggi rimanenti in questa sezione all'interno di una singola sessione dell'interfaccia della riga di comando. Se l'interfaccia della riga di comando di Azure non è installata e configurata, completare la procedura riportata nell'articolo [Installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e accedere all'account Azure.

2. Completare i passaggi in una delle sezioni seguenti, a seconda delle esigenze:

    - **Aggiungere un indirizzo IP privato**
    
        Per aggiungere un indirizzo IP privato a una scheda di interfaccia di rete, è necessario creare una configurazione IP tramite il comando seguente. L'indirizzo statico deve essere un indirizzo non usato per la subnet.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        Creare tutte le configurazioni usando nomi di configurazione univoci e indirizzi IP privati (per le configurazioni con indirizzi IP statici).

    - **Aggiungere un indirizzo IP pubblico**
    
        L'indirizzo IP pubblico viene aggiunto associandolo a una nuova configurazione IP o a una configurazione IP esistente. Completare i passaggi in una delle sezioni che seguono, a seconda del caso.

        > [!NOTE]
        > Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits).
        >

        **Associare la risorsa a una nuova configurazione IP**
    
        Ogni volta che si aggiunge un indirizzo IP pubblico a una nuova configurazione IP, è necessario aggiungere anche un indirizzo IP privato, perché tutte le configurazioni IP devono avere un indirizzo IP privato. È possibile aggiungere una risorsa indirizzo IP pubblico esistente o crearne una nuova. Per crearne una nuova, usare il comando seguente:

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

        Per creare una nuova configurazione IP con un indirizzo IP privato statico e la risorsa indirizzo IP pubblico *myPublicIP3* associata, immettere il comando seguente:

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **Associare la risorsa a una nuova configurazione IP**

        Una risorsa indirizzo IP pubblico può essere associata a una configurazione IP cui non ne sia associata alcuna. È possibile stabilire se una configurazione IP dispone di un indirizzo IP pubblico associato immettendo il comando seguente:

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        Cercare una riga simile a quella che segue per IPConfig-3 nell'output restituito:

        ```         
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        Poiché la colonna **IP pubblico** per *IpConfig-3* è vuota, nessuna risorsa di indirizzo IP pubblico è attualmente associata. È possibile aggiungere una risorsa indirizzo IP pubblico esistente a IpConfig-3 o immettere il comando seguente per crearne una:

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        Immettere il comando seguente per associare la risorsa indirizzo IP pubblico alla configurazione IP esistente denominata *IPConfig-3*:
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. Visualizzare le risorse indirizzo IP privato e indirizzo IP pubblico assegnate alla scheda di interfaccia di rete immettendo il comando seguente:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      L'output restituito è simile al seguente:
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. Aggiungere al sistema operativo della macchina virtuale gli indirizzi IP privati aggiunti alla scheda di interfaccia di rete seguendo le istruzioni disponibili nella sezione [Aggiungere indirizzi IP a una macchina virtuale](#os-config) di questo articolo. Non aggiungere gli indirizzi IP pubblici al sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

