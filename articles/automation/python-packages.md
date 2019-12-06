---
title: Gestire pacchetti Python 2 in Automazione di Azure
description: Questo articolo descrive come gestire pacchetti Python 2 in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f20865b92df2a197410f209cf921c5e573723286
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850194"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gestire pacchetti Python 2 in Automazione di Azure

Automazione di Azure consente di eseguire runbook Python 2 in Azure e nei ruoli di lavoro ibridi per runbook Linux. Per favorire la semplificazione dei runbook, è possibile importare i moduli necessari usando pacchetti Python. Questo articolo descrive come gestire e usare pacchetti Python in Automazione di Azure.

## <a name="import-packages"></a>Importare pacchetti

Nell'account di Automazione selezionare **Pacchetti Python 2** in **Risorse condivise**. Fare clic su **+ Aggiungi pacchetto Python 2**.

![Aggiungere un pacchetto Python](media/python-packages/add-python-package.png)

Nella pagina **Aggiungi pacchetto Python 2** selezionare un pacchetto locale da caricare. Il pacchetto può essere un file con estensione `.whl` o `.tar.gz`. Dopo aver selezionato il pacchetto, fare clic su **OK** per caricarlo.

![Aggiungere un pacchetto Python](media/python-packages/upload-package.png)

Una volta importato, un pacchetto viene elencato nella pagina **Python 2 packages** dell'account di automazione. Se è necessario rimuovere un pacchetto, selezionare il pacchetto da eliminare e scegliere **Elimina** nella pagina dei pacchetti.

![Elenco di pacchetti](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importa pacchetti con dipendenze

Automazione di Azure non risolve le dipendenze per i pacchetti Python durante il processo di importazione. Esistono due modi per importare un pacchetto con tutte le relative dipendenze. Per importare i pacchetti nell'account di automazione è necessario usare solo uno dei passaggi seguenti.

### <a name="manually-download"></a>Scarica manualmente

In un computer Windows a 64 bit con [Python 2.7](https://www.python.org/downloads/release/latest/python2) e [PIP](https://pip.pypa.io/en/stable/) installato, eseguire il comando seguente per scaricare un pacchetto e tutte le relative dipendenze:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Una volta scaricati i pacchetti, è possibile importarli nell'account di automazione.

### <a name="runbook"></a>Runbook

Importare i pacchetti Python Runbook [Import Python 2 da pypi nell'account di automazione di Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) dalla raccolta all'account di automazione. Verificare che le impostazioni esecuzione test siano impostate su **Azure** e avviare runbook con i parametri. Per il corretto funzionamento dell'account di automazione, Runbook richiede un account RunAs. Per ogni parametro assicurarsi di avviarlo con l'opzione come illustrato nell'elenco e nell'immagine seguenti:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Elenco di pacchetti](media/python-packages/import-python-runbook.png)

Runbook consente di specificare il pacchetto da scaricare, ad esempio `Azure` (il quarto parametro) scaricherà tutti i moduli di Azure e tutte le relative dipendenze, ovvero circa 105.

Una volta completato il Runbook, è possibile controllare la pagina dei **pacchetti Python 2** in **risorse condivise** nell'account di automazione per verificare che il pacchetto sia stato importato correttamente.

## <a name="use-a-package-in-a-runbook"></a>Usare un pacchetto in un runbook

Dopo aver importato un pacchetto, è ora possibile utilizzarlo in un Runbook. L'esempio seguente usa il [ pacchetto di utilità di Automazione di Azure](https://github.com/azureautomation/azure_automation_utility). Questo pacchetto rende più semplice l'uso di Python con Automazione di Azure. Per usare il pacchetto, seguendo le istruzioni nel repository GitHub aggiungerlo al runbook tramite `from azure_automation_utility import get_automation_runas_credential`, ad esempio, per importare la funzione per il recupero dell'account RunAs.

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
