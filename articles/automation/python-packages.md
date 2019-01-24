---
title: Gestire pacchetti Python 2 in Automazione di Azure
description: Questo articolo descrive come gestire pacchetti Python 2 in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: de0998dffeac54db5311bbcde1c9499488b23556
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434973"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gestire pacchetti Python 2 in Automazione di Azure

Automazione di Azure consente di eseguire runbook Python 2 in Azure e nei ruoli di lavoro ibridi per runbook Linux. Per favorire la semplificazione dei runbook, è possibile importare i moduli necessari usando pacchetti Python. Questo articolo descrive come gestire e usare pacchetti Python in Automazione di Azure.

## <a name="import-packages"></a>Importare pacchetti

Nell'account di Automazione selezionare **Pacchetti Python 2** in **Risorse condivise**. Fare clic su **+ Aggiungi pacchetto Python 2**.

![Aggiungere un pacchetto Python](media/python-packages/add-python-package.png)

Nella pagina **Aggiungi pacchetto Python 2** selezionare un pacchetto locale da caricare. Il pacchetto può essere un file con estensione `.whl` o `.tar.gz`. Dopo aver selezionato il pacchetto, fare clic su **OK** per caricarlo.

![Aggiungere un pacchetto Python](media/python-packages/upload-package.png)

Dopo l'importazione, il pacchetto viene elencato nella pagina **Pacchetti Python 2** nell'account di Automazione. Se è necessario rimuovere un pacchetto, selezionare il pacchetto da eliminare e scegliere **Elimina** nella pagina dei pacchetti.

![Elenco di pacchetti](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>Usare un pacchetto in un runbook

Dopo aver importato un pacchetto, è possibile usarlo in un runbook. L'esempio seguente usa il [ pacchetto di utilità di Automazione di Azure](https://github.com/azureautomation/azure_automation_utility). Questo pacchetto rende più semplice l'uso di Python con Automazione di Azure. Per usare il pacchetto, seguendo le istruzioni nel repository GitHub aggiungerlo al runbook tramite `from azure_automation_utility import get_automation_runas_credential`, ad esempio, per importare la funzione per il recupero dell'account RunAs.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Sviluppare e testare i runbook offline

Per sviluppare e testare i runbook Python 2 offline, è possibile usare il modulo [Azure Automation python emulated assets](https://github.com/azureautomation/python_emulated_assets) (Asset emulati in Pyhton per Automazione di Azure) in GitHub. Questo modulo consente di fare riferimento alle risorse condivise, ad esempio credenziali, variabili, connessioni e certificati.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare con i runbook Python 2, vedere [Il primo runbook Python 2](automation-first-runbook-textual-python2.md)
