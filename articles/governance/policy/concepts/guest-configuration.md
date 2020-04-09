---
title: Informazioni su come controllare il contenuto delle macchine virtualiLearn to audit the contents of virtual machines
description: Informazioni su come Criteri di Azure usa l'agente di configurazione guest per controllare le impostazioni all'interno delle macchine virtuali.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 889e99e94b2c81a6654fcbe7851e93c40163a0c6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985321"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendere la configurazione guest di Criteri di Azure

Oltre a controllare e [correggere le](../how-to/remediate-resources.md) risorse di Azure, Criteri di Azure può controllare le impostazioni all'interno di un computer. La convalida viene eseguita dall'estensione della configurazione guest e dal client. L'estensione, tramite il client, convalida impostazioni come:

- La configurazione del sistema operativo
- Configurazione o presenza di applicazioni
- Impostazioni dell'ambiente

Al momento, la maggior parte dei criteri di configurazione guest di Criteri di Azure controlla solo le impostazioni all'interno del computer. Non applicano configurazioni. L'eccezione è un criterio predefinito a [cui si fa riferimento di seguito.](#applying-configurations-using-guest-configuration)

## <a name="extension-and-client"></a>Estensione e client

Per controllare le impostazioni all'interno di una macchina, viene abilitata [un'estensione](../../../virtual-machines/extensions/overview.md) della macchina virtuale. L'estensione scarica l'assegnazione dei criteri applicabile e la configurazione corrispondente.

### <a name="limits-set-on-the-extension"></a>Limiti impostati sull'estensione

Per limitare l'estensione dall'impatto delle applicazioni in esecuzione all'interno del computer, la configurazione guest non può superare più del 5% dell'utilizzo della CPU. Questa limitazione esiste sia per le definizioni predefinite che per le definizioni personalizzate.

## <a name="register-guest-configuration-resource-provider"></a>Registrare il provider di risorse della configurazione guest

Prima di poter usare la configurazione guest, è necessario registrare il provider di risorse. È possibile eseguire la registrazione nel portale o con PowerShell. Il provider di risorse viene registrato automaticamente se l'assegnazione di un criterio di configurazione guest viene eseguita tramite il portale.

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

All'interno del computer, il client di configurazione guest utilizza gli strumenti locali per eseguire il controllo.

La tabella seguente elenca gli strumenti locali usati on ciascun sistema operativo supportato:

|Sistema operativo|Strumento di convalida|Note|
|-|-|-|
|Windows|[Configurazione dello stato desiderato](/powershell/scripting/dsc/overview/overview) di Windows PowerShell v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Se Ruby e Python non sono presenti nel computer, vengono installati dall'estensione Configurazione ospite. |

### <a name="validation-frequency"></a>Frequenza di convalida

Il client della configurazione guest verifica la presenza di nuovi contenuti ogni cinque minuti. Una volta ricevuta un'assegnazione guest, le impostazioni per tale configurazione vengono ricontrollate a intervalli di 15 minuti.
I risultati vengono inviati al provider di risorse Configurazione ospite al termine del controllo. Quando vengono applicati criteri di tipo [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers), nel provider di risorse di configurazione guest viene scritto lo stato del computer. Questo aggiornamento fa sì che criteri di Azure per valutare le proprietà di Azure Resource Manager.This update causes Azure Policy to evaluate the Azure Resource Manager properties. Una valutazione di Criteri di Azure su richiesta recupera il valore più recente dal provider di risorse Di configurazione guest. Tuttavia, non attiva un nuovo controllo della configurazione all'interno del computer.

## <a name="supported-client-types"></a>Tipi di client supportati

La tabella seguente elenca i sistemi operativi supportati su Immagini di Azure:

|Editore|Nome|Versioni|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|Datacenter 2012, Datacenter 2012 R2, Datacenter 2016, Datacenter 2019 Datacenter|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7|
|SUSE|SLES|12 SP3|

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Windows Server Nano Server non è supportato in nessuna versione.

## <a name="guest-configuration-extension-network-requirements"></a>Requisiti di rete dell'estensione di configurazione guestGuest Configuration Extension network requirements

Per comunicare con il provider di risorse Configurazione ospite in Azure, i computer richiedono l'accesso in uscita ai data center di Azure sulla porta **443.** Se si usa una rete virtuale privata in Azure che non consente il traffico in uscita, configurare le eccezioni con le regole del gruppo di sicurezza di [rete.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
Il [tag del servizio](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" può essere utilizzato per fare riferimento al servizio di configurazione guest.

## <a name="guest-configuration-definition-requirements"></a>Requisiti per la definizione della configurazione guest

Ogni controllo eseguito da Configurazione ospite richiede due definizioni di criteri, una definizione DeployIfNotExists e una definizione **AuditIfNotExists.Each** audit run by Guest Configuration requires two policy definitions, a **DeployIfNotExists** definition and an AuditIfNotExists definition. La definizione **DeployIfNotExists** viene utilizzata per preparare il computer con l'agente di configurazione guest e altri componenti per supportare gli strumenti di [convalida.](#validation-tools)

La definizione dei criteri **DeployIfNotExists** convalida e corregge gli elementi seguenti:

- Convalidare la macchina è stata assegnata una configurazione da valutare. Se al momento non è presente alcuna assegnazione, ottenere l'assegnazione e preparare la macchina:
  - Autenticazione alla macchina tramite [un'identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installazione della versione più recente dell'estensione **Microsoft.GuestConfiguration**
  - Installazione degli [strumenti di convalida](#validation-tools) e delle dipendenze, se necessario

Se l'assegnazione **DeployIfNotExists** è non conforme, è possibile usare [un'attività](../how-to/remediate-resources.md#create-a-remediation-task) di correzione.

Quando l'assegnazione **DeployIfNotExists** è conforme, l'assegnazione dei criteri **AuditIfNotExists** utilizza gli strumenti di convalida locali per determinare se l'assegnazione di configurazione è conforme o non conforme. Lo strumento di convalida fornisce i risultati al client della configurazione guest. Il client inoltra i risultati all'estensione guest, che li rende disponibili tramite il provider di risorse di configurazione guest.

Criteri di Azure usa la proprietà **complianceStatus** dei provider di risorse della configurazione guest per segnalare la conformità nel nodo **Conformità**. Per altre informazioni, vedere [Ottenere dati sulla conformità](../how-to/get-compliance-data.md).

> [!NOTE]
> Il criterio **DeployIfNotExists** è necessario affinché il criterio **AuditIfNotExists** restituisca risultati. Senza **DeployIfNotExists**, il criterio **AuditIfNotExists** mostra "0 di 0" risorse come stato.

Tutti i criteri predefiniti per la configurazione guest sono inclusi in un'iniziativa per raggruppare le definizioni da usare nelle assegnazioni. L'iniziativa integrata denominata _ \[\]Anteprima : Controllare le impostazioni di sicurezza della password all'interno_ di computer Linux e Windows contiene 18 criteri. Esistono sei coppie **DeployIfNotExists** e **AuditIfNotExists** per Windows e tre coppie per Linux. La logica di [definizione dei criteri](definition-structure.md#policy-rule) convalida che viene valutato solo il sistema operativo di destinazione.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Controllo delle impostazioni del sistema operativo in base alle linee di base del settore

Una delle iniziative disponibili in Criteri di Azure consente di controllare le impostazioni del sistema operativo all'interno delle macchine virtuali seguendo una "linea di base" di Microsoft.One of the initiatives available in Azure Policy provides the ability to audit operating system settings inside virtual machines following a "baseline" from Microsoft. La definizione, _ \[Anteprima: controllo delle macchine virtuali Windows che non corrispondono\]alle impostazioni_ della linea di base della sicurezza di Azure include un set completo di regole di controllo in base alle impostazioni di Criteri di gruppo di Active Directory.The definition, Preview : Audit Windows VMs that do not match Azure security baseline settings includes a complete set of audit rules based on settings from Active Directory Group Policy.

La maggior parte delle impostazioni sono disponibili come parametri. Questa funzionalità consente di personalizzare ciò che viene controllato per allineare i criteri con i requisiti dell'organizzazione o per eseguire il mapping dei criteri a informazioni di terze parti, ad esempio gli standard normativi del settore.

Alcuni parametri supportano un intervallo di valori integer. Ad esempio, il parametro Validità massima password può essere impostato utilizzando un operatore di intervallo per dare flessibilità ai proprietari delle macchine. È possibile verificare che l'impostazione di criteri di gruppo efficace che richiede agli utenti di modificare le password non deve essere più di 70 giorni, ma non dovrebbe essere inferiore a un giorno. Come descritto nel fumetto info per il parametro, per rendere questo criterio aziendale il valore di controllo effettivo, impostare il valore su "1,70".

Se si assegnano i criteri usando un modello di distribuzione di Azure Resource Manager, è possibile usare un file di parametri per gestire queste impostazioni dal controllo del codice sorgente. Utilizzo di uno strumento come Git per gestire le modifiche ai criteri di controllo con commenti a ogni documento di archiviazione evidenza sul motivo per cui un'assegnazione deve essere un'eccezione al valore previsto.

#### <a name="applying-configurations-using-guest-configuration"></a>Applicazione delle configurazioni tramite Configurazione ospite

La funzionalità più recente di Criteri di Azure configura le impostazioni all'interno dei computer. La definizione _Configurare il fuso orario nei computer Windows_ apporta modifiche al computer configurando il fuso orario.

Quando si assegnano definizioni che iniziano con _Configura_, è inoltre necessario assegnare la definizione _Distribuisci prerequisiti per abilitare_i criteri di configurazione guest nelle macchine virtuali Windows . È possibile combinare queste definizioni in un'iniziativa, se lo si desidera.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assegnazione di criteri a computer esterni ad AzureAssigning policies to machines outside of Azure

I criteri di controllo disponibili per la configurazione Guest includono il tipo di risorsa **Microsoft.HybridCompute/machines.The** Audit policies available for Guest Configuration include the Microsoft.HybridCompute/machines resource type. Tutti i computer connessi ad [Azure Arc per i](../../../azure-arc/servers/overview.md) server inclusi nell'ambito dell'assegnazione dei criteri vengono inclusi automaticamente.

### <a name="multiple-assignments"></a>Assegnazioni multiple

I criteri di configurazione guest attualmente supportano l'assegnazione della stessa assegnazione guest una sola volta per computer, anche se l'assegnazione dei criteri utilizza parametri diversi.

## <a name="client-log-files"></a>File di log client

L'estensione Configurazione ospite scrive i file di registro nei percorsi seguenti:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Dove `<version>` si riferisce al numero di versione corrente.

### <a name="collecting-logs-remotely"></a>Raccolta dei registri in remoto

Il primo passaggio per la risoluzione dei `Test-GuestConfigurationPackage` problemi relativi alle configurazioni o ai moduli di Configurazione ospite deve essere quello di utilizzare il cmdlet seguendo la procedura relativa alla creazione di un criterio di [controllo della configurazione guest personalizzato per Windows.](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)
Se l'operazione non riesce, la raccolta dei log client consente di diagnosticare i problemi.

#### <a name="windows"></a>Windows

Per usare la funzionalità comando di esecuzione della macchina virtuale di Azure per acquisire informazioni dai file di log nei computer Windows, può essere utile lo script di PowerShell di esempio seguente. Per altre informazioni, vedere [Eseguire script di PowerShell nella macchina virtuale Windows con Esegui comando](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Per usare la funzionalità comando di esecuzione delle macchine virtuali di Azure per acquisire informazioni dai file di log nei computer Linux, può essere utile lo script Bash di esempio seguente. Per altre informazioni, vedere [Eseguire script della shell nella macchina virtuale Linux con Esegui comandoFor](../../../virtual-machines/linux/run-command.md) more information, see Run shell scripts in your Linux VM with Run Command

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Esempi di configurazione guest

L'origine per le iniziative predefinite Di configurazione guest dei criteri è disponibile nei seguenti percorsi:

- [Definizioni dei criteri predefinite - Configurazione guest](../samples/built-in-policies.md#guest-configuration)
- [Iniziative integrate - Configurazione degli ospiti](../samples/built-in-initiatives.md#guest-configuration)
- [Esempi di criteri di Azure Repository GitHubAzure Azure Policy samples GitHub repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Vedere la [struttura delle definizioni di Criteri di Azure](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Comprendere come creare criteri a livello di [codice.](../how-to/programmatically-create.md)
- Scopri come ottenere i dati di [conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](../how-to/remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
