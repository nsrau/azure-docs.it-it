---
title: Selezionare immagini di VM Linux con l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come determinare l&quot;editore, l&quot;offerta e lo SKU per le immagini durante la creazione di una macchina virtuale Linux con il modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1c4e8b5168d15c480d2aea41ddf7570d310e1252
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-find-linux-vm-images-with-the-azure-cli"></a>Come trovare immagini di VM Linux con l'interfaccia della riga di comando di Azure
Questo argomento descrive come trovare editori, offerte, SKU e versioni per ciascuna posizione di distribuzione. 


## <a name="use-azure-cli-20"></a>Usare l'interfaccia della riga di comando 2.0 di Azure

Dopo avere [installato l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), usare il comando `az vm image list` per visualizzare un elenco memorizzato nella cache di immagini di VM comuni. Ad esempio, il comando seguente `az vm image list -o table` visualizza:

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Ricerca di tutte le immagini correnti

Per ottenere l'elenco corrente di tutte le immagini, usare il comando `az vm image list` con l'opzione `--all`. A differenza dei comandi dell'interfaccia della riga di comando di Azure 1.0, il comando `az vm image list --all` restituisce tutte le immagini in **westus** per impostazione predefinita; a meno che non si specifichi un argomento `--location` particolare, il completamento del comando `--all` richiede tempo. Se si prevede di eseguire l'analisi in modo interattivo, usare `az vm image list --all > allImages.json`, che restituisce un elenco di tutte le immagini attualmente disponibili in Azure e le memorizza come file per uso locale. 

È possibile specificare una delle numerose opzioni per limitare la ricerca a uno specifico valore di località, offerta, server di pubblicazione o SKU, se si hanno già in mente uno o più valori. Se non si specifica una località, vengono restituiti i valori per **westus**.

### <a name="find-specific-images"></a>Trovare immagini specifiche

Usare `az vm image list` con un filtro per trovare informazioni specifiche. Ad esempio, di seguito vengono visualizzate le **offerte** disponibili per **Debian**; tenere presente che senza l'opzione `--all`, la ricerca viene eseguita solo nella cache locale delle immagini comuni:

```azurecli
az vm image list --offer Debian -o table --all
```

Il risultato sarà simile al seguente: 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

È possibile eseguire filtri simili per l'opzione **-publisher** e **- sku**. È possibile anche eseguire corrispondenze parziali in un filtro, ad esempio la ricerca di **--offer Deb** per trovare tutte le immagini Debian o **--publisher Micr** per trovare tutte le immagini pubblicate da Microsoft.

Se si conosce il percorso in cui eseguire la distribuzione, è possibile usare i risultati delle ricerche di immagini generali con i comandi `az vm image list-skus`, `az vm image list-offers` e `az vm image list-publishers` per individuare esattamente l'elemento desiderato e dove può essere distribuito. Ad esempio, se dall'esempio precedente si è certi che `credativ` dispone di un'offerta Debian, è possibile usare l'opzione `--location` e altre opzioni per individuare esattamente il risultato desiderato. Nell'esempio seguente si cerca un'immagine di Debian 8 in **westeurope**:

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

L'output è:

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>Usare l'interfaccia della riga di comando di Azure 1.0 

> [!NOTE]
> Questo articolo descrive come individuare e selezionare immagini di macchine virtuali usando un'installazione di Azure PowerShell o dell'interfaccia della riga di comando di Azure 1.0 che supporti il modello di distribuzione Azure Resource Manager. Come prerequisito, cambiare nella modalità Resource Manager. Con l'interfaccia della riga di comando di Azure, immettere tale modalità digitando `azure config mode arm`. 
> 

Il modo più rapido per individuare un'immagine è chiamare il comando `azure vm image list` e passare il percorso, il nome dell'editore (senza distinzione tra maiuscole e minuscole) e, se è nota, un'offerta. Contrariamente a molti elenchi, solitamente lunghi, l'elenco seguente rappresenta un breve esempio, in cui si sa che "Canonical" è un editore per l'offerta "UbuntuServer".

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

La colonna **Urn** rappresenta il formato che verrà passato a `azure vm quick-create`.

Tuttavia, spesso non si conoscono gli elementi disponibili. In questo caso, è possibile spostarsi tra le immagini usando il comando `azure vm image list-publishers` e specificare una località di data center al prompt dei comandi. Ad esempio, di seguito sono elencati tutti gli editori di immagini nel percorso degli Stati Uniti occidentali (per passare l'argomento location, usare caratteri minuscoli e rimuovere gli spazi dai percorsi standard)

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Questi elenchi possono essere piuttosto lunghi; nell'esempio precedente ne viene quindi riportato solo un frammento. Si supponga che Canonical sia un editore di immagini nel percorso degli Stati Uniti occidentali. È possibile individuare le offerte chiamando `azure vm image list-offers` , quindi passare il percorso e l'editore quando richiesto, come nell'esempio seguente:

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

A questo punto, si sa che nell'area degli Stati Uniti occidentali Canonical pubblica l'offerta **UbuntuServer** in Azure. Ma quali sono le SKU? Per ottenere questi valori, chiamare `azure vm image list-skus` e rispondere alla richiesta indicando il percorso, l'editore e l'offerta individuati.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Con queste informazioni è possibile individuare precisamente l'immagine desiderata chiamando la chiamata originale riportata nella parte superiore.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto è possibile scegliere con precisione l'immagine da usare. Per creare rapidamente una macchina virtuale usando le informazioni URN appena trovate o per usare un modello con tali informazioni URN, vedere [Creare una VM Linux usando l'interfaccia della riga di comando di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

