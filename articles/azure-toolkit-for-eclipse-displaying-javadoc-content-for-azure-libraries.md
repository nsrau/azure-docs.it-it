---
title: Visualizzare il contenuto Javadoc in Eclipse per il pacchetto di librerie di Azure per Java
description: Come visualizzare il contenuto Javadoc per le librerie di Azure in Eclipse.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 30f8b6a1-1d76-4d1c-861b-1db478c46e6b
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: d52054de2ac5119db39e1cd3c927a790db7524aa


---
# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Visualizzare il contenuto Javadoc in Eclipse per il pacchetto di librerie di Azure per Java
Il contenuto Javadoc per le librerie di Azure per Java può essere visualizzato nell'ambiente Eclipse associando il contenuto Javadoc per le librerie di Azure per Java. La procedura seguente mostra come usare questa funzionalità in Eclipse.

Questa procedura presuppone che la libreria di Azure per Java sia già stata aggiunta al percorso di compilazione.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Visualizzare il contenuto Javadoc in Eclipse per le librerie di Azure per Java
* In Project Explorer di Eclipse, nella sezione del progetto **Librerie a cui si fa riferimento** , aprire il menu di scelta rapida per la libreria di Azure per Java JAR. Ad esempio, **microsoft-windowsazure-api-0.1.0.jar** (il numero della versione può essere diverso, a seconda della versione installata).
* Fare clic su **Proprietà**.
* Nella finestra di dialogo **Properties** (Proprietà) nel riquadro sinistro, fare clic su **Javadoc Location** (Posizione Javadoc). Viene visualizzata la finestra di dialogo **Posizione Javadoc** .
* È possibile specificare un **URL Javadoc** o un **Javadoc nell'archivio**.
  * Se si sceglie di specificare un **URL Javadoc**, usare URL simili a **http://dl.windowsazure.com/javadoc** o **http://dl.windowsazure.com/storage/javadoc**.
  * Se si sceglie di utilizzare **Javadoc nell'archivio**, è possibile specificare un file esterno o un file dell’area di lavoro.
    Effettuare la selezione e sfogliare/convalidare in base alle esigenze. L'esempio seguente associa le librerie di Azure per Java con il corrispondente file JAR di Javadoc scaricato localmente in una cartella denominata **c:\MyAzureJARs**.
    ![][ic553487]
* *Facoltativo*: fare clic su **Convalidare**. Potrebbero essere visualizzati dei potenziali problemi con il file JAR di Javadoc.
* Fare clic su **OK**.

Una volta associato alla libreria, il contenuto Javadoc deve essere visualizzato nell'IDE di Eclipse. Ad esempio, se `blob` è definito come tipo `CloudBlockBlob` nel codice, quello che segue è un esempio del contenuto Javadoc che viene visualizzato quando si digita `blob.acquireLease` nel codice:

![][ic553488]

## <a name="see-also"></a>Vedere anche
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Creare un'applicazione Hello World per Azure in Eclipse][Creating a Hello World Application for Azure in Eclipse]

[Installazione di Azure Toolkit for Eclipse][Installing the Azure Toolkit for Eclipse] 

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->



<!--HONumber=Jan17_HO1-->


