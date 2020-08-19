---
title: Informazioni su come controllare i contenuti delle macchine virtuali
description: Informazioni su come Criteri di Azure usa l'agente di Configurazione guest per controllare le impostazioni all'interno delle macchine virtuali.
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: 624f0a2464323e8002b9940471c93b3030f053d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544673"
---
# <a name="understand-azure-policys-guest-configuration"></a>Informazioni su Configurazione guest di Criteri di Azure

Criteri di Azure consente di controllare le impostazioni all'interno di un computer, sia per i computer in esecuzione in Azure che per i computer [connessi ad Arc](../../../azure-arc/servers/overview.md). La convalida viene eseguita dall'estensione Configurazione guest e dal client. L'estensione, tramite il client, convalida impostazioni come:

- Configurazione del sistema operativo
- Configurazione o presenza di applicazioni
- Impostazioni dell'ambiente

Al momento, la maggior parte dei criteri di Configurazione guest di Criteri di Azure controlla solo le impostazioni all'interno del computer.
Non vengono invece applicate le configurazioni. L'eccezione è rappresentata da un criterio predefinito [illustrato di seguito](#applying-configurations-using-guest-configuration).

## <a name="enable-guest-configuration"></a>Abilita configurazione Guest

Per controllare lo stato dei computer nell'ambiente, inclusi i computer in Azure e i computer connessi ad Arc, esaminare i dettagli seguenti.

## <a name="resource-provider"></a>Provider di risorse

Prima di poter usare Configurazione guest, è necessario registrare il provider di risorse. Il provider di risorse viene registrato automaticamente se l'assegnazione dei criteri di Configurazione guest viene eseguita tramite il portale. È possibile eseguire la registrazione manuale tramite il [portale](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) o l'[interfaccia della riga di comando di Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Distribuire i requisiti per le macchine virtuali di Azure

Per controllare le impostazioni all'interno di un computer, è abilitata un' [estensione della macchina virtuale](../../../virtual-machines/extensions/overview.md) e il computer deve disporre di un'identità gestita dal sistema. L'estensione scarica l'assegnazione dei criteri applicabile e la configurazione corrispondente. L'identità viene utilizzata per autenticare il computer durante la lettura e la scrittura nel servizio di configurazione Guest. L'estensione non è necessaria per i computer connessi ad Arc perché è inclusa nell'agente del computer connesso ad Arc.

> [!IMPORTANT]
> Per controllare le macchine virtuali di Azure, è necessaria l'estensione di configurazione Guest e un'identità gestita. Per distribuire l'estensione su larga scala, assegnare la seguente iniziativa per i criteri:
> 
> - [Distribuisci i prerequisiti per abilitare i criteri di configurazione guest nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8)

### <a name="limits-set-on-the-extension"></a>Limiti impostati per l'estensione

Per limitare l'impatto dell'estensione sulle applicazioni in esecuzione all'interno del computer, Configurazione guest non può usare più del 5% della CPU. Questo limite si applica sia alle definizioni predefinite sia a quelle personalizzate. Lo stesso vale per il servizio di configurazione Guest in Arc Connected Machine Agent.

### <a name="validation-tools"></a>Strumenti di convalida

All'interno del computer, il client di Configurazione guest usa gli strumenti locali per eseguire il controllo.

Nella tabella seguente è riportato un elenco degli strumenti locali utilizzati in ogni sistema operativo supportato. Per il contenuto predefinito, la configurazione Guest gestisce automaticamente il caricamento di questi strumenti.

|Sistema operativo|Strumento di convalida|Note|
|-|-|-|
|Windows|[Configurazione dello stato desiderato di PowerShell](/powershell/scripting/dsc/overview/overview) V2| Caricato da un lato in una cartella usata solo da criteri di Azure. Non è in conflitto con Windows PowerShell DSC. PowerShell Core non è stato aggiunto al percorso di sistema.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Installa chef INSPEC versione 2.2.61 nel percorso predefinito e aggiunto al percorso di sistema. Sono installate anche le dipendenze per il pacchetto INSPEC, inclusi Ruby e Python. |

### <a name="validation-frequency"></a>Frequenza di convalida

Il client di Configurazione guest verifica la presenza di nuovi contenuti ogni cinque minuti. Una volta ricevuta un'assegnazione guest, le impostazioni per tale configurazione vengono controllate di nuovo con un intervallo di 15 minuti. Al termine del controllo, i risultati vengono inviati al provider di risorse di Configurazione guest. Quando vengono applicati criteri di tipo [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers), nel provider di risorse di Configurazione guest viene scritto lo stato del computer. In seguito a questo aggiornamento, il servizio Criteri di Azure valuta le proprietà di Azure Resource Manager. Una valutazione on demand di Criteri di Azure recupera il valore più recente dal provider di risorse di Configurazione guest. Non viene tuttavia attivato un nuovo controllo della configurazione nel computer.

## <a name="supported-client-types"></a>Tipi di client supportati

I criteri di Configurazione guest includono le nuove versioni. Le versioni precedenti dei sistemi operativi disponibili in Azure Marketplace vengono escluse se l'agente di configurazione Guest non è compatibile.
La tabella seguente elenca i sistemi operativi supportati nelle immagini di Azure:

|Editore|Nome|Versioni|
|-|-|-|
|Canonical|Ubuntu Server|14.04 e versioni successive|
|Credativ|Debian|8 e versioni successive|
|Microsoft|Windows Server|2012 e versioni successive|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7.3 e versioni successive|
|Red Hat|Red Hat Enterprise Linux|7,4-7,8|
|SUSE|SLES|12 SP3 e versioni successive|

Le immagini di macchine virtuali personalizzate sono supportate dai criteri di Configurazione guest a condizione che il sistema operativo sia uno di quelli indicati nella tabella precedente.

## <a name="network-requirements"></a>Requisiti di rete

Le macchine virtuali in Azure possono usare la scheda di rete locale o un collegamento privato per comunicare con il servizio di configurazione Guest.

I computer Azure Arc si connettono usando l'infrastruttura di rete locale per raggiungere i servizi di Azure e segnalare lo stato di conformità.

### <a name="communicate-over-virtual-networks-in-azure"></a>Comunicazione su reti virtuali in Azure

Per le macchine virtuali che usano reti virtuali per la comunicazione è necessario l'accesso in uscita ai Data Center di Azure sulla porta `443` . Se si usa una rete virtuale privata in Azure che non consente il traffico in uscita, configurare le eccezioni con le regole del gruppo di sicurezza di rete. È possibile usare il tag del servizio "GuestAndHybridManagement" per fare riferimento al servizio Configurazione guest.

### <a name="communicate-over-private-link-in-azure"></a>Comunicazione tramite collegamento privato in Azure

Le macchine virtuali possono usare un [collegamento privato](../../../private-link/private-link-overview.md) per la comunicazione con il servizio di configurazione Guest. Applicare il tag con il nome `EnablePrivateNeworkGC` e il valore `TRUE` per abilitare questa funzionalità. Il tag può essere applicato prima o dopo l'applicazione dei criteri di configurazione Guest alla macchina.

Il traffico viene instradato tramite l' [indirizzo IP pubblico virtuale](../../../virtual-network/what-is-ip-address-168-63-129-16.md) di Azure per stabilire un canale protetto e autenticato con le risorse della piattaforma Azure.

### <a name="azure-arc-connected-machines"></a>Computer connessi ad Azure Arc

I nodi che si trovano all'esterno di Azure connessi da Azure Arc richiedono la connettività al servizio di configurazione Guest. Informazioni dettagliate sui requisiti di rete e proxy forniti nella [documentazione di Azure Arc](../../../azure-arc/servers/overview.md).

Per comunicare con il provider di risorse di Configurazione guest in Azure, i computer devono disporre di accesso in uscita verso i data center di Azure sulla porta **443**. Se una rete in Azure non consente il traffico in uscita, configurare le eccezioni con le regole del [gruppo di sicurezza di rete](../../../virtual-network/manage-network-security-group.md#create-a-security-rule). È possibile usare il [tag del servizio](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" per fare riferimento al servizio Configurazione guest.

## <a name="managed-identity-requirements"></a>Requisiti delle identità gestite

Le definizioni dei criteri nell'iniziativa [Distribuisci prerequisiti per abilitare i criteri di configurazione Guest nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8) abilitano un'identità gestita assegnata dal sistema, se non ne esiste una. Nell'iniziativa sono presenti due definizioni di criteri che gestiscono la creazione di identità. Le condizioni IF nelle definizioni dei criteri garantiscono il comportamento corretto in base allo stato corrente della risorsa del computer in Azure.

Se al computer non sono attualmente associate identità gestite, i criteri validi saranno: [ \[ Anteprima \] : aggiungere l'identità gestita assegnata dal sistema per abilitare le assegnazioni di configurazione Guest nelle macchine virtuali senza identità](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Se il computer dispone attualmente di un'identità di sistema assegnata dall'utente, i criteri validi saranno: [ \[ Anteprima \] : Aggiungi identità gestita assegnata dal sistema per abilitare le assegnazioni di configurazione Guest nelle macchine virtuali con un'identità assegnata dall'utente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>Requisiti per la definizione di Configurazione guest

Ogni controllo eseguito da Configurazione guest richiede due definizioni dei criteri, **DeployIfNotExists** e **AuditIfNotExists**. Le definizioni dei criteri **DeployIfNotExists** gestiscono le dipendenze per l'esecuzione di controlli in ogni computer.

La definizione dei criteri **DeployIfNotExists** convalida e corregge gli elementi seguenti:

- Conferma che al computer sia stata assegnata una configurazione da valutare. Se non è attualmente presente nessuna assegnazione, ottiene l'assegnazione e prepara il computer eseguendo queste operazioni:
  - Autenticazione del computer usando un'[identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installazione della versione più recente dell'estensione **Microsoft.GuestConfiguration**
  - Installazione degli [strumenti di convalida](#validation-tools) e delle dipendenze, se necessario

Se l'assegnazione **DeployIfNotExists** è non conforme, è possibile usare un'[attività di correzione](../how-to/remediate-resources.md#create-a-remediation-task).

Quando l'assegnazione di **DeployIfNotExists** è conforme, l'assegnazione dei criteri **AuditIfNotExists** determina se l'assegnazione guest è conforme o meno. Lo strumento di convalida fornisce i risultati al client di Configurazione guest. Il client inoltra i risultati all'estensione guest, che li rende disponibili tramite il provider di risorse di Configurazione guest.

Criteri di Azure usa la proprietà **complianceStatus** dei provider di risorse di Configurazione guest per segnalare la conformità nel nodo **Conformità**. Per altre informazioni, vedere [Ottenere dati sulla conformità](../how-to/get-compliance-data.md).

> [!NOTE]
> I criteri **DeployIfNotExists** sono necessari affinché i criteri **AuditIfNotExists** restituiscano i risultati. Senza **DeployIfNotExists**, i criteri **AuditIfNotExists** mostrano "0 di 0" risorse come stato.

Tutti i criteri predefiniti per Configurazione guest sono inclusi in un'iniziativa per raggruppare le definizioni da usare nelle assegnazioni. L'iniziativa predefinita denominata _\[Anteprima\]: Controlla le impostazioni di sicurezza della password nelle macchine virtuali Linux e Windows_ contiene 18 criteri. Esistono sei coppie **DeployIfNotExists** e **AuditIfNotExists** per Windows e tre coppie per Linux. La logica di [definizione dei criteri](definition-structure.md#policy-rule) verifica che venga valutato solo il sistema operativo di destinazione.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Controllo delle impostazioni del sistema operativo secondo le impostazioni di base del settore

Un'iniziativa in Criteri di Azure consente di controllare le impostazioni del sistema operativo seguendo le "impostazioni di base". La definizione, _\[Anteprima\]: Controlla le macchine virtuali Windows che non corrispondono alle impostazioni di base di sicurezza di Azure_ include un set di regole basate su Criteri di gruppo di Active Directory.

La maggior parte delle impostazioni è disponibile sotto forma di parametri. I parametri consentono di personalizzare gli elementi controllati.
Allineare i criteri ai propri requisiti o eseguire il mapping dei criteri a informazioni di terze parti, ad esempio standard normativi del settore.

Alcuni parametri supportano un intervallo di valori interi. L'impostazione Validità massima password, ad esempio, consente di controllare l'impostazione di Criteri di gruppo effettiva. Un intervallo di "1,70" indica che gli utenti devono modificare la password almeno ogni 70 giorni, ma non prima di un giorno.

Se si assegnano i criteri usando un modello di Azure Resource Manager (modello ARM), usare un file di parametri per gestire le eccezioni. Archiviare i file in un sistema di controllo della versione come Git. I commenti sulle modifiche ai file indicano perché un'assegnazione è un'eccezione al valore previsto.

#### <a name="applying-configurations-using-guest-configuration"></a>Applicazione di configurazioni con Configurazione guest

La funzionalità più recente di Criteri di Azure consente di configurare le impostazioni all'interno dei computer. La definizione _Configura il fuso orario nelle macchine Windows_ consente di apportare modifiche nel computer configurando il fuso orario.

Quando si assegnano definizioni che iniziano con _Configura_, è necessario assegnare anche la definizione _Distribuisci i prerequisiti per abilitare i criteri di Configurazione guest nelle macchine virtuali Windows_. Se lo si desidera, è possibile combinare queste definizioni in un'iniziativa.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assegnazione di criteri a computer esterni ad Azure

I criteri di controllo disponibili per Configurazione guest includono il tipo di risorsa **Microsoft.HybridCompute/machines**. Tutti i computer di cui è stato eseguito l'onboarding in [Azure Arc per server](../../../azure-arc/servers/overview.md) inclusi nell'ambito dell'assegnazione dei criteri vengono inclusi automaticamente.

### <a name="multiple-assignments"></a>Assegnazioni multiple

I criteri di Configurazione guest attualmente supportano solo l'assegnazione della stessa assegnazione guest una volta per ogni computer, anche se l'assegnazione dei criteri usa parametri diversi.

## <a name="client-log-files"></a>File di log del client

L'estensione Configurazione guest scrive i file di log nei percorsi seguenti:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Dove `<version>` si riferisce al numero di versione corrente.

### <a name="collecting-logs-remotely"></a>Raccolta di log in modalità remota

Il primo passaggio per la risoluzione dei problemi relativi alle configurazioni o ai moduli di Configurazione guest consiste nell'usare il cmdlet `Test-GuestConfigurationPackage` seguendo la procedura per [creare criteri di controllo di Configurazione guest personalizzati per Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Se l'operazione non riesce, la raccolta dei log del client può aiutare a diagnosticare i problemi.

#### <a name="windows"></a>Windows

Acquisire informazioni dai file di log usando il [comando Run per le macchine virtuali di Azure](../../../virtual-machines/windows/run-command.md). Lo script di esempio di PowerShell seguente può essere utile.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Acquisire informazioni dai file di log usando il [comando Run per le macchine virtuali di Azure](../../../virtual-machines/linux/run-command.md). Lo script di esempio di Bash seguente può essere utile.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Esempi di Configurazione guest

Gli esempi di criteri predefiniti di Configurazione guest sono disponibili nelle posizioni seguenti:

- [Definizioni dei criteri predefiniti - Configurazione guest](../samples/built-in-policies.md#guest-configuration)
- [Iniziative predefinite - Configurazione guest](../samples/built-in-initiatives.md#guest-configuration)
- [Repository GitHub degli esempi di Criteri di Azure](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come visualizzare i dettagli di ogni impostazione nella [visualizzazione conformità di Configurazione guest](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](./definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](./effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
