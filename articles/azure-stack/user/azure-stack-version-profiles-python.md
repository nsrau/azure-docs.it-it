---
title: Utilizzo dei profili versione API con Python nello Stack di Azure | Documenti Microsoft
description: Informazioni sull'utilizzo dei profili versione API con Python nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Usare i profili di versione API con Python in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="python-samples-for-azure-stack"></a>Esempi di Python per Azure Stack 

È possibile utilizzare gli esempi di codice seguente per eseguire attività di gestione comuni per le macchine virtuali nello Stack di Azure.

Gli esempi di codice viene mostrato a:

- Creare macchine virtuali:
    - Creare una macchina virtuale Linux
    - Creare una macchina virtuale Windows
- Aggiornare una macchina virtuale:
    - Espandere un'unità
    - Contrassegnare una macchina virtuale
    - Collegare i dischi dei dati
    - Scollegare dischi di dati
- Utilizzare una macchina virtuale:
    - Avviare una macchina virtuale
    - Arrestare una macchina virtuale
    - Riavviare una macchina virtuale
- Elenco di macchine virtuali
- Eliminare una macchina virtuale

Per esaminare il codice per eseguire queste operazioni, consultare il **run_example()** funzione nello script Python **Hybrid/unmanaged-disks/example.py** nel repository GitHub [ la gestione-python virtuale macchine](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Ogni operazione è chiaramente etichettata con un commento e una funzione di stampa.
Gli esempi non sono necessariamente nell'ordine indicato nell'elenco precedente.


## <a name="run-the-python-sample"></a>Eseguire l'esempio di Python

1.  Se non è già disponibile, [installare Python](https://www.python.org/downloads/).

    In questo esempio (e il SDK) è compatibile con Python 2.7, 3.4, 3.5 e versioni 3.6.

2.  Indicazioni generali per lo sviluppo Python consiste nell'usare un ambiente virtuale. 
    Per ulteriori informazioni, vedere https://docs.python.org/3/tutorial/venv.html
    
    Installare e inizializzare l'ambiente virtuale con il modulo "venv" 3 Python (è necessario installare [virtualenv](https://pypi.python.org/pypi/virtualenv) per Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Clonare il repository.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Installare le dipendenze di uso di pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Creare una [dell'entità servizio](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) per funzionare con lo Stack di Azure. Verificare che l'entità servizio disponga [ruolo di collaboratore/proprietario](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) nella sottoscrizione.

6.  Impostare le seguenti variabili ed esportare queste variabili di ambiente nella shell corrente. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Si noti che per eseguire questo esempio, 16.04-LTS Ubuntu e le immagini di Windows Server 2012 R2-Data Center devono essere presente in Stack Azure Marketplace. Può trattarsi di uno [scaricato da Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) oppure [aggiunto all'archivio di immagini della piattaforma](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Eseguire l'esempio.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Note

Potrebbe essere tentati di provare a recuperare disco del sistema operativo della macchina virtuale utilizzando `virtual_machine.storage_profile.os_disk`.
In alcuni casi, ciò potrebbe eseguire le operazioni desiderate, ma essere a conoscenza che offre un `OSDisk` oggetto.
Per aggiornare le dimensioni del disco del sistema operativo, come `example.py` , è necessario non un' `OSDisk` oggetto ma `Disk` oggetto.
`example.py` Ottiene il `Disk` oggetto con il codice seguente:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Passaggi successivi

- [Centro per sviluppatori Python di Azure](https://azure.microsoft.com/develop/python/)
- [Documentazione di macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Percorso di apprendimento per le macchine virtuali](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Se non si dispone di una sottoscrizione di Microsoft Azure è possibile ottenere un account di valutazione gratuito [qui](http://go.microsoft.com/fwlink/?LinkId=330212).
