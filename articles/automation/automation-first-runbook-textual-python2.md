---
title: Il primo runbook Python in Automazione di Azure
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook Python.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b788e5bda65702305ed6f6b001b57c28939aa875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405904"
---
# <a name="my-first-python-runbook"></a>Il primo runbook Python

> [!div class="op_single_selector"]
> - [Grafico](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Questa esercitazione illustra la creazione di un [runbook Python](automation-runbook-types.md#python-runbooks) in Automazione di Azure. Iniziare con un runbook semplice che viene testato e pubblicato. Si modifica quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si rende infine il runbook più affidabile aggiungendo i relativi parametri.

> [!NOTE]
> L'utilizzo di un webhook per avviare un runbook Python non è supportato.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
- Macchina virtuale di Azure. La macchina virtuale viene arrestata e avviata, quindi non deve essere una macchina virtuale di produzione.

## <a name="create-a-new-runbook"></a>Creare un nuovo runbook

Si inizia creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di Azure aprire l'account di automazione.

    La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di Automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).<br>

1. Selezionare **Runbooks** in **Process Automation** per aprire l'elenco dei runbook.
1. Selezionare **Aggiungi un runbook** per creare un nuovo runbook.
1. Denominare il runbook **MyFirstRunbook-Python**.
1. Selezionare **Python 2** per **Tipo Runbook**.
1. Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## <a name="add-code-to-the-runbook"></a>Aggiungere un codice al runbook

Ora si aggiunge un semplice `Hello World`comando per stampare il testo .

```python
print("Hello World!")
```

Fare clic su **Salva** per salvare il runbook.

## <a name="test-the-runbook"></a>Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione Bozza e visualizzarne l'output in modo interattivo.

1. Fare clic su **Pannello di test** per aprire il pannello di test.
1. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
1. Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato.
   Lo stato del processo inizia come In coda, a indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passa ad Avvio in corso quando un ruolo di lavoro richiede il processo e quindi a In esecuzione quando l'esecuzione del runbook viene effettivamente avviata.
1. Al termine del processo del runbook, viene visualizzato l'output. In questo caso, `Hello World`si dovrebbe vedere .
1. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="publish-and-start-the-runbook"></a>Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione.
Quando si pubblica un runbook, si sovrascrive la versione pubblicata esistente con la versione bozza.
In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.
1. Se si scorre verso sinistra per visualizzare il runbook nella pagina **Runbooks,** verrà visualizzato **lo stato di creazione** **pubblicato**.
1. Scorrere verso destra per visualizzare il pannello **MyFirstRunbook-Python**.
   Le opzioni nella parte superiore consentono di avviare il runbook, visualizzare il runbook o pianificarne l'avvio in futuro.
2. Fare clic **sul pulsante Start** e quindi **scegliere OK** all'apertura del pannello Avvia Runbook.
3. Viene aperto un riquadro Processo per il processo runbook creato. È possibile chiudere questo riquadro, ma lasciarlo aperto in modo da poter controllare lo stato di avanzamento del processo.
1. Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.
2. Quando lo stato del runbook è Completato, fare clic su **Output**. Viene aperto il riquadro Output, in cui è possibile visualizzare `Hello World`.
3. Chiudere il riquadro Output.
4. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Dovresti vedere `Hello World` solo nel flusso di output. Tuttavia, questo riquadro può mostrare altri flussi per un processo di runbook, ad esempio Verbose ed Error, se il runbook scrive in essi.
5. Chiudere il riquadro dei flussi e il riquadro dei processi per tornare al riquadro MyFirstRunbook-Python.
6. Fare clic su **Processi** per aprire la pagina Processi per questo runbook. In questa pagina sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.
7. È possibile fare clic su questo processo per aprire lo stesso pannello visualizzato quando è stato avviato il runbook. Questo riquadro consente di tornare indietro nel tempo e visualizzare i dettagli di qualsiasi processo creato per un runbook specifico.

## <a name="add-authentication-to-manage-azure-resources"></a>Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure.
A tale scopo, lo script deve eseguire l'autenticazione utilizzando le credenziali dell'account di automazione. Per semplificare l'autenticazione e l'interazione con le risorse di Azure, è possibile usare il [pacchetto di utilità di Automazione di Azure](https://github.com/azureautomation/azure_automation_utility).

> [!NOTE]
> L'account di automazione deve essere stato creato con la funzionalità dell'entità servizio affinché sia presente un certificato RunAs.
> Se l'account di automazione non è stato creato con l'entità servizio, è possibile eseguire l'autenticazione come descritto in [Authenticate with the Azure Management Libraries for Python](/azure/python/python-sdk-azure-authenticate).

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

Dove `MyResourceGroup` è il nome del gruppo di `TestVM` risorse che contiene la macchina virtuale ed è il nome della macchina virtuale che si vuole avviare.

Testare ed eseguire nuovamente il runbook per verificare che venga avviata la macchina virtuale.

## <a name="use-input-parameters"></a>Usare parametri di input

Il runbook usa attualmente valori hardcoded per i nomi del gruppo di risorse e della macchina virtuale.
A questo punto, aggiungere il codice che ottiene tali valori dai parametri di input.

Si usa la variabile `sys.argv` per ottenere i valori dei parametri.
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
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Quando si avvia un runbook Python (nel riquadro Test o come runbook pubblicato), è possibile immettere i valori per i parametri nella pagina Start Runbook in **Parameters**.

Dopo aver iniziato a immettere un valore nella prima casella, viene visualizzato un secondo e così via, in modo da poter immettere tutti i valori di parametro necessari.

I valori sono disponibili per `sys.argv` lo script nella matrice come nel codice appena aggiunto.

Immettere il nome del gruppo di risorse come valore per il primo parametro e il nome della macchina virtuale da avviare come valore del secondo parametro.

![Immettere i valori dei parametri](media/automation-first-runbook-textual-python/runbook-python-params.png)

Fare clic su **OK** per avviare il runbook. Il runbook viene eseguito e avvia la macchina virtuale specificata.

## <a name="error-handling-in-python"></a>Gestione degli errori in PythonError Handling in Python

È inoltre possibile utilizzare le convenzioni seguenti per recuperare vari flussi dai runbook python, inclusi i flussi WARNING, ERROR e DEBUG.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Nell'esempio seguente viene illustrata questa convenzione utilizzata in un `try...except` blocco.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Automazione di `sys.stderr`Azure non supporta .

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare i runbook di PowerShell, vedere il mio primo runbook di [PowerShell.](automation-first-runbook-textual-powershell.md)
- Per iniziare con i runbook grafici, vedere [Il mio primo runbook grafico](automation-first-runbook-graphical.md).
- Per iniziare a usare i runbook del flusso di lavoro di PowerShell, vedere [Il mio primo runbook del flusso di lavoro](automation-first-runbook-textual.md)di PowerShell .
- Per altre informazioni sui tipi di runbook, sui relativi vantaggi e limitazioni, vedere [Tipi di runbook di Automazione di Azure.](automation-runbook-types.md)
- Per altre informazioni sullo sviluppo di Azure con Python, vedere [Azure per sviluppatori Python](/azure/python/).
- Per visualizzare i runbook di Python 2 di esempio, vedere il [GitHub di Automazione di Azure](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
