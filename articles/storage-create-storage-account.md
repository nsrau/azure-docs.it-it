<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# <span id="createstorageaccount"></span></a>Come creare un account di archiviazione

Per archiviare file e dati nei servizi BLOB, tabelle, file e di accodamento di Azure, è necessario creare un account di archiviazione nell'area geografica in cui si desidera archiviare i dati. In questo argomento viene descritto come creare un account di archiviazione nel portale di gestione di Azure.

Per informazioni sulla capacità e sulla velocità effettiva dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure][].

> [WACOM.NOTE] Per una macchina virtuale di Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se non si ha già un account di archiviazione in tale percorso. Il nome dell'account di archiviazione sarà basato sul nome della macchina virtuale.

## Sommario

-   [Procedura: Creare un account di archiviazione][]
-   [Passaggi successivi][]

## <span id="create"></span></a>Procedura: Creare un account di archiviazione

1.  Accedere al [portale di gestione][].

2.  Fare clic su **Create New**, quindi su **Storage** e infine su **Quick Create**.

    ![NewStorageAccount][]

3.  In **URL** immettere un nome di sottodominio da utilizzare nell'URL dell'account di archiviazione. Per accedere a un oggetto in memoria, si aggiungerà il percorso dell'oggetto all'endpoint. L'URL per accedere a un BLOB, ad esempio, potrebbe essere http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob\*.

4.  In **Region/Affinity Group** selezionare un'area o un gruppo di affinità per la memoria. Selezionare un gruppo di affinità invece di un'area se si desidera che servizi di archiviazione si trovino nello stesso data center con gli altri servizi di Azure in uso. Ciò può migliorare le prestazioni e non sono previste spese per l'uscita.

    > [WACOM.NOTE] \> Per creare un gruppo di affinità, aprire l'area **Impostazioni** del portale di gestione, fare clic su **Gruppi di affinità** e quindi fare clic su **Aggiungi un gruppo di affinità** o su **Aggiungi**. È inoltre possibile creare e gestire gruppi di affinità mediante l'API Gestione servizi di Azure. Per ulteriori informazioni, vedere [Operazioni sui gruppi di affinità][].

5.  Se si dispone di più sottoscrizioni di Azure, viene visualizzato il campo **Subscription**. In **Subscription** immettere la sottoscrizione di Azure con cui si desidera utilizzare l'account di archiviazione. È possibile creare fino a cinque account di archiviazione per una sottoscrizione.

6.  In **Replication** selezionare i livello di replica desiderato per l'account di archiviazione.

    [WACOM.INCLUDE [storage-replication-options][]]

7.  Fare clic su **Create Storage Account**.

    Potrebbero essere necessari alcuni minuti per creare l'account di archiviazione. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Il nuovo account di archiviazione creato presenta lo stato **Online** ed è pronto per essere utilizzato.

    ![StoragePage][]

## <span id="next"></span></a>Passaggi successivi

-   Per altre informazioni sull'archiviazione di Azure, vedere la relativa documentazione sul sito Web [azure.com][] e su [MSDN][].

-   Visitare il [Blog del team di Archiviazione di Azure][].

  [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
  [Procedura: Creare un account di archiviazione]: #create
  [Passaggi successivi]: #next
  [portale di gestione]: https://manage.windowsazure.com
  [NewStorageAccount]: ./media/storage-create-storage-account/storage_NewStorageAccount.png
  [Operazioni sui gruppi di affinità]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx
  [storage-replication-options]: ../includes/storage-replication-options.md
  [StoragePage]: ./media/storage-create-storage-account/Storage_StoragePage.png
  [azure.com]: http://azure.microsoft.com/en-us/documentation/services/storage/
  [MSDN]: http://msdn.microsoft.com/en-us/library/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
