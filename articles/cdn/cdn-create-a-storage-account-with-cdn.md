<properties 
	pageTitle="Come utilizzare la rete CDN | Microsoft Azure" 
	description="Informazioni su come usare la rete per la distribuzione di contenuti (rete CDN) di Azure per distribuire contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico." 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# Integrare un account di archiviazione con la rete CDN

È possibile abilitare la rete CDN per memorizzare nella cache i contenuti delle risorse di archiviazione di Azure. Questa rete offre agli sviluppatori una soluzione globale per il recapito di contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico di istanze di calcolo in nodi fisici negli Stati Uniti, in Europa, Asia, Australia e Sud America.


## Passaggio 1: Creare un account di archiviazione

Usare la procedura seguente per creare un nuovo account di archiviazione per una sottoscrizione ad Azure. Un account di archiviazione consente di accedere ai servizi di archiviazione di Azure. L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso a ogni componente dei servizi di archiviazione di Azure, ovvero servizi BLOB, servizi di accodamento e servizi tabelle. Per altre informazioni, fare riferimento a [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).

Per creare un account di archiviazione, è necessario essere amministratori del servizio o coamministratori della sottoscrizione.

> [AZURE.NOTE] Esistono diversi metodi che è possibile usare per creare un account di archiviazione, compresi il portale di Azure e Powershell. Per questa esercitazione, verrà utilizzato il portale di Azure.

**Per creare un account di archiviazione per una sottoscrizione di Azure**

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Nell'angolo in alto a sinistra della schermata fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Dati + Archiviazione**, quindi fare clic su **Account di archiviazione**. Lasciare selezionata l’opzione **Classico** come modello di distribuzione, quindi fare clic su **Crea**.

    Appare il pannello **Account di archiviazione**.

    ![Create Storage Account][create-new-storage-account]

4. Nel campo **Archiviazione**, immettere un nome di sottodominio. Il nome può contenere tra 3 e 24 lettere minuscole e numeri.

    Questo valore diventa il nome host all'interno dell'URI usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione. Per fare riferimento a una risorsa contenitore nel servizio BLOB, usare un URI con il formato seguente, dove *&lt;StorageAccountLabel&gt;* corrisponde al valore immesso in **Immettere un URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante:** l'etichetta dell'URL costituisce il sottodominio dell'URI dell'account di archiviazione e deve essere univoca in tutti i servizi ospitati in Azure.

	Questo valore viene usato anche come nome dell'account di archiviazione nel portale o quando si accede a questo account a livello di codice.

5.  Selezionare un **Piano tariffario** oppure usare il valore predefinito. Per altre informazioni sui piani tariffari, vedere [Prezzi di Archiviazione di Azure](../../pricing/details/storage).

6.  Selezionare o creare un **gruppo di risorse**. Per altre informazioni sui Gruppi di risorse, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview/#resource-groups).

7. Selezionare la **Sottoscrizione** con cui verrà usato l'account di archiviazione.

8.  Fare clic su **Crea**. Il completamento del processo di creazione dell'account di archiviazione potrebbe richiedere diversi minuti.

9.  Per verificare che l'account di archiviazione sia stato creato correttamente, assicurarsi che l'account sia incluso negli elementi elencati per **Archiviazione** con stato **Online**.


## Passaggio 2: Creare un nuovo profilo di rete CDN

Un profilo di rete CDN è una raccolta di endpoint della rete CDN. Ogni profilo contiene uno o più endpoint della rete CDN. Si consiglia di usare più profili per organizzare gli endpoint della rete CDN tramite il dominio internet, l’applicazione web o altri criteri.

> [AZURE.TIP] Se si dispone già di un profilo di rete CDN che si desidera usare per questa esercitazione, passare a [Passaggio 3](#step-3-create-a-new-cdn-endpoint).

**Per creare un nuovo profilo di rete CDN**

1. Nel [portale di gestione di Azure](https://portal.azure.com), in alto a sinistra, fare clic su **Nuovo**. Nel pannello **Nuovo** selezionare **Contenuti multimediali e rete CDN**, quindi **Rete CDN**.

    Viene visualizzato il pannello del nuovo profilo di rete CDN.
    
    ![Nuovo profilo di rete CDN][new-cdn-profile]

2. Inserire un nome per il profilo di rete CDN.

3. Selezionare un **Piano tariffario** oppure usare il valore predefinito.

4. Selezionare o creare un **Gruppo di risorse**. Non è necessario che questo sia lo stesso Gruppo di risorse dell’account di archiviazione.

5. Selezionare la **Sottoscrizione** per questo profilo di rete CDN. Questa dovrà essere la stessa sottoscrizione dell'account di archiviazione ai fini di questa esercitazione.

6. Selezionare un **Percorso**. Questo è il percorso di Azure in cui verranno archiviate le informazioni relative al profilo di rete CDN. Non incide sulle posizioni dell’endpoint di rete CDN. Non è necessario che sia lo stesso percorso dell'account di archiviazione.

7. Per creare il nuovo profilo, fare clic sul pulsante **Crea**.

## Passaggio 3: creare un nuovo endpoint della rete CDN

**Per creare un nuovo endpoint della rete CDN per l'account di archiviazione**

1. Nel [portale di gestione di Azure](https://portal.azure.com) passare al profilo di rete CDN. Lo si potrebbe aver bloccato nel dashboard nel passaggio precedente. Se così non fosse, è possibile trovarlo cliccando su **Sfoglia**, quindi su **Profili di rete CDN** e facendo clic sul profilo in cui si prevede di aggiungere l'endpoint.

    Viene visualizzato il pannello del profilo di rete CDN.
    
    ![Profilo di rete CDN][cdn-profile-settings]
    
2. Fare clic sul pulsante **Aggiungi Endpoint**.

    ![Pulsante Aggiungi endpoint][cdn-new-endpoint-button]

    Viene visualizzato il pannello **Aggiungi un endpoint**.
    
    ![Pannello Aggiungi endpoint][cdn-add-endpoint]

3. Immettere un **Nome** per questo endpoint della rete CDN. Questo nome verrà usato per accedere alle risorse memorizzate nella cache nel dominio `<EndpointName>.azureedge.net`.

4. Nell'elenco a discesa**Tipo di origine**, selezionare *Archiviazione*.

5. Nell'elenco a discesa **nome host di origine**, selezionare l'account di archiviazione.

6. Lasciare le impostazioni predefinite per **Percorso di origine**, **Intestazione host di origine**, e **Porta protocollo/origine**. È necessario specificare almeno un protocollo (HTTP o HTTPS).

    > [AZURE.NOTE] Questa configurazione abilita tutti i contenitori visibili pubblicamente nell'account di archiviazione alla memorizzazione nella cache nella rete CDN . Se si desidera limitare l'ambito di un singolo contenitore, usare il **Percorso di origine**. Si noti che il contenitore deve avere la visibilità impostata su pubblica.

7. Per creare il nuovo endpoint, fare clic sul pulsante **Aggiungi**.

8. Dopo la creazione, l'endpoint sarà visualizzato in un elenco di endpoint per il profilo. Nella visualizzazione elenco sono mostrati gli URL da usare per accedere a contenuti memorizzati nella cache, oltre al dominio di origine.

    ![Endpoint della rete CDN][cdn-endpoint-success]

    > [AZURE.NOTE] L'endpoint non sarà immediatamente disponibile per l'uso. Ci possono volere fino a 90 minuti per far sì che la registrazione si propaghi attraverso la rete CDN. È possibile che gli utenti che provano a usare immediatamente il nome di dominio della rete CDN ricevano un errore con codice di stato 404 fino a quando il contenuto non risulterà disponibile tramite la rete CDN.


## Passaggio 4: Accedere al contenuto della rete CDN

Per accedere al contenuto memorizzato nella cache nella rete CDN, usare l'URL della rete CDN specificato nel portale. L'indirizzo per un oggetto BLOB memorizzato nella cache sarà analogo al seguente:

http://<*EndpointName*>.azureedge.net/<*myPublicContainer*>/<*BlobName*>

> [AZURE.NOTE] Dopo l'abilitazione dell'accesso della rete CDN a un account di archiviazione o a un servizio ospitato, tutti gli oggetti disponibili pubblicamente saranno idonei per la memorizzazione nella cache perimetrale della rete CDN. Se si modifica un oggetto attualmente memorizzato nella cache nella rete CDN, il nuovo contenuto sarà disponibile tramite la rete CDN solo dopo l'aggiornamento dei contenuti della rete CDN alla scadenza della durata specificata per i contenuti memorizzati nella cache.

## Passaggio 5: rimuovere contenuto dalla rete CDN

Se non si desidera più memorizzare un oggetto nella cache della rete per la distribuzione di contenuti (rete CDN) di Azure, è possibile eseguire una delle operazioni seguenti:

-   È possibile rendere privato il contenitore, invece di pubblico. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../storage-manage-access-to-resources/).
-   È possibile disabilitare o eliminare l'endpoint della rete CDN usando il portale di gestione.
-   È possibile modificare il servizio ospitato, in modo che non risponda più a richieste per l'oggetto.

Un oggetto già memorizzato nella cache della rete CDN rimarrà nella cache fino alla scadenza della durata prevista per l'oggetto o fino a quando l’endpoint non verrà eliminato. Al termine della durata prevista, la rete CDN verificherà se l'endpoint della rete CDN è ancora valido e se l'oggetto è ancora accessibile in modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato nella cache.


## Risorse aggiuntive

-   [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0204_2016-->