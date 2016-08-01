<properties
	pageTitle="Componente aggiuntivo Excel per i servizi Web di Machine Learning | Microsoft Azure"
	description="Come usare i servizi Web di Azure Machine Learning direttamente in Excel senza scrivere alcun codice."
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
	ms.date="07/06/2016"
	ms.author="tedway;garye" />

# Componente aggiuntivo Excel per i servizi Web di Azure Machine Learning

Excel consente di chiamare servizi Web direttamente senza dover scrivere alcun codice.

## Procedura per usare un servizio Web esistente nella cartella di lavoro

1. Aprire il [file di Excel di esempio](http://aka.ms/amlexcel-sample-2) che contiene il componente aggiuntivo Excel e i dati relativi ai passeggeri sul Titanic.
2. Scegliere un servizio Web facendo clic su di esso: in questo esempio "Stime sopravvissuti Titanic (esempio componente aggiuntivo Excel) [Score]".

    ![Selezionare il servizio Web][01]

3. Viene visualizzata la sezione **Stima**. Questa cartella di lavoro contiene già dati di esempio, ma per una cartella di lavoro vuota è anche possibile selezionare una cella in Excel e fare clic su **Use sample data** (Usa dati di esempio).
4. Selezionare i dati con intestazioni e fare clic sull'icona dell'intervallo dei dati di input. Assicurarsi che sia selezionata la casella "Dati con intestazioni".
5. In **Output** immettere il numero di celle in cui si desidera inserire l'output, ad esempio "H1" di seguito.
6. Fare clic su **Stima**.

	![Sezione Stima][02]

## Procedura per aggiungere un nuovo servizio Web

Pubblicare un servizio Web (in [questa pagina](machine-learning-walkthrough-5-publish-web-service.md) viene illustrato come eseguire questa operazione) o usare un servizio Web esistente.

Ottenere la chiave API per il servizio Web. Il luogo da cui ottenere la chiave varia a seconda che sia stato pubblicato un servizio Web classico o un nuovo servizio Web.

**Servizio Web classico**

1. In Machine Learning Studio fare clic sulla sezione **SERVIZI WEB** sulla sinistra e quindi selezionare il servizio Web da usare.

	![Selezione di Servizio Web in Studio][04]

2. Copiare la chiave dell'API per il servizio Web.

	![Chiave API in Studio][05]

3. Nella scheda **DASHBOARD** per il servizio Web fare clic sul collegamento **RICHIESTA/RISPOSTA**.
4. Cercare la sezione **Request URI** (Richiedi URI). Copiare e salvare l'URL.

**Nuovo servizio Web**

1. Nel portale Microsoft Azure Machine Learning Web Services (Servizi Web Microsoft Azure Machine Learning), fare clic su **Web Services** (Servizi Web), quindi selezionare il servizio Web scelto.
2. Fare clic su **Consume** (Uso).
3. Cercare la sezione **Basic consumption info** (Informazioni di base sull'uso). Copiare e salvare **Chiave primaria** e l'URL di **Richiesta-risposta**.


## Procedura per aggiungere un nuovo servizio Web

1. Pubblicare un servizio Web (in [questa pagina](machine-learning-walkthrough-5-publish-web-service.md) viene illustrato come eseguire questa operazione) o usare un servizio Web esistente.
2. In Excel andare alla sezione **Servizi Web** (se si è nella sezione **Stima**, fare clic sulla freccia indietro per andare all'elenco dei servizi Web).

	![Passare alla selezione Servizio Web][03]
3. Fare clic su **Aggiungi servizio Web**.
4. Incollare l'URL nella casella di testo **URL** del componente aggiuntivo Excel.
5. Incollare la chiave API/primaria nella casella di testo **Chiave API**.
6. Fare clic su **Aggiungi**.

	![URL e chiave API per un servizio Web classico.][06]

10.	Per utilizzare il servizio web, seguire le istruzioni sopra riportate, "Procedura per utilizzare un servizio Web esistente".

## Condivisione della cartella di lavoro

Se si salva la cartella di lavoro, verrà salvata anche la chiave API/primaria per i servizi Web che sono stati aggiunti. Pertanto, è necessario condividere la cartella di lavoro solo con persone attendibili.

È possibile porre domande di seguito o sul [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png

<!---HONumber=AcomDC_0720_2016-->