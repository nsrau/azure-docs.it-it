<properties
	pageTitle="Soluzione di valutazione dell'aggiornamento del sistema in Log Analytics | Microsoft Azure"
	description="È possibile usare la soluzione System Update Assessment (Valutazione aggiornamento del sistema) in Log Analytics per applicare gli aggiornamenti mancanti ai server dell'infrastruttura."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="banders"/>

# Soluzione di valutazione dell'aggiornamento del sistema in Log Analytics


È possibile usare la soluzione System Update Assessment (Valutazione aggiornamento del sistema) in Log Analytics per applicare gli aggiornamenti mancanti ai server dell'infrastruttura. Dopo l'installazione della soluzione, è possibile visualizzare gli aggiornamenti mancanti nei server monitorati usando il riquadro **System Update Assessment** (Valutazione aggiornamento del sistema) nella pagina **Overview** (Panoramica) in OMS.

Gli eventuali aggiornamenti mancanti rilevati sono descritti nel dashboard **Updates** (Aggiornamenti). È possibile usare il dashboard **Updates** per elaborare gli aggiornamenti mancanti e sviluppare un piano per applicarli ai server su cui sono necessari.

## Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

- Aggiungere la soluzione di valutazione dell'aggiornamento del sistema all'area di lavoro OMS usando la procedura descritta nell'articolo sull'[aggiunta di soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.

## Informazioni dettagliate sulla raccolta dei dati di aggiornamento del sistema

La tabella seguente mostra i metodi di raccolta dei dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati per la valutazione dell'aggiornamento del sistema.

| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
|---|---|---|---|---|---|---|
|Windows|![Sì](./media/log-analytics-system-update/oms-bullet-green.png)|![Sì](./media/log-analytics-system-update/oms-bullet-green.png)|![No](./media/log-analytics-system-update/oms-bullet-red.png)| ![No](./media/log-analytics-system-update/oms-bullet-red.png)|![Sì](./media/log-analytics-system-update/oms-bullet-green.png)| Almeno 2 volte al giorno e 15 minuti dopo l'installazione di un aggiornamento|


### Per usare gli aggiornamenti

1. Nella pagina **Overview** fare clic sul riquadro **System Update Assessment**. ![immagine della pagina Overview](./media/log-analytics-system-update/oms-updates01.png)
2. Nel dashboard **Updates** visualizzare le categorie degli aggiornamenti. ![immagine della pagina Updates](./media/log-analytics-system-update/oms-updates02.png)
3. Scorrere verso destra la pagina per visualizzare il pannello **Type of Updates Missing** e quindi fare clic su **Security Updates**. ![immagine della pagina Updates](./media/log-analytics-system-update/oms-updates03.png)
4. Nella pagina di ricerca, viene visualizzato un elenco di aggiornamenti di sicurezza mancante nei server dell'infrastruttura. Fare clic sull'ID di un articolo della Knowledge Base (KBID) per visualizzare altre informazioni sull'aggiornamento mancante. In questo esempio, *KBID 3032323*. ![immagine della pagina Updates](./media/log-analytics-system-update/oms-updates04.png)
5. Il browser Web apre l'articolo della Knowledge Base che descrive l'aggiornamento. ![immagine dell'articolo della knowledge base](./media/log-analytics-system-update/oms-updates05.png)
6. Con le informazioni trovate, è possibile creare un piano per applicare gli aggiornamenti mancanti.

## Passaggi successivi

- [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare dati dettagliati di aggiornamento del sistema.

<!---HONumber=AcomDC_0518_2016-->