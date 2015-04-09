# Uso della rete CDN per Azure

La rete di distribuzione dei contenuti di Azure (CDN) offre agli sviluppatori una
soluzione globale per il recapito di contenuto con esigenze di larghezza di banda elevata, memorizzando nella cache BLOB
e contenuto statico delle istanze di calcolo nei nodi fisici di Stati
Uniti, Europa, Asia, Australia e Sudamerica. Per un elenco attuale
delle posizioni dei nodi della rete CDN, vedere [Posizioni dei nodi della rete CDN di Azure].

Questa attività include i passaggi seguenti:

* [Passaggio 1: Creare un account di archiviazione](#Step1)
* [Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account di archiviazione](#Step2)
* [Passaggio 3: Accedere ai contenuti della rete CDN](#Step3)
* [Passaggio 4: Rimuovere i contenuti della rete CDN](#Step4)

L'uso della rete CDN per la memorizzazione dei dati Azure nella cache offre molti vantaggi, inclusi i seguenti:

-   una migliore esperienza utente e migliori prestazioni per gli utenti finali, che sono lontani dall'origine del contenuto e che utilizzano applicazioni dove sono necessari numerosi 'internet trips' per caricare contenuto
-   Elevata scalabilità distribuita, per una gestione migliore dei carichi elevati istantanei, ad esempio nelle fasi iniziali di un evento quale il lancio di un prodotto.

I clienti dell'attuale rete CDN, ora possono utilizzare la rete CDN di Azure nel [Portale di gestione di Azure]. La rete CDN è una funzionalità aggiuntiva della sottoscrizione e prevede un [piano di fatturazione] separato.

<a id="Step1"> </a>
<h2>Passaggio 1: Creare un account di archiviazione</h2>

Attenersi alla procedura seguente per creare un nuovo account di archiviazione per una
sottoscrizione di Azure. Un account di archiviazione consente di accedere ai 
servizi di archiviazione Azure. L'account di archiviazione rappresenta il livello più elevato
dello spazio dei nomi per accedere a ciascuno dei componenti del servizio di archiviazione
Azure: servizi BLOB, servizi di accodamento e servizi tabelle. Per altre
informazioni sui servizi di archiviazione Azure, vedere [Utilizzo dei
servizi di archiviazione Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Per creare un account di archiviazione, è necessario essere amministratore
del servizio o co-amministratore per la sottoscrizione associata.

> [AZURE.NOTE] Per informazioni sull'esecuzione di questa operazione tramite
l'API di gestione del servizio Azure, vedere l'argomento di riferimento [Creare un account di archiviazione](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**Per creare un account di archiviazione per una sottoscrizione di Azure**

1.  Accedere al [Portale di gestione di Azure].
2.  Nell'angolo in basso a sinistra della schermata fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Servizi dati**, fare clic su **Archiviazione** e quindi su **Creazione rapida**.

    Verrà visualizzata la finestra di dialogo **Crea account di archiviazione**.

    ![Crea account di archiviazione][create-new-storage-account]

4. Nel campo **URL** immettere un nome di sottodominio. Il nome può contenere tra 3 e 24 lettere minuscole e numeri.

    Questo valore diventa il nome host nell'URI utilizzato per
    indirizzare risorse BLOB, di accodamento o tabelle per la sottoscrizione. Per
    indirizzare una risorsa contenitore nel servizio BLOB, si userà
    un URI nel formato seguente, dove *&lt;StorageAccountLabel&gt;* fa riferimento
    al valore digitato in **Immettere un URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante:** l'etichetta dell'URL forma il sottodominio dell'URI dell'account
    di archiviazione e deve essere unica tra tutti i servizi ospitati in 
    Azure.

	Questo valore viene usato anche come nome dell'account di archiviazione nel portale o quando si accede a questo account a livello di codice.

5.  Dall'elenco a discesa **Regione/Gruppo di affinità**, selezionare una regione o un gruppo di affinità per l'account di archiviazione. Selezionare un gruppo di affinità invece di una regione se si desidera che servizi di archiviazione si trovino nello stesso data center con gli altri servizi di Microsoft Azure in uso. Ciò può migliorare le prestazioni e non sono previste spese in uscita.  

    **Nota:** per creare un gruppo di affinità, aprire l'area **Impostazioni** del portale di gestione, fare clic su **Gruppi di affinità** e quindi fare clic su **Aggiungi un gruppo di affinità** o su **Aggiungi**. È anche possibile creare e gestire gruppi di affinità mediante l'API di gestione del servizio Microsoft Azure. Per altre informazioni, vedere [Operazioni sui gruppi di affinità].

6. Dall'elenco a discesa **Sottoscrizione**, selezionare la sottoscrizione con cui verrà utilizzato l'account di archiviazione.
7.  Fare clic su **Crea account di archiviazione**. Il completamento del processo di creazione dell'account di archiviazione potrebbe richiedere diversi minuti.
8.  Per verificare che l'account di archiviazione sia stato creato correttamente, assicurarsi che sia incluso negli elementi elencati per **Archiviazione** stato **Online**.

<a id="Step2"> </a>
<h2>Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account di archiviazione</h2>

Una volta abilitato l'accesso della rete CDN a un account di archiviazione o a un servizio ospitato, tutti
gli oggetti disponibili pubblicamente saranno idonei per la memorizzazione nella cache perimetrale della rete CDN. Se si
modifica un oggetto attualmente memorizzato nella cache della rete CDN, il nuovo contenuto
non sarà disponibile tramite la rete CDN fino all'aggiornamento del contenuto
alla scadenza del periodo di durata del contenuto memorizzato nella cache.

**Per creare un nuovo endpoint della rete CDN per l'account di archiviazione**

1. Nel pannello di navigazione del [portale di gestione di Azure] fare clic su **CDN**.

2. Sulla barra multifunzione fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Servizi app**, quindi **CDN** e infine **Creazione rapida**.

3. Dall'elenco a discesa **Dominio origine** selezionare l'account di archiviazione creato nella sezione precedente dall'elenco di account di archiviazione disponibili. 

4. Per creare il nuovo endpoint, fare clic sul pulsante **Crea**.

5. Dopo la creazione, l'endpoint sarà visualizzato in un elenco di endpoint per la sottoscrizione. Nella visualizzazione elenco sono mostrati gli URL da usare per accedere a contenuto memorizzato nella cache, oltre al dominio di origine. 

	Il dominio di origine è il percorso da cui la rete CDN memorizza il contenuto
    nella cache. Il dominio di origine può essere un account di archiviazione o un servizio cloud. In questo esempio viene usato un account di archiviazione. Il contenuto di archiviazione viene memorizzato nella cache dei server perimetrali in base all'impostazione di controllo della cache specificata dall'utente o dall'euristica predefinita della rete di memorizzazione nella cache. Per altre informazioni, vedere [Come gestire la scadenza del contenuto di BLOB](http://msdn.microsoft.com/library/gg680306.aspx). 


    > [AZURE.NOTE] La configurazione creata per l'endpoint non sarà
    disponibile immediatamente. Potrebbero essere necessari fino a 60 minuti per la
    propagazione della registrazione nella rete CDN. È possibile che gli utenti che provano a usare
    immediatamente il nome di dominio della rete CDN ricevano un errore con codice di stato 400
    (Richiesta non valida) fino a quando il contenuto non risulterà disponibile tramite la rete CDN.

<a id="Step3"> </a>
<h2>Passaggio 3: Accedere al contenuto della rete CDN</h2> 

Per accedere al contenuto memorizzato nella cache nella rete CDN, usare l'URL della rete CDN specificato nel portale. L'indirizzo per un oggetto BLOB memorizzato nella cache sarà analogo al seguente:

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Passaggio 4: Rimuovere contenuto dalla rete CDN</h2>

Se non si desidera più memorizzare un oggetto nella cache della rete per la
distribuzione di contenuti (CDN) di Azure, è possibile eseguire una delle operazioni seguenti:

-   per un BLOB Azure, è possibile eliminare il BLOB dal
    contenitore pubblico.
-   È possibile rendere privato il contenitore, invece di pubblico. Vedere [Limitare l'accesso a contenitori e BLOB](http://msdn.microsoft.com/library/dd179354.aspx) per ulteriori informazioni.
-   È possibile disabilitare o eliminare l'endpoint della rete CDN utilizzando il portale di
    gestione.
-   È possibile modificare il servizio ospitato affinché non risponda alle richieste per
    l'oggetto.

Un oggetto già memorizzato nella cache della rete CDN, vi rimarrà fino alla scadenza del periodo di durata
per l'oggetto. Alla scadenza del periodo di durata,
la rete CDN verificherà la validità dell'endpoint della rete CDN
e l'accessibilità in modalità anonima dell'oggetto. In caso di risultati negativi,
l'oggetto non sarà più memorizzato nella cache.

Nel portale di gestione di Azure non è attualmente supportata una funzionalità per ripulire immediatamente il contenuto. Se è necessario ripulire immediatamente il contenuto, contattare il [Supporto tecnico di Azure](http://azure.microsoft.com/support/options/). 

## Risorse aggiuntive

-   [Come creare un gruppo di affinità in Azure]
-   [Procedura: Gestire account di archiviazione per una sottoscrizione di Azure]
-   [Informazioni sull'API di gestione del servizio]
-   [Come localizzare sulla mappa il contenuto della rete CDN in un dominio personalizzato]

  [Crea account di archiviazione]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Posizioni dei nodi della rete CDN di Azure]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Portale di gestione di Azure]: https://manage.windowsazure.com/
  [piano di fatturazione]: /pricing/calculator/?scenario=full
  [Come registrare un nome di sottodominio personalizzato per accedere ai BLOB in Azure]: http://msdn.microsoft.com/library/windowsazure/ee795179.aspx
  [Come creare un gruppo di affinità in Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Panoramica della rete CDN di Azure]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Procedura: Gestire account di archiviazione per una sottoscrizione di Azure]: http://msdn.microsoft.com/library/windowsazure/hh531567.aspx
  [Informazioni sull'API di gestione del servizio]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Come localizzare sulla mappa il contenuto della rete CDN in un dominio personalizzato]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Portale di gestione precedente]: ../../Shared/Media/previous-portal.png

<!--HONumber=49-->