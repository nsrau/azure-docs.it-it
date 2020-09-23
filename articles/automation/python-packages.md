---
title: Gestire pacchetti Python 2 in Automazione di Azure
description: Questo articolo descrive come gestire pacchetti Python 2 in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 1ab0b2def1a22470c1d0b6339e1525cd683b4a0b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987561"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gestire pacchetti Python 2 in Automazione di Azure

Automazione di Azure consente di eseguire runbook Python 2 in Azure e nei ruoli di lavoro ibridi per runbook Linux. Per favorire la semplificazione dei runbook, è possibile importare i moduli necessari usando pacchetti Python. Questo articolo descrive come gestire e usare pacchetti Python in Automazione di Azure.

## <a name="import-packages"></a>Importare pacchetti

Nell'account di Automazione selezionare **Pacchetti Python 2** in **Risorse condivise**. Fare clic su **+ Aggiungi pacchetto Python 2**.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="Screenshot della pagina pacchetti Python 2 Mostra i pacchetti Python 2 nel menu a sinistra e aggiungere un pacchetto python 2 evidenziato.":::

Nella pagina Aggiungi pacchetto Python 2 selezionare un pacchetto locale da caricare. Il pacchetto può essere un file con estensione **whl** o **tar.gz**. Dopo aver selezionato il pacchetto, fare clic su **OK** per caricarlo.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="Screenshot mostra la pagina Aggiungi pacchetto python 2 con un file tar. gz caricato selezionato.":::

Dopo l'importazione, il pacchetto viene elencato nella pagina dei pacchetti Python 2 dell'account di Automazione. Se è necessario rimuovere un pacchetto, selezionarlo e fare clic su **Elimina**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Screenshot mostra la pagina dei pacchetti Python 2 dopo l'importazione di un pacchetto.":::

## <a name="import-packages-with-dependencies"></a>Importare pacchetti con dipendenze

Automazione di Azure non risolve le dipendenze per i pacchetti Python durante il processo di importazione. Esistono due modi per importare un pacchetto con tutte le relative dipendenze. Per importare i pacchetti nell'account di Automazione è necessario usare solo uno dei passaggi seguenti.

### <a name="manually-download"></a>Download manuale

In un computer Windows a 64 bit con [Python 2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) installato, eseguire questo comando per scaricare un pacchetto e tutte le relative dipendenze:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Una volta scaricati i pacchetti, è possibile importarli nell'account di Automazione.

### <a name="runbook"></a>Runbook

 Per ottenere un runbook, [importare i pacchetti Python 2 da pypi nell'account di Automazione di Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) dalla raccolta nell'account di automazione. Verificare che le impostazioni di esecuzione siano impostate su **Azure** e avviare il runbook con i parametri. Per il corretto funzionamento dell'account di Automazione, il runbook richiede un account RunAs. Assicurarsi di avviare ogni parametro con l'opzione come illustrato nell'elenco e nell'immagine seguenti:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Screenshot mostra la pagina di panoramica per import_py2package_from_pypi con il riquadro Avvia Runbook sul lato destro.":::

Il runbook consente di specificare il pacchetto da scaricare. Ad esempio, l'uso del parametro `Azure` scarica tutti i moduli di Azure e tutte le dipendenze (circa 105).

Al termine del runbook è possibile controllare **Pacchetti Python 2** in **Risorse condivise** nell'account di Automazione per verificare che il pacchetto sia stato importato correttamente.

## <a name="use-a-package-in-a-runbook"></a>Usare un pacchetto in un runbook

Un pacchetto importato può essere usato in un runbook. L'esempio seguente usa il [pacchetto di utilità di Automazione di Azure](https://github.com/azureautomation/azure_automation_utility). Questo pacchetto rende più semplice l'uso di Python con Automazione di Azure. Per usare il pacchetto, seguire le istruzioni nel repository GitHub e aggiungerlo al runbook. Ad esempio, è possibile usare `from azure_automation_utility import get_automation_runas_credential` per importare la funzione per il recupero dell'account RunAs.

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

Per sviluppare e testare i runbook Python 2 offline, è possibile usare il modulo [Azure Automation Python emulated assets](https://github.com/azureautomation/python_emulated_assets) in GitHub. Questo modulo consente di fare riferimento alle risorse condivise, ad esempio credenziali, variabili, connessioni e certificati.

## <a name="next-steps"></a>Passaggi successivi

Per preparare un runbook Python, vedere [Creare un runbook di Python](learn/automation-tutorial-runbook-textual-python2.md).
