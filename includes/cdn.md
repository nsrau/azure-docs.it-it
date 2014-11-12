# Uso della rete CDN per Azure

La rete per la distribuzione di contenuti (CDN) di Azure offre agli sviluppatori una soluzione globale per il recapito di contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico di istanze di calcolo in nodi fisici negli Stati Uniti, in Europa, Asia, Australia e Sud America. Per un elenco aggiornato delle ubicazioni dei nodi della rete CDN, vedere [Ubicazioni dei nodi della rete di distribuzione dei contenuti (CDN) di Azure][Ubicazioni dei nodi della rete di distribuzione dei contenuti (CDN) di Azure].

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Creare un account di archiviazione][Passaggio 1: Creare un account di archiviazione]
-   [Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account di archiviazione][Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account di archiviazione]
-   [Passaggio 3: Accedere ai contenuti della rete CDN][Passaggio 3: Accedere ai contenuti della rete CDN]
-   [Passaggio 4: Eliminare i contenuti della rete CDN][Passaggio 4: Eliminare i contenuti della rete CDN]

L'uso della rete CDN per la memorizzazione dei dati di Azure nella cache offre molti vantaggi, inclusi i seguenti:

-   Migliori prestazioni ed esperienza utente ottimale per gli utenti finali che si trovano lontano da un'origine di contenuto e usano applicazioni in cui per il caricamento del contenuto sono necessari numerosi passaggi in Internet.
-   Elevata scalabilità distribuita, per una gestione migliore dei carichi elevati istantanei, ad esempio nelle fasi iniziali di un evento quale il lancio di un prodotto.

I clienti esistenti della rete CDN possono ora usare la Rete di distribuzione dei contenuti di Azure CDN nel [portale di gestione di Azure][portale di gestione di Azure]. La rete CDN è una caratteristica aggiuntiva della sottoscrizione e prevede un [piano di fatturazione specifico][piano di fatturazione specifico].

<span id="Step1"></span> </a>

## Passaggio 1: Creare un account di archiviazione

</p>
Usare la procedura seguente per creare un nuovo account di archiviazione per una sottoscrizione di Azure. Un account di archiviazione consente di accedere ai servizi di archiviazione di Azure. L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso a ogni componente dei servizi di archiviazione di Azure, ovvero: servizi BLOB, servizi di accodamento e servizi tabelle. Per altre informazioni sui servizi di archiviazione di Azure, vedere la pagina relativa all'[uso dei servizi di archiviazione di Azure].

Per creare un account di archiviazione, è necessario essere amministratori del servizio o coamministratori della sottoscrizione.

<div class="dev-callout">
<strong>Nota</strong>
<p>Per informazioni sull'esecuzione di questa operazione tramite l'API di Gestione servizi di Azure, vedere l'argomento di riferimento <a href="http://msdn.microsoft.com/it-it/library/windowsazure/hh264518.aspx">Creare un account di archiviazione</a>.</p>
</div>

**Per creare un account di archiviazione per una sottoscrizione di Azure**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Nell'angolo inferiore sinistro fare clic su **Nuovo**, quindi su **Archiviazione**.
3.  Fare clic su **Creazione rapida**.

    Verrà visualizzata la finestra di dialogo **Crea account di archiviazione**.

    ![Crea account di archiviazione][1]

4.  Nel campo **URL** immettere un nome di sottodominio. Il nome può contenere tra 3 e 24 lettere minuscole e numeri.

    Questo valore diventa il nome host all'interno dell'URI usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione. Per fare riferimento a una risorsa contenitore nel servizio BLOB, usare un URI con il formato seguente, dove *\<EtichettaAccountArchiviazione\>* corrisponde al valore immesso in **Immettere un URL**:

    http://*\<EtichettaAccountArchiviazione\>*.blob.core.windows.net/*\<contenitorepersonale\>*

    **Importante:** L'etichetta dell'URL costituisce il sottodominio dell'URI dell'account di archiviazione e deve essere univoco in tutti i servizi ospitati in Azure.

    Questo valore viene usato anche come nome dell'account di archiviazione nel portale o quando si accede a questo account a livello di codice.

5.  Dall'elenco a discesa **Regione/gruppo di affinità** selezionare una regione o un gruppo di affinità per l'account di archiviazione. Selezionare un gruppo di affinità invece di una regione se si desidera che servizi di archiviazione si trovino nello stesso data center con gli altri servizi di Microsoft Azure in uso. Ciò può migliorare le prestazioni e non sono previste spese per l'uscita.

    **Nota:** Per creare un gruppo di affinità, aprire l'area **Impostazioni** del portale di gestione, fare clic su **Gruppi di affinità** e quindi fare clic su **Aggiungi un gruppo di affinità** o su **Aggiungi**. È anche possibile creare e gestire gruppi di affinità mediante l'API di Gestione servizi di Microsoft Azure. Per altre informazioni, vedere [Operazioni sui gruppi di affinità][Operazioni sui gruppi di affinità].

6.  Dall'elenco a discesa **Subscription** selezionare la sottoscrizione con cui verrà usato l'account di archiviazione.
7.  Fare clic su **Create Storage Account**. Il completamento del processo di creazione dell'account di archiviazione potrebbe richiedere diversi minuti.
8.  Per verificare che l'account di archiviazione sia stato creato correttamente, assicurarsi che l'account sia incluso negli elementi elencati per **Storage** con stato **Online**.

<span id="Step2"></span> </a>

## Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account di archiviazione

</p>
Dopo l'abilitazione dell'accesso della rete CDN a un account di archiviazione o a un servizio ospitato, tutti gli oggetti disponibili pubblicamente saranno idonei per la memorizzazione nella cache perimetrale della rete CDN. Se si modifica un oggetto attualmente memorizzato nella cache nella rete CDN, il nuovo contenuto sarà disponibile tramite la rete CDN solo dopo l'aggiornamento del contenuto della rete CDN alla scadenza della durata specificata per il contenuto memorizzato nella cache.

**Per creare un nuovo endpoint della rete CDN per l'account di archiviazione**

1.  Nel [pannello di navigazione del portale di gestione di Azure][portale di gestione di Azure] fare clic su **CDN**.

2.  Sulla barra multifunzione fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Servizi app**, quindi **CDN** e infine **Creazione rapida**.

3.  Dall'elenco a discesa **Dominio origine** selezionare l'account di archiviazione creato nella sezione precedente dall'elenco di account di archiviazione disponibili.

4.  Per creare il nuovo endpoint, fare clic sul pulsante **Crea**.

5.  Dopo la creazione, l'endpoint sarà visualizzato in un elenco di endpoint per la sottoscrizione. Nella visualizzazione elenco sono mostrati gli URL da usare per accedere a contenuto memorizzato nella cache, oltre al dominio di origine.

    Il dominio di origine indica l'ubicazione da cui il contenuto viene memorizzato nella cache dalla rete CDN. Il dominio di origine può essere un account di archiviazione o un servizio cloud. In questo esempio viene usato un account di archiviazione. Il contenuto di archiviazione viene memorizzato nella cache dei server perimetrali in base all'impostazione di controllo della cache specificata dall'utente o dall'euristica predefinita della rete di memorizzazione nella cache. Per altre informazioni, vedere [Come gestire la scadenza del contenuto di BLOB][Come gestire la scadenza del contenuto di BLOB].

    <div class="dev-callout">
<strong>Nota</strong>
<p>La configurazione creata per l'endpoint non sar&agrave;
disponibile immediatamente. Potrebbero essere necessari fino a 60 minuti per la
propagazione della registrazione nella rete CDN. &Egrave; possibile che gli utenti che provano a usare
immediatamente il nome di dominio della rete CDN ricevano un errore con codice di stato 400
(Richiesta non valida) fino a quando il contenuto non risulter&agrave; disponibile tramite la rete CDN.</p>
</div>

<span id="Step3"></span> </a>

## Passaggio 3: Accedere al contenuto della rete CDN

</p>
Per accedere al contenuto memorizzato nella cache nella rete CDN, usare l'URL della rete CDN specificato nel portale. L'indirizzo per un oggetto BLOB memorizzato nella cache sarà analogo al seguente:

http://\<*SpaziodeinomiCDN*\>.vo.msecnd.net/\<*ContenitorePubblicoPersonale*\>/\<*NomeBLOB*\>

<span id="Step4"></span> </a>

## Passaggio 4: Rimuovere contenuto dalla rete CDN

</p>
Se non si desidera più memorizzare un oggetto nella cache della rete per la distribuzione di contenuti (CDN) di Azure, è possibile eseguire una delle operazioni seguenti:

-   Per un oggetto BLOB di Azure è possibile eliminare tale oggetto dal contenitore pubblico.
-   È possibile rendere privato il contenitore, invece di pubblico. Per altre informazioni, vedere [Limitare l'accesso a contenitori e Blob][Limitare l'accesso a contenitori e Blob].
-   L'endpoint della rete CDN può essere disabilitato o eliminato tramite il portale di gestione.
-   È possibile modificare il servizio ospitato, in modo che non risponda più a richieste per l'oggetto.

Un oggetto già memorizzato nella cache della rete CDN rimarrà nella cache fino alla scadenza della durata prevista per l'oggetto. Al termine della durata prevista, la rete CDN verificherà se l'endpoint della rete CDN è ancora valido e se l'oggetto è ancora accessibile in modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato nella cache.

Per la rete CDN di Azure non è attualmente disponibile alcuno strumento esplicito per la ripulitura.

## Risorse aggiuntive

-   [Come creare un gruppo di affinità in Azure]
-   [Procedura: Gestione degli account di archiviazione per una sottoscrizione di Azure][Procedura: Gestione degli account di archiviazione per una sottoscrizione di Azure]
-   [Informazioni sull'API di gestione del servizio][Informazioni sull'API di gestione del servizio]
-   [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato][Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato]

  [Azure CDN Node Locations]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680302.aspx
  [Step 1: Create a storage account]: #Step1
  [Step 2: Create a new CDN endpoint for your storage account]: #Step2
  [Step 3: Access your CDN content]: #Step3
  [Step 4: Delete your CDN content]: #Step4
  [Azure Management Portal]: https://manage.windowsazure.com/
  [billing plan]: /en-us/pricing/calculator/?scenario=full
  [Create Storage Account]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
  [1]: ./media/cdn/CDN_CreateNewStorageAcct.png
  [Operations on Affinity Groups]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [How to Manage Expiration of Blob Content]: http://msdn.microsoft.com/en-us/library/gg680306.aspx
  [Restrict Access to Containers and Blobs]: http://msdn.microsoft.com/en-us/library/dd179354.aspx
  [How to: Manage Storage Accounts for an Azure Subscription]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531567.aspx
  [About the Service Management API]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460807.aspx
  [How to Map CDN Content to a Custom Domain]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680307.aspx
