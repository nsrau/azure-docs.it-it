<properties 
	pageTitle="Come utilizzare la rete CDN | Microsoft Azure" 
	description="Informazioni su come usare la rete per la distribuzione di contenuti (rete CDN) di Azure per distribuire contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="mazha"/>


# Integrare un account di archiviazione con la rete CDN

È possibile abilitare la rete CDN per memorizzare nella cache i contenuti delle risorse di archiviazione di Azure. Questa rete offre agli sviluppatori una soluzione globale per il recapito di contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico di istanze di calcolo in nodi fisici negli Stati Uniti, in Europa, Asia, Australia e Sud America.


## Passaggio 1: Creare un account di archiviazione

Usare la procedura seguente per creare un nuovo account di archiviazione per una sottoscrizione ad Azure. Un account di archiviazione consente di accedere ai servizi di archiviazione di Azure. L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso a ogni componente dei servizi di archiviazione di Azure, ovvero servizi BLOB, servizi di accodamento e servizi tabelle. Per altre informazioni sui servizi di archiviazione di Azure, vedere [Uso dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Per creare un account di archiviazione, è necessario essere amministratori del servizio o coamministratori della sottoscrizione.

> [AZURE.NOTE]Per informazioni sull'esecuzione di questa operazione tramite l'API di gestione servizi di Azure, vedere l'argomento di riferimento [Creare un account di archiviazione](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**Per creare un account di archiviazione per una sottoscrizione di Azure**

1.  Accedere al [portale di gestione di Azure].
2.  Nell'angolo in basso a sinistra della schermata fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Servizi dati**, fare clic su **Archiviazione** e quindi su **Creazione rapida**.

    Verrà visualizzata la finestra di dialogo **Crea account di archiviazione**.

    ![Crea account di archiviazione][create-new-storage-account]

4. Nel campo **URL** immettere un nome di sottodominio. Il nome può contenere tra 3 e 24 lettere minuscole e numeri.

    Questo valore diventa il nome host all'interno dell'URI usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione. Per fare riferimento a una risorsa contenitore nel servizio BLOB, usare un URI con il formato seguente, dove *&lt;StorageAccountLabel&gt;* corrisponde al valore immesso in **Immettere un URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante:** l'etichetta dell'URL costituisce il sottodominio dell'URI dell'account di archiviazione e deve essere univoca in tutti i servizi ospitati in Azure.

	Questo valore viene usato anche come nome dell'account di archiviazione nel portale o quando si accede a questo account a livello di codice.

5.  Dall'elenco a discesa **Regione/gruppo di affinità** selezionare un'area o un gruppo di affinità per l'account di archiviazione. Selezionare un gruppo di affinità invece di un'area se si vuole che servizi di archiviazione si trovino nello stesso data center con gli altri servizi di Microsoft Azure in uso. Ciò può migliorare le prestazioni e non sono previste spese in uscita.

    **Nota:** per creare un gruppo di affinità, aprire l'area **Impostazioni** del portale di gestione, fare clic su **Gruppi di affinità** e quindi fare clic su **Aggiungi un gruppo di affinità** o su **Aggiungi**. È anche possibile creare e gestire gruppi di affinità mediante l'API di gestione del servizio Microsoft Azure. Per altre informazioni, vedere [Operazioni sui gruppi di affinità].

6. Dall'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione con cui verrà usato l'account di archiviazione.
7.  Fare clic su **Crea account di archiviazione**. Il completamento del processo di creazione dell'account di archiviazione potrebbe richiedere diversi minuti.
8.  Per verificare che l'account di archiviazione sia stato creato correttamente, assicurarsi che l'account sia incluso negli elementi elencati per **Archiviazione** con stato **Online**.


## Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account di archiviazione

Dopo l'abilitazione dell'accesso della rete CDN a un account di archiviazione o a un servizio ospitato, tutti gli oggetti disponibili pubblicamente saranno idonei per la memorizzazione nella cache perimetrale della rete CDN. Se si modifica un oggetto attualmente memorizzato nella cache nella rete CDN, il nuovo contenuto sarà disponibile tramite la rete CDN solo dopo l'aggiornamento dei contenuti della rete CDN alla scadenza della durata specificata per i contenuti memorizzati nella cache.

**Per creare un nuovo endpoint della rete CDN per l'account di archiviazione**

1. Nel [pannello di navigazione del portale di gestione di Azure] fare clic su **CDN**.

2. Sulla barra multifunzione fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Servizi app**, quindi **CDN** e infine **Creazione rapida**.

3. Dall'elenco a discesa **Dominio origine** selezionare l'account di archiviazione creato nella sezione precedente dall'elenco di account di archiviazione disponibili.

4. Per creare il nuovo endpoint, fare clic sul pulsante **Crea**.

5. Dopo la creazione, l'endpoint sarà visualizzato in un elenco di endpoint per la sottoscrizione. Nella visualizzazione elenco sono mostrati gli URL da usare per accedere a contenuti memorizzati nella cache, oltre al dominio di origine.

	Il dominio di origine indica l'ubicazione da cui i contenuti vengono memorizzati nella cache dalla rete CDN. Il dominio di origine può essere un account di archiviazione o un servizio cloud. In questo esempio viene usato un account di archiviazione. Il contenuto di archiviazione viene memorizzato nella cache dei server perimetrali in base all'impostazione di controllo della cache specificata dall'utente o dall'euristica predefinita della rete di memorizzazione nella cache. Per altre informazioni, vedere [Come gestire la scadenza del contenuto di BLOB](http://msdn.microsoft.com/library/gg680306.aspx).


    > [AZURE.NOTE]la configurazione creata per l'endpoint non sarà disponibile immediatamente. Per la propagazione della registrazione nella rete CDN potrebbero essere necessari fino a 60 minuti. È possibile che gli utenti che provano a usare immediatamente il nome di dominio della rete CDN ricevano un errore con codice di stato 400 (Richiesta non valida) fino a quando il contenuto non risulterà disponibile tramite la rete CDN.


## Passaggio 3: Accedere al contenuto della rete CDN

Per accedere al contenuto memorizzato nella cache nella rete CDN, usare l'URL della rete CDN specificato nel portale. L'indirizzo per un oggetto BLOB memorizzato nella cache sarà analogo al seguente:

http://<*SpazioDeiNomiCDN*>.vo.msecnd.net/<*ContenitorePubblicoPersonale*>/<*NomeBLOB*>


## Passaggio 4: Rimuovere contenuto dalla rete CDN

Se non si desidera più memorizzare un oggetto nella cache della rete per la distribuzione di contenuti (rete CDN) di Azure, è possibile eseguire una delle operazioni seguenti:

-   È possibile rendere privato il contenitore, invece di pubblico. Per ulteriori informazioni, vedere [Limitare l'accesso a contenitori e Blob](http://msdn.microsoft.com/library/dd179354.aspx).
-   È possibile disabilitare o eliminare l'endpoint della rete CDN usando il portale di gestione.
-   È possibile modificare il servizio ospitato, in modo che non risponda più a richieste per l'oggetto.

Un oggetto già memorizzato nella cache della rete CDN rimarrà nella cache fino alla scadenza della durata prevista per l'oggetto. Al termine della durata prevista, la rete CDN verificherà se l'endpoint della rete CDN è ancora valido e se l'oggetto è ancora accessibile in modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato nella cache.

Nel portale di gestione di Azure non è attualmente supportata una funzionalità per ripulire immediatamente il contenuto. Se è necessario ripulire immediatamente il contenuto, contattare il [Supporto tecnico di Azure](http://azure.microsoft.com/support/options/).

## Risorse aggiuntive

-   [Come creare un gruppo di affinità in Azure]
-   [Procedura: Gestire account di archiviazione per una sottoscrizione di Azure]
-   [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato]

[Create Storage Account]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure CDN Node Locations]: cdn-pop-locations.md
[pannello di navigazione del portale di gestione di Azure]: https://manage.windowsazure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[billing plan]: /pricing/calculator/?scenario=full
[How to Register a Custom Subdomain Name for Accessing Blobs in Azure]: ../storage-custom-domain-name.md
[Come creare un gruppo di affinità in Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: cdn-overview.md
[Procedura: Gestire account di archiviazione per una sottoscrizione di Azure]: https://msdn.microsoft.com/it-it/library/azure/hh531793.aspx
[Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato]: cdn-map-content-to-custom-domain.md


[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

 

<!---HONumber=August15_HO8-->