<properties urlDisplayName="How to create" pageTitle="Come creare un account di archiviazione | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />


<h1><a id="createstorageaccount"></a>Come creare un account di archiviazione</h1>

Per archiviare file e dati nei servizi BLOB, tabelle, file e di accodamento di Azure, è necessario creare un account di archiviazione nell'area geografica in cui si desidera archiviare i dati. Questo argomento descrive come creare un account di archiviazione nel portale di gestione di Azure.

Per informazioni sulla capacità e sulla velocità effettiva dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/it-it/library/dn249410.aspx).

> [WACOM.NOTE] Per una macchina virtuale di Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se non si dispone già di un account di archiviazione in tale percorso. Il nome dell'account di archiviazione sarà basato sul nome della macchina virtuale.

##Sommario##

* [Procedura: Creare un account di archiviazione](#create)
* [Passaggi successivi](#next)

<h2><a id="create"></a>Procedura: Creare un account di archiviazione</h2>

1. Accedere al [portale di gestione](https://manage.windowsazure.com).

2. Fare clic su **Crea nuovo**, **Archiviazione** e quindi su **Creazione rapida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. In **URL** immettere un nome di sottodominio da usare nell'URL dell'account di archiviazione. Per accedere a un oggetto in memoria, si aggiungerà il percorso dell'oggetto all'endpoint. L'URL per accedere a un BLOB, ad esempio, potrebbe essere http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

4. In **Regione/gruppo di affinità** selezionare un'area o un gruppo di affinità per l'archiviazione.  Selezionare un gruppo di affinità invece di un'area se si desidera che servizi di archiviazione si trovino nello stesso data center con gli altri servizi di Azure in uso. Ciò può migliorare le prestazioni e non sono previste spese per l'uscita.

	> [WACOM.NOTE]  Per creare un gruppo di affinità, aprire l'area <b>Impostazioni</b> del portale di gestione, fare clic su <b>Gruppi di affinità</b>, quindi fare clic su <b>Aggiungi un gruppo di affinità</b> o su <b>Aggiungi</b>. È inoltre possibile creare e gestire gruppi di affinità mediante l'API Gestione servizi di Azure. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/ee460798.aspx">Operazioni sui gruppi di affinità</a>.

5. Se si dispone di più sottoscrizioni di Azure, viene visualizzato il campo **Sottoscrizione**. In **Sottoscrizione** immettere la sottoscrizione di Azure con cui usare l'account di archiviazione. È possibile creare fino a cinque account di archiviazione per una sottoscrizione.

6. In **Replica** selezionare il livello di replica desiderato per l'account di archiviazione.

	[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

6. Fare clic su **Crea account di archiviazione**.

	Potrebbero essere necessari alcuni minuti per creare l'account di archiviazione. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Il nuovo account di archiviazione creato presenta lo stato **Online** ed è pronto per l'uso. 

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>Passaggi successivi</h2>

- Per altre informazioni sull'archiviazione di Azure, vedere la relativa documentazione sul sito Web [azure.com](http://azure.microsoft.com/it-it/documentation/services/storage/) e su [MSDN](http://msdn.microsoft.com/it-it/library/gg433040.aspx). 

- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/).

