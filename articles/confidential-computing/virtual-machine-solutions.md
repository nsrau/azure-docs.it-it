---
title: Soluzioni di Azure Confidential computing in macchine virtuali
description: Informazioni sulle soluzioni di Azure Confidential computing nelle macchine virtuali.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: e574ac33e5f7da814c4bd813fc1c083c7cb4c2c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187886"
---
# <a name="solutions-on-azure-virtual-machines"></a>Soluzioni in macchine virtuali di Azure

Questo articolo descrive come distribuire le macchine virtuali di Azure Confidential Computing (VM) che eseguono processori Intel supportati da [Intel Software Guard Extension](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Dimensioni delle macchine virtuali di Azure computing riservato

Le macchine virtuali di Azure Confidential computing sono progettate per proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud 

[Serie DCsv2](../virtual-machines/dcv2-series.md) Le macchine virtuali sono la famiglia più recente e più recente per la dimensione di calcolo riservata. Queste macchine virtuali supportano una gamma più ampia di funzionalità di distribuzione, con 2x la cache della pagina enclave (EPC) e una maggiore selezione di dimensioni rispetto alle macchine virtuali della serie DC. Le VM della [serie DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) sono attualmente in anteprima e verranno deprecate e non incluse nella disponibilità generale.

Avviare la distribuzione di una macchina virtuale della serie DCsv2 tramite Microsoft Commercial Marketplace seguendo l' [esercitazione introduttiva](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Dimensioni e aree disponibili correnti

Per ottenere un elenco di tutte le dimensioni delle macchine virtuali di calcolo riservate disponibili a livello generale in aree disponibili e zone di disponibilità, eseguire il comando seguente nell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest):

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

A partire dal 2020 aprile, questi SKU sono disponibili nelle aree e nelle zone di disponibilità seguenti:

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

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Seguire un'esercitazione introduttiva per distribuire una macchina virtuale della serie DCsv2 in meno di 10 minuti. 

- **Sottoscrizione di Azure** : per distribuire un'istanza di VM di calcolo riservata, prendere in considerazione una sottoscrizione con pagamento in base al consumo o un'altra opzione di acquisto. Se si usa un [account Azure gratuito](https://azure.microsoft.com/free/), non sarà disponibile una quota per la quantità appropriata di core di calcolo di Azure.

- **Prezzi e disponibilità a livello** di area: trovare i prezzi per le macchine virtuali serie DCsv2 nella [pagina dei prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Per informazioni sulla disponibilità nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .


- **Quota di core**: potrebbe essere necessario aumentare la quota di core nella sottoscrizione di Azure rispetto al valore predefinito. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di VM, inclusa la serie DCsv2. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) gratuitamente. Si noti che i limiti predefiniti possono variare a seconda della categoria di sottoscrizione.

  > [!NOTE]
  > Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. Le quote di Azure sono limiti di credito e non garanzie di capacità. A prescindere dalla quota, viene addebitato solo l'uso dei core effettivamente impiegati.
  
- **Ridimensionamento: a** causa dell'hardware specifico, è possibile ridimensionare solo le istanze di calcolo riservate all'interno della stessa famiglia di dimensioni. Ad esempio, è possibile ridimensionare solo una macchina virtuale della serie DCsv2 da una dimensione della serie DCsv2 a un'altra. Il ridimensionamento da una dimensione di calcolo non riservata a una dimensione di calcolo riservata non è supportato.  

- **Image** : per fornire il supporto Intel SGX (Intel Software Guard Extension) sulle istanze di calcolo riservate, è necessario eseguire tutte le distribuzioni su immagini di generazione 2. Azure Confidential computing supporta i carichi di lavoro in esecuzione su Ubuntu 18,04 gen 2, Ubuntu 16,04 gen 2 e Windows Server 2016 gen 2. Per altre informazioni sugli scenari supportati e non supportati, vedere [supporto per le macchine virtuali di seconda generazione in Azure](../virtual-machines/linux/generation-2.md) . 

- **Archiviazione** : i dischi dati della macchina virtuale di Azure Confidential computing e i dischi del sistema operativo temporanei si trovano in dischi NVMe. Le istanze supportano solo SSD Premium e SDD Standard dischi, non Ultra SSD o HDD Standard. Dimensioni della macchina virtuale **DC8_v2** non supporta l'archiviazione Premium. 

- **Crittografia del disco** : le istanze di calcolo riservate non supportano la crittografia del disco in questo momento. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerazioni sulla disponibilità elevata e sul ripristino di emergenza

Quando si usano macchine virtuali in Azure, si è responsabili dell'implementazione di una soluzione di disponibilità elevata e ripristino di emergenza per evitare qualsiasi tempo di inattività. 

Azure Confidential computing non supporta la ridondanza della zona tramite zone di disponibilità al momento. Per garantire la massima disponibilità e ridondanza per l'elaborazione riservata, usare i [set di disponibilità](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy). A causa delle restrizioni hardware, i set di disponibilità per le istanze di elaborazione riservata possono avere un massimo di 10 domini di aggiornamento. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Distribuzione tramite un modello di Azure Resource Manager 

Azure Resource Manager è il servizio di distribuzione e gestione di Azure. Fornisce un livello di gestione che consente di creare, aggiornate ed eliminare risorse nella sottoscrizione di Azure. È possibile usare funzionalità di gestione, come il controllo di accesso, i blocchi e i tag, per proteggere e organizzare le risorse dopo la distribuzione.

Per informazioni sui modelli di Azure Resource Manager, vedere [Panoramica della distribuzione di modelli](../azure-resource-manager/templates/overview.md).

Per distribuire una VM della serie DCsv2 in un modello ARM, si utilizzerà la [risorsa della macchina virtuale](../virtual-machines/windows/template-description.md). È necessario assicurarsi di specificare le proprietà corrette per **vmSize** e per il **imageReference**.

### <a name="vm-size"></a>Dimensioni macchina virtuale

Specificare una delle dimensioni seguenti nel modello ARM nella risorsa della macchina virtuale. Questa stringa viene inserita come **vmSize** nelle **Proprietà**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Immagine del sistema operativo Gen2

In **Proprietà**sarà anche necessario fare riferimento a un'immagine in **storageProfile**. Usare *solo una* delle immagini seguenti per **imageReference**.

```json
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

In questo articolo sono state illustrate le qualifiche e le configurazioni necessarie per la creazione di una macchina virtuale di calcolo riservata. È ora possibile passare ad Azure Marketplace per distribuire una macchina virtuale di serie DCsv2.

> [!div class="nextstepaction"]
> [Distribuire una macchina virtuale della serie DCsv2 in Azure Marketplace](quick-create-marketplace.md)