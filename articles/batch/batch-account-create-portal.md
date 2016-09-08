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
	ms.date="08/26/2016"
	ms.author="marsma"/>

# Creare un account Azure Batch usando il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](batch-account-create-portal.md)
- [.NET per la gestione di Batch](batch-management-dotnet.md)

Informazioni su come creare un account Azure Batch nel [portale di Azure][azure_portal] e su dove trovare importanti proprietà dell'account, come le chiavi di accesso e gli URL degli account. Vengono anche indicati i prezzi di Batch e viene illustrato il collegamento di un account di archiviazione di Azure all'account Batch per poter usare i [pacchetti dell'applicazione](batch-application-packages.md) e [salvare in modo permanente l'output dei processi e delle attività](batch-task-output.md).

## Creare un account Batch

1. Accedere al [portale di Azure][azure_portal].

2. Fare clic su **Nuovo** > **Macchine virtuali** > **Servizio Batch**.

	![Batch in Marketplace][marketplace_portal]

3. Verrà visualizzato il pannello **Nuovo account Batch**. Per la descrizione di ogni elemento del pannello, vedere gli elementi da *a* a *e* di seguito.

    ![Creare un account Batch][account_portal]

	a. **Nome account**: nome univoco dell'account Batch. Questo nome deve essere univoco nell'area di Azure in cui viene creato l'account. Vedere *Località* di seguito. Può contenere solo caratteri minuscoli e numeri e deve avere una lunghezza di 3-24 caratteri.

	b. **Sottoscrizione**: sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

	c. **Gruppo di risorse**: gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

	d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione** (facoltativo): account di archiviazione **per utilizzo generico** associato (collegato) al nuovo account Batch. Per altri dettagli, vedere più avanti [Account di archiviazione di Azure collegato](#linked-azure-storage-account).

4. Fare clic su **Crea** per creare l'account.

  Il portale indica che è in corso la **distribuzione** dell'account e al termine viene visualizzata la notifica **Le distribuzioni sono riuscite** in *Notifiche*.

## Visualizzare le proprietà dell'account Batch

Dopo avere creato l'account, è possibile aprire il pannello **Account Batch** per accedere alle impostazioni e alle proprietà. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu di sinistra del pannello Account Batch.

![Pannello Account Batch nel portale di Azure][account_blade]

* **URL dell'account Batch**: per le applicazioni create con le [API di sviluppo per Batch](batch-technical-overview.md#batch-development-apis) è necessario un URL dell'account per gestire le risorse ed eseguire i processi nell'account. Un URL dell'account Batch ha il seguente formato:

    `https://<account_name>.<region>.batch.azure.com`

![URL dell'account Batch nel portale][account_url]

* **Chiavi di accesso**: per le applicazioni è necessaria anche una chiave di accesso quando si usano le risorse nell'account Batch. Per visualizzare o rigenerare le chiavi di accesso dell'account Batch, immettere `keys` nella casella di **ricerca** del menu a sinistra nel pannello Account Batch, quindi selezionare **Chiavi**.

    ![Chiavi dell'account Batch nel portale di Azure][account_keys]

## Prezzi

Gli account Batch sono disponibili solo nel "livello gratuito" e quindi non vengono effettuati addebiti per l'account Batch in sé. Vengono addebitati i costi per le risorse di calcolo di Azure sottostanti utilizzate dalle soluzioni Batch e per le risorse utilizzate da altri servizi durante l'esecuzione dei carichi di lavoro. Ad esempio, vengono addebitati i costi per i nodi di calcolo nei pool e per i dati archiviati in Archiviazione di Azure come input o output per le attività. Analogamente, se si usa la funzionalità dei [pacchetti dell'applicazione](batch-application-packages.md) di Batch, vengono addebitati i costi per le risorse di Archiviazione di Azure usate per archiviare i pacchetti dell'applicazione. Per altre informazioni, vedere [Prezzi di Batch][batch_pricing].

## Account di archiviazione di Azure collegato

Come accennato in precedenza, è possibile (facoltativamente) collegare un account di archiviazione **Utilizzo generico** all'account Batch. La funzionalità [Pacchetti dell'applicazione](batch-application-packages.md) di Batch usa l'archivio BLOB in un account di archiviazione per utilizzo generico collegato, proprio come la libreria [.NET Batch File Conventions](batch-task-output.md). Queste funzionalità facoltative facilitano la distribuzione delle applicazioni eseguite dalle attività di Batch e il salvataggio permanente dei dati prodotti.

Batch attualmente supporta *solo* account di archiviazione di tipo **Utilizzo generico**, come descritto nel passaggio 5 [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) dell'articolo [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). Quando si collega un account di archiviazione di Azure all'account Batch, assicurarsi di collegare *solo* un account di archiviazione **Utilizzo generico**.

![Creazione di un account di archiviazione "Utilizzo generico"][storage_account]

È consigliabile creare un account di archiviazione da usare esclusivamente con l'account Batch.

>[AZURE.WARNING] Prestare attenzione quando si rigenerano le chiavi di accesso di un account di archiviazione collegato. Rigenerare solo una chiave dell'account di archiviazione e fare clic su **Sincronizza chiavi** nel pannello dell'account di archiviazione collegato. Attendere cinque minuti per consentire la propagazione delle chiavi ai nodi di calcolo dei pool, quindi rigenerare e sincronizzare l'altra chiave, se necessario. Se si rigenerano entrambe le chiavi contemporaneamente, i nodi di calcolo non riusciranno a sincronizzare entrambe le chiavi e perderanno l'accesso all'account di archiviazione.

  ![Rigenerazione delle chiavi degli account di archiviazione][4]

## Quote e limiti del servizio Batch

Tenere presente che, come alla sottoscrizione di Azure e ad altri servizi di Azure, agli account Batch vengono applicati determinati [limiti e quote](batch-quota-limit.md). Le quote correnti per un account Batch vengono visualizzate nel portale nelle **Proprietà** dell'account.

![Quote dell'account Batch nel portale di Azure][quotas]

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero di destinazione di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch.

Si noti anche che non esiste un limite di un solo account Batch per la sottoscrizione di Azure. È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Per aumentare molte di queste quote, è sufficiente inviare una richiesta di supporto gratuito per il prodotto nel portale di Azure. Per informazioni dettagliate sulla richiesta di aumenti delle quote, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

## Altre opzioni di gestione dell'account Batch

Oltre a usare il portale di Azure, è anche possibile creare e gestire account Batch con gli strumenti seguenti:

* [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
* [Interfaccia della riga di comando di Azure](../xplat-cli-install.md)
* [.NET per la gestione di Batch](batch-management-dotnet.md)

## Passaggi successivi

* Per altre informazioni sui concetti e sulle funzionalità del servizio Batch, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md). L'articolo descrive le risorse primarie di Batch, ad esempio i pool, i nodi di calcolo, i processi e le attività e fornisce una panoramica delle funzionalità del servizio che consentono l'esecuzione di carichi di lavoro di calcolo su larga scala.

* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](batch-dotnet-get-started.md). L'[articolo introduttivo](batch-dotnet-get-started.md) illustra un'applicazione funzionante che usa il servizio Batch per eseguire un carico di lavoro in più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Rigenerazione delle chiavi degli account di archiviazione"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png

<!---HONumber=AcomDC_0831_2016-->