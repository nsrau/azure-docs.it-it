---
title: Panoramica dell'agente di monitoraggio di Azure
description: Panoramica dell'agente di monitoraggio di Azure (AMA), che raccoglie i dati di monitoraggio dal sistema operativo guest delle macchine virtuali.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 466851ce04a047f3edabcf33b45dba9cab0db20e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132704"
---
# <a name="azure-monitor-agent-overview-preview"></a>Panoramica dell'agente di monitoraggio di Azure (anteprima)
L'agente di monitoraggio di Azure raccoglie i dati di monitoraggio dal sistema operativo guest delle macchine virtuali e li recapita a monitoraggio di Azure. Questo articolo fornisce una panoramica dell'agente di monitoraggio di Azure, tra cui come installarlo e come configurare la raccolta dei dati.

## <a name="relationship-to-other-agents"></a>Relazione con altri agenti
L'agente di monitoraggio di Azure sostituisce gli agenti seguenti attualmente usati da monitoraggio di Azure per raccogliere i dati Guest dalle macchine virtuali:

- [Log Analytics Agent](log-analytics-agent.md) : Invia i dati all'area di lavoro log Analytics e supporta monitoraggio di Azure per le macchine virtuali e le soluzioni di monitoraggio.
- [Estensione di diagnostica](diagnostics-extension-overview.md) : Invia i dati alle metriche di monitoraggio di Azure (solo Windows), Hub eventi di Azure e archiviazione di Azure.
- [Agente Telegraf](collect-custom-metrics-linux-telegraf.md) : Invia i dati alle metriche di monitoraggio di Azure (solo Linux).

Oltre a consolidare questa funzionalità in un singolo agente, l'agente di monitoraggio di Azure offre i seguenti vantaggi rispetto agli agenti esistenti:

- Ambito di monitoraggio. Configurare centralmente la raccolta per diversi set di dati da diversi set di macchine virtuali.  
- Multihoming Linux: consente di inviare dati da macchine virtuali Linux a più aree di lavoro.
- Filtro eventi di Windows: utilizzare query XPATH per filtrare gli eventi di Windows raccolti.
- Gestione delle estensioni migliorata: l'agente di monitoraggio di Azure usa un nuovo metodo di gestione dell'estensibilità più trasparente e controllabile rispetto ai Management Pack e ai plug-in di Linux negli agenti Log Analytics correnti.

### <a name="changes-in-data-collection"></a>Modifiche alla raccolta dati
I metodi per la definizione della raccolta dei dati per gli agenti esistenti sono distintamente diversi l'uno dall'altro e ognuno di essi ha problemi che vengono affrontati con l'agente di monitoraggio di Azure.

- Log Analytics Agent ottiene la configurazione da un'area di lavoro di Log Analytics. Si tratta di una configurazione facile da configurare a livello centralizzato, ma difficile da definire definizioni indipendenti per diverse macchine virtuali. Può inviare dati solo a un'area di lavoro Log Analytics.

- L'estensione di diagnostica ha una configurazione per ogni macchina virtuale. Si tratta di una facile definizione di definizioni indipendenti per diverse macchine virtuali, ma difficili da gestire a livello centralizzato. Può inviare dati solo a metriche di monitoraggio di Azure, Hub eventi di Azure o archiviazione di Azure. Per gli agenti Linux, è necessario l'agente di telegrafing open source per inviare dati alle metriche di monitoraggio di Azure.

L'agente di monitoraggio di Azure usa [le regole di raccolta dati (DCR)](data-collection-rule-overview.md) per configurare i dati da raccogliere da ogni agente. Le regole di raccolta dati consentono la gestibilità delle impostazioni di raccolta su larga scala, pur continuando ad abilitare configurazioni univoche con ambito per subset di computer. Sono indipendenti dall'area di lavoro e indipendenti dalla macchina virtuale, che ne consente la definizione e il riutilizzo tra computer e ambienti. Vedere [configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>È necessario passare all'agente di monitoraggio di Azure?
L'agente di monitoraggio di Azure coesiste con gli [agenti disponibili a livello generale per monitoraggio di Azure](agents-overview.md), ma è possibile prendere in considerazione la transizione delle VM dagli agenti correnti durante il periodo di anteprima pubblica dell'agente di monitoraggio di Azure. Quando si esegue questa determinazione, considerare i fattori seguenti.

- **Requisiti dell'ambiente.** L'agente di monitoraggio di Azure dispone di un set più limitato di sistemi operativi, ambienti e requisiti di rete supportati rispetto agli agenti correnti. Il supporto per l'ambiente futuro, ad esempio le nuove versioni del sistema operativo e i tipi di requisiti di rete, verrà probabilmente fornito solo nell'agente di monitoraggio di Azure. È necessario valutare se l'ambiente è supportato dall'agente di monitoraggio di Azure. In caso contrario, sarà necessario rimanere con l'agente corrente. Se l'agente di monitoraggio di Azure supporta l'ambiente corrente, è necessario prendere in considerazione la transizione.
- **Tolleranza di rischio per l'anteprima pubblica.** Mentre l'agente di monitoraggio di Azure è stato testato accuratamente per gli scenari attualmente supportati, l'agente è ancora in anteprima pubblica. Gli aggiornamenti delle versioni e i miglioramenti delle funzionalità si verificheranno di frequente e potrebbero presentare bug. È necessario valutare il rischio di un bug nell'agente nelle VM che potrebbero arrestare la raccolta dei dati. Se un gap nella raccolta dei dati non avrà un impatto significativo sui servizi, procedere con l'agente di monitoraggio di Azure. Se si dispone di una tolleranza bassa per qualsiasi instabilità, è consigliabile restare con gli agenti disponibili a livello generale finché l'agente di monitoraggio di Azure non raggiunge questo stato.
- **Requisiti delle funzionalità correnti e nuovi.** L'agente di monitoraggio di Azure introduce diverse nuove funzionalità, ad esempio l'applicazione di filtri, l'ambito e il multihominging, ma non ha ancora la parità con gli agenti correnti per altre funzionalità, ad esempio la raccolta di log personalizzati e l'integrazione con le soluzioni. La maggior parte delle nuove funzionalità di monitoraggio di Azure verrà resa disponibile solo con l'agente di monitoraggio di Azure, quindi nel tempo altre funzionalità saranno disponibili solo nel nuovo agente. È necessario considerare se l'agente di monitoraggio di Azure ha le funzionalità richieste e se sono presenti alcune funzionalità che è possibile eseguire temporaneamente senza per ottenere altre importanti funzionalità del nuovo agente. Se l'agente di monitoraggio di Azure dispone di tutte le funzionalità di base necessarie, provare a eseguire la transizione. Se sono presenti funzionalità critiche necessarie, continuare con l'agente corrente fino a quando l'agente di monitoraggio di Azure non raggiunge la parità.
- **Tolleranza per la rielaborazione.** Se si sta configurando un nuovo ambiente con risorse quali gli script di distribuzione e i modelli di onboarding, è necessario valutare se sarà possibile rielaborarli quando l'agente di monitoraggio di Azure diventa disponibile a livello generale. Se il lavoro richiesto per questa operazione è minimo, sarà necessario usare gli agenti correnti per il momento. Se il lavoro prevede una quantità significativa di lavoro, è consigliabile configurare il nuovo ambiente con il nuovo agente. L'agente di monitoraggio di Azure dovrebbe essere disponibile a livello generale e una data di deprecazione pubblicata per gli agenti Log Analytics in 2021. Gli agenti correnti saranno supportati per diversi anni dopo l'inizio della deprecazione.



## <a name="current-limitations"></a>Limitazioni correnti
Durante l'anteprima pubblica dell'agente di monitoraggio di Azure si applicano le limitazioni seguenti:

- L'agente di monitoraggio di Azure non supporta soluzioni e informazioni dettagliate, ad esempio Monitoraggio di Azure per le macchine virtuali e il Centro sicurezza di Azure. L'unico scenario attualmente supportato è la raccolta di dati tramite le regole di raccolta dati configurate. 
- È necessario creare regole di raccolta dati nella stessa area di qualsiasi area di lavoro Log Analytics utilizzata come destinazione.
- Attualmente sono supportate solo macchine virtuali di Azure. Le macchine virtuali locali, i set di scalabilità di macchine virtuali, l'arco per i server, il servizio Azure Kubernetes e altri tipi di risorse di calcolo non sono attualmente supportati.
- La macchina virtuale deve avere accesso agli endpoint HTTPS seguenti:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Coesistenza con altri agenti
L'agente di monitoraggio di Azure può coesistere con gli agenti esistenti per poter continuare a usare la funzionalità esistente durante la valutazione o la migrazione. Questa operazione è particolarmente importante a causa delle limitazioni dell'anteprima pubblica per supportare le soluzioni esistenti. È necessario prestare attenzione quando si raccolgono dati duplicati poiché questo potrebbe inclinare i risultati della query e causare addebiti aggiuntivi per l'inserimento e la conservazione dei dati.

Ad esempio, Monitoraggio di Azure per le macchine virtuali utilizza l'agente Log Analytics per inviare i dati sulle prestazioni a un'area di lavoro Log Analytics. È anche possibile che sia stata configurata l'area di lavoro per raccogliere eventi di Windows e syslog dagli agenti. Se si installa l'agente di monitoraggio di Azure e si crea una regola di raccolta dati per gli stessi eventi e dati sulle prestazioni, si otterranno dati duplicati.


## <a name="costs"></a>Costi
Per l'agente di monitoraggio di Azure non è previsto alcun costo, ma è possibile che vengano addebitati costi per i dati inseriti. Per informazioni dettagliate sulla raccolta e la conservazione dei dati Log Analytics e per le metriche dei clienti, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="data-sources-and-destinations"></a>Origini dati e destinazioni
La tabella seguente elenca i tipi di dati che è attualmente possibile raccogliere con l'agente di monitoraggio di Azure usando le regole di raccolta dei dati e dove è possibile inviare tali dati. Vedere [che cosa viene monitorato da monitoraggio di Azure?](../monitor-reference.md) per un elenco di informazioni dettagliate, soluzioni e altre soluzioni che usano l'agente di monitoraggio di Azure per raccogliere altri tipi di dati.


L'agente di monitoraggio di Azure invia dati alle metriche di monitoraggio di Azure o a un'area di lavoro Log Analytics che supporta i log di monitoraggio di Azure.

| origine dati | Destinations | Descrizione |
|:---|:---|:---|
| Prestazioni        | Metriche di Monitoraggio di Azure<br>Area di lavoro Log Analytics | Valori numerici che misurano le prestazioni di diversi aspetti del sistema operativo e dei carichi di lavoro. |
| Registri eventi di Windows | Area di lavoro Log Analytics | Informazioni inviate al sistema di registrazione eventi di Windows. |
| syslog             | Area di lavoro Log Analytics | Informazioni inviate al sistema di registrazione eventi di Linux. |


## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Vedere [sistemi operativi supportati](agents-overview.md#supported-operating-systems) per un elenco delle versioni del sistema operativo Windows e Linux attualmente supportate dall'agente di monitoraggio di Azure.



## <a name="security"></a>Sicurezza
L'agente di monitoraggio di Azure non richiede chiavi, ma richiede invece un' [identità gestita assegnata dal sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Prima di distribuire l'agente, è necessario che sia abilitata un'identità gestita assegnata dal sistema in ogni macchina virtuale.

## <a name="networking"></a>Rete
L'agente di monitoraggio di Azure supporta i tag dei servizi di Azure (sono necessari sia i tag AzureMonitor che AzureResourceManager) ma non funziona ancora con gli ambiti di collegamento privato di monitoraggio di Azure o con proxy diretti.

## <a name="install-the-azure-monitor-agent"></a>Installare l'agente di monitoraggio di Azure
L'agente di monitoraggio di Azure viene implementato come [estensione della macchina virtuale di Azure](../../virtual-machines/extensions/overview.md) con i dettagli nella tabella seguente. 

| Proprietà | Windows | Linux |
|:---|:---|:---|
| Editore | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

Installare l'agente di monitoraggio di Azure usando uno dei metodi per installare gli agenti di macchine virtuali, inclusi i seguenti, usando PowerShell o l'interfaccia della riga di comando. In alternativa, è possibile installare l'agente e configurare la raccolta dei dati nelle macchine virtuali nella sottoscrizione di Azure usando il portale con la procedura descritta in [configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Passaggi successivi

- [Creare una regola di raccolta dati](data-collection-rule-azure-monitor-agent.md) per raccogliere dati dall'agente e inviarli a monitoraggio di Azure.
