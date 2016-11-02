<properties
	pageTitle=" Creare un account Servizi multimediali di Azure con il portale di Azure | Microsoft Azure"
	description="Questa esercitazione illustra la procedura di creazione di un account Servizi multimediali di Azure con il portale di Azure."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/07/2016"
	ms.author="juliako"/>


# Creare un account Servizi multimediali di Azure con il portale di Azure

> [AZURE.SELECTOR]
- [Portale](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

Nel portale di Azure è possibile creare rapidamente un account di Servizi multimediali di Azure (AMS). È possibile utilizzare l'account per accedere ai servizi multimediali che consentono di archiviare, crittografare, codificare, gestire e distribuire in streaming contenuti multimediali in Azure. Durante la creazione di un account di Servizi multimediali, viene creato l'account di archiviazione associato (se non si desidera utilizzarne uno esistente) nella stessa area geografica dell'account di Servizi multimediali.

Questo articolo illustra alcuni concetti comuni e come creare un account Servizi multimediali con il portale di Azure.

## Concetti

Per accedere a Servizi multimediali è necessario disporre di due account associati:

- Account di Servizi multimediali. L'account consente di accedere a un set di Servizi multimediali basati su cloud disponibili in Azure. In un account di Servizi multimediali non è possibile archiviare contenuti multimediali effettivi, ma è possibile archiviare i metadati relativi al contenuto multimediale e i processi di elaborazione dei file multimediali. Al momento della creazione dell'account, è necessario selezionare un'area di Servizi multimediali disponibile. L'area selezionata è un data center in cui sono archiviati i record dei metadati per l'account.

	Le aree di Servizi multimediali (AMS) disponibili includono: Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali, Asia sudorientale, Asia orientale, Giappone occidentale, Giappone orientale. In Servizi multimediali non vengono utilizzati gruppi di affinità.
	
	AMS è ora disponibile anche nei data center seguenti: Brasile meridionale, India occidentale, India meridionale e India centrale. È ora possibile usare il portale di Azure per creare account Servizi multimediali ed eseguire diverse attività descritte qui. In questi data center la codifica live non è tuttavia abilitata. Inoltre, non sono disponibili tutti i tipi di unità riservate di codifica.
	
	- Brasile meridionale: sono disponibili solo unità riservate di codifica Standard e Basic.
	- India occidentale, India meridionale: fornire BLOB di archiviazione per i file multimediali. Gli account di archiviazione devono trovarsi nella stessa area geografica dell'account Servizi multimediali. Quando si crea un account di Servizi multimediali, è possibile scegliere un account di archiviazione esistente nella stessa area geografica oppure è possibile crearne uno nuovo, ma sempre nella stessa area. Se si elimina un account di Servizi multimediali, gli oggetti BLOB presenti nell'account di archiviazione associato non vengono eliminati.

## Creare un account AMS

I passaggi descritti in questa sezione illustrano come creare un account Servizi multimediali di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+Nuovo** > **Contenuti multimediali e rete CDN** > **Servizi multimediali**.

	![Creare Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. In **CREARE UN ACCOUNT DEL SERVIZIO MULTIMEDIALE** immettere i valori richiesti.

	![Creare Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new3.png)
	
	1. In **Nome account** immettere il nome del nuovo account di AMS. Un nome di account di Servizi multimediali deve essere composto da tutte lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.
	2. In Sottoscrizione selezionare una delle diverse sottoscrizioni di Azure per le quali è disponibile l'accesso.
	
	2. In **Gruppo di risorse** selezionare la risorsa nuova o esistente. Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. fare clic [qui](resource-group-overview.md#resource-groups) per ulteriori informazioni.
	3. In **Località** selezionare l'area geografica che verrà usata per archiviare i record di metadati e contenuto multimediale per l'account Servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili.
	
	3. In **Account di archiviazione** selezionare un account di archiviazione per l'archivio BLOB del contenuto multimediale dell'account Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account Servizi multimediali oppure è possibile crearne uno. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.

		Altre informazioni sull'archiviazione sono disponibili [qui](storage-introduction.md).

	4. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
	
7. Fare clic su **Crea** nella parte inferiore del form.

	Dopo aver creato l'account, lo stato diventa **In esecuzione**.

	![Impostazioni di Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-settings.png)

	Per gestire l'account AMS, ad esempio per caricare video, codificare asset, monitorare lo stato dei processi, usare la finestra **Impostazioni**.

## Gestione delle chiavi

Per accedere a livello di codice all'account Servizi multimediali, sono necessarie le informazioni relative al nome dell'account e alla chiave primaria.

1. Nel portale di Azure selezionare l'account.

	Su lato destro verrà visualizzata la finestra **Impostazioni**.

2. Nella finestra **Impostazioni** selezionare **Chiavi**.

	Nella finestra **Gestisci chiavi** sono visualizzati il nome dell'account e le chiavi primaria e secondaria.
3. Per copiare i valori, scegliere il pulsante Copia.
	
	![Chiavi di Servizi multimediali](./media/media-services-portal-vod-get-started/media-services-keys.png)

## Passaggi successivi

Ora è possibile caricare i file nell'account AMS. Per altre informazioni, vedere [Upload files](media-services-portal-upload-files.md) (Caricare file).

## Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->