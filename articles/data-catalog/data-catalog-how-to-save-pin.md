<properties
   pageTitle="Come salvare le ricerche e aggiungere risorse di dati | Microsoft Azure"
   description="Articolo che include procedure che illustrano le funzionalità del Catalogo dati di Azure per il salvataggio delle origini dati e risorse dei dati per un utilizzo successivo."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/12/2016"
   ms.author="maroche"/>

# Come salvare le ricerche e aggiungere risorse di dati

## Introduzione

Il Catalogo dati di Microsoft Azure fornisce le funzionalità per l'individuazione delle origini dati. Gli utenti possono cercare e filtrare rapidamente il catalogo per trovare le origini dati e comprenderne le finalità previste, semplificando l'individuazione dei dati corretti per il processo specifico.

Nelle situazioni in cui gli utenti devono utilizzare regolarmente gli stessi dati o contribuiscono regolarmente alle stesse origini dati del catalogo in base alle proprie competenze, l'esecuzione ripetuta delle stesse ricerche può risultare poco efficiente. In questi casi, le ricerche salvate e le risorse di dati aggiunte possono risultare utili.

## Ricerche salvate

Una ricerca salvata del Catalogo dati di Azure è una definizione di ricerca riutilizzabile per singolo utente. Dopo che un utente ha definito una ricerca, inclusi i termini di ricerca, i tag e altri filtri, potrà salvarla per un uso successivo. La definizione della ricerca salvata può essere quindi rieseguita in un momento successivo, per restituire eventuali risorse di dati corrispondenti ai rispettivi criteri di ricerca.

### Creazione di una ricerca salvata

Per creare una ricerca salvata, immettere prima di tutto i criteri di ricerca da riutilizzare. Fare quindi clic sul collegamento "Salva" nella casella "Ricerca corrente" nel portale del Catalogo dati di Azure.

 ![Selezionare 'Salva' per salvare le impostazioni di ricerca correnti](./media/data-catalog-how-to-save-pin/01-save-option.png)

Quando richiesto, immettere un nome per la ricerca salvata. Scegliere un nome significativo e descrittivo per le risorse di dati che verranno restituite dalla ricerca.

 ![Specificare un nome per la ricerca salvata](./media/data-catalog-how-to-save-pin/02-name.png)

### Gestione delle ricerche salvate

Dopo che un utente ha salvato una o più ricerche, l'opzione "Ricerche salvate" viene visualizzata nel portale del Catalogo dati di Azure sotto la casella "Ricerca corrente". Se si espande questa opzione, verrà visualizzato l'elenco completo delle ricerche salvate.

 ![Elenco di ricerche salvate](./media/data-catalog-how-to-save-pin/03-list.png)

Se si seleziona una ricerca dall'elenco, la ricerca verrà eseguita.

Se si seleziona il menu a discesa verranno visualizzate alcune opzioni di gestione:

 ![Opzioni per la gestione delle ricerche salvate](./media/data-catalog-how-to-save-pin/04-managing.png)

Se si seleziona "Rinomina", verrà richiesta all'utente l'immissione di un nuovo nome per la ricerca salvata. La definizione della ricerca non verrà modificata.

Se si seleziona "Elimina", verrà richiesta la conferma dell'utente e la ricerca salvata verrà quindi rimossa dall'elenco dell'utente.

Se si seleziona "Salva come predefinito", la ricerca salvata scelta verrà contrassegnata come ricerca predefinita per l'utente. Se l'utente esegue una ricerca "vuota" dalla home page del Catalogo dati di Azure, verrà eseguita la ricerca predefinita dell'utente. La ricerca contrassegnata come predefinita verrà inoltre visualizzata nella parte superiore dell'elenco di ricerche salvate.

## Risorse di dati aggiunte

Le ricerche salvate consentono agli utenti di salvare e riutilizzare le definizioni di ricerca. Le risorse di dati restituiti dalle ricerche potrebbero cambiare nel tempo, in base alle modifiche dei contenuti del catalogo. L'aggiunta di risorse di dati consente agli utenti di identificare esplicitamente risorse di dati specifiche per semplificarne l'accesso senza dovere usare una ricerca.

L'aggiunta di una risorsa di dati è molto semplice. Gli utenti possono semplicemente fare clic sull'icona "aggiungi" per la risorsa di dati per aggiungerla all'elenco di risorse aggiunte. Questa icona viene visualizzata nell'angolo del riquadro della risorsa nella visualizzazione affiancata e nella colonna all'estrema sinistra della visualizzazione elenco nel portale del Catalogo dati di Azure.

![Aggiunta di una risorsa di dati](./media/data-catalog-how-to-save-pin/05-pinning.png)

Anche la rimozione di una risorsa è molto semplice. È sufficiente fare di nuovo clic sull'icona "aggiungi" per attivare o disattivare l'impostazione per la risorsa selezionata.

![Rimozione di una risorsa di dati](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## "Risorse personali"
La home page del portale del Catalogo dati di Azure include una sezione "Risorse personali" che visualizza le risorse di interesse dell'utente corrente. Questa sezione include sia le risorse aggiunte che le ricerche salvate.

!['Risorse personali' nella home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## Riepilogo
Il Catalogo dati di Azure offre funzionalità che semplificano l'individuazione delle origini dati necessarie da parte degli utenti, in modo che sia necessario dedicare meno tempo alla ricerca dei dati e concentrarsi invece sull'uso dei dati. Le ricerche salvate e le risorse di dati aggiunte si basano su queste funzionalità di base, in modo che gli utenti possano identificare con facilità le origini dati con sui lavorano ripetutamente.

<!---HONumber=AcomDC_0713_2016-->