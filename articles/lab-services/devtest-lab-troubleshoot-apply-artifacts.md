---
title: Risolvere i problemi relativi agli elementi in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come risolvere i problemi che si verificano quando si applicano elementi in una macchina virtuale Azure DevTest Labs.
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
ms.openlocfilehash: a0505b987deb67f93de6f6166154211359515ad7
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807888"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Risolvere i problemi relativi all'applicazione di elementi in una macchina virtuale Azure DevTest Labs
L'applicazione di artefatti in una macchina virtuale può avere esito negativo per diversi motivi. Questo articolo illustra alcuni dei metodi che consentono di identificare le possibili cause.

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure DevTest Labs (DTL) sui [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottieni supporto.   

> [!NOTE]
> Questo articolo si applica alle macchine virtuali Windows e non Windows. Sebbene esistano alcune differenze, verranno denominate in modo esplicito in questo articolo.

## <a name="quick-troubleshooting-steps"></a>Passaggi rapidi per la risoluzione dei problemi
Verificare che la macchina virtuale sia in esecuzione. DevTest Labs richiede che la VM sia in esecuzione e che l' [agente di macchine virtuali Microsoft Azure (agente VM)](../virtual-machines/extensions/agent-windows.md) sia installato e pronto.

> [!TIP]
> Nella **portale di Azure**passare alla pagina **Gestisci elementi** della macchina virtuale per verificare se la macchina virtuale è pronta per l'applicazione di artefatti. Viene visualizzato un messaggio nella parte superiore della pagina. 
> 
> Utilizzando **Azure PowerShell**, controllare il flag **canApplyArtifacts**, che viene restituito solo quando si espande un'operazione get. Vedere il comando di esempio seguente:

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
È possibile risolvere i problemi relativi alle macchine virtuali create con DevTest Labs e al modello di distribuzione Gestione risorse usando uno dei metodi seguenti:

- **Portale di Azure** -ottimo se è necessario ottenere rapidamente un suggerimento visivo di ciò che potrebbe causare il problema.
- **Azure PowerShell** : se si ha familiarità con un prompt di PowerShell, è possibile eseguire rapidamente query sulle risorse di DevTest Labs usando i cmdlet di Azure PowerShell.

> [!TIP]
> Per altre informazioni su come esaminare l'esecuzione degli artefatti all'interno di una macchina virtuale, vedere [diagnosticare gli errori degli elementi nel Lab](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Sintomi, cause e possibili risoluzioni 

### <a name="artifact-appears-to-hang"></a>L'artefatto sembra bloccarsi   
Un artefatto sembra bloccarsi fino alla scadenza di un periodo di timeout predefinito e l'artefatto viene contrassegnato come **non riuscito**.

Quando un artefatto sembra bloccarsi, determinare innanzitutto dove è bloccato. Durante l'esecuzione di un artefatto può essere bloccato in uno dei passaggi seguenti:

- **Durante la richiesta iniziale**. DevTest Labs crea un modello di Azure Resource Manager per richiedere l'uso dell'estensione di script personalizzata (CSE). Quindi, dietro le quinte, viene attivata una distribuzione del gruppo di risorse. Quando si verifica un errore a questo livello, si ottengono i dettagli nei **log attività** del gruppo di risorse per la macchina virtuale in questione.  
    - È possibile accedere al log attività dalla barra di spostamento della pagina VM Lab. Quando si seleziona questa opzione, viene visualizzata una voce per **applicare gli artefatti alla macchina virtuale** (se l'operazione applica elementi è stata attivata direttamente) o **aggiungere o modificare macchine virtuali** (se l'operazione di applicazione degli artefatti faceva parte del processo di creazione della macchina virtuale).
    - Individuare gli errori in queste voci. In alcuni casi, l'errore non verrà contrassegnato di conseguenza e sarà necessario esaminare ogni voce.
    - Quando si esaminano i dettagli di ogni voce, assicurarsi di esaminare il contenuto del payload JSON. È possibile che venga visualizzato un errore nella parte inferiore del documento.
- **Quando si tenta di eseguire l'artefatto**. Il problema potrebbe essere causato da problemi di rete o di archiviazione. Per informazioni dettagliate, vedere la sezione corrispondente più avanti in questo articolo. Può anche verificarsi a causa del modo in cui viene creato lo script. ad esempio:
    - Uno script di PowerShell ha **parametri obbligatori**, ma uno non riesce a passarvi un valore, perché consente all'utente di lasciarlo vuoto o perché non si dispone di un valore predefinito per la proprietà nel file di definizione artifactfile. JSON. Lo script si bloccherà perché è in attesa dell'input dell'utente.
    - Uno script di PowerShell **richiede l'input dell'utente** come parte dell'esecuzione. Gli script devono essere scritti per funzionare in modo invisibile all'utente senza richiedere alcun intervento da parte dell'utente.
- **L'agente VM impiega molto tempo per essere pronto**. Quando la macchina virtuale viene avviata per la prima volta o quando l'estensione di script personalizzata viene installata per la prima volta per soddisfare la richiesta di applicazione degli artefatti, è possibile che la macchina virtuale richieda l'aggiornamento dell'agente di macchine virtuali o l'inizializzazione dell'agente di macchine virtuali. Potrebbero essere presenti servizi da cui l'agente di macchine virtuali dipende da molto tempo per l'inizializzazione. In questi casi, vedere [Panoramica dell'agente di macchine virtuali di Azure](/virtual-machines/extensions/agent-windows.md) per ulteriori informazioni sulla risoluzione dei problemi.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Per verificare se l'artefatto sembra bloccarsi a causa dello script

1. Accedere alla macchina virtuale in questione.
2. Copiare lo script localmente nella macchina virtuale o individuarlo nella macchina virtuale in `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`. Si tratta della posizione in cui vengono scaricati gli script degli artefatti.
3. Utilizzando un prompt dei comandi con privilegi elevati, eseguire lo script localmente, specificando gli stessi valori dei parametri utilizzati per generare il problema.
4. Determinare se lo script è affetto da un comportamento indesiderato. In tal caso, richiedere un aggiornamento all'artefatto (se presente nel repository pubblico); in alternativa, apportare le correzioni manualmente, se provenienti dal repository privato.

> [!TIP]
> È possibile correggere i problemi con gli elementi ospitati nel [repository pubblico](https://github.com/Azure/azure-devtestlab) e inviare le modifiche per la revisione e l'approvazione. Vedere la sezione **contributi** nel documento di [Readme.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) .
> 
> Per informazioni sulla scrittura di elementi personalizzati, vedere documento [Authoring.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) .

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Per verificare se l'artefatto sembra bloccarsi a causa dell'agente di macchine virtuali:
1. Accedere alla macchina virtuale in questione.
2. Tramite Esplora file passare a **C:\WindowsAzure\logs**.
3. Individuare e aprire il file **WaAppAgent. log**.
4. Cercare le voci che mostrano quando viene avviato l'agente di macchine virtuali e quando termina l'inizializzazione, ovvero viene inviato il primo heartbeat. Preferire le voci più recenti o in particolare quelle relative al periodo di tempo in cui si verifica il problema.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    In questo esempio, è possibile vedere che l'ora di inizio dell'agente VM ha richiesto 10 minuti e 20 secondi perché è stato inviato un heartbeat. La causa in questo caso è il fatto che il servizio OOBE impiega molto tempo per l'avvio.

> [!TIP]
> Per informazioni generali sulle estensioni di Azure, vedere [estensioni e funzionalità delle macchine virtuali di Azure](/virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Errori di archiviazione
DevTest Labs richiede l'accesso all'account di archiviazione del Lab creato per memorizzare nella cache gli artefatti. Quando DevTest Labs applica un artefatto, la configurazione dell'artefatto e i relativi file vengono letti dai repository configurati. Per impostazione predefinita, DevTest Labs configura l'accesso al **repository di artefatti pubblici**.

A seconda della configurazione di una macchina virtuale, potrebbe non avere accesso diretto a questo repository. Pertanto, per impostazione predefinita, DevTest Labs memorizza nella cache gli elementi in un account di archiviazione creato quando il Lab viene inizializzato per la prima volta.

Se l'accesso a questo account di archiviazione viene bloccato in qualsiasi modo, ad esempio quando il traffico viene bloccato dalla macchina virtuale al servizio di archiviazione di Azure, potrebbe essere visualizzato un errore simile a quello riportato di seguito:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

L'errore precedente verrà visualizzato nella sezione **messaggio di distribuzione** della pagina **risultati artefatto** in **Gestisci elementi**. Verrà visualizzato anche nei **log attività** del gruppo di risorse della macchina virtuale in questione.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Per assicurarsi che la comunicazione con il servizio di archiviazione di Azure non venga bloccata:

- Controllare se sono stati **aggiunti gruppi di sicurezza di rete (NSG)** . È possibile che sia stato aggiunto un criterio di sottoscrizione in cui gruppi vengono configurati automaticamente in tutte le reti virtuali. Influisce anche sulla rete virtuale predefinita del Lab, se usata, o su altre reti virtuali configurate nel Lab, usate per la creazione di macchine virtuali.
- **Controllare l'account di archiviazione del lab predefinito** , ovvero il primo account di archiviazione creato al momento della creazione del Lab, il cui nome inizia in genere con la lettera "a" e termina con un numero a più cifre, ovvero un\<labname\>#).
    1. Passare al gruppo di risorse del Lab.
    2. Individuare la risorsa di tipo **account di archiviazione**il cui nome corrisponde alla convenzione.
    3. Passare alla pagina dell'account di archiviazione denominata **firewall e reti virtuali**.
    4. Verificare che sia impostato su **tutte le reti**. Se è selezionata l'opzione **reti selezionate** , assicurarsi che le reti virtuali del Lab usate per creare le VM vengano aggiunte all'elenco.

Per informazioni più dettagliate sulla risoluzione dei problemi, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](../storage/common/storage-network-security.md).

> [!TIP]
> **Verificare le regole del gruppo di sicurezza di rete**. Usare la [Verifica del flusso IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) per verificare che una regola in un gruppo di sicurezza di rete stia bloccando il traffico verso o da una macchina virtuale. È anche possibile esaminare le regole valide del gruppo di sicurezza per assicurarsi che la regola di **autorizzazione** NSG in ingresso esista. Per altre informazioni, vedere [Uso di regole di sicurezza effettive per risolvere i problemi di flusso del traffico delle macchine virtuali](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Altre origini di errore
Sono presenti altre possibili origini di errore meno frequenti. Assicurarsi di valutare ognuno per verificare se si applica al caso. Di seguito è riportato un esempio: 

- **Il token di accesso personale è scaduto per il repository privato**. Quando è scaduto, l'artefatto non viene elencato e tutti gli script che fanno riferimento agli artefatti di un repository con un token di accesso privato scaduto avranno esito negativo.

## <a name="next-steps"></a>Passaggi successivi
Se non si è verificato alcun errore e non è ancora possibile applicare elementi, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiedi supporto**.

