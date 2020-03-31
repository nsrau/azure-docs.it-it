---
title: Gestire pacchetti Python 2 in Automazione di Azure
description: Questo articolo descrive come gestire pacchetti Python 2 in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417636"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gestire pacchetti Python 2 in Automazione di Azure

Automazione di Azure consente di eseguire runbook Python 2 in Azure e nei ruoli di lavoro ibridi per runbook Linux. Per favorire la semplificazione dei runbook, è possibile importare i moduli necessari usando pacchetti Python. Questo articolo descrive come gestire e usare pacchetti Python in Automazione di Azure.

## <a name="import-packages"></a>Importare pacchetti

Nell'account di Automazione selezionare **Pacchetti Python 2** in **Risorse condivise**. Fare clic su **+ Aggiungi pacchetto Python 2**.

![Aggiungere un pacchetto Python](media/python-packages/add-python-package.png)

Nella pagina **Aggiungi pacchetto Python 2** selezionare un pacchetto locale da caricare. Il pacchetto può essere un file con estensione `.whl` o `.tar.gz`. Dopo aver selezionato il pacchetto, fare clic su **OK** per caricarlo.

![Aggiungere un pacchetto Python](media/python-packages/upload-package.png)

Una volta che un pacchetto è stato importato, è elencato nella pagina **dei pacchetti Python 2** nel tuo account di automazione. Se è necessario rimuovere un pacchetto, selezionare il pacchetto da eliminare e scegliere **Elimina** nella pagina dei pacchetti.

![Elenco di pacchetti](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importare pacchetti con dipendenzeImport packages with dependencies

L'automazione di Azure non risolve le dipendenze per i pacchetti python durante il processo di importazione. Esistono due modi per importare un pacchetto con tutte le relative dipendenze. Solo uno dei seguenti passaggi deve essere utilizzato per importare i pacchetti nel tuo account di automazione.

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

Il runbook consente di specificare il pacchetto `Azure` da scaricare, ad esempio, (il quarto parametro) scaricherà tutti i moduli di Azure e tutte le relative dipendenze, ovvero circa 105.The runbook allows you to specify what package to download, for example, (the fourth parameter) will download all Azure modules and all its dependencies, which is about 105.

Una volta completato il runbook, puoi controllare la pagina dei **pacchetti Python 2** in Risorse **condivise** nel tuo account di automazione per verificare che il pacchetto sia stato importato correttamente.

## <a name="use-a-package-in-a-runbook"></a>Usare un pacchetto in un runbook

Dopo aver importato un pacchetto, è ora possibile utilizzarlo in un runbook. L'esempio seguente usa il [ pacchetto di utilità di Automazione di Azure](https://github.com/azureautomation/azure_automation_utility). Questo pacchetto rende più semplice l'uso di Python con Automazione di Azure. Per usare il pacchetto, seguendo le istruzioni nel repository GitHub aggiungerlo al runbook tramite `from azure_automation_utility import get_automation_runas_credential`, ad esempio, per importare la funzione per il recupero dell'account RunAs.

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
