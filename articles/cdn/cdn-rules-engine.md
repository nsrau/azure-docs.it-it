<properties
	pageTitle="Override del comportamento HTTP predefinito nella rete CDN di Azure con il motore regole | Microsoft Azure"
	description="Il motore regole consente di personalizzare la modalità con cui vengono gestite le richieste HTTP nella rete CDN di Azure, ad esempio la distribuzione di determinati tipi di contenuto, la definizione di criteri di memorizzazione nella cache e la modifica delle intestazioni HTTP."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Override del comportamento HTTP predefinito mediante il motore regole

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## Panoramica

Il motore regole consente di personalizzare la modalità con cui vengono gestite le richieste HTTP, come ad esempio il blocco della distribuzione di determinati tipi di contenuto, la definizione di un criterio di memorizzazione nella cache e la modifica della intestazioni HTTP. Questa esercitazione illustra la creazione di una regola che modifica il comportamento di memorizzazione nella cache degli asset della rete CDN. Nella sezione "[Vedere anche](#see-also)" sono disponibili anche contenuti video.

## Esercitazione

1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci**.

	![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

	Si aprirà il portale di gestione della rete CDN.

2. Fare clic sulla scheda **HTTP Grande**, seguita da **Motore regole**.

	Vengono visualizzate le opzioni per una nuova regola.

	![Opzioni delle regole della nuova rete CDN](./media/cdn-rules-engine/cdn-new-rule.png)

	>[AZURE.IMPORTANT] L'ordine in cui sono elencate più regole influisce sulla modalità di gestione. Una regola successiva potrebbe seguire l’override delle azioni specificate da una regola precedente.
	
3. Inserire un nome per la casella di testo **Nome / Descrizione**.

4. Identificare il tipo di richieste che a cui verrà applicata la regola. Per impostazione predefinita, viene selezionata la condizione corrispondente **Sempre**. Si utilizzerà **Sempre** per questa esercitazione, quindi lasciarla selezionata.

	![Condizione di corrispondenza della rete CDN](./media/cdn-rules-engine/cdn-request-type.png)

	>[AZURE.TIP] Vi sono molti tipi di condizioni di corrispondenza disponibili nell'elenco a discesa. Facendo clic sull'icona blu delle informazioni a sinistra della condizione di corrispondenza viene illustrata la condizione selezionata in modo dettagliato.
	>
	>Per l'elenco completo delle condizioni di corrispondenza illustrate in modo dettagliato, vedere [Condizione di corrispondenza del motore regole e Informazioni sulle funzionalità](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0).

5.  Fare clic sul pulsante **+** accanto a **Funzionalità** per aggiungere una nuova funzionalità. Nell'elenco a discesa a sinistra, selezionare **Forza interna Max-Age**. Nella casella di testo che viene visualizzata, inserire **300**. Lasciare i valori predefiniti restanti.

	![Funzionalità della rete CDN](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE] Come con le condizioni di corrispondenza, fare clic sull'icona blu delle informazioni a sinistra della nuova funzionalità per visualizzare i dettagli su questa funzionalità. Nel caso di **Forza interna Max-Age**, è stato eseguito l'override dell'asset **Cache-Control** e delle intestazioni **Scadenze** per controllare quando il nodo edge della rete CDN aggiornerà l'asset dall'origine. L’esempio di 300 secondi indica che il nodo edge della rete CDN memorizza l’asset nella cache per 5 minuti prima di aggiornare la risorsa dall'origine.
	>
	>Per l'elenco completo delle funzionalità illustrate in modo dettagliato, vedere [Condizione di corrispondenza del motore regole e Informazioni sulle funzionalità](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1).

6.  Fare clic sul pulsante **Aggiungi** per salvare la nuova regola. La nuova regola ora è in attesa di approvazione. Dopo l’approvazione, lo stato viene modificato da **XML in sospeso** a **XML Attivo**.

	>[AZURE.IMPORTANT] La propagazione delle modifiche delle regole in tutta la rete CDN può durare fino a 90 minuti.

## Vedere anche
* [Vedere il video relativo alle nuove potenti funzionalità Premium della rete CDN di Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/)
* [Informazioni dettagliate sulle condizioni di corrispondenza e sulle funzionalità del motore regole](https://msdn.microsoft.com/library/mt757336.aspx)

<!---HONumber=AcomDC_0803_2016-->