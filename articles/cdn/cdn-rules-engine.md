<properties 
	pageTitle="Rete CDN - Override del comportamento HTTP predefinito mediante il motore regole" 
	description="Il motore regole consente di personalizzare la modalità con cui vengono gestite le richieste HTTP, come ad esempio la distribuzione di determinati tipi di contenuto, la definizione di un criterio di memorizzazione nella cache e la modifica delle intestazioni HTTP." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Override del comportamento HTTP predefinito mediante il motore regole

## Panoramica

Il motore regole consente di personalizzare la modalità con cui vengono gestite le richieste HTTP, come ad esempio la distribuzione di determinati tipi di contenuto, la definizione di un criterio di memorizzazione nella cache e la modifica della intestazioni HTTP. Questa esercitazione illustra la creazione di una regola che modifica il comportamento di memorizzazione nella cache degli asset della rete CDN.

> [AZURE.NOTE]Il motore regole è una funzionalità del piano Premium per la rete CDN. Per un confronto delle funzionalità della rete CDN Standard e Premium, vedere [Panoramica della rete CDN di Azure](cdn-overview.md).

## Esercitazione

1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci**.

	![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-rules-engine/cdn-rules-manage-btn.png)
	
	Si aprirà il portale di gestione della rete CDN.
	
2. Fare clic sulla scheda **HTTP Grande**, seguita da **Motore regole**.
	
	Vengono visualizzate le opzioni per una nuova regola.
	
	![Opzioni delle regole della nuova rete CDN](./media/cdn-rules-engine/cdn-new-rule.png)

3. Inserire un nome per la casella di testo **Nome / Descrizione**.

4. Identificare il tipo di richieste che a cui verrà applicata la regola. Per impostazione predefinita, viene selezionata la condizione corrispondente **Sempre**. Si utilizzerà **Sempre** per questa esercitazione, quindi lasciarla selezionata.

	![Condizione di corrispondenza della rete CDN](./media/cdn-rules-engine/cdn-request-type.png)
	
	>[AZURE.TIP]Vi sono molti tipi di condizioni di corrispondenza disponibili nell'elenco a discesa. Facendo clic sull'icona blu delle informazioni a sinistra della condizione di corrispondenza viene illustrata la condizione selezionata in modo dettagliato.
	>
	>Per l'elenco completo delle condizioni di corrispondenza illustrate in modo dettagliato, vedere [Condizione di corrispondenza del motore regole e Informazioni sulle funzionalità](cdn-rules-engine-details.md#match-conditions).
	
5.  Fare clic sul pulsante **+** accanto a **Funzionalità** per aggiungere una nuova funzionalità. Nell'elenco a discesa a sinistra, selezionare **Forza interna Max-Age**. Nella casella di testo che viene visualizzata, inserire **300**. Lasciare i valori predefiniti restanti.

	![Funzionalità della rete CDN](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE]Come con le condizioni di corrispondenza, fare clic sull'icona blu delle informazioni a sinistra della nuova funzionalità per visualizzare i dettagli su questa funzionalità. Nel caso di **Forza interna Max-Age**, è stato eseguito l'override dell'asset **Cache-Control** e delle intestazioni **Scadenze** per controllare quando il nodo edge della rete CDN aggiornerà l'asset dall'origine. L’esempio di 300 secondi indica che il nodo edge della rete CDN memorizza l’asset nella cache per 5 minuti prima di aggiornare la risorsa dall'origine.
	>
	>Per l'elenco completo delle funzionalità illustrate in modo dettagliato, vedere [Condizione di corrispondenza del motore regole e Informazioni sulle funzionalità](cdn-rules-engine-details.md#features).
	
6.  Fare clic sul pulsante **Aggiungi** per salvare la nuova regola. La nuova regola ora è in attesa di approvazione. Dopo l’approvazione, lo stato viene modificato da **XML in sospeso** a **XML Attivo**.

## Considerazioni

- L'ordine in cui sono elencate più regole influisce sulla modalità di gestione. Una regola successiva potrebbe seguire l’override delle azioni specificate da una regola precedente.

## Vedere anche
* [Informazioni dettagliate sulle condizioni di corrispondenza e sulle funzionalità del motore regole](cdn-rules-engine-details.md)
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Statistiche in tempo reale nella rete CDN di Microsoft Azure](cdn-real-time-stats.md)
* [Report HTTP avanzati](cdn-advanced-http-reports.md)
* [Analizzare delle prestazioni edge](cdn-edge-performance.md)


	

<!---HONumber=AcomDC_1217_2015-->