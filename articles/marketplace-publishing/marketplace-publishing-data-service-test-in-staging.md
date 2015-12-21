<properties
   pageTitle="Test dell'offerta del servizio dati per il Marketplace | Microsoft Azure"
   description="Informazioni su come testare l'offerta del servizio dati per Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/03/2015"
   ms.author="hascipio; avikova" />

# Test dell'offerta del servizio dati in gestione temporanea
Dopo aver completato i primi due passaggi della [creazione di un account per il Dashboard venditori](marketplace-publishing-accounts-creation-registration.md) e della [creazione dell'offerta del servizio dati nel portale di pubblicazione](marketplace-publishing-data-service-creation.md), si è pronti per rendere disponibile l'offerta in Azure Marketplace. Questo argomento illustra il primo passaggio intermedio denominato "Gestione temporanea"

Per gestione temporanea si intende la distribuzione dell'offerta in un ambiente "sandbox" privato, in cui è possibile testarne e verificarne le funzionalità prima di eseguirne il push in produzione. L'offerta verrà visualizzata nella gestione temporanea esattamente come verrebbe mostrata a un cliente che l'ha distribuita.

## Passaggio 1. Push dell'offerta nella gestione temporanea
Il push dell'offerta nella gestione temporanea consente di testare l'offerta prima che diventi disponibile ai sottoscrittori futuri. È possibile visualizzare come l'offerta verrà visualizzata e funzionerà per coloro che effettuano la sottoscrizione ai dati.

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.	Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2.	Selezionare **Servizi dati** nella finestra di navigazione a sinistra
3.	Selezionare l'offerta di cui si desidera effettuare il push in gestione temporanea. Verrà visualizzata la schermata precedente.
4.	Fare clic sul pulsante **Push gestione temporanea**.  
5.	Se sono presenti problemi con l'offerta che devono essere risolti prima del push nella gestione temporanea, verrà visualizzato un elenco. Correggere questi elementi facendo clic su ogni elemento nell'elenco. Quando tutte le correzioni sono state apportate, fare clic nuovamente sul pulsante **Push gestione temporanea**.

Se non sono presenti problemi con l'offerta, si noterà la finestra popup di seguito.

Se non si prevede/non si è stati approvati per la presentazione dell'offerta nel portale di Azure (attualmente dispone di capacità limitate), chiudere semplicemente la finestra popup.

Per testare il servizio dati nel portale di Azure (oltre al portale di DataMarket), è necessario un ID di sottoscrizione di Azure con cui eseguire il test. Questo ID di sottoscrizione identificherà l'account consentito per testare la l’offerta.

Tagliare e incollare l'ID di sottoscrizione e fare clic sul segno di spunta per continuare.

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE]Questi ID di sottoscrizione di Azure sono necessari per il testing e la gestione temporanea nel [portale di gestione di Azure](https://manage.windowsazure.com). Non sono necessari per eseguire il test in Azure Marketplace.

La schermata successiva visualizzata mostra che la pubblicazione ha luogo visualizzando l'icona "In corso" evidenziata in giallo di seguito. Il push in gestione temporanea richiede tra 10 e 15 minuti. Se richiede più tempo, prima di aggiornare il browser (premere F5 in Internet Explorer). In rari casi in cui l'offerta è ancora in fase di push in gestione temporanea dopo un'ora, fare clic sul collegamento dei contatti per comunicarci che è presente un problema.

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Quando il push in gestione temporanea viene completato, l'icona "In corso" smette di muoversi e lo stato viene aggiornato in "Gestione temporanea". È ora possibile testare l'offerta.

## Passaggio 2. Testare l'offerta in gestione temporanea in DataMarket

Fare clic sul collegamento che segue il testo **"Visualizza l’offerta di servizio in..."** per visualizzare la schermata che il sottoscrittore potrà vedere quando l'offerta passerà alla produzione e verrà visualizzata nel DataMarket.

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Testare o verificare ognuno dei 12 elementi contrassegnati in precedenza affinché tutti i logo, prezzi/transazioni, testi, immagini, documentazione e collegamenti siano corretti e funzionino in modo appropriato. Questo è il momento opportuno per assicurarsi che i valori di test specificati durante la creazione dell'offerta siano stati sostituiti con valori effettivi.

1. Logo offerta
2. Nome offerta
3. Nome server di pubblicazione/collegamento al sito Web della società
4. Categorie di ricerca per l'offerta
5. Collegamento di supporto dell'offerta per agevolare i sottoscrittori
6. Descrizione contestuale dell'offerta
7. Piano dell’offerta che descrive i dettagli di fatturazione
8. Collegamento al codice di implementazione
9. Immagini di esempio che illustrano l'utilizzo dei dati dell’offerta
10. Metadati di input/output per ogni servizio all'interno dell'offerta
11. Condizioni di utilizzo dell'offerta
12. Anteprima dei dati dell'offerta


Infine, controllare che il servizio funzioni nel Datamarket facendo clic sul collegamento "ESPLORA QUESTO SET DI DATI". Verrà aperta una nuova finestra dello strumento chiamiamo "Service Explorer", in modo che sia possibile visualizzare in anteprima i risultati di una query sul servizio. In questa finestra, è possibile immettere i parametri necessari e vedere i risultati visualizzati da una query sul servizio. Inoltre, viene visualizzato l'URL per la Query.

> [AZURE.NOTE]Assicurarsi di esaminare la descrizione testuale del servizio visualizzato nella parte superiore. Se l'offerta è costituita da più di un servizio, fare clic sulle schede nella parte inferiore per passare al servizio successivo da esaminare e testare.



## Passaggio successivo
Se si riscontrano problemi e serve assistenza per risolverli, contattare il [Supporto di pubblicazione di Azure](http://go.microsoft.com/fwlink/?LinkId=272975).

Se si è soddisfatti e pronti per la pubblicazione dell'offerta, leggere la documentazione [Richiedere l’approvazione per il push in produzione](marketplace-publishing-push-to-production.md).

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_1210_2015-->