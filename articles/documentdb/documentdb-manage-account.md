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
	ms.date="08/15/2016"
	ms.author="anhoh"/>

# Come gestire un account DocumentDB

Informazioni su come impostare la coerenza globale, usare le chiavi ed eliminare un account DocumentDB nel portale di Azure.

## <a id="consistency"></a>Gestire le impostazioni di coerenza di DocumentDB

La scelta del livello di coerenza giusto dipende dalla semantica dell'applicazione. È consigliabile acquisire familiarità con i livelli di coerenza disponibili in DocumentDB: [Utilizzo dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni di DocumentDB][consistency]. DocumentDB offre garanzia su coerenza, disponibilità e prestazioni a ogni livello di coerenza disponibile per l'account di database. La configurazione dell'account di database con un forte livello di coerenza richiede che i dati vengano limitati a una singola area di Azure, senza disponibilità globale. D'altra parte, i livelli di coerenza ampi (obsolescenza associata, sessione o finale) consentono di associare tutte le aree di Azure desiderate con l'account di database. La semplice procedura seguente mostra come selezionare il livello di coerenza predefinito per l'account di database.

### Per specificare la coerenza predefinita per un account DocumentDB

1. Nel [portale di Azure](https://portal.azure.com/) accedere all'account DocumentDB.
2. Nel pannello dell'account fare clic su **Coerenza predefinita**.
3. Nel pannello **Coerenza predefinita** selezionare il nuovo livello di coerenza e fare clic su **OK**. ![Sessione coerenza predefinita][5]

## <a id="keys"></a>Visualizzare, copiare e rigenerare le chiavi di accesso
Quando si crea un account DocumentDB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account DocumentDB. Fornendo due chiavi di accesso, DocumentDB consente di rigenerare le chiavi senza interruzioni dell'account DocumentDB.

Nel [portale di Microsoft Azure](https://portal.azure.com/) accedere al pannello **Chiavi** dal pannello **Account DocumentDB** per visualizzare, copiare e rigenerare le chiavi di accesso usate per accedere all'account DocumentDB.

![Schermata del portale di Azure, pannello delle chiavi](./media/documentdb-manage-account/keys.png)

> [AZURE.NOTE] Il pannello **Chiavi** include anche le stringhe di connessione primaria e secondaria che possono essere usate per connettersi al proprio account dall'[Utilità di migrazione dati](documentdb-import-data.md).

In questo pannello sono anche disponibili chiavi di sola lettura. Lettura e query sono operazioni di sola lettura, a differenza di creazione, eliminazione e sostituzione.

### Visualizzare e copiare una chiave di accesso nel portale di Azure

Nel pannello **Keys** fare clic sul pulsante **Copy** a destra della chiave da copiare.

![Visualizzare e copiare una chiave di accesso nel portale di Azure, pannello delle chiavi](./media/documentdb-manage-account/copykeys.png)

### Per rigenerare le chiavi di accesso

È consigliabile modificare periodicamente le chiavi di accesso all'account DocumentDB per mantenere più sicure le connessioni. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account DocumentDB con una delle due chiavi mentre si rigenera l'altra.

> [AZURE.WARNING] La rigenerazione delle chiavi di accesso influisce su tutte le applicazioni che dipendono dalla chiave corrente. Per usare la nuova chiave è necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account DocumentDB.

Se si dispone di applicazioni o servizi cloud che usano l'account DocumentDB e si rigenerano le chiavi, si perderanno le connessioni, a meno che non si registrino le chiavi. I passaggi seguenti illustrano il processo necessario per la registrazione delle chiavi.

1. Aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla chiave di accesso secondaria dell'account DocumentDB.
2. Rigenerare la chiave di accesso primaria per l'account DocumentDB. Nel [portale di Azure](https://portal.azure.com/) accedere all'account DocumentDB.
3. Nel pannello **Account DocumentDB** fare clic su **Chiavi**.
4. Nel pannello **Chiavi** fare clic sul comando **Rigenera primaria** e quindi su **OK** per confermare che si vuole generare una nuova chiave. ![Per rigenerare le chiavi di accesso](./media/documentdb-manage-account/regenerate-keys.png)

5. Dopo aver verificato che la nuova chiave sia disponibile per l'utilizzo (circa 5 minuti dopo la rigenerazione), aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla nuova chiave di accesso primaria.
6. Rigenerare la chiave di accesso secondaria. ![Per rigenerare le chiavi di accesso](./media/documentdb-manage-account/regenerate-secondary-key.png)


> [AZURE.NOTE] Potrebbero trascorrere diversi minuti prima che una chiave appena generata possa essere usata per accedere all'account DocumentDB.

## <a id="delete"></a> Eliminare un account DocumentDB
Per rimuovere un account DocumentDB non più usato dal portale di Azure, usare il comando **Elimina account** nel pannello **Account DocumentDB**.

![Come eliminare un account di DocumentDB nel portale di Azure](./media/documentdb-manage-account/deleteaccount.png)


1. Nel [portale di Azure](https://portal.azure.com/) accedere all'account DocumentDB che si desidera eliminare.
2. Nel pannello **Account DocumentDB** fare clic su **Altro** e quindi su **Elimina account**. In alternativa, fare clic con il pulsante destro del mouse sul nome del database e selezionare **Elimina account**.
3. Nel pannello di conferma risultante, digitare il nome dell'account DocumentDB per confermarne l'eliminazione.
4. Fare clic sul pulsante **Elimina**.

![Come eliminare un account di DocumentDB nel portale di Azure](./media/documentdb-manage-account/delete-account-confirm.png)

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
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/

<!---HONumber=AcomDC_0817_2016-->