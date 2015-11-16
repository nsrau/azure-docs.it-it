<properties
	pageTitle="Quote e limiti del servizio Batch | Microsoft Azure"
	description="Informazioni su quote, limiti e vincoli per l'uso del servizio Azure Batch"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>



# Quote e limiti per il servizio Azure Batch

Questo articolo elenca i limiti predefiniti e massimi di alcune risorse che è possibile usare con il servizio Azure Batch. La maggior parte di questi limiti sono le quote che Azure applica alla sottoscrizione o agli account Batch.

Se si prevede di eseguire carichi di lavoro Batch di produzione, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, aprire una richiesta di assistenza clienti online gratuitamente.

>[AZURE.NOTE]Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.

## Quote di sottoscrizione
Risorsa|Limite predefinito|Limite massimo
---|---|---
Account Batch per area per sottoscrizione|1|50

## Quote di account Batch
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Altri limiti
Risorsa|Limite massimo
---|---
Attività per nodo di calcolo|4 x numero di core del nodo

## Visualizzare le quote Batch

Visualizzare le quote dell'account Batch nel [portale di anteprima di Azure](https://portal.azure.com).

1. Nel portale di anteprima fare clic su **Account Batch** e quindi sul nome dell'account Batch.

2. Nel pannello dell'account fare clic su **Impostazioni** > **Proprietà**.

	![Quote di account Batch][account_quotas]

3. Nel pannello **Proprietà** esaminare le quote attualmente applicate all'account Batch.

## Aumentare una quota

Usare i passaggi seguenti per richiedere un aumento di quota nel portale di anteprima di Azure. È possibile richiedere un aumento anche nel [portale di Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

1. Nel dashboard del portale di anteprima fare clic su **Guida e supporto**.

2. Fare clic su **Crea richiesta di supporto > Dati principali**.

3. Nel pannello **Dati principali** eseguire le operazioni seguenti:

	a. In **Tipo di problema** selezionare **Quota**.

	b. Selezionare la propria sottoscrizione.

	c. In **Servizio** selezionare **Servizio Batch**.

	d. In **Piano di supporto** selezionare **Piano di supporto di Azure - Sviluppatore**.

	Fare clic su **Avanti**.

4. Nel pannello **Problema** eseguire le operazioni seguenti:

	a. In **Tipo di problema** selezionare **Batch**.

	b. In **Dettagli** elencare la quota o le quote che si vuole modificare in un particolare account e i nuovi limiti desiderati.

	Fare clic su **Avanti**.

5. Nel pannello **Informazioni contatto** immettere i dettagli contatto e fare clic su **Avanti**.

6. Fare clic su **Crea** per inviare la nuova richiesta di supporto.

Si verrà contatti dal supporto di Azure. Il completamento della richiesta può richiedere fino a 2 giorni lavorativi.

## Argomenti correlati

* [Creare e gestire un account Azure Batch](batch-account-create-portal.md)

* [Nozioni di base delle API per Azure Batch](batch-api-basics.md)

* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=Nov15_HO2-->