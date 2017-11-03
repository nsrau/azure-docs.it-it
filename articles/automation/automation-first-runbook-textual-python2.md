---
title: Il primo runbook Python in Automazione di Azure | Microsoft Docs
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook Python.
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: eslesar
ms.openlocfilehash: 4e7b3049fff76c86956e08d71b22a0f8dbf55b0e
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="my-first-python-runbook"></a>Il primo runbook Python

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Questa esercitazione illustra la creazione di un [runbook Python](automation-runbook-types.md#python-runbooks) in Automazione di Azure. Si inizia con un runbook semplice che sarà testato e pubblicato, quindi viene illustrato come tenere traccia dello stato del processo del runbook. Si modifica quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si rende infine il runbook più affidabile aggiungendo i relativi parametri.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure.  Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. La macchina virtuale viene arrestata e avviata, quindi non deve essere una macchina virtuale di produzione.

## <a name="create-a-new-runbook"></a>Creare un nuovo runbook

Si inizia creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di Azure aprire l'account di automazione.
   La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).
1. Fare clic nel pannello **Runbook** per aprire l'elenco dei runbook.
   ![RunbooksControl](media/automation-first-runbook-textual-python/runbooks-control-tiles.png)
1. Creare un nuovo runbook facendo clic sul pulsante **Aggiungi runbook** e quindi su **Crea un nuovo runbook**.
1. Denominare il runbook *MyFirstRunbook-Python*.
1. In questo caso si sta creando un [runbook Python](automation-runbook-types.md#python-runbooks), quindi selezionare **Python 2** per **Tipo di Runbook**.
1. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="add-code-to-the-runbook"></a>Aggiungere un codice al runbook

È ora possibile aggiungere un semplice comando per stampare il testo "Hello World":

```python
print("Hello World!")
```

Fare clic su **Salva** per salvare il runbook.

## <a name="test-the-runbook"></a>Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.
   ![Test Pane](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
1. Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato.
   Lo stato del processo verrà avviato come *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.
1. Al termine del processo del runbook, viene visualizzato l'output. In questo caso, dovrebbe essere visualizzato *Hello World*.
1. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="publish-and-start-the-runbook"></a>Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità di bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione.
Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza.
In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
   ![Pulsante Publish](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Se ora si scorre verso sinistra per visualizzare il runbook nel pannello **Runbook**, come **Stato di creazione** viene visualizzato **Pubblicato**.
1. Scorrere verso destra per visualizzare il pannello **MyFirstRunbook-Python**.
   Le opzioni nella parte superiore consentono di avviare il runbook, visualizzarlo, pianificarlo per l'avvio in un momento successivo o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP.
1. Per avviare il runbook, fare clic su **Avvia** e quindi su **OK** quando si apre il pannello Avvia runbook.
1. Verrà aperto un pannello per il processo del runbook creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare lo stato del processo.
1. Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.
1. Quando lo stato del runbook risulta *Completato*fare clic su **Output**. Viene aperto il pannello Output dove si può vedere il testo *Hello World*.
1. Chiudere il riquadro Output.
1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output dovrebbe essere visibile solo *Hello World* , ma potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Verbose ed Error se il runbook scrive in questi flussi.
1. Chiudere il riquadro dei flussi e il riquadro dei processi per tornare al riquadro MyFirstRunbook-Python.
1. Fare clic su **Processi** per aprire il pannello dei processi per questo runbook. Sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.
1. È possibile fare clic su questo processo per aprire lo stesso pannello visualizzato quando è stato avviato il runbook. In questo modo è possibile tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## <a name="add-authentication-to-manage-azure-resources"></a>Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure.
Per gestire le risorse di Azure, lo script deve eseguire l'autenticazione tramite le credenziali dell'[account di Automazione](automation-offering-get-started.md).

> [!NOTE]
> L'account di Automazione deve essere stato creato con la funzionalità entità servizio perché sia disponibile un certificato runas.
> Se l'account di Automazione non è stato creato con l'entità servizio, è possibile eseguire l'autenticazione tramite il metodo descritto in [Eseguire l'autenticazione con le librerie di gestione di Azure per Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Aprire l'editor di testo facendo clic su **Modifica** nel pannello MyFirstRunbook-Python.
1. Aggiungere il codice seguente per l'autenticazione in Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Aggiungere il codice per creare client Python di calcolo e avviare la macchina virtuale

Per usare le macchine virtuali di Azure, creare un'istanza di [client di calcolo Azure per Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.compute.computemanagementclient?view=azure-python).

Usare il client di calcolo per avviare la macchina virtuale. Aggiungere il codice seguente al runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

_MyResourceGroup_ è il nome del gruppo di risorse che contiene la macchina virtuale e _TestVM_ è il nome della macchina virtuale che si desidera avviare. 

Testare ed eseguire nuovamente il runbook per verificare che venga avviata la macchina virtuale.

## <a name="use-input-parameters"></a>Usare parametri di input

Attualmente, il runbook usa valori hardcoded per i nomi del gruppo di risorse e della macchina virtuale.
A questo punto, aggiungere il codice che ottiene tali valori dai parametri di input.

Usiamo la variabile `sys.argv` per ottenere i valori dei parametri.
Aggiungere il codice seguente al runbook immediatamente dopo le altre istruzioni `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

In questo modo il modulo `sys` viene importato e crea due variabili per contenere i nomi del gruppo di risorse e della macchina virtuale.
Si noti che l'elemento dell'elenco di argomenti, `sys.argv[0]`, è il nome dello script e non viene inserito dall'utente.

A questo punto è possibile modificare le ultime due righe del runbook per usare i valori del parametro di input invece dei valori hardcoded:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Quando si avvia un runbook Python, nel pannello **Test** o come un runbook pubblicato, è possibile immettere i valori per i parametri nel pannello **Avvia Runbook** alla voce **Parametri**.

![Casella del valore del parametro](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Dopo avere avviato l'immissione di un valore nella prima casella, viene visualizzata una seconda casella, poi una terza e così via, per immettere tutti i valori dei parametri in base alle esigenze.

I valori sono disponibili per lo script come matrice `sys.argv`, come nel codice appena aggiunto.

Immettere il nome del gruppo di risorse come valore per il primo parametro e il nome della macchina virtuale da avviare come valore del secondo parametro.

![Immettere i valori dei parametri](media/automation-first-runbook-textual-python/runbook-python-params.png)

Fare clic su **OK** per avviare il runbook. Il runbook viene eseguito e avvia la macchina virtuale specificata.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md)
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
* Per altre informazioni sullo sviluppo di Azure con Python, vedere [Azure per sviluppatori Python](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
* Per visualizzare i runbook di Python 2 di esempio, vedere il [GitHub di Automazione di Azure](https://docs.microsoft.com/en-us/python/azure/?view=azure-python).
