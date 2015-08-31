<properties
	pageTitle="Componente aggiuntivo Excel per i servizi web Machine Learning | Microsoft Azure"
	description="Come accedere ai servizi web di Azure Machine Learning direttamente in Excel senza scrivere alcun codice."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/10/2015"
	ms.author="tedway;garye" />

# Componente aggiuntivo Excel per i servizi web di Azure Machine Learning

Excel consente di chiamare servizi web direttamente senza dover scrivere alcun codice.

## Procedura per utilizzare un servizio web esistente

1. Aprire il file di Excel di esempio qui o dalla scheda **DASHBOARD** dei servizi web.
2. Scegliere un servizio web facendo clic su di esso-"Titanic superstite predittive (esempio di componente aggiuntivo Excel) [punteggio]" in questo esempio.

    ![Selezionare il servizio web][01]

3. Verrà visualizzata la sezione **Stima**. Selezionare una cella in Excel e fare clic su **Usa dati di esempio**. Se lo si desidera è possibile modificare i dati in Excel.

	![Sezione stima][02]

4. Evidenziare i dati e fare clic sull'icona dell’intervallo dei dati di input.
5. In **Output**, immettere il numero di celle in cui si desidera inserire l'output.
6. Fare clic su **Stima**.

## Procedura per aggiungere un nuovo servizio web

1. Pubblicare un servizio web (in [questa pagina](machine-learning-walkthrough-5-publish-web-service.md) viene illustrato come eseguire questa operazione) o trovare un servizio web esistente.
2. Dopo aver creato un servizio Web, fare clic sulla sezione **SERVIZI WEB** sulla sinistra di Machine Learning Studio e quindi selezionare il servizio Web da utilizzare.
3. Dalla scheda **DASHBOARD** per il servizio web, copiare la chiave dell'API per il servizio web.
4. In Excel andare alla sezione **Servizi Web** (se si è nella sezione **Stima** fare clic sulla freccia indietro per andare alla lista dei servizi web).

	![Sezione servizi Web][03]

5. Fare clic su **Aggiungi servizio Web**.
6. Incollare la chiave nella casella di testo **Chiave API**.
7. Nella scheda **DASHBOARD** per il servizio web, fare clic sul collegamento**Pagina di assistenza API**.
8. Copiare l'URI di richiesta in **Richiesta** e incollarlo nella casella di testo **URL**.
9. Fare clic su **Aggiungi**.

	![URL e chiave API][04]

10.	Per utilizzare il servizio web, seguire le istruzioni sopra riportate, "Procedura per utilizzare un servizio Web esistente".

## Condivisione della cartella di lavoro

Se si salva la cartella di lavoro, verranno salvate anche le chiavi API per i servizi web che sono stati aggiunti. Pertanto, è necessario condividere la cartella di lavoro solo con persone attendibili.


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->