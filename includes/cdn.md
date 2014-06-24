# Utilizzo della rete CDN per Azure

La rete per la distribuzione di contenuti (CDN) di Azure offre agli
sviluppatori una soluzione globale per il recapito di contenuto con
esigenze di larghezza di banda elevata, tramite la memorizzazione nella
cache di oggetti BLOB e contenuto statico di istanze di calcolo in nodi
fisici negli Stati Uniti, in Europa, Asia, Australia e Sud America. Per
un elenco aggiornato delle ubicazioni dei nodi della rete CDN, vedere
[Ubicazioni dei nodi della rete CDN di Azure][1].

Questa attività include i passaggi seguenti:

* [Passaggio 1: Creare un account di archiviazione](#Step1)
* [Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account
  di archiviazione](#Step2)
* [Passaggio 3: Accedere ai contenuti della rete CDN](#Step3)
* [Passaggio 4: Eliminare i contenuti della rete CDN](#Step4)

L'utilizzo della rete CDN per la memorizzazione dei dati di Azure nella
cache offre molti vantaggi, inclusi i seguenti:

* Migliori prestazioni ed esperienza utente ottimale per gli utenti
  finali che si trovano lontano da un'origine di contenuto e utilizzano
  applicazioni in cui per il caricamento del contenuto sono necessari
  numerosi passaggi in Internet.
* Elevata scalabilità distribuita, per una gestione migliore dei carichi
  elevati istantanei, ad esempio nelle fasi iniziali di un evento quale
  il lancio di un prodotto.

I clienti esistenti della rete CDN possono ora utilizzare la Rete di
distribuzione dei contenuti di Azure CDN nel [portale di gestione di
Azure][2]. La rete CDN è una caratteristica aggiuntiva della
sottoscrizione e prevede un [piano di fatturazione
specifico](/en-us/pricing/calculator/?scenario=full).

<a  id="Step1"> </a>

<h2>Passaggio 1: Creare un account di archiviazione</h2>


Utilizzare la procedura seguente per creare un nuovo account di
archiviazione per una sottoscrizione ad Azure. Un account di
archiviazione consente di accedere ai servizi di archiviazione di Azure.
L'account di archiviazione rappresenta il livello più elevato dello
spazio dei nomi per l'accesso a ogni componente dei servizi di
archiviazione di Azure, ovvero servizi BLOB, servizi di accodamento e
servizi tabelle. Per ulteriori informazioni sui servizi di archiviazione
di Azure, vedere [Utilizzo dei servizi di archiviazione di Azure][3].

Per creare un account di archiviazione, è necessario essere
amministratori del servizio o coamministratori della sottoscrizione.

 
<div  class="dev-callout">
<strong>Nota</strong>
<p>Per informazioni sull'esecuzione di questa operazione tramite l'API di gestione servizi di Azure, vedere l'argomento di riferimento <a  href="http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx">Creare un account di archiviazione</a>.</p>
</div>

 **Per creare un account di archiviazione per una sottoscrizione ad
Azure**

1.  Accedere al [portale di gestione di Azure][2].
2.  Nell'angolo inferiore sinistro fare clic su **New**, quindi su
    **Storage**.
3.  Fare clic su **Quick Create**.
    
    Verrà visualizzata la finestra di dialogo **Create Storage
    Account**.
    
    ![Crea account di
    archiviazione](./media/cdn/CDN_CreateNewStorageAcct.png)

4.  Nel campo **URL** immettere un nome di sottodominio. Il nome può
    contenere tra 3 e 24 lettere minuscole e numeri.
    
    Questo valore diventa il nome host all'interno dell'URI utilizzato
    per fare riferimento a risorse BLOB, di accodamento o tabelle per la
    sottoscrizione. Per fare riferimento a una risorsa contenitore nel
    servizio BLOB, utilizzare un URI con il formato seguente, dove
    *<EtichettaAccountArchiviazione>* corrisponde al valore
    immesso in **Enter a URL**:
    
    http://*<EtichettaAccountArchiviazione>*.blob.core.windows.net/*<contenitorepersonale>*
    
    **Importante:** L'etichetta dell'URL costituisce il sottodominio
    dell'URI dell'account di archiviazione e deve essere univoco in
    tutti i servizi ospitati in Azure.
    
    Questo valore viene utilizzato anche come nome dell'account di
    archiviazione nel portale o quando si accede a questo account a
    livello di codice.

5.  Dall'elenco a discesa **Region/Affinity Group** selezionare
    un'ubicazione geografica per l'account di archiviazione. In
    alternativa, utilizzare un gruppo di affinità. Per istruzioni sulla
    creazione di un gruppo di affinità, vedere [Come creare un gruppo di
    affinità in Azure][4].
6.  Dall'elenco a discesa **Subscription** selezionare la
    sottoscrizione con cui verrà utilizzato l'account di archiviazione.
7.  Fare clic su **Create Storage Account**. Il completamento del
    processo di creazione dell'account di archiviazione potrebbe
    richiedere diversi minuti.
8.  Per verificare che l'account di archiviazione sia stato creato
    correttamente, assicurarsi che l'account sia incluso negli elementi
    elencati per **Storage** con stato **Online**.

<a  id="Step2"> </a>

<h2>Passaggio 2: Creare un nuovo endpoint della rete CDN per l'account di archiviazione</h2>


Dopo l'abilitazione dell'accesso della rete CDN a un account di
archiviazione o a un servizio ospitato, tutti gli oggetti disponibili
pubblicamente saranno idonei per la memorizzazione nella cache
perimetrale della rete CDN. Se si modifica un oggetto attualmente
memorizzato nella cache nella rete CDN, il nuovo contenuto sarà
disponibile tramite la rete CDN solo dopo l'aggiornamento dei contenuti
della rete CDN alla scadenza della durata specificata per i contenuti
memorizzati nella cache.

**Per creare un nuovo endpoint della rete CDN per l'account di
archiviazione**

1.  Nel pannello di navigazione del [portale di gestione di Azure][2]
    fare clic su **CDN**.

2.  Sulla barra multifunzione fare clic su **New**. Nella finestra di
    dialogo **New** selezionare **App Services**, quindi **CDN** e
    infine **Quick Create**.

3.  Dall'elenco a discesa **Origin Domain** selezionare l'account di
    archiviazione creato nella sezione precedente dall'elenco di
    account di archiviazione disponibili.

4.  Per creare il nuovo endpoint, fare clic sul pulsante **Create**.

5.  Dopo la creazione, l'endpoint sarà visualizzato in un elenco di
    endpoint per la sottoscrizione. Nella visualizzazione elenco sono
    mostrati gli URL da utilizzare per accedere a contenuti memorizzati
    nella cache, oltre al dominio di origine.
    
    Il dominio di origine indica l'ubicazione da cui i contenuti
    vengono memorizzati nella cache dalla rete CDN. Il dominio di
    origine può essere un account di archiviazione o un servizio cloud.
    In questo esempio viene utilizzato un account di archiviazione. Il
    contenuto di archiviazione viene memorizzato nella cache dei server
    perimetrali in base all'impostazione di controllo della cache
    specificata dall'utente o dall'euristica predefinita della rete di
    memorizzazione nella cache. Per ulteriori informazioni, vedere [Come
    gestire la scadenza del contenuto di Blob][5].
    
    <div  class="dev-callout" markdown="1">

**Nota**
La configurazione creata per l`endpoint non sarà disponibile
immediatamente. Per la propagazione della registrazione nella rete
CDN potrebbero essere necessari fino a 60 minuti. È possibile che
gli utenti che provano a utilizzare immediatamente il nome di
dominio della rete CDN ricevano un errore con codice di stato 400
(Richiesta non valida) fino a quando il contenuto non risulterà
disponibile tramite la rete CDN.

<a  id="Step3"> </a>

<h2>Passaggio 3: Accedere ai contenuti della rete CDN</h2>


Per accedere ai contenuti memorizzati nella cache nella rete CDN,
utilizzare l'URL della rete CDN specificato nel portale. L'indirizzo
per un oggetto BLOB memorizzato nella cache sarà analogo al seguente:

http://<*SpazioDeiNomiCDN*>.vo.msecnd.net/<*ContenitorePubblicoPersonale*>/<*NomeBLOB*>

<a  id="Step4"> </a>

<h2>Passaggio 4: Rimuovere contenuti dalla rete CDN</h2>


Se non si desidera più memorizzare un oggetto nella cache della rete per
la distribuzione di contenuti (CDN) di Azure, è possibile eseguire una
delle operazioni seguenti:

* Per un oggetto BLOB di Azure è possibile eliminare tale oggetto dal
  contenitore pubblico.
* È possibile rendere privato il contenitore, invece di pubblico. Per
  ulteriori informazioni, vedere [Limitare l'accesso a contenitori e
  Blob][6].
* È possibile disabilitare o eliminare l'endpoint della rete CDN
  utilizzando il portale di gestione.
* È possibile modificare il servizio ospitato, in modo che non risponda
  più a richieste per l'oggetto.

Un oggetto già memorizzato nella cache della rete CDN rimarrà nella
cache fino alla scadenza della durata prevista per l'oggetto. Al
termine della durata prevista, la rete CDN verificherà se l'endpoint
della rete CDN è ancora valido e se l'oggetto è ancora accessibile in
modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato
nella cache.

Per la rete CDN di Azure non è attualmente disponibile alcuno strumento
esplicito per la ripulitura.
## Risorse aggiuntive

* [Come creare un gruppo di affinità in Azure][4]
* [Procedura: Gestione degli account di archiviazione per una
  sottoscrizione di Azure][7]
* [Informazioni sull'API di gestione del servizio][8]
* [Come eseguire il mapping del contenuto della rete CDN a un dominio
  personalizzato][9]



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680302.aspx
[2]: https://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/ee924681.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531560.aspx
[5]: http://msdn.microsoft.com/en-us/library/gg680306.aspx
[6]: http://msdn.microsoft.com/en-us/library/dd179354.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531567.aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460807.aspx
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680307.aspx

