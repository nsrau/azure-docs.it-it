<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" authors="tysonn" solutions="" manager="paulettm" editor="cgronlun" />

Come creare un account di archiviazione
=======================================

Per archiviare file e dati nei servizi BLOB, tabelle e di accodamento di Azure, è necessario creare un account di archiviazione nell'area geografica in cui si desidera archiviare i dati. Un account di archiviazione può contenere fino a 200 TB di dati ed è possibile creare fino a venti account di archiviazione per ogni sottoscrizione di Azure. Per ulteriori informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/en-us/library/dn249410.aspx).

In questo argomento viene descritto come creare un account di archiviazione nel portale di gestione di Azure.
**Nota**

Per una macchina virtuale di Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se non si dispone già di un account di archiviazione in tale percorso. Il nome dell'account di archiviazione sarà basato sul nome della macchina virtuale.

Sommario
--------

-   [Procedura: Creare un account di archiviazione](#create)
-   [Passaggi successivi](#next)

Procedura: Creare un account di archiviazione
---------------------------------------------

1.  Accedere al [portale di gestione](https://manage.windowsazure.com).

2.  Fare clic su **Create New**, quindi su **Storage** e infine su **Quick Create**.

    ![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3.  In **URL** immettere un nome di sottodominio da utilizzare nell'URL dell'account di archiviazione. Per accedere a un oggetto in memoria, si aggiungerà il percorso dell'oggetto all'endpoint. L'URL per accedere a un BLOB, ad esempio, potrebbe essere http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

4.  In **Region/Affinity Group** selezionare un'area o un gruppo di affinità per la memoria. Selezionare un gruppo di affinità invece di un'area se si desidera che servizi di archiviazione si trovino nello stesso data center con gli altri servizi di Azure in uso. Ciò può migliorare le prestazioni e non sono previste spese per l'uscita.

    > [WACOM.NOTE] \> Per creare un gruppo di affinità, aprire l'area **Networks** del portale di gestione, fare clic su **Affinity Groups** e quindi fare clic su **Create a new affinity group** o su **Create**. È possibile utilizzare i gruppi di affinità creati nel precedente portale di gestione. Per aprire l'altro portale, fare clic su **Preview** nella barra del titolo e quindi fare clic su **Take me to the previous portal**. Per tornare a questo portale, fare clic su **View the Preview Portal** nella parte inferiore del portale. È inoltre possibile creare e gestire gruppi di affinità mediante l'API Gestione servizi di Azure. Per ulteriori informazioni, vedere [Operazioni sui gruppi di affinità](http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx).

5.  Se si dispone di più sottoscrizioni Azure, viene visualizzato il campo **Subscription**. In **Subscription** immettere la sottoscrizione di Azure con cui si desidera utilizzare l'account di archiviazione. È possibile creare fino a cinque account di archiviazione per una sottoscrizione.

6.  In **Replication** selezionare i livello di replica desiderato per l'account di archiviazione.

    Per impostazione predefinita, la replica è impostata su **Geo-Redundant**. Con la replica con ridondanza geografica, in caso di grave emergenza nella posizione primaria, viene eseguito il failover dell'account di archiviazione e di tutti i dati in esso contenuti in una posizione secondaria. Azure assegna una posizione secondaria nella stessa area, che non può essere modificata. Dopo un failover, la posizione secondaria diventa la posizione primaria per l'account di archiviazione e i dati vengono replicati in una nuova posizione secondaria.

    Per poter leggere i dati dalla posizione secondaria, è possibile selezionare la replica **Read-Access Geo-Redundant**. Questa opzione fornisce la replica con ridondanza geografica e consente l'accesso in sola lettura ai dati replicati nella posizione secondaria. La replica con ridondanza geografica con accesso in lettura consente di accedere ai dati dalla posizione primaria o secondaria, qualora una posizione non sia disponibile.

    Una terza opzione replica, **Read Access Geo-Redundant**, è attualmente in anteprima. Questa opzione consente l'accesso in sola lettura ai dati replicati nella posizione secondaria. La replica con ridondanza geografica con accesso in lettura consente di accedere ai dati dalla posizione primaria o secondaria, qualora una posizione non sia disponibile.

    > [WACOM.NOTE] \> Per utilizzare la replica con ridondanza geografica con accesso in lettura mentre è in anteprima, è necessario richiedere manualmente che la funzionalità venga abilitata per la sottoscrizione. Per richiedere la replica con ridondanza geografica con accesso in lettura per la sottoscrizione, visitare la pagina relativa all'[anteprima delle funzionalità di Azure](https://account.windowsazure.com/PreviewFeatures). Per ulteriori informazioni sulla replica con ridondanza geografica con accesso in lettura, vedere il [blog del team di archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx). Se la replica con ridondanza geografica con accesso in lettura non è abilitata come funzionalità di anteprima nella sottoscrizione, l'opzione per selezionarla per l'account di archiviazione sarà disabilitata.

    Per informazioni sui prezzi per la replica dell'account di archiviazione, vedere [Dettagli prezzi di archiviazione](http://www.windowsazure.com/en-us/pricing/details/storage/).

7.  Fare clic su **Create Storage Account**.

    Potrebbero essere necessari alcuni minuti per creare l'account di archiviazione. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Il nuovo account di archiviazione creato presenta lo stato **Online** ed è pronto per essere utilizzato.

    ![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

Passaggi successivi
-------------------

-   Per ulteriori informazioni sui servizi di archiviazione di Azure, vedere le pagine relative ad [archiviazione del cloud](http://www.windowsazure.com/en-us/develop/net/fundamentals/cloud-storage/) e a [BLOB, code e tabelle](http://msdn.microsoft.com/en-us/library/gg433040.aspx).

-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)

-   Configurare le app per utilizzare servizi BLOB, tabelle e di accodamento di Azure. Nel [Centro per sviluppatori Azure](http://www.windowsazure.com/en-us/develop/overview/) sono disponibili guide dettagliate per l'utilizzo dei servizi di archiviazione di accodamento, tabelle e BLOB con le applicazioni .NET, Node.js, Java e PHP. Per istruzioni specifiche per un linguaggio di programmazione, vedere le guide dettagliate relative a tale linguaggio.


