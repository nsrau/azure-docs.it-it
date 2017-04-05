---
title: Aggiungere spazio di archiviazione di Azure mediante Servizi connessi in Visual Studio | Documentazione Microsoft
description: Aggiungere archiviazione di Azure all&quot;applicazione usando la finestra di dialogo Aggiungi servizi connessi di Visual Studio
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 521ec044-ad4b-4828-8864-01decde2e758
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 614f212bb9afb71bb432bec3df7201807083b7f5
ms.lasthandoff: 03/27/2017


---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Aggiunta dell’archiviazione di Azure tramite Servizi connessi di Visual Studio
Con Visual Studio è possibile connettere uno dei servizi seguenti ad Archiviazione di Azure tramite la finestra di dialogo **Add Connected Services** (Aggiungi servizi connessi):

- Servizio cloud C#
- Servizio mobile back-end .NET
- Sito Web ASP.NET
- Servizio ASP.NET Core
- Servizio Processo Web di Azure 

La funzionalità servizio connesso aggiunge al progetto tutti i riferimenti richiesti e il codice di connessione e modifica i file di configurazione in modo appropriato. 

Dopo il completamento, la finestra di dialogo **Add Connected Services** (Aggiungi servizi connessi) visualizza automaticamente la documentazione che illustra in dettaglio i passaggi necessari per iniziare a lavorare con l'archiviazione BLOB, le code e le tabelle.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Connettersi ad archiviazione di Azure usando la finestra di dialogo Servizi connessi
1. Aprire il progetto in Visual Studio

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo **Servizi connessi** e scegliere **Add Connected Services** (Aggiungi servizi connessi) dal menu di scelta rapida.
   
    ![Aggiunta di un servizio connesso di Azure](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Nella pagina **Servizi connessi** selezionare **Archiviazione cloud con Archiviazione di Azure**.
   
    ![Aggiungi Archiviazione di Azure](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. Nella finestra di dialogo **Archiviazione di Azure** selezionare un account di archiviazione esistente, quindi **Aggiungi**.
   
    Se è necessario creare un account di archiviazione, andare al passaggio successivo. In caso contrario, andare direttamente al passaggio 6.
    
    ![Aggiungere un account di archiviazione esistente al progetto l'elenco](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Per creare un account di archiviazione: 
   
   1. Nella parte inferiore della finestra di dialogo fare clic su **Crea un nuovo account di archiviazione**.

   1. Compilare la finestra di dialogo **Crea account di archiviazione** e selezionare **Crea**.
      
       ![Nuovo account di archiviazione di Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. Quando viene visualizzata la finestra di dialogo **Archiviazione di Azure**, la nuova risorsa di archiviazione viene visualizzata nell'elenco. Selezionare la nuova risorsa di archiviazione nell'elenco e quindi **Aggiungi**.

1. Il servizio connesso di archiviazione viene visualizzato sotto il nodo **Riferimenti servizio** del progetto.
   
## <a name="how-your-project-is-modified"></a>Come viene modificato il progetto
Una volta terminata la finestra di dialogo, Visual Studio aggiunge riferimenti e modifica di alcuni file di configurazione. Le modifiche specifiche dipendono dal tipo di progetto. 

- Progetti ASP.NET - [Risultati – Progetti ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126).
- Progetti ASP.NET 5 - [Risultati – Progetti ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
- Progetti del servizio cloud (ruoli Web e ruoli di lavoro) [Risultati - Progetti del servizio cloud](http://go.microsoft.com/fwlink/p/?LinkId=516965).
- Progetti di tipo processo Web - [Risultati - Progetti di tipo processo Web](storage/vs-storage-webjobs-what-happened.md).

## <a name="next-steps"></a>Passaggi successivi
- [Forum MSDN: Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Documentazione di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/)

