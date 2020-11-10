---
title: Macchine virtuali Vantaggio Azure Hybrid e Linux
description: Il Vantaggio Azure Hybrid permette di risparmiare denaro sulle macchine virtuali Linux in esecuzione in Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: alsin
ms.openlocfilehash: c1200121d1c768a3fdddd7749184d7f8b5c98a96
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413106"
---
# <a name="preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Anteprima: Vantaggio Azure Hybrid-come viene applicato per macchine virtuali Linux

## <a name="overview"></a>Panoramica

Vantaggio Azure Hybrid consente di eseguire più facilmente la migrazione delle macchine virtuali Red Hat Enterprise Linux in Azure (RHEL) e SUSE Linux Enterprise Server (SLES) in Azure usando la propria sottoscrizione di Red Hat o SUSE software già esistente. Con questo vantaggio, paghi solo per i costi di infrastruttura della VM perché la tariffa software è coperta dalla tua sottoscrizione RHEL o SLES. Il vantaggio è applicabile a tutte le immagini RHEL e SLES Marketplace con pagamento in base al consumo (PAYG).

> [!IMPORTANT]
> Vantaggio Azure Hybrid per le macchine virtuali Linux è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefit-description"></a>Descrizione del vantaggio

Grazie alla Vantaggio Azure Hybrid, è possibile eseguire più facilmente la migrazione dei server RHEL e SLES locali in Azure convertendo le macchine virtuali RHEL e SLES PAYG esistenti in Azure per la fatturazione BYOS (Bring your own Subscription). In genere, le macchine virtuali distribuite da immagini PAYG in Azure addebiteranno sia una tariffa dell'infrastruttura che una tariffa software. Con il Vantaggio Azure Hybrid, le macchine virtuali PAYG possono essere convertite in un modello di fatturazione BYOS senza una ridistribuzione, evitando eventuali rischi di inattività.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Visualizzazione dei costi Vantaggio Azure Hybrid sulle VM Linux.":::

Quando si Abilita il vantaggio in una macchina virtuale RHEL o SLES, non verrà più addebitata la tariffa software aggiuntiva in genere in una macchina virtuale PAYG. Al contrario, la macchina virtuale inizierà a emettere un addebito per BYOS, che include solo la tariffa hardware di calcolo e nessuna tariffa software.

Se lo si desidera, è anche possibile convertire una macchina virtuale in cui è stato abilitato il vantaggio su un modello di fatturazione PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Ambito dell'idoneità Vantaggio Azure Hybrid per le macchine virtuali Linux

Vantaggio Azure Hybrid è disponibile per tutte le immagini RHEL e SLES Marketplace PAYG. Il vantaggio non è ancora disponibile per le immagini RHEL o SLES Marketplace BYOS o immagini personalizzate.

Le istanze riservate, gli host dedicati e i vantaggi ibridi SQL non sono idonei per il Vantaggio Azure Hybrid se si usa già il vantaggio con le VM Linux.

## <a name="how-to-get-started"></a>Operazioni preliminari

Vantaggio Azure Hybrid è attualmente in fase di anteprima per le macchine virtuali Linux. Una volta ottenuto l'accesso all'anteprima, è possibile abilitare il vantaggio usando il portale di Azure o l'interfaccia della riga di comando di Azure.

### <a name="preview"></a>Anteprima

In questa fase, è possibile ottenere l'accesso al vantaggio compilando il modulo [qui](https://aka.ms/ahb-linux-form). Una volta compilato il modulo, le sottoscrizioni di Azure verranno abilitate per i vantaggi e si riceverà una conferma da Microsoft entro tre giorni lavorativi.

### <a name="red-hat-customers"></a>Clienti Red Hat

1.    Compila il modulo di richiesta di anteprima sopra
1.    Eseguire la registrazione con il [programma Red Hat cloud Access](https://aka.ms/rhel-cloud-access)
1.    Abilitare le sottoscrizioni di Azure per l'accesso al cloud e abilitare le sottoscrizioni contenenti le macchine virtuali con cui si vuole usare il vantaggio
1.    Applicare il vantaggio alle macchine virtuali esistenti tramite l'portale di Azure o l'interfaccia della riga di comando di Azure
1.    Facoltativamente, registrare le VM ricevendo il vantaggio con una fonte separata di aggiornamenti (le macchine virtuali a commutazione possono rimanere collegate a [RHUI](../workloads/redhat/redhat-rhui.md) o registrate tramite RHSM)

### <a name="suse-customers"></a>Clienti SUSE

1.    Compila il modulo di richiesta di anteprima sopra
1.    Eseguire la registrazione con il programma di cloud pubblico SUSE
1.    Applicare il vantaggio alle macchine virtuali esistenti tramite l'portale di Azure o l'interfaccia della riga di comando di Azure
1.    Registrare le VM ricevendo il vantaggio con una fonte di aggiornamenti separata

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Abilitare e disabilitare il vantaggio nella portale di Azure

È possibile abilitare il vantaggio sulle macchine virtuali esistenti visitando il pannello **configurazione** e seguendo la procedura. È possibile abilitare il vantaggio nelle nuove VM durante l'esperienza di creazione della macchina virtuale.

### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Abilitare e disabilitare il vantaggio nell'interfaccia della riga di comando di Azure

È possibile usare il comando "AZ VM Update" per aggiornare le macchine virtuali esistenti. Per le VM RHEL, eseguire il comando con un parametro--License-Type di "RHEL_BYOS". Per le macchine virtuali SLES, eseguire il comando con un parametro--License-Type di "SLES_BYOS".

#### <a name="cli-example-to-enable-the-benefit"></a>Esempio di interfaccia della riga di comando per abilitare il vantaggio:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Esempio di interfaccia della riga di comando per disabilitare il vantaggio:
Per disabilitare il vantaggio, usare il valore "None" per il tipo di licenza
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Esempio di interfaccia della riga di comando per abilitare il vantaggio su un numero elevato di macchine virtuali
Per abilitare il vantaggio in un numero elevato di macchine virtuali, è possibile usare il parametro nell'interfaccia della riga di comando di `--ids` Azure.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Gli esempi seguenti illustrano due metodi per ottenere un elenco di ID di risorsa, uno a livello di gruppo di risorse, uno a livello di sottoscrizione.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Controllare lo stato di vantaggio Azure Hybrid di una macchina virtuale
È possibile visualizzare lo stato di vantaggio Azure Hybrid di una macchina virtuale in tre modi: archiviazione nel portale, uso dell'interfaccia della riga di comando di Azure o uso del servizio metadati dell'istanza di Azure (Azure IMDS).


### <a name="portal"></a>Portale

Visualizzare il pannello configurazione e controllare lo stato delle licenze per verificare se vantaggio Azure Hybrid è abilitato per la macchina virtuale.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

`az vm get-instance-view`Per questo scopo, è possibile usare il comando. Cercare un campo licenseType nella risposta. Se il campo licenseType è presente e il valore è' RHEL_BYOS ' o ' SLES_BYOS ', il vantaggio è abilitato per la macchina virtuale.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Dall'interno della macchina virtuale, è possibile eseguire una query sui metadati attestati IMDS per determinare il licenseType della macchina virtuale. Il valore licenseType ' RHEL_BYOS ' o ' SLES_BYOS ' indicherà che il vantaggio è abilitato per la macchina virtuale. Altre informazioni sui metadati attestati sono disponibili [qui](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Conformità

### <a name="red-hat"></a>Red Hat

Per usare Vantaggio Azure Hybrid per le VM RHEL, è prima necessario essere registrati con il programma Red Hat cloud Access. Questa operazione può essere eseguita tramite il sito Red Hat cloud Access. Dopo aver abilitato il vantaggio nella macchina virtuale, è necessario registrare la VM con la propria origine degli aggiornamenti con Red Hat Subscription Manager o Red Hat satellite. La registrazione per gli aggiornamenti garantirà la permanenza in uno stato supportato.

### <a name="suse"></a>SUSE

Per usare Vantaggio Azure Hybrid per le macchine virtuali SLES, è necessario prima registrarsi con il programma SUSE public cloud. Altre informazioni sul programma sono disponibili qui. Dopo aver acquistato le sottoscrizioni SUSE, è necessario registrare le VM usando tali sottoscrizioni per la propria origine degli aggiornamenti tramite SUSE Customer Center, Subscription Management Tool Server o SUSE Manager.

## <a name="frequently-asked-questions"></a>Domande frequenti
*D: è possibile usare un tipo di licenza "RHEL_BYOS" con un'immagine SLES o viceversa?*

R: No. Il tentativo di immettere un tipo di licenza che corrisponde erroneamente alla distribuzione in esecuzione nella macchina virtuale non aggiornerà i metadati di fatturazione. Tuttavia, se si immette accidentalmente il tipo di licenza errato, l'aggiornamento della macchina virtuale al tipo di licenza corretto consentirà comunque il vantaggio.

*D: si è registrato con Red Hat cloud Access ma non è ancora possibile abilitare il vantaggio nelle macchine virtuali RHEL. Cosa devo fare?*

R: la propagazione della registrazione della sottoscrizione di Red Hat cloud Access da Red Hat ad Azure potrebbe richiedere del tempo. Se l'errore viene ancora visualizzato dopo un giorno lavorativo, contattare il supporto tecnico Microsoft.

## <a name="common-issues"></a>Problemi comuni
Questa sezione contiene un elenco di problemi comuni che possono essere rilevati e i passaggi per la mitigazione.

| Errore | Strategia di riduzione del rischio |
| ----- | ---------- |
| "La sottoscrizione non è registrata per l'anteprima di Linux Vantaggio Azure Hybrid. Per istruzioni dettagliate, vedere https://aka.ms/ahb-linux " | Compilare il modulo in https://aka.ms/ahb-linux-form per eseguire la registrazione per l'anteprima di Linux del vantaggio Azure Hybrid.
| "Non è stato possibile completare l'azione perché i nostri record indicano che non è stato abilitato correttamente Red Hat cloud Access nella sottoscrizione di Azure..." | Per usare il vantaggio con le VM RHEL, è necessario prima registrare le sottoscrizioni di Azure con Red Hat cloud Access. Visitare questo collegamento per altre informazioni su come registrare le sottoscrizioni di Azure per Red Hat cloud Access
|"L'opzione per Vantaggio Azure Hybrid non viene visualizzata nel portale" | Si tratta di un problema noto per le VM RHEL e SLES create da raccolta immagini condivise, snapshot o immagini PAYG acquisite. In questo caso, usare la procedura dell'interfaccia della riga di comando descritta nella sezione "[abilitare e disabilitare il vantaggio nell'interfaccia della riga di comando di Azure](#enable-and-disable-the-benefit-in-the-azure-cli)". Per visualizzare lo stato di vantaggio Azure Hybrid, usare il comando ` az vm get-instance-view -g MyResourceGroup -n MyVm` .|

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare l'anteprima, compilare il modulo [qui](https://aka.ms/ahb-linux-form).