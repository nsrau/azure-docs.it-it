<properties
   pageTitle="Microsoft Power BI Embedded - Connessione a un'origine dati"
   description="Microsoft Power BI Embedded - Connettersi a origini dati"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="mblythe"/>  

# Connettersi a un'origine dati

Con **Power BI Embedded** è possibile incorporare report in proprie app. Quando un report di Power BI viene incorporato in un'app, il report si connette ai dati sottostanti **importando** una copia dei dati o **connettendosi direttamente** all'origine dati tramite **DirectQuery**.

Di seguito sono spiegate le differenze tra l'**importazione** e la modalità **DirectQuery**.

|Importazione | DirectQuery
|---|---
|Vengono importati o copiati nel set di dati del report tabelle, colonne *e dati* Per visualizzare le modifiche apportate ai dati sottostanti, è necessario aggiornare o importare nuovamente un set di dati completo e corrente.|Vengono importate o copiate nel set di dati del report solo *tabelle e colonne*. Vengono visualizzati sempre i dati più recenti.
Con Power BI Embedded è attualmente possibile usare DirectQuery con le origini dati cloud ma non su origini dati locali.

## Vantaggi di DirectQuery

L'uso di **DirectQuery** presenta due vantaggi principali:

   -	**DirectQuery** consente di creare visualizzazioni su set di dati molto grandi. In caso contrario, sarebbe impossibile importare tutti i dati in una sola volta.
   -	Le modifiche ai dati sottostanti possono richiedere un aggiornamento dei dati. Nel caso di alcuni report, per visualizzare i dati correnti, può essere quindi necessario trasferire molti dati, rendendo la re-importazione dei dati impossibile. I report **DirectQuery** usano invece sempre dati correnti.

## Limitazioni di DirectQuery

   L'uso di **DirectQuery** presenta alcune limitazioni:

   -	Tutte le tabelle devono provenire da un database singolo.
   -	Se la query è troppo complessa, si verificherà un errore. Per correggere l'errore è necessario eseguire il refactoring della query per renderla meno complessa. Se la query deve essere necessariamente complessa, i dati saranno importati e non sarà possibile usare la modalità **DirectQuery**.
   -	Il filtro di relazione è limitato a un'unica direzione, piuttosto che ad entrambe le direzioni.
   -	Non è possibile modificare il tipo di dati di una colonna.
   -	Per impostazione predefinita, alle espressioni DAX valide per le misure sono imposte limitazioni. Vedere [DirectQuery e misure](#measures).

<a name="measures"/>  
## DirectQuery e misure

Per garantire che le prestazioni delle query inviate all'origine dati sottostante siano accettabili, vengono imposte limitazioni alle misure. In **Power BI Desktop** gli utenti esperti possono scegliere di ignorare questa limitazione selezionando **File > Opzioni e impostazioni > Opzioni**. Scegliere **DirectQuery** e selezionare l'opzione **Consenti misure senza limitazioni in modalità DirectQuery** dalla finestra di dialogo **Opzioni**. Se tale opzione viene selezionata, è possibile usare qualsiasi espressione DAX valida per una misura. È tuttavia necessario sapere che alcune espressioni che funzionano molto bene quando i dati sono importati possono invece rallentare considerevolmente le query all'origine back-end se si usa la modalità **DirectQuery**.

## Vedere anche
- [Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

<!---HONumber=AcomDC_0907_2016-->