<properties title="How to Create a DocumentDB Account" pageTitle="How to create a DocumentDB account | Azure" description="Find out how to create a DocumentDB account and choose account settings in the Azure Preview portal."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Come creare un account DocumentDB

Per usare DocumentDB è necessario creare un account DocumentDB in un'area geografica disponibile. In questo argomento viene descritto come creare un account DocumentDB nel portale di anteprima di Azure.

## Sommario

-   [Procedura: Creare un account DocumentDB][Procedura: Creare un account DocumentDB]
-   [Passaggi successivi][Passaggi successivi]

## <span id="HowTo"></span></a>Procedura: Creare un account DocumentDB

1.  Accedere al [portale di anteprima di gestione di Azure][portale di anteprima di gestione di Azure]
2.  Fare clic su Nuovo -\> Account DocumentDB.
    ![][0]

    In alternativa, sfogliare la Raccolta di Azure, selezionare la categoria "Dati, archiviazione, cache e backup", scegliere **DocumentDB** e fare clic su **Crea**.

    ![][1]

    Nel pannello **Nuovo DocumentDB (anteprima)** specificare la configurazione desiderata per l'account DocumentDB.

    ![][2]

    In Nome immettere il nome da usare nell'URI per l'account DocumentDB. Questo valore diventerà il nome host all'interno dell'URI usato per fare riferimento all'account DocumentDB. Il nome deve avere una lunghezza compresa tra 3 e 50 caratteri e può contenere solo lettere minuscole, numeri e il carattere '-'.

    *Notare che al nome di endpoint scelto verrà aggiunto documents.azure.com e il risultato finale sarà l'endpoint dell'account DocumentDB.*

    La sezione **Livello di prezzo** è bloccata, perché l'anteprima di DocumentDB supporta un unico livello di prezzo standard.

    *Per altre informazioni, vedere i [dettagli sui prezzi di DocumentDB][dettagli sui prezzi di DocumentDB]*

    La sezione **Configurazione facoltativa** consente di specificare la capacità iniziale allocata all'account DocumentDB. DocumentDB viene offerto in unità di capacità, che consentono di scalare l'account DocumentDB in base alle esigenze. Ogni unità di capacità include velocità effettiva e spazio di archiviazione di database riservato. Per impostazione predefinita, viene effettuato il provisioning di 1 unità di capacità. È possibile modificare il numero di unità di capacità disponibili per l'account DocumentDB in qualsiasi momento mediante il [portale di anteprima di gestione di Azure][portale di anteprima di gestione di Azure].

    *Per informazioni sulla capacità e la velocità effettiva dell'account DocumentDB, vedere l'articolo [Gestire la capacità e le prestazioni di DocumentDB][Gestire la capacità e le prestazioni di DocumentDB].*

    In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account DocumentDB. Per impostazione predefinita, verrà creato un nuovo gruppo di risorse. È tuttavia possibile selezionare un gruppo di risorse esistente al quale aggiungere l'account DocumentDB.

    *Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure][Uso di Gruppi di risorse per gestire le risorse di Azure].*

    Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per l'account DocumentDB.

    *Se l'account dispone di una sola sottoscrizione, verrà selezionata automaticamente.*

    Usare **Località** per specificare la posizione geografica in cui verrà ospitato l'account DocumentDB.

3.  Dopo aver configurato le opzioni del nuovo account DocumentDB, fare clic su **Crea**. La creazione dell'account DocumentDB può richiedere alcuni minuti. Per controllare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale.
    ![][3]

    Oppure dall'hub Notifiche.

    ![][4]

    ![][5]

4.  Dopo la creazione, l'account DocumentDB è pronto all'uso con le impostazioni predefinite.

    *Notare che la coerenza predefinita dell'account DocumentDB sarà impostata su Sessione. È possibile modificare le impostazioni predefinite relative alla coerenza nel [portale di anteprima di gestione di Azure][portale di anteprima di gestione di Azure].*
    ![][6]

5.  È anche possibile accedere agli account DocumentDB esistenti dal pannello **Sfoglia**.
    ![][7]

## <span id="NextSteps"></span></a>Passaggi successivi

-   Per altre informazioni su DocumentDB, vedere la documentazione su Azure DocumentDB su [azure.com][azure.com]

<!--Image references-->

  [Procedura: Creare un account DocumentDB]: #Howto
  [Passaggi successivi]: #NextSteps
  [portale di anteprima di gestione di Azure]: https://portal.azure.com/
  [0]: ./media/documentdb-create-account/ca1.png
  [1]: ./media/documentdb-create-account/ca2.png
  [2]: ./media/documentdb-create-account/ca3.png
  [dettagli sui prezzi di DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409
  [Gestire la capacità e le prestazioni di DocumentDB]: ../documentdb-manage/
  [Uso di Gruppi di risorse per gestire le risorse di Azure]: http://azure.microsoft.com/it-it/documentation/articles/azure-preview-portal-using-resource-groups/
  [3]: ./media/documentdb-create-account/ca4.png
  [4]: ./media/documentdb-create-account/ca5.png
  [5]: ./media/documentdb-create-account/ca6.png
  [6]: ./media/documentdb-create-account/ca7.png
  [7]: ./media/documentdb-create-account/ca8.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
