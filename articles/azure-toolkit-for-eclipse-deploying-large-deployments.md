<properties
    pageTitle="Distribuire distribuzioni di grandi dimensioni"
    description="Informazioni su come distribuire le distribuzioni di grandi dimensioni usando il Toolkit di Azure per Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="05/04/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# Distribuire distribuzioni di grandi dimensioni #

Se la distribuzione è troppo grande per essere contenuta nella cartella approot predefinita, è possibile usare una risorsa di archiviazione locale come la cartella radice di distribuzione per il JDK e il server dell’applicazione.

## Per utilizzare una risorsa di archiviazione locale come la cartella radice di distribuzione per distribuzioni di grandi dimensioni ##

1. Creare una nuova una risorsa di archiviazione locale Il nome della risorsa non è rilevante. Le risorse di archiviazione sono definite a livello di ruolo. Il modo più rapido per avere accesso alla finestra di dialogo di configurazione dell’archiviazione locale, da cui è possibile creare una nuova risorsa di archiviazione locale, è quello di accedere utilizzando la procedura seguente: fare clic con il tasto destro sul ruolo nella visualizzazione **Project Explorer** (espandere il nodo del progetto di Azure se non viene visualizzato il ruolo), fare clic su **Azure**, quindi fare clic su **Archiviazione locale**. Nella finestra di dialogo **Archiviazione locale**, fare clic su **Aggiungi** per creare una nuova risorsa di archiviazione locale.
1. Impostare le dimensioni desiderate ad almeno 2048 MB (qualunque valore inferiore potrebbe causare gli stessi problemi di dimensioni del file che si riscontrerebbero nella cartella approot).
1. Assicurarsi di selezionare **Pulire i contenuti quando l'istanza del ruolo viene riciclata**; ciò impedirà alla logica di avvio della distribuzione di imbattersi in conflitti con i file esistenti nella risorsa quando l'istanza del ruolo viene riciclata.
1. Assicurarsi che il valore **Variabile di ambiente che archivia il percorso della directory della risorsa dopo la distribuzione** sia impostato sulla stringa **DEPLOYROOT**. La finestra di dialogo della risorsa di archiviazione locale sarà simile a quella seguente. ![][ic667943]

In alternativa, se si utilizza **DEPLOYROOT** come *nome* della risorsa locale e non si modifica il nome della variabile di ambiente generata automaticamente (che verrà impostato su **DEPLOYROOT\_PATH** in questo caso), anche questo nome andrà bene per l’applicazione.

Altre informazioni sulla creazione di una risorsa di archiviazione locale sono reperibili in [Proprietà dell’archiviazione locale][].

## Vedere anche ##

[Toolkit di Azure per Eclipse][]

[Creazione di un'applicazione Hello World per Azure in Eclipse][]

[Installare il Toolkit di Azure per Eclipse.][]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][].

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546
[Proprietà dell’archiviazione locale]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!---HONumber=AcomDC_0504_2016-->