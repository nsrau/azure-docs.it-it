---
title: Usando i profili delle versioni API di Python in Azure Stack | Microsoft Docs
description: Informazioni sull'uso di profili della versione API di Python in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "34806837"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Usare i profili delle versioni API con Python in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

## <a name="python-and-api-version-profiles"></a>Profili delle versioni di Python e API

Python SDK supporta i profili delle versioni API per diverse piattaforme cloud, ad esempio Azure Stack e Azure globale di destinazione. È possibile usare i profili delle API di creazione di soluzioni per un cloud ibrido. Python SDK supporta i profili seguenti API:

1. **più recente**  
    Il profilo ha come destinazione le versioni API più recenti per tutti i provider di servizi nella piattaforma Azure.
2.  **2017-03-09-profilo**  
    **2017-03-09-profilo**  
    Il profilo ha come destinazione le versioni dell'API dei provider di risorse supportati da Azure Stack.

    Per altre informazioni sui profili di API e Azure Stack, vedere [profili della versione di gestione API in Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Installare Azure Python SDK

1.  Installare Git dalla [il sito ufficiale](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Per istruzioni su come installare il SDK di Python, vedere [Azure per sviluppatori Python](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da usare in un secondo momento. Per istruzioni su come creare una sottoscrizione, vedere [creare le sottoscrizioni di offerte in Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Creare un'entità servizio e salvare i relativi ID e segreto. Per istruzioni su come creare un'entità servizio per Azure Stack, vedere [offrono alle applicazioni di accedere ad Azure Stack](../azure-stack-create-service-principals.md). 
5.  Assicurarsi che l'entità servizio possiede ruolo Collaboratore/proprietario per la sottoscrizione. Per istruzioni su come assegnare un ruolo all'entità servizio, vedere [offrono alle applicazioni di accedere ad Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare Python SDK di Azure con Azure Stack, è necessario fornire i valori seguenti e quindi impostare i valori con le variabili di ambiente. Vedere le istruzioni dopo la tabella per il sistema operativo su come impostare le variabili di ambiente. 

| Valore | Variabili di ambiente | DESCRIZIONE |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenant | AZURE_TENANT_ID | Il valore di Azure Stack [tenant ID](../azure-stack-identity-overview.md). |
| ID client | AZURE_CLIENT_ID | Il servizio di ID entità applicazione salvati dell'entità servizio è stato creato nella sezione precedente di questo documento. |
| ID sottoscrizione | AZURE_SUBSCRIPTION_ID | Il [ID sottoscrizione](../azure-stack-plan-offer-quota-overview.md#subscriptions) è la modalità di accesso offerte in Azure Stack. |
| Client Secret | AZURE_CLIENT_SECRET | L'applicazione di un'entità servizio segreto salvato se è stata creata l'entità servizio. |
| Endpoint di Resource Manager | ARM_ENDPOINT | Visualizzare [l'endpoint di gestione risorse di Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Esempi Python per Azure Stack 

È possibile usare gli esempi di codice seguente per eseguire attività comuni di gestione per le macchine virtuali in Azure Stack.

Gli esempi di codice illustrano a:

- Creare macchine virtuali:
    - Creare una macchina virtuale Linux
    - Creare una macchina virtuale Windows
- Aggiornare una macchina virtuale:
    - Espandere un'unità
    - Contrassegnare una macchina virtuale
    - Collegare i dischi dei dati
    - Scollegare dischi di dati
- Operare una macchina virtuale:
    - Avviare una macchina virtuale
    - Arrestare una macchina virtuale
    - Riavviare una macchina virtuale
- Elenco di macchine virtuali
- Eliminare una macchina virtuale

Per esaminare il codice per eseguire queste operazioni, consultare il **run_example()** funzione nello script di Python **Hybrid/unmanaged-disks/example.py** nel repository GitHub [ virtuale-computer-python-gestire](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Ogni operazione viene etichettato in modo chiaro con un commento e una funzione di stampa.
Gli esempi non sono necessariamente nell'ordine indicato nell'elenco precedente.


## <a name="run-the-python-sample"></a>Eseguire l'esempio di Python

1.  Se non ne hai già, [installare Python](https://www.python.org/downloads/).

    In questo esempio (e il SDK) è compatibile con Python 2.7, 3.4, 3.5 e 3.6.

2.  Indicazioni generali per lo sviluppo Python consiste nell'usare un ambiente virtuale. 
    Per altre informazioni, vedere https://docs.python.org/3/tutorial/venv.html
    
    Installare e inizializzare l'ambiente virtuale con il modulo "venv" Python 3 (è necessario installare [virtualenv](https://pypi.python.org/pypi/virtualenv) per Python 2.7):

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

4.  Installare le dipendenze tramite pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Creare un [entità servizio](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) per lavorare con Azure Stack. Assicurarsi che l'entità servizio possiede [ruolo di collaboratore/proprietario](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) sull'abbonamento.

6.  Impostare le variabili seguenti ed esportare queste variabili di ambiente nella shell corrente. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).