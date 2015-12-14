<properties
	pageTitle="Richiedere un aumento dei limiti di account DocumentDB | Microsoft Azure"
	description="Informazioni su come richiedere una modifica ai limiti di DocumentDB, ad esempio il numero di raccolte consentite, di stored procedure e di clausole di query."
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/22/2015"
	ms.author="anhoh"/>

# Richiedere un aumento dei limiti di account DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) ha un set di limiti predefiniti e di applicazioni delle quote. Diverse quote possono essere modificate contattando il supporto tecnico Azure. Questo articolo spiega come richiedere un aumento del limite di account.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

-	Quali quote dell'account DocumentDB possono essere modificate contattando il supporto tecnico Azure?
-	Come è possibile richiedere una modifica delle quote dell'account DocumentDB?

##<a id="AdjustableQuotas"></a> Quote dell'account DocumentDB modificabili

La tabella seguente descrive le quote di DocumentDB che è possibile modificare contattando il supporto tecnico di Azure:

|Entità |Quota (offerta standard)|
|-------|--------|
|Account di database |5
|Numero di stored procedure, trigger e UDF per ogni raccolta |25 ciascuna
|Numero massimo di raccolte per ogni account di database |100
|Dimensione massima dell'archivio documenti per ogni database (100 raccolte) |1 TB
|Numero massimo di UDF per ogni query |2
|Numero massimo di JOIN per ogni query |5
|Numero massimo di clausole AND per ogni query |20
|Numero massimo di clausole OR per ogni query |10
|Numero massimo di valori per ogni espressione IN |100
|Numero massimo di punti in un argomento del poligono in una query ST\_WITHIN |16
|Numero massimo di raccolte create al minuto |5
|Numero massimo di operazioni di scalabilità al minuto |5

##<a id="RequestQuotaIncrease"></a> Richiedere una modifica delle quote
I passaggi seguenti mostrano come richiedere una modifica delle quote.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Sfoglia** e quindi su **Guida e supporto**.

	![Schermata di avvio di Guida e supporto](media/documentdb-increase-limits/helpsupport.png)

2. Nel pannello **Guida e supporto** fare clic su **Supporto tecnico**.

	![Schermata di creazione di un ticket di supporto](media/documentdb-increase-limits/getsupport.png)

3. Nel pannello **Nuova richiesta di assistenza**, fare clic su **Nozioni di base**. Successivamente, impostare **Tipo di rilascio** su **Quota**, **Sottoscrizione** su sottoscrizione che ospita l’account DocumentDB, **Servizio** su **DocumentDB**, e **Piano di supporto** su **SUPPORTO Quota - compreso**. Infine, fare clic su **Next**.

	![Schermata del tipo di richiesta del ticket di supporto](media/documentdb-increase-limits/supportrequest1.png)

4. Nel pannello **Problema**, scegliere un livello di gravità. Impostare **Tipo di problema** su **DocumentDB** e inserire informazioni sull'aumento della quota in **Dettagli**. Fare clic su **Avanti**.

	![Schermata di selezione della sottoscrizione del ticket di supporto](media/documentdb-increase-limits/supportrequest2.png)

5. Infine, inserire le informazioni di contatto nel pannello **Informazioni di contatto**.

	![Schermata di selezione delle risorse del ticket di supporto](media/documentdb-increase-limits/supportrequest3.png)

Una volta creato il ticket di supporto, si dovrebbe ricevere per posta elettronica il numero della richiesta di supporto. È inoltre possibile visualizzare la richiesta di supporto facendo clic su **Gestire le richieste di supporto** nel pannello **Guida + supporto**.

![Schermata del pannello Richieste di supporto](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a> Passaggi successivi
- Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).

<!---HONumber=AcomDC_1203_2015-->