<properties title="Monitor a DocumentDB Account" pageTitle="Manage a DocumentDB account | Azure" description="Learn how to manage your DocumentDB account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/03/2014" ms.author="hawong" />

# Come gestire un account DocumentDB

## Sommario

-   [Procedura: per visualizzare, copiare e rigenerare le chiavi di accesso a DocumentDB](#keys)
-   [Procedura: per gestire le impostazioni di coerenza di DocumentDB](#consistency)
-   [Procedura: per gestire le impostazioni di capacità di DocumentDB](#capacity)
-   [Procedura: per eliminare un account DocumentDB](#delete)
-   [Passaggi successivi](#next)

## <span id="keys"></span></a>Procedura: visualizzare, copiare e rigenerare le chiavi di accesso

Quando si crea un account DocumentDB, vengono generate due chiavi di accesso
principali che possono essere utilizzate per l'autenticazione quando si accede
all'account. Fornendo due chiavi di accesso, DocumentDB consente
di rigenerare le chiavi senza interruzioni dell'account
DocumentDB.

Nel [portale di anteprima di gestione di Azure
](https://portal.azure.com/)[](http://manage.windowsazure.com)
accedere alla parte relativa alle **chiavi** dalla falda dell'account DocumentDB per visualizzare,
copiare e rigenerare le chiavi che vengono utilizzate per accedere all'account.

![](./media/documentdb-manage-account/image002.jpg)

### Per visualizzare e copiare una chiave di accesso

1.      Nel [portale di anteprima di gestione di Azure]https://portal.azure.com/) accedere all'account DocumentDB. 

2.      Nel filtro di riepilogo, fare clic su **Keys**.

3.      Nella falda delle chiavi, fare clic sul pulsante **Copia** a destra della chiave
che si desidera copiare.

4.      Premere Ctrl+C per copiare la chiave.

  ![](./media/documentdb-manage-account/image004.jpg)

### Per rigenerare le chiavi di accesso

È consigliabile modificare periodicamente le chiavi di accesso all'account DocumentDB
per mantenere più sicure le connessioni. Vengono assegnate
due chiavi di accesso per consentire di mantenere le connessioni all'account
DocumentDB con una delle due chiavi mentre si rigenera l'altra.

**Avviso**

La rigenerazione delle chiavi di accesso influisce su tutte le applicazioni che
dipendono dalla chiave corrente. Per utilizzare la nuova chiave è necessario aggiornare tutti i client che usano la chiave di accesso
per accedere all'account DocumentDB.

Se si dispone di applicazioni o servizi cloud che utilizzano l'account DocumentDB e si rigenerano le chiavi,
si perderanno le connessioni, a meno che non si registrino
le chiavi. Di seguito è riportata una delle procedure che è possibile eseguire:

1.      Aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento
alla chiave di accesso secondaria dell'account DocumentDB.

2.      Rigenerare la chiave di accesso primaria dell'account DocumentDB.
Nel [portale di anteprima di gestione di Azure](https://portal.azure.com/),
accedere all'account DocumentDB.

3.      Nel filtro di riepilogo, fare clic su **Keys**.

4.      Nella falda delle chiavi, fare clic sul comando **Regenerate Primary**, quindi
fare clic su **OK** per confermare che si desidera generare una nuova chiave.

5.      Dopo aver verificato che la nuova chiave sia disponibile per l'utilizzo
(circa 5 minuti dopo la rigenerazione), aggiornare la chiave di accesso
nel codice dell'applicazione in modo che faccia riferimento alla nuova chiave di accesso primaria.

6.      Rigenerare la chiave di accesso secondaria.

*Si noti che potrebbero trascorrere diversi minuti prima che una chiave appena generata possa
essere utilizzata per accedere all'account DocumentDB.*

## <span id="consistency"></span></a>Procedura: per gestire le impostazioni di coerenza di DocumentDB

DocumentDB supporta quattro livelli di coerenza dei dati, ben definiti e configurabili dall'utente,
per consentire agli sviluppatori di trovare compromessi prevedibili tra
coerenza, disponibilità e latenza.

·         La coerenza **assoluta** garantisce che le operazioni di lettura restituiscano
sempre il valore scritto per ultimo.

·         La coerenza **con obsolescenza associata** garantisce che le letture non siano
troppo obsolete. In particolare, garantisce che le letture non siano
più di K versioni precedenti all'ultima versione scritta.

·         La coerenza di **sessione** garantisce letture monotoniche (non si leggeranno mai
dati obsoleti, poi nuovi, poi di nuovo obsoleti), scritture monotoniche (le scritture vengono
ordinate) e che si leggano le scritture più recenti in ogni singolo punto di vista
del client.

·         La coerenza **finale** garantisce che le operazioni di lettura
leggano sempre un subset di scritture valido e che alla fine convergano.

*Si noti che, per impostazione predefinita, gli account DocumentDB sono sottoposti a provisioning con la coerenza a livello di sessione
. Per ulteriori informazioni sulle impostazioni di coerenza di
DocumentDB, vedere la sezione [Livello di
coerenza](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Per specificare la coerenza predefinita per un account DocumentDB

1.      Nel [portale di anteprima di gestione di Azure](https://portal.azure.com/) accedere all'account DocumentDB. 

2.      Nel filtro di configurazione, fare clic su **Consistency**.

3.      Nella falda della coerenza predefinita, selezionare il livello di
coerenza predefinito desiderato per l'account DocumentDB.

4.      Fare clic su **Salva**.

5.      L'avanzamento dell'operazione può essere monitorato tramite
l'hub delle notifiche del portale di anteprima di gestione di Azure.

 ![](./media/documentdb-manage-account/image005.png)

 ![](./media/documentdb-manage-account/image006.png)

*Si noti che potrebbero trascorrere diversi minuti prima che una modifica alle impostazioni
di coerenza predefinite si propaghino nell'intero account DocumentDB.*

## <span id="capacity"></span></a>Procedura: per gestire le impostazioni di capacità di DocumentDB

Microsoft Azure DocumentDB consente una scalabilità flessibile a mano a mano che
le esigenze dell'applicazione cambiano durante il suo ciclo di vita. La scalabilità di
DocumentDB si ottiene aumentando la capacità dell'account database di DocumentDB
 mediante il portale di anteprima di gestione di Azure.

Quando si crea un account database, gli vengono assegnate risorse di archiviazione
database e una velocità effettiva riservata. È possibile modificare le risorse di archiviazione
e la velocità effettiva assegnate all'account in qualsiasi momento, aggiungendo
o rimuovendo unità di capacità tramite il portale di anteprima di gestione di Azure. 

### Per aggiungere o rimuovere unità di capacità

1.      Nel [portale di anteprima di gestione di Azure
][portale di anteprima di gestione di Azure
] accedere all'account DocumentDB. 

2.      Nel filtro di utilizzo, fare clic su **Scale**.

3.      Nella falda della scalabilità, specificare il numero di unità di capacità
desiderato per l'account DocumentDB.

4.      Fare clic su **Salva** (si noti che il completamento dell'operazione di ridimensionamento
potrebbe richiedere diversi minuti e che è possibile monitorare l'avanzamento tramite
l'hub delle notifiche del portale di anteprima di gestione di Azure).

*Si noti che l'anteprima di DocumentDB supporta massimo 5 unità
di capacità per ogni account DocumentDB.*

![](./media/documentdb-manage-account/image007.png)

 

## <span id="delete"></span></a>Procedura: per eliminare un account DocumentDB

Per rimuovere un account DocumentDB non più in uso, utilizzare il comando
**Elimina** nella falda dell'account DocumentDB.

**Avviso**

*Nella versione di anteprima non è possibile ripristinare il contenuto di un account
DocumentDB eliminato. L'eliminazione di un account DocumentDB comporterà l'eliminazione
di tutte le risorse dell'account, inclusi database, raccolte,
documenti e allegati.*

![](./media/documentdb-manage-account/image009.png)

1.      Nel [portale di anteprima di gestione di Azure](https://portal.azure.com/) accedere all'account DocumentDB
che si desidera eliminare. 

2.      Nella falda dell'account, fare clic sul comando **Elimina**.

3.      Nella falda di conferma risultante, digitare il nome dell'account DocumentDB
per confermarne l'eliminazione.

4.      Fare clic sul pulsante **Elimina** nella falda di conferma.

## <span id="next"></span></a>Passaggi successivi

-   Ulteriori informazioni su come [iniziare a utilizzare l'account
    DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).
-   Per ulteriori informazioni su DocumentDB, vedere la documentazione di
    Azure DocumentDB su
    [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409)

 

  [Procedura: per visualizzare, copiare e rigenerare le chiavi di accesso a DocumentDB]: #keys
  [Procedura: per gestire le impostazioni di coerenza di DocumentDB]: #consistency
  [Procedura: per gestire le impostazioni di capacità di DocumentDB]: #capacity
  [Procedura: per eliminare un account DocumentDB]: #delete
  [Passaggi successivi]: #next
  []: http://manage.windowsazure.com
  []: ./media/documentdb-manage-account/image002.jpg
  [azure.com]: http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409
