<properties 
	pageTitle="Richiedere un aumento dei limiti di account DocumentDB | Microsoft Azure" 
	description="Informazioni su come richiedere una modifica ai limiti di DocumentDB, ad esempio il numero di raccolte consentite, di stored procedure e di clausole di query." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="stbaro"/>

# Richiedere un aumento dei limiti di account DocumentDB

[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) ha un set di limiti predefiniti e di applicazioni delle quote. Diverse quote possono essere modificate contattando il supporto tecnico Azure. Questo articolo spiega come richiedere un aumento del limite di account.

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
|Numero massimo di UDF per ogni query |1
|Numero massimo di JOIN per ogni query |2
|Numero massimo di clausole AND per ogni query |5
|Numero massimo di clausole OR per ogni query |5
|Numero massimo di valori per ogni espressione IN |100
|Numero massimo di raccolte create al minuto |5
|Numero massimo di operazioni di scalabilità al minuto |5

##<a id="RequestQuotaIncrease"></a> Richiedere una modifica delle quote
I passaggi seguenti mostrano come richiedere una modifica delle quote.

1. Nel [portale di anteprima di Azure](https://portal.azure.com) fare clic su **Sfoglia** e quindi su **Guida e supporto**.

	![Schermata di avvio di Guida e supporto](media/documentdb-increase-limits/helpsupport.png)

2. Nel pannello **Guida e supporto** fare clic su **Supporto tecnico**.

	![Schermata di creazione di un ticket di supporto](media/documentdb-increase-limits/getsupport.png)

3. Nel pannello **Nuova richiesta di assistenza** fare clic su **Tipo richiesta** e quindi nel pannello **Tipo richiesta** fare clic su **Quote**.

	![Schermata del tipo di richiesta del ticket di supporto](media/documentdb-increase-limits/supportrequest1.png)

4. Nel pannello **Sottoscrizione** scegliere la sottoscrizione che ospita l'account DocumentDB.

	![Schermata di selezione della sottoscrizione del ticket di supporto](media/documentdb-increase-limits/supportrequest2.png)

5. Nel pannello **Risorse** scegliere **Account DocumentDB**.

	![Schermata di selezione delle risorse del ticket di supporto](media/documentdb-increase-limits/supportrequest3.png)

6. Nel pannello **Piano per il servizio di assistenza** scegliere **Supporto gratuito quote**.

	![Schermata di selezione del piano di assistenza del ticket di supporto](media/documentdb-increase-limits/supportrequest4.png)

7. Nel pannello **Problema** scegliere la categoria problema **Richieste di aumento quote o memoria centrale - DocumentDB**.

	![Schermata di selezione della categoria problema del ticket di supporto](media/documentdb-increase-limits/supportrequest5.png)

8. Nel pannello **Descrizione** immettere una descrizione della richiesta. Assicurarsi di includere le modifiche delle quote specifiche oggetto della richiesta, oltre agli account a cui apportare le modifiche.

	![Schermata della casella di testo per la descrizione del ticket di supporto](media/documentdb-increase-limits/supportrequest6.png)

9. Fare clic su **Crea**.

	![Schermata del pulsante Crea del ticket di supporto](media/documentdb-increase-limits/supportrequest7.png)

Una volta creato il ticket di supporto, si dovrebbe ricevere per posta elettronica il numero della richiesta di supporto. Per visualizzare la richiesta di supporto, si può anche fare clic su **Richieste di supporto** nel pannello **Guida e supporto**.

![Schermata del pannello Richieste di supporto](media/documentdb-increase-limits/supportrequest8.png)
  

##<a name="NextSteps"></a> Passaggi successivi
- Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).
 

<!---HONumber=August15_HO6-->