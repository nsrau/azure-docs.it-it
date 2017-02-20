---
title: Gestire un account DocumentDB con il portale di Azure | Documentazione Microsoft
description: Informazioni su come gestire il proprio account DocumentDB tramite il portale di Azure. Trovare una guida sull&quot;uso del portale di Azure per visualizzare, copiare, eliminare e accedere agli account.
keywords: Portale di Azure, documentdb, azure, Microsoft azure
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: e82d677c9d53ec5d28a40a7def4b911081765ca5
ms.openlocfilehash: 0a249d63df35f9c3334a658672777e9b1359265e


---
# <a name="how-to-manage-a-documentdb-account"></a>Come gestire un account DocumentDB
Informazioni su come impostare la coerenza globale, usare le chiavi ed eliminare un account DocumentDB nel portale di Azure.

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>Gestire le impostazioni di coerenza di DocumentDB
La scelta del livello di coerenza giusto dipende dalla semantica dell'applicazione. È consigliabile acquisire familiarità con i livelli di coerenza disponibili in DocumentDB leggendo [Utilizzo dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni di DocumentDB][consistency]. DocumentDB offre garanzia su coerenza, disponibilità e prestazioni a ogni livello di coerenza disponibile per l'account di database. La configurazione dell'account di database con un forte livello di coerenza richiede che i dati vengano limitati a una singola area di Azure, senza disponibilità globale. D'altra parte, i livelli di coerenza ampi (obsolescenza associata, sessione o finale) consentono di associare tutte le aree di Azure desiderate con l'account di database. La semplice procedura seguente mostra come selezionare il livello di coerenza predefinito per l'account di database. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>Per specificare la coerenza predefinita per un account DocumentDB
1. Nel [portale di Azure](https://portal.azure.com/)accedere all'account DocumentDB.
2. Nel pannello dell'account fare clic su **Coerenza predefinita**.
3. Nel pannello **Default Consistency** (Uniformità predefinita) selezionare il nuovo livello di coerenza e fare clic su **Salva**.
    ![Sessione Coerenza predefinita][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>Visualizzare, copiare e rigenerare le chiavi di accesso
Quando si crea un account DocumentDB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account DocumentDB. Fornendo due chiavi di accesso, DocumentDB consente di rigenerare le chiavi senza interruzioni dell'account DocumentDB. 

Nel [Portale di Azure](https://portal.azure.com/) accedere al pannello **Keys** (Chiavi) dal menu delle risorse nel pannello **DocumentDB account** (Account DocumentDB) per visualizzare, copiare e rigenerare le chiavi di accesso usate per accedere all'account DocumentDB.

![Schermata del portale di Azure, pannello delle chiavi](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> Il pannello **Chiavi** include anche le stringhe di connessione primaria e secondaria che possono essere usate per connettersi al proprio account dall' [Utilità di migrazione dati](documentdb-import-data.md).
> 
> 

In questo pannello sono anche disponibili chiavi di sola lettura. Lettura e query sono operazioni di sola lettura, a differenza di creazione, eliminazione e sostituzione.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copiare una chiave di accesso nel portale di Azure
Nel pannello **Keys** (Chiavi) fare clic sul pulsante **Copy** (Copia) a destra della chiave da copiare.

![Visualizzare e copiare una chiave di accesso nel portale di Azure, pannello delle chiavi](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso
È consigliabile modificare periodicamente le chiavi di accesso all'account DocumentDB per mantenere più sicure le connessioni. Vengono assegnate due chiavi di accesso per consentire di mantenere le connessioni all'account DocumentDB con una delle due chiavi mentre si rigenera l'altra.

> [!WARNING]
> La rigenerazione delle chiavi di accesso influisce su tutte le applicazioni che dipendono dalla chiave corrente. Per usare la nuova chiave è necessario aggiornare tutti i client che usano la chiave di accesso per accedere all'account DocumentDB.
> 
> 

Se si dispone di applicazioni o servizi cloud che usano l'account DocumentDB e si rigenerano le chiavi, si perderanno le connessioni, a meno che non si registrino le chiavi. I passaggi seguenti illustrano il processo necessario per la registrazione delle chiavi.

1. Aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla chiave di accesso secondaria dell'account DocumentDB.
2. Rigenerare la chiave di accesso primaria per l'account DocumentDB. Nel [portale di Azure](https://portal.azure.com/)accedere all'account DocumentDB.
3. Nel pannello **DocumentDB Account** (Account DocumentDB) fare clic su **Keys** (Chiavi).
4. Nel pannello **Keys** (Chiavi) fare clic sul comando per rigenerare e quindi su **OK** per confermare che si vuole generare una nuova chiave.
    ![Per rigenerare le chiavi di accesso](./media/documentdb-manage-account/regenerate-keys.png)
5. Dopo aver verificato che la nuova chiave sia disponibile per l'utilizzo (circa 5 minuti dopo la rigenerazione), aggiornare la chiave di accesso nel codice dell'applicazione in modo che faccia riferimento alla nuova chiave di accesso primaria.
6. Rigenerare la chiave di accesso secondaria.
   
    ![Per rigenerare le chiavi di accesso](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Potrebbero trascorrere diversi minuti prima che una chiave appena generata possa essere usata per accedere all'account DocumentDB.
> 
> 

## <a name="get-the--connection-string"></a>Ottenere la stringa di connessione
Per recuperare la stringa di connessione, eseguire le operazioni seguenti: 

1. Nel [Portale di Azure](https://portal.azure.com) accedere all'account DocumentDB.
2. Nel menu delle risorse fare clic su **Keys** (Chiavi).
3. Fare clic sul pulsante **Copy** (Copia) accanto alla casella **Primary Connection String** (Stringa di connessione primaria) o **Secondary Connection String** (stringa di connessione secondaria). 

Se si utilizza la stringa di connessione nello [strumento di migrazione del database di DocumentDB](documentdb-import-data.md), aggiungere il nome del database alla fine della stringa di connessione. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> Eliminare un account DocumentDB
Per rimuovere un account DocumentDB non più usato dal portale di Azure, fare clic sul nome dell'account e quindi su **Elimina account**.

![Come eliminare un account di DocumentDB nel portale di Azure](./media/documentdb-manage-account/deleteaccount.png)

1. Nel [portale di Azure](https://portal.azure.com/)accedere all'account DocumentDB da eliminare.
2. Nel pannello **Account DocumentDB** fare clic con il pulsante destro del mouse sull'account e quindi su **Elimina account**. 
3. Nel pannello di conferma risultante digitare il nome dell'account DocumentDB per confermarne l'eliminazione.
4. Fare clic sul pulsante **Elimina** .

![Come eliminare un account di DocumentDB nel portale di Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>Passaggi successivi
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
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Feb17_HO1-->


