---
title: Risolvere i problemi relativi agli elementi in Azure DevTest Labs - Documenti Microsoft
description: Informazioni su come risolvere i problemi che si verificano durante l'applicazione di elementi in una macchina virtuale Azure DevTest Labs.Learn how to troubleshoot issues that occur when applying artifacts in an Azure DevTest Labs virtual machine.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456980"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Risolvere i problemi relativi all'applicazione di elementi in una macchina virtuale Azure DevTest LabsTroubleshoot issues when applying artifacts in an Azure DevTest Labs virtual machine
L'applicazione di elementi in una macchina virtuale può avere esito negativo per vari motivi. In questo articolo viene illustrato alcuni dei metodi per identificare le possibili cause.

Per altre informazioni in qualsiasi momento di questo articolo, è possibile contattare gli esperti di Azure DevTest Labs (DTL) nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni supporto.   

> [!NOTE]
> Questo articolo si applica alle macchine virtuali Windows e non Windows.This article applies to both Windows and non-Windows virtual machines. Mentre ci sono alcune differenze, saranno chiamati in modo esplicito in questo articolo.

## <a name="quick-troubleshooting-steps"></a>Passaggi rapidi per la risoluzione dei problemi
Verificare che la macchina virtuale sia in esecuzione. DevTest Labs richiede che la macchina virtuale sia in esecuzione e che L'agente di macchine virtuali di [Microsoft Azure (agente VM)](../virtual-machines/extensions/agent-windows.md) sia installato e pronto.

> [!TIP]
> Nel **portale di Azure**passare alla pagina **Gestisci elementi** per la macchina virtuale per verificare se la macchina virtuale è pronta per l'applicazione di elementi. Viene visualizzato un messaggio nella parte superiore di quella pagina. 
> 
> Tramite **Azure PowerShell**, esaminare il flag **canApplyArtifacts**, che viene restituito solo quando si espande su un'operazione GET. Vedere il seguente comando di esempio:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Modalità di risoluzione dei problemi 
È possibile risolvere i problemi relativi alle macchine virtuali create utilizzando DevTest Labs e il modello di distribuzione di Resource Manager usando uno dei metodi seguenti:You can troubleshoot VMs created using DevTest Labs and the Resource Manager deployment model by using one of the following methods:

- **Portale di Azure:** ottimo se è necessario ottenere rapidamente un suggerimento visivo della causa del problema.
- **Azure PowerShell:** se si ha familiarità con un prompt di PowerShell, eseguire rapidamente una query sulle risorse devTest Labs usando i cmdlet di Azure PowerShell.Azure PowerShell - if you're comfortable with a PowerShell prompt, quickly query DevTest Labs resources using the Azure PowerShell cmdlets.

> [!TIP]
> Per altre informazioni su come esaminare l'esecuzione di elementi all'interno di una macchina virtuale, vedere Diagnosticare gli errori degli [elementi nel lab.](devtest-lab-troubleshoot-artifact-failure.md)

## <a name="symptoms-causes-and-potential-resolutions"></a>Sintomi, cause e potenziali soluzioni 

### <a name="artifact-appears-to-hang"></a>L'artefatto sembra bloccarsi   
Un elemento sembra bloccarsi fino alla scadenza di un periodo di timeout predefinito e l'elemento viene contrassegnato come **Non riuscito**.

Quando un elemento sembra bloccarsi, determinare innanzitutto dove è bloccato. Un elemento può essere bloccato in uno dei passaggi seguenti durante l'esecuzione:An artifact can be blocked at any of the following steps during execution:

- **Durante la richiesta iniziale**. DevTest Labs crea un modello di Azure Resource Manager per richiedere l'uso dell'estensione Script personalizzato (CSE). Pertanto, dietro le quinte, viene attivata la distribuzione di un gruppo di risorse. Quando si verifica un errore a questo livello, si ottengono i dettagli nei **registri attività** del gruppo di risorse per la macchina virtuale in questione.  
    - È possibile accedere al log attività dalla barra di spostamento della pagina della macchina virtuale del laboratorio. Quando viene selezionata, viene visualizzata una voce per **l'applicazione** di elementi alla macchina virtuale (se l'operazione Applica elementi è stata attivata direttamente) o **aggiungere o modificare macchine virtuali** (se l'operazione di applicazione degli elementi faceva parte del processo di creazione della macchina virtuale).
    - Cercare gli errori in queste voci. A volte, l'errore non verrà contrassegnato di conseguenza e dovrai esaminare ogni voce.
    - Quando si esaminano i dettagli di ogni voce, assicurarsi di esaminare il contenuto del payload JSON. È possibile che venga visualizzato un errore nella parte inferiore del documento.
- **Quando si tenta di eseguire l'elemento**. Potrebbe essere a causa di problemi di rete o di archiviazione. Vedere la rispettiva sezione più avanti in questo articolo per i dettagli. Può verificarsi anche a causa del modo in cui lo script viene creato. Ad esempio:
    - Uno script di PowerShell dispone di **parametri obbligatori,** ma non si riesce a passare un valore, perché si consente all'utente di lasciarlo vuoto o perché non si dispone di un valore predefinito per la proprietà nel file di definizione artifactfile.json. Lo script si bloccherà perché è in attesa dell'input dell'utente.
    - Uno script di PowerShell **richiede l'input dell'utente** come parte dell'esecuzione. Gli script devono essere scritti per funzionare in modo invisibile all'utente senza richiedere alcun intervento da parte dell'utente.
- **L'agente VM richiede molto tempo per essere pronto.** Quando la macchina virtuale viene avviata per la prima volta o quando l'estensione di script personalizzata viene installata per la prima volta per soddisfare la richiesta di applicazione degli elementi, la macchina virtuale potrebbe richiedere l'aggiornamento dell'agente di macchine virtuali o attendere l'inizializzazione dell'agente di macchine virtuali. Potrebbero esserci servizi da cui dipende l'agente di macchine virtuali che richiede molto tempo per l'inizializzazione. In questi casi, vedere [Panoramica](../virtual-machines/extensions/agent-windows.md) di Agente macchina virtuale di Azure per ulteriori informazioni sulla risoluzione dei problemi.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Per verificare se l'elemento sembra bloccarsi a causa dello script

1. Accedere alla macchina virtuale in questione.
2. Copiare lo script in locale nella macchina virtuale `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`o individuarlo nella macchina virtuale in . È il percorso in cui vengono scaricati gli script dell'elemento.
3. Utilizzando un prompt dei comandi con privilegi elevati, eseguire lo script localmente, fornendo gli stessi valori di parametro utilizzati per causare il problema.
4. Determinare se lo script soffre di qualsiasi comportamento indesiderato. In tal caso, richiedere un aggiornamento dell'elemento (se proviene dal repository pubblico); oppure, apportare le correzioni da soli (se proviene dal repository privato).

> [!TIP]
> È possibile correggere i problemi relativi agli elementi ospitati nel nostro [repository pubblico](https://github.com/Azure/azure-devtestlab) e inviare le modifiche per la revisione e l'approvazione. Vedere la sezione **Contributi** nel [documento README.md.](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)
> 
> Per informazioni sulla scrittura di elementi personalizzati, vedere [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) documento.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>To verify if the artifact appears to hang because of the VM Agent:
1. Accedere alla macchina virtuale in questione.
2. Utilizzando Esplora file, passare a **C:.**
3. Individuare e aprire il file **WaAppAgent.log**.
4. Cercare le voci che mostrano quando viene avviato l'agente di macchine virtuali e quando sta terminando l'inizializzazione, ovvero viene inviato il primo heartbeat. Favorire le voci più recenti o specificamente quelli intorno al periodo di tempo per il quale si verifica il problema.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    In questo esempio, è possibile vedere che l'ora di avvio dell'agente di macchine virtuali ha richiesto 10 minuti e 20 secondi perché è stato inviato un heartbeat. La causa in questo caso è che il servizio di configurazione in stato di e-indele che richiede molto tempo per l'avvio.

> [!TIP]
> Per informazioni generali sulle estensioni di Azure, vedere [Funzionalità e estensioni](../virtual-machines/extensions/overview.md)di macchine virtuali di Azure.For general information about Azure extensions, see Azure virtual machine extensions and features .

## <a name="storage-errors"></a>Errori di archiviazione
DevTest Labs richiede l'accesso all'account di archiviazione del lab creato per memorizzare nella cache gli elementi. Quando DevTest Labs applica un elemento, leggerà la configurazione dell'elemento e i relativi file dai repository configurati. Per impostazione predefinita, DevTest Labs configura l'accesso **all'archivio degli elementi pubblici.**

A seconda della configurazione di una macchina virtuale, potrebbe non avere accesso diretto a questo repository. Pertanto, per impostazione specifica, DevTest Labs memorizza nella cache gli elementi in un account di archiviazione creato quando la lab viene inizializzata per la prima volta.

Se l'accesso a questo account di archiviazione è bloccato in qualsiasi modo, come quando il traffico viene bloccato dalla macchina virtuale al servizio di archiviazione di Azure, è possibile che venga visualizzato un errore simile al seguente:If access to this storage account is blocked in any, as when when traffic is blocked from the VM to the Azure Storage service, you may see an error similar to the following:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

L'errore precedente verrà visualizzato nella sezione **Messaggio di distribuzione** della pagina Risultati **elemento** in **Gestisci elementi**. Verrà inoltre visualizzato nei **registri** attività nel gruppo di risorse della macchina virtuale in questione.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Per verificare che la comunicazione con il servizio Archiviazione di Azure non venga bloccata:To ensure communication to the Azure Storage service isn't being blocked:

- **Verificare la presenza**di gruppi di sicurezza di rete aggiunti. È possibile che sia stato aggiunto un criterio di sottoscrizione in cui i gruppi di sicurezza di rete vengono configurati automaticamente in tutte le reti virtuali. Influirebbe anche sulla rete virtuale predefinita del lab, se usata, o su un'altra rete virtuale configurata nel lab, usata per la creazione di macchine virtuali.
- **Controllare l'account di archiviazione del lab predefinito,** ovvero il primo account di archiviazione creato al momento della creazione del lab, il\<cui\>nome inizia in genere con la lettera "a" e termina con un numero a più cifre, ovvero un nome di laboratorio.
    1. Passare al gruppo di risorse del lab.
    2. Individuare la risorsa di tipo account di **archiviazione**, il cui nome corrisponde alla convenzione.
    3. Passare alla pagina dell'account di archiviazione denominata **Firewall e reti virtuali.**
    4. Assicurarsi che sia impostato su **Tutte le reti**. Se è selezionata l'opzione **Reti selezionate,** verificare che le reti virtuali del lab usate per creare macchine virtuali vengano aggiunte all'elenco.

Per una risoluzione dei problemi più approfondita, vedere [Configurare i firewall e le reti virtuali](../storage/common/storage-network-security.md)di Archiviazione di Azure.

> [!TIP]
> **Verificare le regole**dei gruppi di sicurezza di rete . Usare la [verifica del flusso IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) per verificare che una regola in un gruppo di sicurezza di rete blocchi il traffico da o verso una macchina virtuale. È inoltre possibile esaminare le regole valide del gruppo di sicurezza per verificare l'esito **relè** della regola del gruppo di sicurezza di sicurezza in ingresso. Per altre informazioni, vedere [Uso di regole di sicurezza effettive per risolvere i problemi di flusso del traffico delle macchine virtuali](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Altre fonti di errore
Ci sono altre possibili fonti di errore meno frequenti. Assicurati di valutare ciascuno per vedere se si applica al tuo caso. Eccone uno: 

- Token di **accesso personale scaduto per il repository privato.** Quando è scaduto, l'elemento non verrà elencato e tutti gli script che fanno riferimento agli elementi da un repository con un token di accesso privato scaduto avranno esito negativo di conseguenza.

## <a name="next-steps"></a>Passaggi successivi
Se nessuno di questi errori si è verificato e non è ancora possibile applicare elementi, è possibile archiviare un evento imprevisto di supporto di Azure.If none of these errors occurred and you still can't apply artifacts, you can file an Azure support incident. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.

