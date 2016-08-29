<properties 
	pageTitle="Download di Azure SDK per Java" 
	description="Informazioni su come scaricare SDK di Azure per Java, con il codice di esempio fornito per i progetti Maven e i passaggi dell’installazione di base per il Toolkit di Azure per Eclipse." 
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
	ms.date="08/11/2016" 
	ms.author="robmcm"/>

# Download di Azure SDK per Java

Questo articolo contiene le istruzioni per scaricare e installare le librerie di Azure per Java.

**Nota:** Le librerie di Azure per Java vengono distribuite con la [licenza Apache versione 2.0][license].

## Librerie di Azure per Java - Download manuale

Per installare manualmente le librerie di Azure per Java, fare clic su <http://go.microsoft.com/fwlink/?LinkId=690320> per scaricare un file ZIP che contiene tutte le librerie e tutte le dipendenze.

Dopo aver scaricato il file zip sul computer, estrarre il contenuto e usare una delle opzioni seguenti per aggiungere i file JAR al progetto:

* Importare i file JAR nel progetto Java in Eclipse.

* Configurare il **Percorso di compilazione** per il progetto Java in Eclipse per includere il percorso verso i file JAR.

Per informazioni dettagliate sull'impostazione dei percorsi di compilazione in Eclipse, vedere l’articolo [Java Build Path] sul sito Web Eclipse.

**Nota:** Per i dettagli sulla licenza e altre informazioni, vedere i file license.txt e ThirdPartyNotices.txt all'interno dell'archivio ZIP.

## Librerie di Azure per Java - Compilazione con Maven

### Passaggio 1: configurare il progetto per usare Maven per la compilazione

Per creare progetti Maven in Eclipse, che usano le librerie di Azure per Java, seguire le istruzioni presenti nell’articolo [Introduzione ad Azure Management Libraries per Java][maven-getting-started].

### Passaggio 2 - configurare le impostazioni di Maven con le dipendenze necessarie

Una volta che il progetto è stato configurato per l’uso di Maven per la compilazione, è possibile aggiungere le dipendenze necessarie al file pom.xml usando una sintassi simile a quella dell’esempio seguente. Notare che non è necessario aggiungere tutte le dipendenze elencate nel seguente esempio; è sufficiente aggiungere le dipendenze specifiche richieste dal progetto.

> [AZURE.NOTE] All'interno di ogni elemento `<version>` nell'esempio seguente, sostituire i segnaposto "n.n.n" di questo esempio con numeri di versione validi, che possono essere ottenuti dall'[archivio delle librerie di Azure su Maven].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## Installare il Toolkit di Azure per Eclipse.

Questa sezione contiene istruzioni di base per l'installazione del Toolkit di Azure per Eclipse; per istruzioni dettagliate, vedere l’[Installazione del Toolkit di Azure per Eclipse].

### Prerequisiti

1. I sistemi operativi Windows elencati nell’articolo [Novità del Toolkit di Azure per Eclipse].
1. I sistemi operativi Macintosh o Linux elencati nell’articolo [Novità del Toolkit di Azure per Eclipse].
1. IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>.

### Procedura dell’installazione di base

1. Dal menu **Help** di Eclipse, scegliere **Install New Software**.
1. Immettere il percorso del sito <http://dl.microsoft.com/eclipse> e premere **INVIO**.
1. Selezionare gli elementi da installare e fare clic su **Finish**.

Il Toolkit di Azure per Eclipse usa l'ultima versione dell’SDK di Azure. Questa può essere scaricata utilizzando Installazione guidata piattaforma Web (WebPI) all'indirizzo <http://go.microsoft.com/fwlink/?LinkID=252838>. Se tale piattaforma non è installata, tuttavia, quando si crea il primo progetto di distribuzione di Azure, il Toolkit di Azure per Eclipse installerà automaticamente la versione appropriata di Azure SDK.

## Vedere anche

[Toolkit di Azure per Eclipse]

[Installare il Toolkit di Azure per Eclipse.]

[Creare un'applicazione Hello World per Azure in Eclipse]

Per altre informazioni su come usare Azure con Java, visitare il [Centro per sviluppatori Java di Azure].

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[archivio delle librerie di Azure su Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creare un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546
[Installazione del Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Novità del Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333

<!---HONumber=AcomDC_0817_2016-->