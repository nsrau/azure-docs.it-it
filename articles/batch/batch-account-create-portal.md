<properties
	pageTitle="Creare un account Azure Batch | Microsoft Azure"
	description="Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud"
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
	ms.topic="get-started-article"
	ms.date="05/12/2016"
	ms.author="marsma"/>

# Creare e gestire un account Azure Batch nel portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](batch-account-create-portal.md)
- [.NET per la gestione di Batch](batch-management-dotnet.md)

Il [portale di Azure][azure_portal] fornisce gli strumenti necessari per creare e gestire un account Batch di Azure, che è possibile usare per l'elaborazione di carichi di lavoro paralleli su larga scala. In questo articolo verrà illustrata la creazione di un account Batch usando il portale, oltre ad alcune delle più importanti impostazioni e proprietà dell'account Batch. Ad esempio, per le applicazioni e i servizi sviluppati con Batch sono necessari l'URL dell'account e una chiave di accesso per comunicare con le API del servizio Batch. Entrambi sono disponibili nel portale di Azure.

>[AZURE.NOTE] Il portale di Azure attualmente supporta un subset delle funzionalità disponibili nel servizio Batch, incluse la creazione dell'account e la gestione delle impostazioni e delle proprietà dell'account. Il set completo di funzionalità di Batch, incluse la creazione e l'esecuzione di processi e attività, è disponibile per gli sviluppatori tramite le API di Batch.

## Creare un account Batch

1. Accedere al [portale di Azure][azure_portal].

2. Fare clic su **Nuovo** > **Calcolo** > **Servizio Batch**.

	![Batch in Marketplace][marketplace_portal]

3. Esaminare le informazioni nel pannello **Servizio Batch**, quindi fare clic su **Crea**. Si noti che la selezione del modello di distribuzione è disabilitata. Infatti Batch usa solo il modello di distribuzione del gruppo di risorse.

	![Pannello per la creazione del servizio Batch nel portale di Azure][3]

4. Viene visualizzato il pannello **Nuovo account Batch**. Per la descrizione di ogni elemento del pannello, vedere sotto gli elementi da *a* a *e*.

    ![Creare un account Batch][account_portal]

	a. **Nome account**: specificare un nome univoco per l'account Batch. Questo nome deve essere univoco nell'area di Azure in cui viene creato l'account (vedere sotto *Località*). Può contenere solo caratteri minuscoli e numeri e deve avere una lunghezza di 3-24 caratteri.

	b. **Sottoscrizione**: selezionare una sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

	c. **Gruppo di risorse**: selezionare un gruppo di risorse per il nuovo account Batch oppure crearne uno nuovo se nella sottoscrizione non sono presenti gruppi di risorse.

	d. **Località**: selezionare un'area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse verranno visualizzate come opzioni.

    e. **Account di archiviazione** (facoltativo): è possibile associare (collegare) un account di archiviazione **Utilizzo generico** al nuovo account Batch. La funzionalità dei [pacchetti dell'applicazione](batch-application-packages.md) di Batch userà l'account di archiviazione collegato per l'archiviazione e il recupero dei pacchetti dell'applicazione. Per altre informazioni su questa funzionalità, vedere [Distribuzione delle applicazioni con i pacchetti dell'applicazione di Azure Batch](batch-application-packages.md).

     > [AZURE.TIP] La rigenerazione delle chiavi in un account di archiviazione collegato richiede speciali considerazioni. Per altri dettagli, vedere più avanti [Considerazioni sugli account Batch](#considerations-for-batch-accounts).

5. Fare clic su **Crea** per creare l'account.

  Il portale indicherà che è in corso la **Distribuzione** dell'account e al termine verrà visualizzato il pannello Account Batch.

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

* Non vengono addebitati i costi relativi all'account Batch in sé. Vengono addebitati i costi per le risorse di calcolo di Azure utilizzate dalle soluzioni Batch e per le risorse utilizzate da altri servizi durante l'esecuzione dei carichi di lavoro. Ad esempio, vengono addebitati i costi per i nodi di calcolo nei pool e, se si usa la funzionalità dei [pacchetti dell'applicazione](batch-application-packages.md), vengono addebitati i costi per le risorse di archiviazione di Azure usate per archiviare le versioni dei pacchetti dell'applicazione. Per altre informazioni, vedere [Prezzi di Batch][batch_pricing].

* È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch in aree di Azure diverse.

* Se si eseguono diversi carichi di lavoro Batch su larga scala, tenere presente che alla sottoscrizione di Azure e a ogni account Batch vengono applicati determinati [limiti e quote per il servizio Batch](batch-quota-limit.md). Le quote correnti per un account Batch vengono visualizzate nel portale nelle proprietà dell'account.

* Se si associa (si collega) un account di archiviazione all'account Batch, prestare attenzione durante la rigenerazione delle chiavi di accesso all'account di archiviazione. È consigliabile rigenerare una sola chiave dell'account di archiviazione, fare clic su **Chiavi di sincronizzazione** nel pannello dell'account di archiviazione collegato, attendere 5 minuti per consentire alle chiavi di propagarsi ai nodi di calcolo nei pool, quindi rigenerare e sincronizzare l'altra chiave, se necessario. Se si rigenerano entrambe le chiavi contemporaneamente, i nodi di calcolo non riusciranno a sincronizzare entrambe le chiavi e perderanno l'accesso all'account di archiviazione.

  ![Rigenerazione delle chiavi degli account di archiviazione][4]

> [AZURE.IMPORTANT] Batch attualmente supporta *solo* il tipo di account di archiviazione **Utilizzo generico**, come descritto nel passaggio 5 [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). Quando si collega un account di archiviazione di Azure all'account Batch, collegare *solo* un account di archiviazione **Utilizzo generico**.

## Passaggi successivi

* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md). L'articolo descrive le risorse primarie di Batch, ad esempio i pool, i nodi di calcolo, i processi e le attività e fornisce una panoramica delle funzionalità del servizio che consentono l'esecuzione di carichi di lavoro di calcolo su larga scala.

* Informazioni sulle nozioni di base per lo sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](batch-dotnet-get-started.md). L'[articolo introduttivo](batch-dotnet-get-started.md) illustra un'applicazione funzionante che usa il servizio Batch per eseguire un carico di lavoro in più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "Pannello per la creazione del servizio Batch nel portale di Azure"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "Rigenerazione delle chiavi degli account di archiviazione"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Pannelli delle impostazioni e delle proprietà dell'account Batch"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0518_2016-->