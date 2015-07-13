<properties
	pageTitle="Guida alle funzionalità di Operational Insights"
	description="Operational Insights è un servizio di analisi che consente agli amministratori IT di ottenere una visione approfondita di ambienti locali e nel cloud. Consente di interagire con i dati dei computer, sia quelli cronologici che quelli in tempo reale, per lo sviluppo rapido di informazioni personalizzate, oltre a fornire modelli sviluppati da Microsoft e dalla community per l'analisi dei dati."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="banders"/>

# Guida alle funzionalità di Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Questo articolo consente di comprendere quali problemi possono essere risolti con l'aiuto di Operational Insights, da cosa è costituito l'ambiente di Operational Insights e come creare un account di Operational Insights per accedere al servizio.

## Trasformare i dati del computer

Operational Insights è un servizio di analisi che consente agli amministratori IT di ottenere una visione approfondita di ambienti locali e nel cloud. Consente di interagire con i dati dei computer, sia quelli cronologici che quelli in tempo reale, per lo sviluppo rapido di informazioni personalizzate, oltre a fornire modelli sviluppati da Microsoft e dalla community per l'analisi dei dati.

Grazie a Operational Insights, è possibile trasformare i dati dei computer in intelligence operativa con le seguenti funzionalità.


|**Icona** | **Cosa utilizzare** | **Risultato**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [Pianificazione della capacità](operational-insights-capacity.md) | È possibile usare la soluzione Capacity Planning in Microsoft Azure Operational Insights per determinare la capacità dell'infrastruttura server. |
| ![](./media/operational-insights-feature-guide/update.png) | [Valutazione dell’aggiornamento del sistema](operational-insights-updates.md) | È possibile usare la soluzione System Updates in Microsoft Azure Operational Insights per applicare gli aggiornamenti mancanti ai server dell'infrastruttura. |
| ![](./media/operational-insights-feature-guide/log-mgt.png) | [Log Management](operational-insights-search.md) | È possibile utilizzare la soluzione Log Management per log di IIS ed eventi per le ricerche di log in Operational Insights. |
| ![](./media/operational-insights-feature-guide/malware.png) | [Valutazione di software dannoso](operational-insights-antimalware.md) | È possibile usare la soluzione Antimalware in Microsoft Azure Operational Insights per proteggere i server dell'infrastruttura da attacchi derivanti da software dannoso. |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [Protezione e controllo](operational-insights-security-audit.md) | Informazioni su come usare la soluzione Security and Audit per ottenere una panoramica completa sulla situazione della sicurezza IT all'interno dell'organizzazione grazie alla disponibilità di query di ricerca predefinite relative a problemi rilevanti che richiedono attenzione |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Valutazione di Active Directory e SQL](operational-insights-assessment.md) | È possibile usare la soluzione Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari |
| ![](./media/operational-insights-feature-guide/alert.png) | [Gestione degli avvisi](operational-insights-alerts.md) | È possibile utilizzare la soluzione Alert Management per gestire gli avvisi dai server monitorati da Operations Manager. |


È anche possibile:

- **Inviare i dati del computer al sistema con o senza l'utilizzo di un agente o in combinazione con System Center Operations Manager**. Per altre informazioni, vedere:
	- [Connettersi a Operational Insights da System Center Operations Manager](operational-insights-connect-scom.md)
	- [Connettere i computer direttamente a Operational Insights](operational-insights-direct-agent.md)
	- [Analizzare i dati dai server in Microsoft Azure](operational-insights-analyze-data-azure.md)
- **Eseguire tutte le operazioni precedenti con applicazioni per dispositivi mobili**
	- Per ulteriori informazioni sull'applicazione Windows Phone, vedere [Operational Insights Mobile app](http://www.windowsphone.com/it-it/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)

## Ambiente di Operational Insights

L'ambiente di Operational Insights è costituito da:

- Aree di lavoro ospitate in Microsoft Azure che rappresentano i contenitori per gli account di Azure
- Il servizio Web di Operational Insights, che è ospitato nel cloud
- Agenti separati che si connettono direttamente al servizio Web, oppure
- Un servizio collegato a System Center Operations Manager, anche se non indispensabile


Se si usava la versione precedente di Operational Insights denominata System Center Advisor, nell'ambiente locale potrebbe essere installato del software Advisor. Tale software non è tuttavia supportato in Operational Insights.

L'uso di Operational Insights come servizio di Operations Manager presuppone l'uso di uno o più gruppi di gestione e di almeno un agente per gruppo di gestione. Gli agenti di Operations Manager raccolgono i dati dai server e li analizzano usando le soluzioni (simili ai Management Pack in System Center Operations Manager). I dati vengono inviati periodicamente da Operations Manager al servizio Web di Operational Insights (se necessario passando attraverso un server proxy), senza elementi archiviati in alcuno dei database di Operations Manager e quindi senza alcun carico aggiuntivo.

Analogamente, gli agenti installati nei singoli computer possono connettersi direttamente al servizio Web per inviare i dati raccolti per l'elaborazione.

I dati in ogni soluzione vengono analizzati e presentati nel portale di Operational Insights. È possibile visualizzare tutti gli avvisi e le relative indicazioni correttive, le valutazioni della configurazione, i problemi di capacità dell'infrastruttura, lo stato degli aggiornamenti del sistema, gli avvisi antimalware e i dati dei log. È inoltre possibile eseguire ricerche ed esplorazioni di log dettagliate ad hoc.

![Grafico della panoramica di Operational Insights](./media/operational-insights-feature-guide/environment.png)

### Dov’è disponibile Operational Insights?
Microsoft Azure Operational Insights è ospitato negli Stati Uniti. Anche se Operational Insights è in lingua inglese, il servizio è disponibile in diversi altri mercati. Per informazioni, vedere [Disponibilità Internazionale](http://go.microsoft.com/fwlink/?LinkId=229842).
 

<!---HONumber=July15_HO1-->