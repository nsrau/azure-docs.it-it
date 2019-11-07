---
title: Informazioni su come controllare il contenuto delle macchine virtuali
description: Informazioni su come i criteri di Azure usano la configurazione Guest per controllare le impostazioni all'interno di una macchina di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 0e5592f629646db3132ffd65fd56b1a0d5d5be39
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581438"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendere la configurazione guest di Criteri di Azure

Oltre al controllo e alla [correzione](../how-to/remediate-resources.md) delle risorse di Azure, i criteri di Azure possono controllare le impostazioni all'interno di un computer. La convalida viene eseguita dall'estensione della configurazione guest e dal client. L'estensione, tramite il client, convalida le impostazioni come:

- Configurazione del sistema operativo
- Configurazione o presenza di applicazioni
- Impostazioni dell'ambiente

A questo punto, la configurazione Guest di criteri di Azure controlla solo le impostazioni all'interno della macchina. Non applica le configurazioni.

## <a name="extension-and-client"></a>Estensione e client

Per controllare le impostazioni all'interno di un computer, è abilitata un' [estensione della macchina virtuale](../../../virtual-machines/extensions/overview.md) . L'estensione scarica l'assegnazione dei criteri applicabile e la configurazione corrispondente.

### <a name="limits-set-on-the-extension"></a>Limiti impostati per l'estensione

Per limitare l'estensione da applicazioni in esecuzione all'interno del computer, la configurazione Guest non può superare il 5% di utilizzo della CPU. Questa limitazione è disponibile sia per le definizioni predefinite sia per quelle personalizzate.

## <a name="register-guest-configuration-resource-provider"></a>Registrare il provider di risorse della configurazione guest

Prima di poter usare la configurazione guest, è necessario registrare il provider di risorse. È possibile eseguire la registrazione nel portale o con PowerShell. Il provider di risorse viene registrato automaticamente se l'assegnazione dei criteri di configurazione Guest viene eseguita tramite il portale.

### <a name="registration---portal"></a>Registrazione - Portale

Per registrare il provider di risorse per la configurazione guest tramite il portale di Azure, seguire questa procedura:

1. Avviare il portale di Azure e fare clic su **Tutti i servizi**. Cercare e selezionare **Sottoscrizioni**.

1. Trovare e fare clic sulla sottoscrizione per cui si intende abilitare la configurazione guest.

1. Nel menu a sinistra della pagina **Sottoscrizione** fare clic su **Provider di risorse**.

1. Per filtrare o scorrere fino a individuare **Microsoft.GuestConfiguration**, quindi fare clic su **Registra** sulla stessa riga.

### <a name="registration---powershell"></a>Registrazione - PowerShell

Per registrare il provider di risorse per la configurazione guest tramite PowerShell, eseguire il seguente comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Strumenti di convalida

All'interno del computer, il client di configurazione Guest utilizza gli strumenti locali per eseguire il controllo.

La tabella seguente elenca gli strumenti locali usati on ciascun sistema operativo supportato:

|Sistema operativo|Strumento di convalida|Note|
|-|-|-|
|Windows|[Microsoft DSC (Desired State Configuration)](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby e Python vengono installati dall'estensione della configurazione guest. |

### <a name="validation-frequency"></a>Frequenza di convalida

Il client della configurazione guest verifica la presenza di nuovi contenuti ogni cinque minuti. Dopo aver ricevuto un'assegnazione guest, le impostazioni vengono controllate a intervalli di 15 minuti. Al termine del controllo, i risultati vengono inviati al provider di risorse di configurazione guest. Quando vengono applicati criteri di tipo [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers), nel provider di risorse di configurazione guest viene scritto lo stato del computer. Questo aggiornamento fa in modo che i criteri di Azure valutino le proprietà del Azure Resource Manager. Una valutazione di criteri di Azure su richiesta Recupera il valore più recente dal provider di risorse di configurazione Guest. Tuttavia, non attiva un nuovo controllo della configurazione all'interno del computer.

## <a name="supported-client-types"></a>Tipi di client supportati

La tabella seguente elenca i sistemi operativi supportati su Immagini di Azure:

|Editore|Name|Versioni|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 datacenter, 2019 Datacenter|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux.|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> La configurazione Guest può controllare i nodi che eseguono un sistema operativo supportato. Se si desidera controllare le macchine virtuali che utilizzano un'immagine personalizzata, è necessario duplicare la definizione **DeployIfNotExists** e modificare la sezione **if** per includere le proprietà dell'immagine.

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Windows Server nano server non è supportato in alcuna versione.

## <a name="guest-configuration-extension-network-requirements"></a>Requisiti di rete dell'estensione di configurazione Guest

Per comunicare con il provider di risorse di configurazione Guest in Azure, i computer richiedono l'accesso in uscita ai Data Center di Azure sulla porta **443**. Se si usa una rete virtuale privata in Azure che non consente il traffico in uscita, configurare le eccezioni con le regole del [gruppo di sicurezza di rete](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Un tag di servizio non esiste attualmente per la configurazione Guest di criteri di Azure.

Per gli elenchi di indirizzi IP è possibile scaricare [Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. È sufficiente consentire l'accesso in uscita agli indirizzi IP nelle aree in cui vengono distribuite le macchine virtuali.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli per le risorse di calcolo, SQL e di archiviazione. Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="guest-configuration-definition-requirements"></a>Requisiti per la definizione della configurazione guest

Ogni controllo eseguito dalla configurazione Guest richiede due definizioni di criteri, una definizione **DeployIfNotExists** e una definizione **AuditIfNotExists** . La definizione **DeployIfNotExists** viene utilizzata per preparare il computer con l'agente di configurazione Guest e altri componenti per supportare gli [strumenti di convalida](#validation-tools).

La definizione dei criteri **DeployIfNotExists** convalida e corregge gli elementi seguenti:

- Verificare che al computer sia stata assegnata una configurazione da valutare. Se non è attualmente presente alcuna assegnazione, ottenere l'assegnazione e preparare il computer:
  - Autenticazione al computer tramite un' [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installazione della versione più recente dell'estensione **Microsoft.GuestConfiguration**
  - Installazione degli [strumenti di convalida](#validation-tools) e delle dipendenze, se necessario

Se l'assegnazione **DeployIfNotExists** non è conforme, è possibile utilizzare un' [attività di correzione](../how-to/remediate-resources.md#create-a-remediation-task) .

Quando l'assegnazione **DeployIfNotExists** è conforme, l'assegnazione dei criteri **AuditIfNotExists** usa gli strumenti di convalida locali per determinare se l'assegnazione di configurazione è conforme o non conforme. Lo strumento di convalida fornisce i risultati al client della configurazione guest. Il client inoltra i risultati all'estensione guest, che li rende disponibili tramite il provider di risorse di configurazione guest.

Criteri di Azure usa la proprietà **complianceStatus** dei provider di risorse della configurazione guest per segnalare la conformità nel nodo **Conformità**. Per altre informazioni, vedere [Ottenere dati sulla conformità](../how-to/getting-compliance-data.md).

> [!NOTE]
> Il criterio **DeployIfNotExists** è necessario affinché i criteri **AuditIfNotExists** restituiscano i risultati. Senza **DeployIfNotExists**, il criterio **AuditIfNotExists** Mostra le risorse "0 di 0" come stato.

Tutti i criteri predefiniti per la configurazione guest sono inclusi in un'iniziativa per raggruppare le definizioni da usare nelle assegnazioni. L'iniziativa predefinita denominata _\[Preview\]: controllare le impostazioni di sicurezza delle password nei computer Linux e Windows_ contiene 18 criteri. Esistono sei coppie **DeployIfNotExists** e **AuditIfNotExists** per Windows e tre coppie per Linux. La logica di [definizione dei criteri](definition-structure.md#policy-rule) convalida che viene valutato solo il sistema operativo di destinazione.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Controllo delle impostazioni del sistema operativo seguenti linee di base del settore

Una delle iniziative disponibili in criteri di Azure consente di controllare le impostazioni del sistema operativo all'interno di macchine virtuali che seguono una "baseline" di Microsoft. La definizione _\[anteprima\]: controllare le macchine virtuali Windows che non corrispondono alle impostazioni di base di sicurezza di Azure_ include un set completo di regole di controllo basate sulle impostazioni di Active Directory Criteri di gruppo.

La maggior parte delle impostazioni è disponibile come parametri. Questa funzionalità consente di personalizzare gli elementi controllati per allineare i criteri ai requisiti dell'organizzazione o per eseguire il mapping dei criteri a informazioni di terze parti, ad esempio standard normativi del settore.

Alcuni parametri supportano un intervallo di valori interi. È ad esempio possibile impostare il parametro validità massima password utilizzando un operatore di intervallo per offrire flessibilità ai proprietari dei computer. È possibile controllare che l'impostazione Criteri di gruppo efficace che richiede agli utenti di modificare le password non sia superiore a 70 giorni, ma non deve essere inferiore a un giorno. Come descritto nella sezione info-Bubble per il parametro, per impostare il valore di controllo effettivo per i criteri di business, impostare il valore su "1, 70".

Se si assegnano i criteri usando un modello di distribuzione di Azure Resource Manager, è possibile usare un file di parametri per gestire queste impostazioni dal controllo del codice sorgente. Usare uno strumento come Git per gestire le modifiche apportate ai criteri di controllo con commenti a ogni archiviazione per documentare il motivo per cui un'assegnazione deve essere un'eccezione al valore previsto.

#### <a name="applying-configurations-using-guest-configuration"></a>Applicazione di configurazioni con la configurazione Guest

La funzionalità più recente di criteri di Azure consente di configurare le impostazioni all'interno dei computer. La definizione _configurare il fuso orario sui computer Windows_ apporta modifiche al computer configurando il fuso orario.

Quando si assegnano definizioni che iniziano con _Configure_, è necessario assegnare anche i _prerequisiti per la definizione di distribuzione per abilitare i criteri di configurazione Guest nelle macchine virtuali Windows_. Se si sceglie, è possibile combinare queste definizioni in un'iniziativa.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assegnazione di criteri a computer esterni ad Azure

I criteri di controllo disponibili per la configurazione Guest includono il tipo di risorsa **Microsoft. HybridCompute/machines** . Tutti i computer caricati in [Azure Arc per i server](../../../azure-arc/servers/overview.md) che rientrano nell'ambito dell'assegnazione di criteri vengono inclusi automaticamente.

### <a name="multiple-assignments"></a>Più assegnazioni

I criteri di configurazione Guest attualmente supportano solo l'assegnazione della stessa assegnazione Guest una volta per ogni computer, anche se l'assegnazione dei criteri USA parametri diversi.

## <a name="built-in-resource-modules"></a>Moduli di risorse predefiniti

Quando si installa l'estensione di configurazione Guest, il modulo di PowerShell ' GuestConfiguration ' è incluso nella versione più recente dei moduli di risorse DSC. È possibile scaricare questo modulo dal PowerShell Gallery usando il collegamento "download manuale" della pagina del modulo [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). Il formato del file ". nupkg" può essere rinominato in ". zip" per decomprimere ed esaminare.

## <a name="client-log-files"></a>File di log del client

L'estensione di configurazione Guest scrive i file di log nei percorsi seguenti:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Dove `<version>` si riferisce al numero di versione corrente.

### <a name="collecting-logs-remotely"></a>Raccolta di log in modalità remota

Il primo passaggio nella risoluzione dei problemi relativi alle configurazioni o ai moduli di configurazione Guest deve essere quello di usare il cmdlet `Test-GuestConfigurationPackage` seguendo la procedura descritta in [testare un pacchetto di configurazione Guest](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
Se l'operazione non riesce, la raccolta dei log del client può aiutare a diagnosticare i problemi.

#### <a name="windows"></a>Windows

Per usare la funzionalità di esecuzione dei comandi della macchina virtuale di Azure per acquisire informazioni dai file di log nei computer Windows, è possibile usare lo script di PowerShell di esempio seguente. Per altre informazioni, vedere [eseguire script di PowerShell nella macchina virtuale Windows con il comando Esegui](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Per usare la funzionalità di esecuzione dei comandi della macchina virtuale di Azure per acquisire informazioni dai file di log nei computer Linux, è possibile usare lo script bash di esempio seguente. Per altre informazioni, vedere [eseguire script della shell nella VM Linux con il comando Run](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Esempi di configurazione Guest

Gli esempi per la configurazione Guest per i criteri sono disponibili nei percorsi seguenti:

- [Indice degli esempi-configurazione Guest](../samples/index.md#guest-configuration)
- [Repository GitHub degli esempi di criteri di Azure](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/getting-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
