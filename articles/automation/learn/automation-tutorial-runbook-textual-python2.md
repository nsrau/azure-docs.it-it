---
title: Creare un runbook Python in Automazione di Azure
description: Esercitazione che illustra come creare, testare e pubblicare un semplice runbook Python.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: has-adal-ref
ms.openlocfilehash: 2b20796fdcf71ccfb60c519d081b42fba982f0b6
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608695"
---
# <a name="tutorial-create-a-python-runbook"></a>Esercitazione: Creare un runbook di Python

Questa esercitazione illustra la creazione di un [runbook Python](../automation-runbook-types.md#python-runbooks) in Automazione di Azure. I runbook Python vengono compilati in Python 2. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.

> [!div class="checklist"]
> * Creare un runbook Python semplice
> * Testare e pubblicare il runbook
> * Eseguire il processo del runbook e monitorarne lo stato
> * Aggiornare il runbook per avviare una macchina virtuale di Azure con i parametri del runbook

> [!NOTE]
> L'uso di un webhook per avviare un runbook Python non è supportato.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Account di Automazione](../automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
- Macchina virtuale di Azure. La macchina virtuale viene arrestata e avviata, quindi non deve essere una macchina virtuale di produzione.

## <a name="create-a-new-runbook"></a>Creare un nuovo runbook

Si inizia creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di Azure aprire l'account di automazione.

    La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di Automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).

2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.

3. Selezionare **Aggiungi runbook** per creare un nuovo runbook.

4. Denominare il runbook **MyFirstRunbook-Python**.

5. In **Tipo di runbook** selezionare **Python 2**.

6. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="add-code-to-the-runbook"></a>Aggiungere un codice al runbook

È ora possibile aggiungere un semplice comando per stampare il testo `Hello World`.

```python
print("Hello World!")
```

Fare clic su **Salva** per salvare il runbook.

## <a name="test-the-runbook"></a>Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione Bozza e visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.

2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.

3. Viene creato un [processo del runbook](../automation-runbook-execution.md) e il relativo stato viene visualizzato.
   Lo stato del processo verrà avviato come In coda per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passa ad Avvio in corso quando un ruolo di lavoro richiede il processo e quindi a In esecuzione quando l'esecuzione del runbook viene effettivamente avviata.

4. Al termine del processo del runbook, viene visualizzato l'output. In questo caso, verrà visualizzato l'output `Hello World`.

5. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="publish-and-start-the-runbook"></a>Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione.
Quando si pubblica un runbook, viene sovrascritta la versione pubblicata esistente con la versione bozza.
In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.

2. Se si scorre verso sinistra per visualizzare il runbook nella pagina **Runbook**, lo **Stato di creazione** mostrerà **Pubblicato**.

3. Scorrere verso destra per visualizzare il pannello **MyFirstRunbook-Python**.

   Le opzioni nella parte superiore consentono di avviare il runbook, visualizzarlo, pianificarlo per l'avvio in un momento successivo.

4. Fare clic su **Avvia** e quindi su **OK** quando si apre il pannello Avvia runbook.

5. Verrà aperto un riquadro per il processo del runbook creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare lo stato del processo.

6. Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.

7. Quando lo stato del runbook risulta Completato, fare clic su **Output**. Verrà aperta la pagina Output in cui è possibile visualizzare `Hello World`.

8. Chiudere il riquadro Output.

9. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output sarà visibile solo `Hello World`. In questo riquadro tuttavia potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Dettagliato ed Errore, se il runbook esegue la scrittura in questi flussi.

10. Chiudere il riquadro dei flussi e il riquadro dei processi per tornare al riquadro MyFirstRunbook-Python.

11. Fare clic su **Processi** per aprire la pagina Processi per questo runbook. In questa pagina sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.

12. È possibile fare clic su questo processo per aprire lo stesso pannello visualizzato quando è stato avviato il runbook. Questo riquadro consente di tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook specifico.

## <a name="add-authentication-to-manage-azure-resources"></a>Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure.
A tale scopo, lo script deve eseguire l'autenticazione tramite le credenziali dell'account di Automazione. Per semplificare l'autenticazione e l'interazione con le risorse di Azure, è possibile usare il [pacchetto di utilità di Automazione di Azure](https://github.com/azureautomation/azure_automation_utility).

> [!NOTE]
> Perché sia disponibile un certificato RunAs, è necessario che l'account di Automazione sia stato creato con l'entità servizio.
> Se l'account di Automazione non è stato creato con l'entità servizio, è possibile eseguire l'autenticazione seguendo la procedura descritta in [Eseguire l'autenticazione con le librerie di gestione di Azure per Python](/azure/python/python-sdk-azure-authenticate).

1. Aprire l'editor di testo facendo clic su **Modifica** nel pannello MyFirstRunbook-Python.

2. Aggiungere il codice seguente per l'autenticazione in Azure:

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

Per usare le macchine virtuali di Azure, creare un'istanza di [client di calcolo Azure per Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Usare il client di calcolo per avviare la macchina virtuale. Aggiungere il codice seguente al runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

`MyResourceGroup` è il nome del gruppo di risorse che contiene la macchina virtuale e `TestVM` è il nome della macchina virtuale che si desidera avviare.

Testare ed eseguire nuovamente il runbook per verificare che venga avviata la macchina virtuale.

## <a name="use-input-parameters"></a>Usare parametri di input

Attualmente, il runbook usa valori hardcoded per i nomi del gruppo di risorse e della macchina virtuale. A questo punto, aggiungere il codice che ottiene tali valori dai parametri di input.

Si usa la variabile `sys.argv` per ottenere i valori dei parametri. Aggiungere il codice seguente al runbook immediatamente dopo le altre istruzioni `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

In questo modo il modulo `sys` viene importato e crea due variabili per contenere i nomi del gruppo di risorse e della macchina virtuale. Si noti che l'elemento dell'elenco di argomenti, `sys.argv[0]`, è il nome dello script e non viene inserito dall'utente.

A questo punto è possibile modificare le ultime due righe del runbook per usare i valori del parametro di input invece dei valori hardcoded:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Quando si avvia un runbook Python, nel riquadro Test o come un runbook pubblicato, è possibile immettere i valori per i parametri nella pagina Avvia runbook alla voce **Parametri**.

Quando si inizia a digitare un valore nella prima casella, viene visualizzata una seconda casella e così via, per immettere il numero di parametri desiderato in base alle esigenze.

I valori sono disponibili per lo script come matrice `sys.argv`, come nel codice appena aggiunto.

Immettere il nome del gruppo di risorse come valore per il primo parametro e il nome della macchina virtuale da avviare come valore del secondo parametro.

![Immettere i valori dei parametri](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Fare clic su **OK** per avviare il runbook. Il runbook viene eseguito e avvia la macchina virtuale specificata.

## <a name="error-handling-in-python"></a>Gestione degli errori in Python

È anche possibile usare le convenzioni seguenti per recuperare flussi diversi dai runbook Python, inclusi i flussi di AVVISO, ERRORE e DEBUG.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

L'esempio seguente mostra l'utilizzo di questa convenzione in un blocco `try...except`.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Automazione di Azure non supporta `sys.stderr`.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare i runbook PowerShell, vedere [Creare un runbook PowerShell](automation-tutorial-runbook-textual-powershell.md).
- Per iniziare a usare runbook grafici, vedere [Creare un runbook grafico](automation-tutorial-runbook-graphical.md).
- Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Creare un runbook del flusso di lavoro PowerShell](automation-tutorial-runbook-textual.md).
- Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](../automation-runbook-types.md).
- Per altre informazioni sullo sviluppo di Azure con Python, vedere [Azure per sviluppatori Python](/azure/python/).
- Per visualizzare i runbook di Python 2 di esempio, vedere il [GitHub di Automazione di Azure](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
