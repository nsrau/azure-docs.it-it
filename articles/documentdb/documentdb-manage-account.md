<properties 
	pageTitle="Gestire un account DocumentDB | Azure" 
	description="Informazioni su come gestire l'account DocumentDB." 
	services="documentdb" 
	documentationCenter="" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="stbaro"/>

#Come gestire un account DocumentDB

Acquisire familiarità con le chiavi, le impostazioni di coerenza e informazioni su come eliminare un account.

## <a id="keys"></a>Per visualizzare, copiare e rigenerare le chiavi di accesso
Quando si crea un account DocumentDB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account DocumentDB. Fornendo due chiavi di accesso, DocumentDB consente di rigenerare le chiavi senza interruzioni dell'account DocumentDB.

Nel [portale di anteprima di Microsoft Azure](https://portal.azure.com/) accedere alla parte relativa alle **chiavi** del pannello dell'**account DocumentDB** per visualizzare, copiare e rigenerare le chiavi usate per accedere all'account DocumentDB.

![](media/documentdb-manage-account/keys.png)

### Per visualizzare e copiare una chiave di accesso

1.      Nel [portale di anteprima di Azure](https://portal.azure.com/) accedere all'account DocumentDB. 

2.      Nel filtro di **riepilogo** fare clic su **Keys**.

3.      Nel pannello **Keys** fare clic sul pulsante **Copy** a destra della chiave da copiare.

  ![](./media/documentdb-manage-account/image004.jpg)

### Per rigenerare le chiavi di accesso

È consigliabile modificare periodicamente le chiavi di accesso all'account DocumentDB per mantenere più sicure le connessioni. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account DocumentDB con una delle due chiavi mentre si rigenera l'altra.

> [AZURE.WARNING]La rigenerazione delle chiavi di accesso influisce su tutte le applicazioni che dipendono dalla chiave corrente. Per usare la nuova chiave è necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account DocumentDB.

Se si dispone di applicazioni o servizi cloud che usano l'account DocumentDB e si rigenerano le chiavi, si perderanno le connessioni, a meno che non si registrino le chiavi. I passaggi seguenti illustrano il processo necessario per la registrazione delle chiavi.

1.      Aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla chiave di accesso secondaria dell'account DocumentDB.

2.      Rigenerare la chiave di accesso primaria per l'account DocumentDB. Nel [portale di anteprima di Azure](https://portal.azure.com/) accedere all'account DocumentDB.

3.      Nel filtro di riepilogo fare clic su **Keys**.

4.      Nel pannello **Keys** fare clic sul comando **Regenerate Primary** e quindi su **OK** per confermare che si vuole generare una nuova chiave.

5.      Dopo aver verificato che la nuova chiave sia disponibile per l'utilizzo (circa 5 minuti dopo la rigenerazione), aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla nuova chiave di accesso primaria.

6.      Rigenerare la chiave di accesso secondaria.

*Si noti che potrebbero trascorrere diversi minuti prima che una chiave appena generata possa essere usata per accedere all'account DocumentDB.*

## <a id="consistency"></a>Per gestire le impostazioni di coerenza di DocumentDB
DocumentDB supporta quattro livelli di coerenza dei dati, ben definiti e configurabili dall'utente, per consentire agli sviluppatori di trovare compromessi prevedibili tra coerenza, disponibilità e latenza.

- La coerenza **assoluta** garantisce che le operazioni di lettura restituiscano sempre il valore scritto per ultimo.

- La coerenza **con obsolescenza** associata garantisce che le letture non siano troppo obsolete. In particolare, garantisce che le letture non siano più di K versioni precedenti all'ultima versione scritta.

- La coerenza **di sessione** garantisce letture monotoniche (non si leggeranno mai dati obsoleti, poi nuovi, poi di nuovo obsoleti), scritture monotoniche (le scritture vengono ordinate) e che si leggano le scritture più recenti in ogni singolo punto di vista del client.

- La coerenza **finale** garantisce che le operazioni di lettura leggano sempre un subset di scritture valido e che alla fine convergano.

*Si noti che, per impostazione predefinita, gli account DocumentDB sono sottoposti a provisioning con la coerenza a livello di sessione. Per altre informazioni sulle impostazioni di coerenza di DocumentDB, vedere la sezione [Livello di coerenza](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Per specificare la coerenza predefinita per un account DocumentDB

1.      Nel [portale di anteprima di Azure](https://portal.azure.com/) accedere all'account DocumentDB. 

2.      Nel filtro **Configuration** fare clic su **Default Consistency**.

3.      Nel pannello **Default Consistency** selezionare il livello di coerenza predefinito desiderato per l'account DocumentDB.

![](./media/documentdb-manage-account/image005.png)

![](./media/documentdb-manage-account/image006.png)

4.      Fare clic su **Save**.

5.      L'avanzamento dell'operazione può essere monitorato tramite l'hub delle notifiche del portale di anteprima di Azure.

*Si noti che potrebbero trascorrere diversi minuti prima che una modifica alle impostazioni di coerenza predefinite si propaghi nell'intero account DocumentDB.*

## <a id="delete"></a>Procedura: eliminare un account DocumentDB
Per rimuovere un account DocumentDB non più in uso, utilizzare il comando **Elimina** nel pannello dell'account **DocumentDB**.

![](./media/documentdb-manage-account/image009.png)

1.      Nel [portale di anteprima di Azure](https://portal.azure.com/) accedere all'account DocumentDB da eliminare. 

2.      Nel pannello dell'**account DocumentDB** fare clic sul comando **Elimina**.

3.      Nel pannello di conferma risultante, digitare il nome dell'account DocumentDB per confermarne l'eliminazione.

4.      Fare clic sul pulsante **Elimina** nel pannello di conferma.

## <a id="next"></a>Passaggi successivi

Altre informazioni su come [iniziare a usare l'account DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Per altre informazioni su DocumentDB, vedere la documentazione relativa ad Azure DocumentDB in [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

 
 

<!-----HONumber=July15_HO5-->