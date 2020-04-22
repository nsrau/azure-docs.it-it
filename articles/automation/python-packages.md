---
title: Gestire pacchetti Python 2 in Automazione di Azure
description: Questo articolo descrive come gestire pacchetti Python 2 in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 9f52dfd92d430abffe5857d231898dd4b0e7745e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679911"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gestire pacchetti Python 2 in Automazione di Azure

Automazione di Azure consente di eseguire runbook Python 2 in Azure e nei ruoli di lavoro ibridi per runbook Linux. Per favorire la semplificazione dei runbook, è possibile importare i moduli necessari usando pacchetti Python. Questo articolo descrive come gestire e usare i pacchetti Python in Automazione di Azure.This article describes how to manage and use Python packages in Azure Automation.

## <a name="import-packages"></a>Importare pacchetti

Nell'account di automazione selezionare **Pacchetti Python 2** in Risorse **condivise**. Fare clic su **+ Aggiungi pacchetto Python 2**.

![Aggiungere un pacchetto Python](media/python-packages/add-python-package.png)

Nella pagina Aggiungi pacchetto Python 2 selezionare un pacchetto locale da caricare. Il pacchetto può essere un file **.whl** o **.tar.gz.** Quando il pacchetto è selezionato, fare clic **su OK** per caricarlo.

![Aggiungere un pacchetto Python](media/python-packages/upload-package.png)

Una volta che un pacchetto è stato importato, è elencato nella pagina dei pacchetti Python 2 nel tuo account di automazione. Se è necessario rimuovere un pacchetto, selezionarlo e fare clic su **Elimina**.

![Elenco di pacchetti](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importare pacchetti con dipendenzeImport packages with dependencies

L'automazione di Azure non risolve le dipendenze per i pacchetti python durante il processo di importazione. Esistono due modi per importare un pacchetto con tutte le relative dipendenze. Solo uno dei passaggi seguenti deve essere usato per importare i pacchetti nell'account Automation.

### <a name="manually-download"></a>Download manuale

In un computer Windows a 64 bit con [python2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) installati, eseguire il comando seguente per scaricare un pacchetto e tutte le relative dipendenze:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Una volta scaricati i pacchetti, puoi importarli nel tuo account di automazione.

### <a name="runbook"></a>Runbook

Importare il python runbook [Importare pacchetti Python 2 da pypi nell'account di automazione](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) di Azure dalla raccolta nell'account di automazione. Verificare che le impostazioni di esecuzione siano impostate su **Azure** e avviare il runbook con i parametri. Il runbook richiede un account RunAs per il funzionamento dell'account di automazione. Per ogni parametro assicurarsi di avviarlo con l'opzione come illustrato nell'elenco e nell'immagine seguenti:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<moduloPacchetto\>

![Elenco di pacchetti](media/python-packages/import-python-runbook.png)

Il runbook consente di specificare il pacchetto da scaricare. Ad esempio, l'uso `Azure` del parametro scarica tutti i moduli di Azure e tutte le dipendenze (circa 105).

Una volta completato il runbook, puoi controllare i **pacchetti Python 2** in Risorse **condivise** nel tuo account di automazione per verificare che il pacchetto sia stato importato correttamente.

## <a name="use-a-package-in-a-runbook"></a>Usare un pacchetto in un runbook

Con un pacchetto importato, è possibile utilizzarlo in un runbook. Nell'esempio seguente viene utilizzato il [pacchetto dell'utilità di automazione](https://github.com/azureautomation/azure_automation_utility)di Azure. Questo pacchetto rende più semplice l'uso di Python con Automazione di Azure. Per usare il pacchetto, seguire le istruzioni nel repository GitHub e aggiungerlo al runbook. Ad esempio, è `from azure_automation_utility import get_automation_runas_credential` possibile utilizzare per importare la funzione per il recupero dell'account RunAs.

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

Per iniziare con i runbook di Python 2, vedere [il mio primo runbook di Python 2.](automation-first-runbook-textual-python2.md)
