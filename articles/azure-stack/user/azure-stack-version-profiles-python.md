---
title: Usando i profili delle versioni API di Python in Azure Stack | Microsoft Docs
description: Informazioni sull'uso di profili della versione API di Python in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: 8049db848e34b0aa9bc23f08169a8c63f765791a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389743"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Usare i profili delle versioni API con Python in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

## <a name="python-and-api-version-profiles"></a>Profili delle versioni di Python e API

Python SDK supporta i profili delle versioni API per diverse piattaforme cloud, ad esempio Azure Stack e Azure globale di destinazione. È possibile usare i profili delle API di creazione di soluzioni per un cloud ibrido. Python SDK supporta i profili seguenti API:

- **latest**  
    Questo profilo ha come destinazione le versioni API più recenti per tutti i provider di servizi nella piattaforma Azure.
- **2018-03-01-hybrid**     
    Questo profilo è destinato a versioni API più recenti per tutti i provider di risorse nella piattaforma Azure Stack.
- **2017-03-09-profile**  
    Questo profilo ha come destinazione le versioni dell'API più compatibile con i provider di risorse supportati da Azure Stack.

   Per altre informazioni sui profili di API e Azure Stack, vedere [profili della versione di gestione API in Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Installare Azure Python SDK

1. Installare Git dalla [il sito ufficiale](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Per istruzioni su come installare il SDK di Python, vedere [Azure per sviluppatori Python](/python/azure/python-sdk-azure-install?view=azure-python).
3. Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da usare in un secondo momento. Per istruzioni sulla creazione di una sottoscrizione, vedere [creare le sottoscrizioni di offerte in Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).
4. Creare un'entità servizio e salvare i relativi ID e segreto. Per istruzioni su come creare un'entità servizio per Azure Stack, vedere [offrono alle applicazioni di accedere ad Azure Stack](../azure-stack-create-service-principals.md).
5. Assicurarsi che all'entità servizio è installato il ruolo di collaboratore/proprietario della sottoscrizione. Per istruzioni su come assegnare un ruolo all'entità servizio, vedere [offrono alle applicazioni di accedere ad Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare Python Azure SDK con Azure Stack, è necessario fornire i valori seguenti e quindi impostare i valori con le variabili di ambiente. Vedere le istruzioni dopo la tabella per il sistema operativo su come impostare le variabili di ambiente.

| Valore | Variabili di ambiente | DESCRIZIONE |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenant | AZURE_TENANT_ID | Il valore di Azure Stack [tenant ID](../azure-stack-identity-overview.md). |
| ID client | AZURE_CLIENT_ID | Il servizio applicazione di un'entità ID salvato quando l'entità servizio è stato creato nella sezione precedente di questo articolo. |
| ID sottoscrizione | AZURE_SUBSCRIPTION_ID | Il [ID sottoscrizione](../azure-stack-plan-offer-quota-overview.md#subscriptions) è la modalità di accesso offerte in Azure Stack. |
| Client Secret | AZURE_CLIENT_SECRET | Segreto dell'applicazione dell'entità servizio salvato quando è stata creata l'entità servizio. |
| Endpoint di Resource Manager | ARM_ENDPOINT | Vedere le [endpoint di gestione risorse di Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Percorso della risorsa | AZURE_RESOURCE_LOCATION | Il percorso della risorsa dell'ambiente Azure Stack.

## <a name="python-samples-for-azure-stack"></a>Esempi Python per Azure Stack

Alcuni degli esempi di codice disponibili per Azure Stack tramite Python SDK sono:

- [Gestire le risorse e gruppi di risorse](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/).
- [Gestire Account di archiviazione](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/).
- [Gestire le macchine virtuali](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/).

## <a name="python-manage-virtual-machine-sample"></a>Esempio di macchina virtuale di gestione Python

È possibile usare l'esempio di codice seguente per eseguire attività comuni di gestione per le macchine virtuali in Azure Stack. Nell'esempio di codice illustra come:

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

Per esaminare il codice che esegue queste operazioni, vedere la **run_example()** funzione nello script di Python **example.py** nel repository GitHub [ibrido-Compute-Python-Gestisci-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Ogni operazione viene etichettato in modo chiaro con un commento e una funzione di stampa. Gli esempi non sono necessariamente nell'ordine indicato in questo elenco.

## <a name="run-the-python-sample"></a>Eseguire l'esempio di Python

1. Se non è ancora disponibile, [installare Python](https://www.python.org/downloads/). In questo esempio (e il SDK) è compatibile con Python 2.7, 3.4, 3.5 e 3.6.

2. Indicazioni generali per lo sviluppo Python consiste nell'usare un ambiente virtuale. Per altre informazioni, vedere la [documentazione di Python](https://docs.python.org/3/tutorial/venv.html).

3. Installare e inizializzare l'ambiente virtuale con il modulo "venv" Python 3 (è necessario installare [virtualenv](https://pypi.python.org/pypi/virtualenv) per Python 2.7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Clonare il repository:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Installare le dipendenze tramite pip:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Creare un [entità servizio](../azure-stack-create-service-principals.md) per lavorare con Azure Stack. Assicurarsi che l'entità servizio possiede [ruolo di collaboratore/proprietario](../azure-stack-create-service-principals.md#assign-a-role) sull'abbonamento.

7. Impostare le variabili seguenti ed esportare queste variabili di ambiente nella shell corrente:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Per eseguire questo esempio, devono essere presente in Azure Stack marketplace immagini di Windows Server 2012-R2-Datacenter e Ubuntu 16.04-LTS. Può trattarsi di uno [scaricato da Azure](../azure-stack-download-azure-marketplace-item.md), o aggiunto alle [Repository di immagini piattaforma](../azure-stack-add-vm-image.md).

9. Eseguire l'esempio:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>Passaggi successivi

- [Centro per sviluppatori Python Azure](https://azure.microsoft.com/develop/python/)
- [Documentazione di macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Percorso di apprendimento per macchine virtuali](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Se non hai una sottoscrizione di Microsoft Azure, è possibile ottenere un account di valutazione gratuita [qui](https://go.microsoft.com/fwlink/?LinkId=330212).
