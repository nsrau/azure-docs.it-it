---
title: Soluzioni di confidential computing di Azure in macchine virtuali
description: Informazioni sulle soluzioni di confidential computing di Azure in macchine virtuali.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 6e853edf5b7ba756aaedceaf59b1f7d1d7e48b39
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985427"
---
# <a name="solutions-on-azure-virtual-machines"></a>Soluzioni nelle macchine virtuali di Azure

Questo articolo descrive come distribuire le macchine virtuali di confidential computing di Azure che dispongono di processori Intel supportati dall'estensione [Intel Software Guard](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Dimensioni delle macchine virtuali di confidential computing di Azure

Le macchine virtuali di confidential computing di Azure sono progettate per proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud 

Le macchine virtuali della [serie DCsv2](../virtual-machines/dcv2-series.md) sono la famiglia di dimensioni di confidential computing più recente. Queste macchine virtuali supportano una gamma più ampia di funzionalità di distribuzione, con una cache della pagina enclave (EPC) doppia e una maggiore selezione di dimensioni rispetto alle macchine virtuali della serie DC. Le macchine virtuali della [serie DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) sono attualmente in anteprima e verranno deprecate e non incluse nella disponibilità generale.

Avviare la distribuzione di una macchina virtuale della serie DCsv2 tramite il marketplace commerciale di Microsoft seguendo le istruzioni della [guida introduttiva](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Dimensioni e aree disponibili correnti

Per ottenere un elenco di tutte le dimensioni delle macchine virtuali di confidential computing disponibili a livello generale nelle aree e zone di disponibilità, eseguire il comando seguente nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest):

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

A partire da maggio 2020, questi SKU sono disponibili nelle aree e nelle zone di disponibilità seguenti:

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

Per una visualizzazione più dettagliata delle dimensioni precedenti, eseguire il comando seguente:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Requisiti host dedicati
La distribuzione di un **Standard_DC8_v2** dimensioni della macchina virtuale nella famiglia di macchine virtuali serie DCSv2 occuperà l'host completo e non verrà condiviso con altri tenant o sottoscrizioni. Questa famiglia di SKU della macchina virtuale fornisce l'isolamento che può essere necessario per soddisfare i requisiti normativi di conformità e sicurezza che in genere soddisfano con un servizio host dedicato. Quando si sceglie **Standard_DC8_v2** SKU, il server host fisico alloca tutte le risorse hardware disponibili, inclusa la memoria EPC, solo alla macchina virtuale. Si noti che questa funzionalità esiste dalla progettazione dell'infrastruttura e che tutte le funzionalità del **Standard_DC8_v2** saranno supportate. Questa distribuzione non corrisponde al servizio [host dedicato di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts) fornito da altre famiglie di macchine virtuali di Azure.


## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Seguire un'esercitazione introduttiva per distribuire una macchina virtuale della serie DCsv2 in meno di 10 minuti. 

- **Sottoscrizione di Azure**: per distribuire un'istanza di macchina virtuale di confidential computing, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Se si usa un [account gratuito di Azure](https://azure.microsoft.com/free/), non sarà disponibile una quota per la quantità appropriata di core di calcolo di Azure.

- **Prezzi e disponibilità a livello di area**: trovare i prezzi per le macchine virtuali della serie DCsv2 nella [pagina dei prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Per informazioni sulla disponibilità nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .


- **Quota di core**: potrebbe essere necessario aumentare la quota di core nella sottoscrizione di Azure rispetto al valore predefinito. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di macchina virtuale, inclusa la serie DCsv2. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) senza alcun addebito. Si noti che i limiti predefiniti possono variare in base alla categoria della sottoscrizione.

  > [!NOTE]
  > Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. Le quote di Azure sono limiti di credito e non garanzie di capacità. A prescindere dalla quota, viene addebitato solo l'uso dei core effettivamente impiegati.
  
- **Ridimensionamento**: a causa dell'hardware specializzato, è possibile ridimensionare solo le istanze di calcolo riservate nella stessa famiglia di dimensioni. Ad esempio, è possibile ridimensionare una VM della serie DCsv2 solo da una dimensione della serie DCsv2 a un'altra. Il ridimensionamento da una dimensione di calcolo non riservata a una dimensione di calcolo riservata non è supportato.  

- **Immagine**: per fornire il supporto a Intel SGX (Intel Software Guard Extension) per le istanze di confidential computing, è necessario eseguire tutte le distribuzioni su immagini di seconda generazione. Confidential computing di Azure supporta i carichi di lavoro in esecuzione su Ubuntu 18.04 Gen 2, Ubuntu 16.04 Gen 2, Windows Server 2019 Gen 2 e Windows Server 2016 Gen 2. Per altre informazioni sugli scenari supportati e non, leggere l'articolo [Supporto per le macchine virtuali di seconda generazione in Azure](../virtual-machines/linux/generation-2.md). 

- **Archiviazione**: i dischi dati della macchina virtuale di confidential computing di Azure e i dischi del sistema operativo temporanei si trovano in dischi NVMe. Le istanze supportano solo dischi SSD Premium e SSD Standard, non Ultra SSD o HDD Standard. Le dimensioni della macchina virtuale **DC8_vs** non supportano gli account di archiviazione Premium. 

- **Crittografia del disco**: le istanze di confidential computing non supportano al momento la crittografia del disco. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerazioni sulla disponibilità elevata e sul ripristino di emergenza

Quando si usano le macchine virtuali in Azure, si è responsabili dell'implementazione di una soluzione a disponibilità elevata e con ripristino di emergenza per evitare i tempi di inattività. 

Confidential computing di Azure non supporta al momento la ridondanza della zona tramite zone di disponibilità. Per garantire la massima disponibilità e ridondanza per l'elaborazione riservata, usare i [set di disponibilità](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy). A causa delle restrizioni hardware, i set di disponibilità per le istanze di elaborazione riservata possono avere un massimo di 10 domini di aggiornamento. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Distribuzione da un modello di Azure Resource Manager 

Azure Resource Manager è il servizio di distribuzione e gestione di Azure. Fornisce un livello di gestione che consente di creare, aggiornate ed eliminare risorse nella sottoscrizione di Azure. È possibile usare funzionalità di gestione, come il controllo di accesso, i blocchi e i tag, per proteggere e organizzare le risorse dopo la distribuzione.

Per informazioni sui modelli di Azure Resource Manager, vedere [Panoramica della distribuzione di modelli](../azure-resource-manager/templates/overview.md).

Per distribuire una macchina virtuale della serie DCsv2 in un modello di Azure Resource Manager, si utilizzerà la [risorsa della macchina virtuale](../virtual-machines/windows/template-description.md). Assicurarsi di specificare le proprietà corrette per **vmSize** e per **imageReference**.

### <a name="vm-size"></a>Dimensioni macchina virtuale

Specificare una delle dimensioni seguenti nel modello di Azure Resource Manager nella risorsa della macchina virtuale. Questa stringa viene inserita come **vmSize** nelle **proprietà**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Immagine del sistema operativo di seconda generazione

In **Proprietà**, sarà anche necessario fare riferimento a un'immagine in **storageProfile**. Usare *solo una* delle immagini seguenti per **imageReference**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Passaggi successivi 

In questo articolo sono state illustrate le qualifiche e le configurazioni necessarie per la creazione di una macchina virtuale di confidential computing. È ora possibile passare a Microsoft Azure Marketplace per distribuire una macchina virtuale della serie DCsv2.

> [!div class="nextstepaction"]
> [Distribuire una macchina virtuale della serie DCsv2 in Azure Marketplace](quick-create-marketplace.md)