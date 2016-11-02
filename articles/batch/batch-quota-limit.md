<properties
	pageTitle="Quote e limiti del servizio Batch | Microsoft Azure"
	description="Informazioni sui vincoli, limiti e quote di Azure Batch predefiniti e su come richiedere incrementi di quota"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2016"
	ms.author="marsma"/>

# Quote e limiti per il servizio Azure Batch

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate al servizio Batch. Molti di questi limiti sono quote predefinite applicate da Azure a livello di account o di sottoscrizione. Questo articolo illustra i valori predefiniti e come è possibile richiedere aumenti di quota.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, è possibile aprire una [richiesta di assistenza clienti](#increase-a-quota) online gratuitamente.

>[AZURE.NOTE] Una quota è un limite di credito, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.

## Quote di sottoscrizione
**Risorsa**|**Limite predefinito**|**Limite massimo**
---|---|---
Account Batch per area per sottoscrizione | 1 | 50

## Quote di account Batch
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Altri limiti
**Risorsa**|**Limite massimo**
---|---
[Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo | 4 x numero di core del nodo
[Applicazioni](batch-application-packages.md) per account Batch | 20
Pacchetti dell'applicazione per applicazione | 40
Dimensioni del pacchetto dell'applicazione (ciascuno) | Circa 195 GB<sup>1</sup>

<sup>1</sup> Limite di archiviazione di Azure per le dimensioni massime del BLOB in blocchi

## Visualizzare le quote Batch

Visualizzare le quote dell'account Batch nel [portale di Azure][portal].

1. Selezionare **Account Batch** nel portale, quindi selezionare l'account Batch di interesse.

2. Selezionare **Proprietà** nel pannello del menu Account Batch

3. Il pannello **Proprietà** mostra le quote attualmente applicate all'account Batch

	![Quote di account Batch][account_quotas]

## Aumentare una quota

Per richiedere un aumento di quota mediante il [portale di Azure][portal], eseguire i passaggi riportati di seguito.

1. Selezionare il riquadro **Guida e supporto** nel dashboard del portale o il punto interrogativo (**?**) nell'angolo superiore destro del portale.

2. Selezionare **Nuova richiesta di supporto** > **Informazioni di base**.

3. Nel pannello **Informazioni di base**:

	a. **Tipo di problema** > **Quota**

	b. Selezionare la propria sottoscrizione.

	c. **Tipo di quota** > **Batch**

	d. **Piano di supporto** > **Supporto quota - compreso**

	Fare clic su **Avanti**.

4. Nel pannello **Problema**:

	a. Selezionare una **Gravità** in base all'impatto sull'[attività aziendale][support_sev].

	b. In **Dettagli** specificare ogni quota che si desidera modificare, il nome dell'account Batch e il nuovo limite.

	Fare clic su **Avanti**.

5. Nel pannello **Informazioni di contatto**:

	a. Selezionare il **metodo di contatto preferito**.

	b. Verificare e immettere i dettagli di contatto richiesti.

	Fare clic su **Crea** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Si noti che il completamento della richiesta può richiedere fino a 2 giorni lavorativi.

## Argomenti correlati

* [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md)

* [Cenni preliminari sulla funzionalità Azure Batch](batch-api-basics.md)

* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0914_2016-->