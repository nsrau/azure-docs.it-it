<properties
	pageTitle="Panoramica del ridimensionamento automatico in Macchine virtuali di Microsoft Azure, Servizi cloud e App Web | Microsoft Azure"
	description="Panoramica del ridimensionamento automatico in Microsoft Azure. Si applica a Macchine virtuali, Servizi cloud e App Web."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="robb"/>

# Panoramica del ridimensionamento automatico in Macchine virtuali di Microsoft Azure, Servizi cloud e App Web

Questo articolo descrive il ridimensionamento automatico di Microsoft Azure e come iniziare a usarlo.

Il ridimensionamento automatico di Azure Insights si applica solo a [set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servizi cloud](https://azure.microsoft.com/services/cloud-services/) e [app Web del servizio app](https://azure.microsoft.com/services/app-service/web/).

>[AZURE.NOTE] Azure offre due metodi per il ridimensionamento automatico. Una versione precedente del ridimensionamento automatico si applica alle macchine virtuali (set di disponibilità). Questa funzionalità offre supporto limitato ed è consigliabile eseguire la migrazione a set di scalabilità di macchine virtuali per ottenere un supporto per il ridimensionamento automatico più veloce e affidabile. Questo articolo include un collegamento su come usare la tecnologia precedente.


## Informazioni sul ridimensionamento automatico

Il ridimensionamento automatico offre la possibilità di avere la quantità corretta di risorse in esecuzione per gestire il carico dell'applicazione. Consente di aggiungere risorse per gestire gli incrementi di carico nonché di risparmiare denaro rimuovendo le risorse inattive. È possibile specificare un numero minimo e massimo di istanze da eseguire e aggiungere o rimuovere automaticamente VM in base a un set di regole. La definizione di un minimo assicura che l'applicazione sia sempre in esecuzione anche in assenza di carico. La definizione di un massimo limita il costo orario totale possibile. Il ridimensionamento viene eseguito automaticamente tra questi due estremi usando le regole create.

 ![Descrizione del ridimensionamento automatico: aggiunta e rimozione di VM](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Quando vengono soddisfatte le condizioni delle regole, vengono attivate una o più azioni di ridimensionamento automatico. È possibile aggiungere e rimuovere VM o eseguire altre azioni. Il diagramma concettuale seguente illustra questo processo.

 ![Diagramma di flusso concettuale del ridimensionamento automatico](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)
 

## Descrizione del processo di ridimensionamento automatico
La descrizione seguente si applica ai componenti del diagramma precedente.

### Metriche delle risorse 
Le risorse generano metriche, che vengono elaborate successivamente dalle regole. Le metriche sono disponibili tramite metodi diversi. I set di scalabilità di macchine virtuali usano i dati di telemetria degli agenti di Diagnostica di Azure, mentre i dati di telemetria per le app Web e i servizi cloud provengono direttamente dall'infrastruttura di Azure. Alcune statistiche comunemente usate includono utilizzo della CPU, utilizzo della memoria, conteggio dei thread, lunghezza della coda e l'utilizzo del disco. Per un elenco dei dati di telemetria che è possibile usare, vedere [Metriche comuni per i ridimensionamento automatico di Azure Insights](insights-autoscale-common-metrics.md).

### Time
Le regole basate sulla pianificazione sono basate su UTC. Quando si configurano le regole, è necessario impostare correttamente il fuso orario.

### Regole
Il diagramma mostra una sola regola di ridimensionamento automatico automatica, ma è possibile averne molte. È possibile creare regole complesse sovrapposte, secondo le esigenze della situazione locale. I tipi di regole includono
 
 - **Basata su metrica**: ad esempio, eseguire questa azione quando l'utilizzo della CPU è superiore al 50%.
 - **Basata sul tempo**: ad esempio, attivare un webhook alle 8:00 di ogni sabato in un determinato fuso orario.

Le regole basate sulle metriche misurano il carico dell'applicazione e aggiungono o rimuovono VM in base a tale carico. Le regole basate sulla pianificazione consentono di eseguire il ridimensionamento quando si rilevano modelli temporali nel carico e si vuole che il ridimensionamento venga eseguito prima di un possibile aumento o una possibile riduzione del carico.

 
### Azioni e automazione

Le regole possono attivare uno o più tipi di azioni.

- **Ridimensionamento**: per aumentare o ridurre il numero di istanze di macchine virtuali
- **Posta elettronica**: per inviare un messaggio di posta elettronica ad amministratori e coamministratori di una sottoscrizione e/o ad altri indirizzi di posta elettronica specificati
- **Automatizzare tramite webhook**: per chiamare webhook che possono attivare più azioni complesse all'interno o all'esterno di Azure. All'interno di Azure è possibile avviare un runbook di automazione di Azure, una funzione Azure o un'app per la logica di Azure. Un esempio di URL di terze parti esterno ad Azure include servizi come Slack e Twilio.


## Impostazioni di scalabilità automatica
Per il ridimensionamento automatico vengono usate la terminologia e la struttura seguenti.

- Un'**impostazione di ridimensionamento automatico** viene letta dal motore di ridimensionamento automatico per determinare se aumentare o ridurre le prestazioni. Contiene uno o più profili, informazioni sulla risorsa di destinazione e impostazioni di notifica.
    - Un **profilo di ridimensionamento automatico** combina un'impostazione di capacità, un set di regole che controlla i trigger, le azioni di ridimensionamento per il profilo e una ricorrenza. È possibile avere più profili per gestire diversi requisiti sovrapposti.
        - Un'**impostazione di capacità** indica i valori minimo, massimo e predefinito per il numero di istanze (figura 1).
        - Una **regola** include un trigger (di metrica o temporale) e un'azione di ridimensionamento, che indica se il ridimensionamento automatico deve aumentare o ridurre le prestazioni quando la regola viene soddisfatta.
        - Una **ricorrenza** indica quando il ridimensionamento automatico renderà effettivo il profilo. È ad esempio possibile avere profili di ridimensionamento automatico per diverse ore del giorno o diversi giorni della settimana.
- Un'**impostazione di notifica** definisce le notifiche da creare quando si verifica un evento di ridimensionamento automatico in base ai criteri di uno dei profili di impostazioni di ridimensionamento automatico. Possono essere inviate notifiche a uno o più indirizzi di posta elettronica oppure possono essere effettuate chiamate a uno o più webhook.
 
![Struttura delle impostazioni, dei profili e delle regole di ridimensionamento automatico in Azure](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

L'elenco completo dei campi e delle descrizioni configurabili è disponibile nella documentazione sull'[API REST per il ridimensionamento automatico](https://msdn.microsoft.com/library/dn931928.aspx).

Per esempi di codice, vedere

* [Configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [API REST per il ridimensionamento automatico](https://msdn.microsoft.com/library/dn931953.aspx)



## Ridimensionamento orizzontale e verticale
  
Il ridimensionamento automatico aumenta le risorse solo orizzontalmente, ovvero aumenta o riduce il numero di istanze di VM. Il ridimensionamento orizzontale è più flessibile in una situazione cloud, perché consente di eseguire potenzialmente migliaia di VM per gestire il carico. Il ridimensionamento verticale è diverso, perché mantiene lo stesso numero di VM ma le rende più o meno potenti. La potenza è misurata in memoria, velocità della CPU, spazio su disco e così via. Il ridimensionamento verticale presenta più limitazioni. Dipende dalla disponibilità di hardware più grande, che può variare in base all'area e raggiungere rapidamente il limite massimo. In genere richiede anche l'arresto e il riavvio della macchina virtuale. Per altre informazioni, vedere [Aumento delle prestazioni delle macchine virtuali di Azure tramite Automazione di Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


## Metodi di accesso 
È possibile configurare il ridimensionamento automatico tramite:

- [Portale di Azure](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [Interfaccia della riga di comando multipiattaforma](insights-cli-samples.md#autoscale)
- [API REST Insights](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## Servizi supportati per il ridimensionamento automatico


| Service | Schema e documenti |
|--------------------------------------|-----------------------------------------------------|
| App Web | [Ridimensionamento di app Web](insights-how-to-scale.md) |
| Servizi cloud | [Ridimensionamento automatico di un servizio cloud](../cloud-services/cloud-services-how-to-scale.md) |
| Macchine virtuali classiche | [Scaling Classic Virtual Machine Availability Sets (Ridimensionamento di set di disponibilità di macchine virtuale classiche)](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Macchine virtuali: set di scalabilità Windows| [Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Macchine virtuali: set di scalabilità Linux | [Ridimensionare automaticamente macchine virtuali Linux in un set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Macchine virtuali: esempio Windows | [Configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## Passaggi successivi

Per altre informazioni sul ridimensionamento automatico, usare le procedure dettagliate per il ridimensionamento automatico elencate in precedenza o vedere le risorse seguenti:

- [Metriche comuni per il ridimensionamento automatico di Azure Insights](insights-autoscale-common-metrics.md)
- [Procedure consigliate per il ridimensionamento automatico in Azure Insights](insights-autoscale-best-practices.md)
- [Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](insights-autoscale-to-webhook-email.md)
- [API REST per il ridimensionamento automatico](https://msdn.microsoft.com/library/dn931953.aspx)
- [Risoluzione dei problemi di ridimensionamento automatico con set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0914_2016-->