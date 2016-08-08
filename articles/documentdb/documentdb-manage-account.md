<properties
	pageTitle="Gestire un account DocumentDB usando il portale di Azure | Microsoft Azure"
	description="Informazioni su come gestire il proprio account DocumentDB tramite il portale di Azure. Trovare una guida sull'uso del portale di Azure per visualizzare, copiare, eliminare e accedere agli account."
	keywords="Portale di Azure, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="anhoh"/>

# Come gestire un account DocumentDB

Informazioni su come impostare la coerenza globale e gestire più aree per la disponibilità globale dei dati in Azure DocumentDB. Include anche informazioni su come usare le chiavi ed eliminare un account nel portale di Azure.

## <a id="consistency"></a>Gestire le impostazioni di coerenza di DocumentDB

La scelta del livello di coerenza giusto dipende dalla semantica dell'applicazione. È consigliabile acquisire familiarità con i livelli di coerenza disponibili in DocumentDB: [Utilizzo dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni di DocumentDB][consistency]. DocumentDB offre garanzia su coerenza, disponibilità e prestazioni a ogni livello di coerenza disponibile per l'account di database. La configurazione dell'account di database con un forte livello di coerenza richiede che i dati vengano limitati a una singola area di Azure, senza disponibilità globale. D'altra parte, i livelli di coerenza ampi (obsolescenza associata, sessione o finale) consentono di associare tutte le aree di Azure desiderate con l'account di database. La semplice procedura seguente mostra come selezionare il livello di coerenza predefinito per l'account di database.

### Per specificare la coerenza predefinita per un account DocumentDB

1. Nel [portale di Azure](https://portal.azure.com/) accedere all'account DocumentDB.
2. Nel pannello dell'account, se il pannello **Impostazioni** non è già aperto, fare clic su **Tutte le impostazioni**. ![Sessione coerenza predefinita][5]

3. Nel pannello **Tutte le impostazioni** fare clic su **Uniformità predefinita** in **Funzionalità**. ![Sessione coerenza predefinita][6]
4. Nel pannello **Coerenza predefinita** selezionare il nuovo livello di coerenza e fare clic su **Salva**.
5. L'avanzamento dell'operazione può essere monitorato tramite l'hub delle notifiche del portale di Azure.

> [AZURE.NOTE] Potrebbero trascorrere diversi minuti prima che una modifica alle impostazioni di coerenza predefinite si propaghi nell'intero account DocumentDB.

## <a id="addregion"></a>Aggiungere aree

DocumentDB è disponibile nella maggior parte delle [aree di Azure][azureregions]. Dopo aver selezionato il livello di coerenza predefinito per l'account di database, è possibile associare una o più aree, a seconda del livello di coerenza predefinito e delle esigenze di distribuzione globale scelti.

> [AZURE.NOTE] A questo punto, è possibile aggiungere nuovi account DocumentDB creati a partire dal 13 giugno 2016. Per creare un account con più aree, selezionare "Azure DocumentDB - Multi-region database Account" (Azure DocumentDB - Account di database multiarea) nel Marketplace. A breve verranno abilitati per la disponibilità globale gli account creati prima del 13 giugno.

1. Nel [portale di Azure](https://portal.azure.com/), nell’indice, fare clic su **Account DocumentDB**.
2. Nel pannello **Account DocumentDB** selezionare l'account di database da modificare.
3. Nel pannello dell'account, se il pannello **Tutte le impostazioni** non è già aperto, fare clic su **Tutte le impostazioni**.
4. Nel pannello **Tutte le impostazioni** fare clic su **Aggiungi/Rimuovi aree**. ![Aggiungere aree in Account DocumentDB > Impostazioni > Aggiungi/Rimuovi aree][1]
5. Nel pannello **Aggiungi/Rimuovi aree** selezionare le aree da aggiungere o rimuovere e quindi fare clic su **OK**. L'aggiunta di aree prevede un costo: per altre informazioni vedere la pagina relativa ai prezzi.

    ![Fare clic sulle aree nella mappa per aggiungerle o rimuoverle][2]

### Selezionare le aree

Quando si configurano due o più aree, è consigliabile che le aree vengano selezionate in base alle coppie di aree descritte nell'articolo [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure][bcdr].

In particolare, durante la configurazione di più aree, accertarsi di selezionare lo stesso numero di aree (+/-1 per pari/dispari) da ognuna delle colonne di aree abbinate. Ad esempio, se si desidera eseguire la distribuzione in 4 aree degli Stati Uniti, selezionare 2 aree degli Stati Uniti dalla colonna a sinistra e 2 dalla colonna destra. Il seguente set potrebbe risultare ideale: Stati Uniti occidentali, Stati Uniti orientali, Stati Uniti centro-settentrionali e Stati Uniti centro-meridionali.

È importante seguire questa guida quando sono configurate solo 2 aree per gli scenari di ripristino di emergenza. Per più di 2 aree, è consigliabile seguire questa guida, ma risulta non di vitale importanza dal momento che alcune delle aree selezionate sono conformi a questa associazione.

## <a id="selectwriteregion"></a>Selezionare l'area di scrittura

Mentre tutte le aree associate all'account di database di DocumentDB possono distribuire letture, con singolo elemento o impaginate con più elementi, e query, solo un'area può ricevere attivamente le richieste di scrittura (insert, upsert, replace, delete). Per impostare l'area di scrittura attiva, seguire questa procedura:


1. Nel pannello **Account DocumentDB** selezionare l'account di database da modificare.
2. Nel pannello dell'account, se il pannello **Tutte le impostazioni** non è già aperto, fare clic su **Tutte le impostazioni**.
3. Nel pannello **Tutte le impostazioni** fare clic su **Write Region Priority** (Priorità area di scrittura). ![Modificare l'area di scrittura in Account DocumentDB > Impostazioni > Aggiungi/Rimuovi aree][3]
4. Fare clic e trascinare le aree per ordinare il relativo elenco. La prima area nell'elenco delle aree è l'area di scrittura attiva. ![Modificare l'area di scrittura riordinando l'elenco delle aree in DocumentDB Account > Impostazioni > Change Write Regions (Modifica aree di scrittura)][4]

## <a id="keys"></a>Visualizzare, copiare e rigenerare le chiavi di accesso
Quando si crea un account DocumentDB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account DocumentDB. Fornendo due chiavi di accesso, DocumentDB consente di rigenerare le chiavi senza interruzioni dell'account DocumentDB.

Nel [portale di Microsoft Azure](https://portal.azure.com/) accedere al pannello **Chiavi** dalla barra **Informazioni di base** nel pannello **Account DocumentDB** per visualizzare, copiare e rigenerare le chiavi di accesso usate per accedere all'account DocumentDB.

![Schermata del portale di Azure, pannello delle chiavi](./media/documentdb-manage-account/keys.png)

È anche possibile accedere a **Chiavi** dal pannello **Tutte le impostazioni**.

![Tutte le impostazioni, pannello Chiavi](./media/documentdb-manage-account/allsettingskeys.png)

> [AZURE.NOTE] Il pannello **Chiavi** include anche le stringhe di connessione primaria e secondaria che possono essere usate per connettersi al proprio account dall'[Utilità di migrazione dati](documentdb-import-data.md).

Include inoltre le chiavi di sola lettura per concedere agli utenti l'accesso di sola lettura a DocumentDB. Lettura e query sono operazioni di sola lettura, a differenza di creazione, eliminazione e sostituzione.

### Visualizzare e copiare una chiave di accesso nel portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) accedere all'account DocumentDB.
2. Nella barra **Informazioni di base** dal pannello **Account di DocumentDB** fare clic su **Chiavi**.
3. Nel pannello **Chiavi** fare clic sul pulsante **Copia** a destra della chiave da copiare. ![Visualizzare e copiare una chiave di accesso nel portale di Azure, pannello delle chiavi](./media/documentdb-manage-account/copykeys.png)

### Per rigenerare le chiavi di accesso

È consigliabile modificare periodicamente le chiavi di accesso all'account DocumentDB per mantenere più sicure le connessioni. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account DocumentDB con una delle due chiavi mentre si rigenera l'altra.

> [AZURE.WARNING] La rigenerazione delle chiavi di accesso influisce su tutte le applicazioni che dipendono dalla chiave corrente. Per usare la nuova chiave è necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account DocumentDB.

Se si dispone di applicazioni o servizi cloud che usano l'account DocumentDB e si rigenerano le chiavi, si perderanno le connessioni, a meno che non si registrino le chiavi. I passaggi seguenti illustrano il processo necessario per la registrazione delle chiavi.

1. Aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla chiave di accesso secondaria dell'account DocumentDB.

2. Rigenerare la chiave di accesso primaria per l'account DocumentDB. Nel [portale di Azure](https://portal.azure.com/) accedere all'account DocumentDB.

3. Nella barra **Informazioni di base** dal pannello **Account di DocumentDB** fare clic su **Chiavi**.

4. Nel pannello **Keys** fare clic sul comando **Regenerate Primary** e quindi su **OK** per confermare che si vuole generare una nuova chiave.

5. Dopo aver verificato che la nuova chiave sia disponibile per l'utilizzo (circa 5 minuti dopo la rigenerazione), aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla nuova chiave di accesso primaria.

6. Rigenerare la chiave di accesso secondaria.


> [AZURE.NOTE] Potrebbero trascorrere diversi minuti prima che una chiave appena generata possa essere usata per accedere all'account DocumentDB.

## <a id="delete"></a> Eliminare un account DocumentDB
Per rimuovere un account DocumentDB non più in uso dal portale di Azure, utilizzare il comando **Elimina** nel pannello dell'**account DocumentDB**.

![Come eliminare un account di DocumentDB nel portale di Azure](./media/documentdb-manage-account/deleteaccountconfirmation.png)


1. Nel [portale di Azure](https://portal.azure.com/) accedere all'account DocumentDB che si desidera eliminare.
2. Nel pannello **Account DocumentDB** fare clic su **Elimina account**.
3. Nel pannello di conferma risultante, digitare il nome dell'account DocumentDB per confermarne l'eliminazione.
4. Fare clic sul pulsante **Elimina** nel pannello di conferma.

## <a id="next"></a>Passaggi successivi

Informazioni su come [iniziare a usare l'account DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Per altre informazioni su DocumentDB, vedere la documentazione relativa ad Azure DocumentDB in [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).


<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/it-IT/regions/#services
[offers]: https://azure.microsoft.com/it-IT/pricing/details/documentdb/

<!---HONumber=AcomDC_0727_2016-->