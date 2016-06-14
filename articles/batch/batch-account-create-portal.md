<properties
	pageTitle="Creare un account Azure Batch | Microsoft Azure"
	description="Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud"
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
	ms.topic="get-started-article"
	ms.date="06/01/2016"
	ms.author="marsma"/>

# Creare e gestire un account Azure Batch nel portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](batch-account-create-portal.md)
- [.NET per la gestione di Batch](batch-management-dotnet.md)

Il [portale di Azure][azure_portal] fornisce gli strumenti necessari per creare e gestire un account Batch di Azure, che è possibile usare per l'elaborazione di carichi di lavoro paralleli su larga scala. In questo articolo verrà illustrata la creazione di un account Batch usando il portale e verranno evidenziate importanti impostazioni e proprietà dell'account Batch. Ad esempio, per le applicazioni e i servizi sviluppati con Batch sono necessari l'URL dell'account e una chiave di accesso per comunicare con le API del servizio Batch. Entrambi sono disponibili nel portale di Azure.

>[AZURE.NOTE] Il portale di Azure attualmente supporta un subset delle funzionalità del servizio Batch, che include la creazione di account, la gestione delle impostazioni e delle proprietà dell'account Batch e la creazione e il monitoraggio di pool e processi. Il set di funzionalità completo di Batch è disponibile per gli sviluppatori nelle API di Batch.

## Creare un account Batch

1. Accedere al [portale di Azure][azure_portal].

2. Fare clic su **Nuovo** > **Macchine virtuali** > **Servizio Batch**.

	![Batch in Marketplace][marketplace_portal]

3. Verrà visualizzato il pannello **Nuovo account Batch**. Per la descrizione di ogni elemento del pannello, vedere gli elementi da *a* a *e* di seguito.

    ![Creare un account Batch][account_portal]

	a. **Nome account**: nome univoco dell'account Batch. Questo nome deve essere univoco nell'area di Azure in cui viene creato l'account. Vedere *Località* di seguito. Può contenere solo caratteri minuscoli e numeri e deve avere una lunghezza di 3-24 caratteri.

	b. **Sottoscrizione**: sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

	c. **Gruppo di risorse**: gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

	d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse verranno visualizzate come opzioni.

    e. **Account di archiviazione** (facoltativo): account di archiviazione **per utilizzo generico** associato (collegato) al nuovo account Batch. La funzionalità dei [pacchetti dell'applicazione](batch-application-packages.md) di Batch userà l'account di archiviazione collegato per l'archiviazione e il recupero dei pacchetti dell'applicazione. Per altre informazioni su questa funzionalità, vedere [Distribuzione delle applicazioni con i pacchetti dell'applicazione di Azure Batch](batch-application-packages.md).

     > [AZURE.IMPORTANT] La rigenerazione delle chiavi in un account di archiviazione collegato richiede speciali considerazioni. Per altri dettagli, vedere [Considerazioni sugli account Batch](#considerations-for-batch-accounts) più avanti.

4. Fare clic su **Crea** per creare l'account.

  Il portale indicherà che è in corso la **distribuzione** dell'account e al termine verrà visualizzata la notifica **Le distribuzioni sono riuscite** in *Notifiche*.

## Visualizzare le proprietà dell'account Batch

Il pannello dell'account Batch visualizza diverse proprietà per l'account, oltre a fornire l'accesso ad altre impostazioni, ad esempio chiavi di accesso, quote, utenti e associazione dell'account di archiviazione.

* **URL dell'account Batch**: questo URL fornisce l'accesso all'account Batch quando si usano le API, ad esempio l'API [Batch REST][api_rest] o la libreria client [Batch .NET][api_net], ed è conforme al formato seguente:

    `https://<account_name>.<region>.batch.azure.com`

* **Chiavi di accesso**: per visualizzare e gestire le chiavi di accesso dell'account Batch, fare clic sull'icona della chiave per aprire il pannello **Gestisci chiavi** oppure fare clic su **Tutte le impostazioni** > **Chiavi**. Una chiave di accesso è necessaria quando si comunica con le API del servizio Batch, ad esempio con [Batch REST][api_rest] o con la libreria client [Batch .NET][api_net].

    ![Chiavi di account Batch][account_keys]

* **Tutte le impostazioni**: per gestire tutte le impostazioni per l'account Batch o per visualizzarne le proprietà, fare clic su **Tutte le impostazioni** per aprire il pannello **Impostazioni**. Questo pannello fornisce l'accesso a tutte le impostazioni e le proprietà dell'account, incluse la visualizzazione delle quote dell'account, la selezione di un account di archiviazione di Azure da collegare all'account Batch e la gestione degli utenti.

    ![Pannelli delle impostazioni e delle proprietà dell'account Batch][5]

## Considerazioni sugli account Batch

* È anche possibile creare e gestire gli account Batch con i [cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md) e la [libreria di gestione .NET per Batch](batch-management-dotnet.md).

* Non vengono effettuati addebiti per l'account Batch in sé. Vengono addebitati i costi per le risorse di calcolo di Azure utilizzate dalle soluzioni Batch e per le risorse utilizzate da altri servizi durante l'esecuzione dei carichi di lavoro. Vengono ad esempio addebitati i costi per i nodi di calcolo nei pool e, se si usa la funzionalità dei [pacchetti dell'applicazione](batch-application-packages.md), vengono addebitati i costi per le risorse di Archiviazione di Azure usate per archiviare le versioni dei pacchetti dell'applicazione. Per altre informazioni, vedere [Prezzi di Batch][batch_pricing].

* È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch in aree di Azure diverse.

* Se si eseguono diversi carichi di lavoro Batch su larga scala, tenere presente che alla sottoscrizione di Azure e a ogni account Batch vengono applicati determinati [limiti e quote per il servizio Batch](batch-quota-limit.md). Le quote correnti per un account Batch vengono visualizzate nel portale nelle proprietà dell'account.

* Se si associa (si collega) un account di archiviazione all'account Batch, prestare attenzione durante la rigenerazione delle chiavi di accesso all'account di archiviazione. È consigliabile rigenerare una sola chiave dell'account di archiviazione; fare clic su **Sincronizza chiavi** nel pannello dell'account di archiviazione collegato, attendere 5 minuti per consentire la propagazione delle chiavi ai nodi di calcolo nei pool e quindi rigenerare e sincronizzare l'altra chiave, se necessario. Se si rigenerano entrambe le chiavi contemporaneamente, i nodi di calcolo non riusciranno a sincronizzare entrambe le chiavi e perderanno l'accesso all'account di archiviazione.

  ![Rigenerazione delle chiavi degli account di archiviazione][4]

> [AZURE.IMPORTANT] Batch attualmente supporta *solo* account di archiviazione **per utilizzo generico**, come descritto nel passaggio 5 [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) dell'articolo [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). Quando si collega un account di archiviazione di Azure all'account Batch, collegare *solo* un account di archiviazione **per utilizzo generico**.

## Passaggi successivi

* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Cenni preliminari sulla funzionalità Azure Batch](batch-api-basics.md). L'articolo descrive le risorse primarie di Batch, ad esempio i pool, i nodi di calcolo, i processi e le attività e fornisce una panoramica delle funzionalità del servizio che consentono l'esecuzione di carichi di lavoro di calcolo su larga scala.

* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](batch-dotnet-get-started.md). L'[articolo introduttivo](batch-dotnet-get-started.md) illustra un'applicazione funzionante che usa il servizio Batch per eseguire un carico di lavoro in più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Rigenerazione delle chiavi degli account di archiviazione"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Pannelli delle impostazioni e delle proprietà dell'account Batch"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0608_2016-->