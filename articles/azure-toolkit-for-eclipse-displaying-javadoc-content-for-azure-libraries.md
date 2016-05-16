<properties
    pageTitle="Visualizzare il contenuto Javadoc in Eclipse per il pacchetto di librerie di Azure per Java"
    description="Come visualizzare il contenuto Javadoc per le librerie di Azure in Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# Visualizzare il contenuto Javadoc in Eclipse per il pacchetto di librerie di Azure per Java #

Il contenuto Javadoc per le librerie di Azure per Java può essere visualizzato nell'ambiente Eclipse associando il contenuto Javadoc per le librerie di Azure per Java. La procedura seguente mostra come usare questa funzionalità in Eclipse.

Questa procedura presuppone che la libreria di Azure per Java sia già stata aggiunta al percorso di compilazione.

## Visualizzare il contenuto Javadoc in Eclipse per le librerie di Azure per Java ##

* In Project Explorer di Eclipse, nella sezione del progetto **Librerie a cui si fa riferimento**, aprire il menu di scelta rapida per la libreria di Azure per Java JAR. Ad esempio, **microsoft-windowsazure-api-0.1.0.jar** (il numero della versione può essere diverso, a seconda della versione installata).
* Fare clic su **Proprietà**.
* Nella finestra di dialogo **Proprietà** nel riquadro sinistro, fare clic su **Posizione Javacod**. Viene visualizzata la finestra di dialogo **Posizione Javadoc**.
* È possibile specificare un **URL Javadoc**, o un **Javadoc nell'archivio**.
    * Se si sceglie di specificare un’**URL Javadoc**, usare gli URL come **http://dl.windowsazure.com/javadoc** o **http://dl.windowsazure.com/storage/javadoc**.
* Se si sceglie di utilizzare **Javadoc nell'archivio**, è possibile specificare un file esterno o un file dell’area di lavoro. Effettuare la selezione e sfogliare/convalidare in base alle esigenze. L'esempio seguente associa le Librerie di Azure per Java con il corrispondente file JAR di Javadoc scaricato localmente in una cartella denominata **c:\\MyAzureJARs**. ![][ic553487]
* *Facoltativo*: fare clic su **Convalidare**. Potrebbero essere visualizzati dei potenziali problemi con il file JAR di Javadoc.
* Fare clic su **OK**.

Una volta associato alla libreria, il contenuto Javadoc deve essere visualizzato nell'IDE di Eclipse. Ad esempio, se `blob` è definito come tipo `CloudBlockBlob` nel codice, quello che segue è un esempio del contenuto Javadoc che viene visualizzato quando si digita `blob.acquireLease` nel codice:

![][ic553488]

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

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!---HONumber=AcomDC_0504_2016-->