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
	ms.date="08/30/2016"
	ms.author="robb"/>

# Panoramica del ridimensionamento automatico in Macchine virtuali di Microsoft Azure, Servizi cloud e App Web

Questo articolo descrive le operazioni di ridimensionamento automatico di Microsoft Azure è, i relativi vantaggi e come iniziare a usarlo.

Il ridimensionamento automatico di Azure Insights di applica solo a

* [Set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/)
* [Servizi cloud](https://azure.microsoft.com/services/cloud-services/)
* [App Web del servizio app](https://azure.microsoft.com/services/app-service/web/)
  
   
>[AZURE.NOTE] Azure offre due metodi per il ridimensionamento automatico. Una versione precedente del ridimensionamento automatico si applica alle macchine virtuali (set di disponibilità). Questa funzionalità offre un supporto limitato ed è consigliabile eseguire la migrazione a set di scalabilità di macchina virtuali per ottenere un supporto per il ridimensionamento automatico più veloce e affidabile. Questo articolo include un collegamento su come usare la tecnologia precedente.


## Informazioni sul ridimensionamento automatico 

Il ridimensionamento automatico consente di avere la giusta quantità di risorse in esecuzione per gestire il carico dell'applicazione, senza sprecare denaro a causa di risorse rimaste inattive. Consente di aggiungere o rimuovere automaticamente le risorse di calcolo in base a un set di regole. La figura 1 illustra questo concetto.

![Descrizione del concetto di ridimensionamento automatico](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

**Figure 1: Descrizione del concetto di ridimensionamento automatico**

Il ridimensionamento automatico, cosiddetto "orizzontale", aumenta o riduce solo il numero di istanze delle macchine virtuali. Questo tipo di ridimensionamento è più flessibile in una situazione cloud, perché consente di eseguire potenzialmente migliaia di macchine virtuali per gestire il carico. L'altro tipo di ridimensionamento, cosiddetto "verticale", riguarda l'aumento o la riduzione delle prestazioni. Questo tipo di ridimensionamento mantiene lo stesso numero di macchine virtuali, ma le rende più o meno potenti. La potenza è misurata in memoria, velocità della CPU, spazio su disco e così via. Il ridimensionamento verticale presenta più limitazioni. Dipende dalla disponibilità di hardware più grande, che può variare in base all'area e raggiungere rapidamente il limite massimo. In genere richiede anche l'arresto e il riavvio della macchina virtuale. Per altre informazioni, vedere [Aumento delle prestazioni delle macchine virtuali di Azure tramite Automazione di Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


Il ridimensionamento automatico richiede la creazione di regole che definiscano la modalità di aumento o riduzione delle risorse. I criteri che è possibile impostare per controllare le azioni di ridimensionamento includono

* Numero **minimo** e **massimo** delle istanze da eseguire. Un minimo per garantire che l'applicazione sia sempre in esecuzione e un massimo per controllare i costi.
* **Regola o condizione** per il ridimensionamento automatico. Può essere un ridimensionamento basato su una metrica o una pianificazione.
* **Tempo di attesa**, ovvero la quantità di tempo di attesa dopo un evento di ridimensionamento automatico prima di consentirne un altro. Questo intervallo di tempo serve per proteggersi da uno stato detto di "instabilità", che si verifica quando le macchine virtuali vengono aggiunte e rimosse rapidamente nell'arco di pochi minuti. L'avvio o l'arresto di una macchina virtuale comporta un costo. L'instabilità non consente un risparmio in termini di costi e la macchina virtuale avviata e arrestata non può eseguire alcun tipo di elaborazione utile, quindi è peggio che lasciare la macchina virtuale in esecuzione.

   
L'elenco completo dei valori configurabili è disponibile nel [API REST di ridimensionamento automatico](https://msdn.microsoft.com/library/dn931928.aspx).


## Diagramma concettuale  
La figura 2 mostra una panoramica concettuale del ridimensionamento automatico, seguita da una spiegazione delle parti del diagramma.

![Aggiunta di una regola](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)

**Figura 2: Panoramica del processo di ridimensionamento automatico**

## Metriche delle risorse 
Le risorse generano metriche, che vengono elaborate successivamente dalle regole. Le metriche sono disponibili tramite metodi diversi. I set di scalabilità di macchine virtuali usano i dati di telemetria degli agenti di Diagnostica di Azure, mentre i dati di telemetria per le app Web e i servizi cloud provengono direttamente dall'infrastruttura di Azure. Alcune statistiche comunemente usate includono utilizzo della CPU, utilizzo della memoria, conteggio dei thread, lunghezza della coda e l'utilizzo del disco. Per un elenco dei dati di telemetria che è possibile usare, vedere [Metriche comuni per i ridimensionamento automatico di Azure Insights](insights-autoscale-common-metrics.md).

 
## Time
Le regole basate sulla pianificazione sono basate su UTC. Quando si configurano le regole, è necessario impostare correttamente il fuso orario.

## Regole di ridimensionamento automatico
Il diagramma mostra una sola regola di ridimensionamento automatico automatica, ma è possibile averne molte. È possibile creare regole complesse sovrapposte, secondo le esigenze della situazione locale. I tipi di regole includono
 
 - **Basata su metrica**: ad esempio, eseguire questa azione quando l'utilizzo della CPU è superiore al 50%.
 - **Basata sul tempo**: ad esempio, attivare un webhook alle 8:00 di ogni sabato in un determinato fuso orario.

 
## Automazione e azioni di ridimensionamento automatico

Le regole possono attivare uno o più tipi di azioni.

- **Ridimensionamento**: per aumentare o ridurre il numero di istanze di macchine virtuali
- **Posta elettronica**: per inviare un messaggio di posta elettronica ad amministratori e coamministratori di una sottoscrizione e ad altri indirizzi di posta elettronica specificati
- **Automatizzare tramite webhook**: per chiamare webhook che possono attivare più azioni complesse all'interno o all'esterno di Azure. All'interno di Azure è possibile avviare un runbook di automazione di Azure, una funzione Azure o un'app per la logica di Azure. Un esempio di URL di terze parti esterno ad Azure include servizi come Slack e Twilio.


## Dettagli delle regole di Resource Manager

Le regole di ridimensionamento automatico hanno la struttura seguente in un modello di Azure Resource Manager.

![Struttura delle regole di ridimensionamento automatico di Azure Resource Manager](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure2.png)

È possibile elencare più profili. Ogni profilo può avere più regole. I percorsi e i metodi di notifica sono inclusi dopo i profili. Gli esempi di notifica includono un webhook con l'URI o messaggi di posta elettronica con i relativi indirizzi.

Per esempi di codice, vedere

* [Configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [API REST di ridimensionamento automatico](https://msdn.microsoft.com/library/dn931953.aspx)

## Metodi di accesso 
È possibile configurare le regole di ridimensionamento automatico tramite

- Portale di Azure
- PowerShell
- Interfaccia della riga di comando
- API REST Insights

## Procedure dettagliate per il ridimensionamento automatico

- [Come configurare il ridimensionamento automatico di un servizio cloud](../cloud-services/cloud-services-how-to-scale-portal.md)
- [Ridimensionamento di app Web](insights-how-to-scale.md)
- [Scaling Classic Virtual Machine Availability Sets](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) (Ridimensionamento di set di disponibilità di macchine virtuale classiche)
- [Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)
- [Ridimensionare automaticamente macchine virtuali Linux in un set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md)
- [Configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali](insights-advanced-autoscale-virtual-machine-scale-sets.md)

## Passaggi successivi

Per altre informazioni sul ridimensionamento automatico, usare le Procedure dettagliate per il ridimensionamento automatico elencate in precedenza o vedere le risorse seguenti:

- [Metriche comuni per la scalabilità automatica di Azure Insights](insights-autoscale-common-metrics.md)
- [Procedure consigliate per la scalabilità automatica in Azure Insights](insights-autoscale-best-practices.md)
- [Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](insights-autoscale-to-webhook-email.md)
- [API REST di ridimensionamento automatico](https://msdn.microsoft.com/library/dn931953.aspx) vedere l'intera API e i significati per ognuno dei campi e dei valori
- [Risoluzione dei problemi di ridimensionamento automatico con set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0907_2016-->