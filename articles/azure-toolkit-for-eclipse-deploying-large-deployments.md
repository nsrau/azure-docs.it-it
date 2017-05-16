---
title: Distribuire distribuzioni di grandi dimensioni
description: Informazioni su come distribuire le distribuzioni di grandi dimensioni usando il Toolkit di Azure per Eclipse.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5e18bace-5df0-4af8-ad86-6151ea8bd823
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: d6b2b28a7e01d5a70cd08f4cd2d8cccff7d918cf
ms.lasthandoff: 01/05/2017


---
# <a name="deploying-large-deployments"></a>Distribuire distribuzioni di grandi dimensioni
Se la distribuzione è troppo grande per essere contenuta nella cartella approot predefinita, è possibile usare una risorsa di archiviazione locale come la cartella radice di distribuzione per il JDK e il server dell'applicazione.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Per utilizzare una risorsa di archiviazione locale come la cartella radice di distribuzione per distribuzioni di grandi dimensioni
1. Creare una nuova una risorsa di archiviazione locale Il nome della risorsa non è rilevante. Le risorse di archiviazione sono definite a livello di ruolo. Il modo più rapido per accedere alla finestra di dialogo di configurazione dell'archiviazione locale, da cui è possibile creare una nuova risorsa di archiviazione locale, è quello di usare la procedura seguente: fare clic con il pulsante destro del mouse sul ruolo nella visualizzazione **Project Explorer** (Esplora progetti), espandendo il nodo del progetto di Azure se non viene visualizzato il ruolo, quindi fare clic su **Azure** e su **Archiviazione locale**. Nella finestra di dialogo **Archiviazione locale** fare clic su **Aggiungi** per creare una nuova risorsa di archiviazione locale.

2. Impostare le dimensioni desiderate ad almeno 2048 MB (qualunque valore inferiore potrebbe causare gli stessi problemi di dimensioni del file che si riscontrerebbero nella cartella approot).

3. Assicurarsi di selezionare **Pulire i contenuti quando l'istanza del ruolo viene riciclata** ; ciò impedirà alla logica di avvio della distribuzione di imbattersi in conflitti con i file esistenti nella risorsa quando l'istanza del ruolo viene riciclata.

4. Assicurarsi che il valore di **Environment variable storing the resource's directory path after deployment** (Variabile di ambiente per l'archiviazione del percorso della directory della risorsa dopo la distribuzione) sia impostato sulla stringa **DEPLOYROOT**. La finestra di dialogo della risorsa di archiviazione locale sarà simile a quella seguente.

   ![][ic667943]

In alternativa, se si usa **DEPLOYROOT** come *nome* della risorsa locale e non si modifica il nome della variabile di ambiente generata automaticamente (che verrà impostato su **DEPLOYROOT_PATH** in questo caso), questo nome andrà bene anche per l'applicazione.

Altre informazioni sulla creazione di una risorsa di archiviazione locale sono reperibili in [Proprietà dell'archiviazione locale][Local storage properties].

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
[Local storage properties]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

