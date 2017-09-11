---
title: Selezionare immagini di macchine virtuali Linux con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per determinare il server di pubblicazione, l'offerta, la SKU e la versione delle immagini di macchine virtuali del Marketplace.
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: e0c27a7ee9e9a7ab1a3b004e070fa556b56a36a5
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Come trovare immagini di macchine virtuali Linux in Azure Marketplace con l'interfaccia della riga di comando di Azure
Questo argomento descrive come usare l'interfaccia della riga di comando di Azure 2.0 per trovare immagini di VM (Virtual Machine, macchina virtuale) in Azure Marketplace. Usare queste informazioni per specificare un'immagine del Marketplace quando si crea una VM Linux.

Assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e di avere effettuato l'accesso a un account di Azure (`az login`).

## <a name="terminology"></a>Terminologia

Le immagini in Marketplace vengono identificate nell'interfaccia della riga di comando e altri strumenti di Azure in base a una gerarchia:

* **Server di pubblicazione**: un'organizzazione che ha creato l'immagine. Esempio: Canonical
* **Offerta**: un gruppo di immagini correlate create da un server di pubblicazione. Esempio: server Ubuntu
* **SKU**: un'istanza di un'offerta, ad esempio una versione principale di una distribuzione. Esempio: 16.04-LTS
* **Versione**: il numero di versione di un'immagine SKU. Quando si specifica l'immagine, è possibile sostituire il numero di versione con "latest", che seleziona la versione più recente della distribuzione.

Per specificare un'immagine in Marketplace, si usa in genere l'immagine *URN*. L'URN combina tali valori, separati dal carattere due punti (:): *Server di pubblicazione*:*Offerta*:*SKU*:*Versione*. 


## <a name="list-popular-images"></a>Elencare immagini popolari

Eseguire il comando [az vm image list](/cli/azure/vm/image#list), senza l'opzione `--all`, per visualizzare un elenco di immagini di VM popolari in Azure Marketplace. Ad esempio, eseguire il comando seguente per visualizzare un elenco memorizzato nella cache di immagini popolari in formato tabella:

```azurecli
az vm image list --output table
```

L'output include l'URN (il valore nella colonna *Urn*), che consente di specificare l'immagine. Se si vuole creare una VM con tali immagini popolari in Marketplace, è possibile specificare in alternativa l'alias URN, ad esempio *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Trovare immagini specifiche

Per trovare un'immagine di VM specifica in Marketplace, usare il comando `az vm image list` con l'opzione `--all`. Questa versione del comando richiede del tempo per essere completata e può restituire un output lungo, pertanto l'elenco si filtra in genere in base a `--publisher` o a un altro parametro. 

Ad esempio, il comando che segue visualizza tutte le offerte Debian. Tenere presente che senza l'opzione `--all`, la ricerca viene eseguita solo nella cache locale delle immagini comuni:

```azurecli
az vm image list --offer Debian --all --output table 

```

Output parziale: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

Applicare filtri simili con le opzioni `--location`, `--publisher` e `--sku`. È anche possibile cercare corrispondenze parziali in base a un filtro, ad esempio cercare `--offer Deb` per trovare tutte le immagini Debian.

Se non si indica una posizione specifica con l'opzione `--location`, per impostazione predefinita vengono restituiti i valori relativi a `westus`. Per impostare un percorso predefinito diverso eseguire `az configure --defaults location=<location>`.

Ad esempio, il comando seguente elenca elencati tutti gli SKU di Debian 8 in `westeurope`:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Output parziale:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>Esplorare le immagini 
Un altro modo per trovare un'immagine in una posizione è l'esecuzione in sequenza dei comandi [az vm image list-publishers](/cli/azure/vm/image#list-publishers), [az vm image list-offers](/cli/azure/vm/image#list-offers) e [az vm image list-skus](/cli/azure/vm/image#list-skus) . Con questi comandi si determinano questi valori:

1. Elencando gli editori di immagini.
2. Elencando le offerte di un determinato editore.
3. Elencando le SKU di una determinata offerta.


Ad esempio, il comando seguente elenca i server di pubblicazione di immagini nella posizione Stati Uniti occidentali (westus):

```azurecli
az vm image list-publishers --location westus --output table
```

Output parziale:

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Usare queste informazioni per individuare le offerte di un server di pubblicazione specifico. Ad esempio, se Canonical è un server di pubblicazione di immagini nella posizione Stati Uniti occidentali, è possibile trovare le relative offerte eseguendo `azure vm image list-offers`. Passare la posizione e il server di pubblicazione come nell'esempio seguente:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Output:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Come si può vedere, nell'area degli Stati Uniti occidentali Canonical pubblica l'offerta **UbuntuServer** in Azure. Ma quali sono le SKU? Per ottenere questi valori, eseguire `azure vm image list-skus` e impostare il percorso, il server di pubblicazione e l'offerta individuati:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Output:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Usare infine il comando `az vm image list` per trovare una versione specifica della SKU voluta, ad esempio, **16.04-LTS**:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Output:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```
## <a name="next-steps"></a>Passaggi successivi
A questo punto è possibile scegliere con precisione l'immagine da usare prendendo nota del valore URN. Passare questo valore con il parametro `--image` quando si crea una macchina virtuale con il [az vm create](/cli/azure/vm#create). Facoltativamente, è possibile sostituire il numero di versione nell'URN con "latest", che rappresenta sempre la versione più recente della distribuzione. Per creare rapidamente una macchina virtuale usando le informazioni relative all'URN, vedere [Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](tutorial-manage-vm.md).

