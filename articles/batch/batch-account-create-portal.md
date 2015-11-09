<properties
	pageTitle="Creare un account Azure Batch | Microsoft Azure"
	description="Informazioni su come creare un account Azure Batch nel portale di anteprima di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud"
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



# Creare e gestire un account Azure Batch nel portale di anteprima di Azure

Questo articolo illustra come usare il [portale di anteprima di Azure](https://portal.azure.com) per creare e gestire un account Azure Batch e le impostazioni, ad esempio le chiavi dell'account. Per autenticare tutte le richieste API Batch, sono necessari un URL dell'account Batch e una chiave di accesso associata. Tutte le risorse Batch (ad esempio, pool, processi e attività) per il carico di lavoro Batch vengono associate con un account Batch specifico.

>[AZURE.NOTE]Il portale di anteprima attualmente supporta funzionalità per la gestione dell'account Batch e la visualizzazione di alcune risorse dell'account. Le funzionalità Batch complete sono disponibili per gli sviluppatori nelle API Batch.

## Creare un account Batch

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

2. Fare clic su **Nuovo** > **Calcolo** > **Servizio Batch**.

	![Batch in Marketplace][marketplace_portal]

3. Verificare le informazioni e quindi fare clic su **Crea**.

4. Nel pannello **Nuovo account Batch** immettere le informazioni seguenti:

	a. In **Nome account** immettere un nome univoco da usare nell'URL dell'account Batch.

	>[AZURE.NOTE]Il nome dell'account Batch deve essere univoco in Azure e contenere tra 3 e 24 caratteri (sono ammessi solo numeri e lettere minuscole).

	b. Se sono presenti più sottoscrizioni, fare clic su **Sottoscrizione** per selezionare una sottoscrizione disponibile in cui verrà creato l'account.

	c. Fare clic su **Gruppo di risorse** per selezionare un gruppo di risorse esistenti per l'account o crearne uno nuovo.

	d. In **Località** selezionare un'area di Azure in cui Batch è disponibile.

	![Creare un account Batch][account_portal]

5. Fare clic su **Crea** per completare la creazione dell'account.

## Gestire le chiavi di accesso e le impostazioni dell'account
Dopo aver creato l'account, sarà possibile trovare nel portale di gestione le chiavi di accesso, gli utenti autorizzati e altre impostazioni.

L'URL dell'account Batch viene visualizzato in **Informazioni di base** con il formato `https://<account_name>.<region>.batch.azure.com`.

Per visualizzare e gestire le chiavi di accesso, fare clic sull'icona a forma di chiave.

![Chiavi di account Batch][account_keys]

## Altre informazioni importanti sull'account Batch

* Per creare e gestire gli account Batch, è anche possibile usare i [cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md) e la [libreria di gestione .NET per Batch](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/).


* Azure non addebita alcun costo per l'account Batch. Viene addebitato solo l'uso delle risorse di calcolo di Azure e di altri servizi quando i carichi di lavoro vengono eseguiti (vedere [Prezzi di Batch](https://azure.microsoft.com/pricing/details/batch/)).

* È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch in aree di Azure diverse.

* Se si eseguono diversi carichi di lavoro Batch su larga scala, tenere presente che alla sottoscrizione di Azure e a ogni account Batch vengono applicati determinati [limiti e quote per il servizio Batch](batch-quota-limit.md). Le quote correnti per un account Batch vengono visualizzate nel portale di anteprima nelle proprietà dell'account.

## Passaggi successivi

* Per altre informazioni su Batch, vedere [Nozioni di base delle API per Azure Batch](batch-api-basics.md).

* Introduzione allo sviluppo della prima applicazione con la [libreria client .NET per Batch](batch-dotnet-get-started.md).

[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=Nov15_HO1-->