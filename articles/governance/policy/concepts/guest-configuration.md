---
title: Informazioni su come controllare il contenuto delle macchine virtuali
description: Informazioni su come criteri di Azure usa l'agente di configurazione Guest per controllare le impostazioni all'interno delle macchine virtuali.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025221"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendere la configurazione guest di Criteri di Azure

Oltre al controllo e alla [correzione](../how-to/remediate-resources.md) delle risorse di Azure, i criteri di Azure possono controllare le impostazioni all'interno di un computer. La convalida viene eseguita dall'estensione della configurazione guest e dal client. L'estensione, tramite il client, convalida impostazioni come:

- Configurazione del sistema operativo
- Configurazione o presenza di applicazioni
- Impostazioni dell'ambiente

Al momento, la maggior parte dei criteri di configurazione Guest di criteri di Azure controlla solo le impostazioni all'interno del computer. Non applicano le configurazioni. L'eccezione è un criterio predefinito a [cui si fa riferimento di seguito](#applying-configurations-using-guest-configuration).

## <a name="resource-provider"></a>Provider di risorse

Prima di poter usare la configurazione guest, è necessario registrare il provider di risorse. Il provider di risorse viene registrato automaticamente se l'assegnazione dei criteri di configurazione Guest viene eseguita tramite il portale. È possibile eseguire la registrazione manualmente tramite il [portale](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)o l'interfaccia della riga di comando di [Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="extension-and-client"></a>Estensione e client

Per controllare le impostazioni all'interno di un computer, è abilitata un' [estensione della macchina virtuale](../../../virtual-machines/extensions/overview.md) . L'estensione scarica l'assegnazione dei criteri applicabile e la configurazione corrispondente.

> [!Important]
> L'estensione di configurazione Guest è necessaria per eseguire controlli in macchine virtuali di Azure.
> Per distribuire l'estensione su larga scala, assegnare le definizioni dei criteri seguenti:
>   - [Distribuisci i prerequisiti per abilitare i criteri di configurazione guest nelle macchine virtuali Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [Distribuisci i prerequisiti per abilitare i criteri di configurazione guest nelle macchine virtuali Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>Limiti impostati per l'estensione

Per limitare l'estensione a un effetto sulle applicazioni in esecuzione all'interno del computer, la configurazione Guest non può superare il 5% della CPU. Questa limitazione è disponibile sia per le definizioni predefinite sia per quelle personalizzate.

### <a name="validation-tools"></a>Strumenti di convalida

All'interno del computer, il client di configurazione Guest utilizza gli strumenti locali per eseguire il controllo.

La tabella seguente elenca gli strumenti locali usati on ciascun sistema operativo supportato:

|Sistema operativo|Strumento di convalida|Note|
|-|-|-|
|Windows|[Windows PowerShell DSC (Desired state Configuration](/powershell/scripting/dsc/overview/overview) ) V2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Se Ruby e Python non sono presenti nel computer, vengono installati dall'estensione di configurazione Guest. |

### <a name="validation-frequency"></a>Frequenza di convalida

Il client della configurazione guest verifica la presenza di nuovi contenuti ogni cinque minuti. Una volta ricevuta un'assegnazione Guest, le impostazioni per tale configurazione vengono controllate nuovamente in un intervallo di 15 minuti.
I risultati vengono inviati al provider di risorse di configurazione Guest al termine del controllo. Quando vengono applicati criteri di tipo [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers), nel provider di risorse di configurazione guest viene scritto lo stato del computer. Questo aggiornamento fa in modo che i criteri di Azure valutino le proprietà del Azure Resource Manager. Una valutazione di criteri di Azure su richiesta Recupera il valore più recente dal provider di risorse di configurazione Guest. Tuttavia, non attiva un nuovo controllo della configurazione all'interno del computer.

## <a name="supported-client-types"></a>Tipi di client supportati

I criteri di configurazione Guest sono inclusi nelle nuove versioni. Le versioni precedenti dei sistemi operativi disponibili in Azure Marketplace vengono escluse se l'agente di configurazione Guest non è compatibile. La tabella seguente mostra un elenco dei sistemi operativi supportati nelle immagini di Azure:

|Pubblicazione|Name|Versioni|
|-|-|-|
|Canonical|Ubuntu Server|14,04 e versioni successive|
|Credativ|Debian|8 e versioni successive|
|Microsoft|Windows Server|2012 e versioni successive|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7,3 e versioni successive|
|Red Hat|Red Hat Enterprise Linux|7,4 e versioni successive|
|SUSE|SLES|12 SP3 e versioni successive|

Le immagini di macchine virtuali personalizzate sono supportate dai criteri di configurazione Guest purché siano uno dei sistemi operativi nella tabella precedente.

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Windows Server nano server non è supportato in alcuna versione.

## <a name="guest-configuration-extension-network-requirements"></a>Requisiti di rete dell'estensione di configurazione Guest

Per comunicare con il provider di risorse di configurazione Guest in Azure, i computer richiedono l'accesso in uscita ai Data Center di Azure sulla porta **443**. Se una rete in Azure non consente il traffico in uscita, configurare le eccezioni con le regole del [gruppo di sicurezza di rete](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) .
Il [tag di servizio](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" può essere usato per fare riferimento al servizio di configurazione Guest.

## <a name="azure-managed-identity-requirements"></a>Requisiti di identità gestita di Azure

I criteri di **DeployIfNotExists** che aggiungono l'estensione alle macchine virtuali abilitano anche un'identità gestita assegnata dal sistema, se non ne esiste una.

> [!WARNING]
> Evitare di abilitare l'identità gestita assegnata dall'utente alle macchine virtuali nell'ambito dei criteri che consentono l'identità gestita assegnata dal sistema. L'identità assegnata dall'utente verrà sostituita e il computer potrebbe non rispondere.

## <a name="guest-configuration-definition-requirements"></a>Requisiti per la definizione della configurazione guest

Ogni controllo eseguito dalla configurazione Guest richiede due definizioni di criteri, una definizione **DeployIfNotExists** e una definizione **AuditIfNotExists** . 

La definizione dei criteri **DeployIfNotExists** convalida e corregge gli elementi seguenti:

- Verificare che al computer sia stata assegnata una configurazione da valutare. Se non è attualmente presente alcuna assegnazione, ottenere l'assegnazione e preparare il computer:
  - Autenticazione al computer tramite un' [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installazione della versione più recente dell'estensione **Microsoft.GuestConfiguration**
  - Installazione degli [strumenti di convalida](#validation-tools) e delle dipendenze, se necessario

Se l'assegnazione **DeployIfNotExists** non è conforme, è possibile utilizzare un' [attività di correzione](../how-to/remediate-resources.md#create-a-remediation-task) .

Quando l'assegnazione **DeployIfNotExists** è conforme, l'assegnazione dei criteri **AuditIfNotExists** determina se l'assegnazione Guest è conforme o non conforme. Lo strumento di convalida fornisce i risultati al client della configurazione guest. Il client inoltra i risultati all'estensione guest, che li rende disponibili tramite il provider di risorse di configurazione guest.

Criteri di Azure usa la proprietà **complianceStatus** dei provider di risorse della configurazione guest per segnalare la conformità nel nodo **Conformità**. Per altre informazioni, vedere [Ottenere dati sulla conformità](../how-to/get-compliance-data.md).

> [!NOTE]
> Il criterio **DeployIfNotExists** è necessario affinché i criteri **AuditIfNotExists** restituiscano i risultati. Senza **DeployIfNotExists**, il criterio **AuditIfNotExists** Mostra le risorse "0 di 0" come stato.

Tutti i criteri predefiniti per la configurazione guest sono inclusi in un'iniziativa per raggruppare le definizioni da usare nelle assegnazioni. L'iniziativa predefinita denominata _ \[Preview\]: controlla la sicurezza delle password nei computer Linux e Windows_ contiene 18 criteri. Esistono sei coppie **DeployIfNotExists** e **AuditIfNotExists** per Windows e tre coppie per Linux. La logica di [definizione dei criteri](definition-structure.md#policy-rule) convalida che viene valutato solo il sistema operativo di destinazione.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Controllo delle impostazioni del sistema operativo seguenti linee di base del settore

Un'iniziativa in criteri di Azure consente di controllare le impostazioni del sistema operativo dopo una "baseline". La definizione, _ \[anteprima\]: controlla VM Windows che non corrispondono alle impostazioni di base di sicurezza di Azure_ include un set di regole basate su Active Directory Criteri di gruppo.

La maggior parte delle impostazioni è disponibile come parametri. I parametri consentono di personalizzare ciò che viene controllato. Allineare i criteri ai propri requisiti o eseguire il mapping dei criteri a informazioni di terze parti, ad esempio standard normativi del settore.

Alcuni parametri supportano un intervallo di valori interi. Ad esempio, l'impostazione Validità massima password potrebbe controllare l'impostazione Criteri di gruppo effettiva. Un intervallo "1, 70" potrebbe confermare che gli utenti devono modificare la password almeno ogni 70 giorni, ma non meno di un giorno.

Se si assegnano i criteri usando un modello di distribuzione Azure Resource Manager, usare un file di parametri per gestire le eccezioni. Archiviare i file in un sistema di controllo della versione come Git. I commenti sulle modifiche ai file forniscono evidenza perché un'assegnazione è un'eccezione al valore previsto.

#### <a name="applying-configurations-using-guest-configuration"></a>Applicazione di configurazioni con la configurazione Guest

La funzionalità più recente di criteri di Azure consente di configurare le impostazioni all'interno dei computer. La definizione _configurare il fuso orario sui computer Windows_ apporta modifiche al computer configurando il fuso orario.

Quando si assegnano definizioni che iniziano con _Configure_, è necessario assegnare anche i _prerequisiti per la definizione di distribuzione per abilitare i criteri di configurazione Guest nelle macchine virtuali Windows_. Se si sceglie, è possibile combinare queste definizioni in un'iniziativa.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assegnazione di criteri a computer esterni ad Azure

I criteri di controllo disponibili per la configurazione Guest includono il tipo di risorsa **Microsoft. HybridCompute/machines** . Tutti i computer caricati in [Azure Arc per i server](../../../azure-arc/servers/overview.md) che rientrano nell'ambito dell'assegnazione di criteri vengono inclusi automaticamente.

### <a name="multiple-assignments"></a>Più assegnazioni

I criteri di configurazione Guest attualmente supportano solo l'assegnazione della stessa assegnazione Guest una volta per ogni computer, anche se l'assegnazione dei criteri USA parametri diversi.

## <a name="client-log-files"></a>File di log client

L'estensione di configurazione Guest scrive i file di log nei percorsi seguenti:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Dove `<version>` si riferisce al numero di versione corrente.

### <a name="collecting-logs-remotely"></a>Raccolta di log in modalità remota

Il primo passaggio nella risoluzione dei problemi relativi alle configurazioni o ai moduli di configurazione Guest `Test-GuestConfigurationPackage` consiste nell'usare il cmdlet seguendo i passaggi come [creare un criterio di controllo di configurazione Guest personalizzato per Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Se l'operazione non riesce, la raccolta dei log del client può aiutare a diagnosticare i problemi.

#### <a name="windows"></a>Windows

Acquisire informazioni dai file di log usando il [comando di esecuzione della macchina virtuale di Azure](../../../virtual-machines/windows/run-command.md). l'esempio di script PowerShell seguente può essere utile.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Acquisire informazioni dai file di log con il [comando di esecuzione della macchina virtuale di Azure](../../../virtual-machines/linux/run-command.md). è possibile usare lo script bash di esempio seguente.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Esempi di configurazione Guest

Gli esempi di criteri predefiniti per la configurazione Guest sono disponibili nelle posizioni seguenti:

- [Definizioni dei criteri predefiniti-configurazione Guest](../samples/built-in-policies.md#guest-configuration)
- [Iniziative predefinite-configurazione Guest](../samples/built-in-initiatives.md#guest-configuration)
- [Repository GitHub degli esempi di criteri di Azure](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come visualizzare i dettagli di ogni impostazione dalla [visualizzazione conformità configurazione Guest](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/get-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](../how-to/remediate-resources.md).
- Esaminare le funzionalità di un gruppo di gestione con [organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
