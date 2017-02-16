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
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c7ca2909de28936f17ab7ac03815d693032b04f


---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Aggiunta dell’archiviazione di Azure tramite Servizi connessi di Visual Studio
## <a name="overview"></a>Overview
Con Visual Studio 2015, è possibile connettersi a qualsiasi servizio cloud in C#, servizio mobile back-end .NET, sito Web o servizio ASP.NET, servizio ASP.NET 5 o servizio di Processo Web di Azure in archiviazione di Azure tramite la finestra di dialogo **Aggiungi servizi connessi** . La funzionalità servizio connesso aggiunge tutti i riferimenti richiesti e il codice di connessione e modifica i file di configurazione in modo appropriato. La finestra di dialogo visualizza inoltre la documentazione che indica i passaggi successivi per avviare l'archiviazione BLOB, code e tabelle.

## <a name="supported-project-types"></a>Tipi di progetto supportati
Per connettersi ad archiviazione di Azure nei seguenti tipi di progetto, è possibile usare la finestra di dialogo Servizi connessi.

* Progetti Web ASP.NET
* Progetti ASP.NET 5
* Progetti del ruolo Web del servizio cloud .NET e del ruolo di lavoro
* Progetti di servizi mobili .NET
* Progetti di Processo Web di Azure

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Connettersi ad archiviazione di Azure usando la finestra di dialogo Servizi connessi
1. È necessario disporre di un account Azure. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://go.microsoft.com/fwlink/?LinkId=518146). Dopo aver creato un account Azure, è possibile creare account di archiviazione, creare servizi mobili e configurare Azure Active Directory.
2. Aprire il progetto in Visual Studio, aprire il menu di scelta rapida per il nodo **Riferimenti** in Esplora soluzioni, quindi scegliere **Aggiungi servizio connesso**.
   
    ![Aggiunta di un servizio connesso](./media/vs-azure-tools-connected-services-storage/IC796702.png)
3. Nella finestra di dialogo **Aggiungi servizio connesso** scegliere **Account di archiviazione di Azure**, quindi fare clic su **Configura**. Se non è già stato effettuato, potrebbe essere richiesto l'accesso ad Azure.
   
    ![Finestra di dialogo Aggiungi servizio connesso - Archiviazione](./media/vs-azure-tools-connected-services-storage/IC796703.png)
4. Nella finestra di dialogo **Archiviazione di Azure** selezionare un account di archiviazione esistente, quindi **Aggiungi**.
   
    Se è necessario creare un nuovo account di archiviazione, andare al passaggio successivo. In caso contrario, andare direttamente al passaggio 6.
   
    ![Finestra di dialogo Archiviazione di Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)
5. Per creare un nuovo account di archiviazione: 
   
   1. Scegliere il pulsante **Crea un nuovo Account di archiviazione** nella parte inferiore della finestra di dialogo Archiviazione di Azure.
   2. Compilare la finestra di dialogo **Crea account di archiviazione**, quindi scegliere il pulsante **Crea**.
      
       ![Finestra di dialogo Archiviazione di Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
       Quando si torna nella finestra di dialogo **Archiviazione di Azure** , la nuova risorsa di archiviazione viene visualizzata nell'elenco.
   3. Selezionare la nuova risorsa di archiviazione nell'elenco e selezionare **Aggiungi**.
6. Il servizio connesso di archiviazione viene visualizzato sotto il nodo Riferimenti servizio del progetto processo Web.
   
    ![Archiviazione di Azure in progetti di tipo processo Web](./media/vs-azure-tools-connected-services-storage/IC796705.png)
7. Esaminare la pagina introduttiva visualizzata e controllare come è stato modificato il progetto. Ogni volta che si aggiunge un servizio connesso, nel browser verrà visualizzata una pagina introduttiva. È possibile esaminare i passaggi successivi e gli esempi di codice suggeriti o passare alla pagina Informazioni sull'evento per vedere quali riferimenti sono stati aggiunti al progetto e in che modo i file di configurazione e il codice sono stati modificati.

## <a name="how-your-project-is-modified"></a>Come viene modificato il progetto
Una volta terminata la finestra di dialogo, Visual Studio aggiunge riferimenti e modifica di alcuni file di configurazione. Le modifiche specifiche dipendono dal tipo di progetto. 

* Per i progetti ASP.NET, vedere [Informazioni sull'evento – Progetti ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
* Per i progetti ASP.NET 5, vedere [Informazioni sull'evento – Progetti ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
* Per i progetti del servizio cloud (ruoli Web e ruoli di lavoro), vedere [Informazioni sull'evento – Progetti del servizio cloud](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
* Per i progetti di tipo processo Web, vedere [Cosa è successo al progetto di tipo processo Web?](storage/vs-storage-webjobs-what-happened.md).

## <a name="next-steps"></a>Passaggi successivi
1. Usando gli esempi di codice introduttivi come guida, creare il tipo di risorsa di archiviazione desiderato, quindi iniziare a scrivere codice per accedere all'account di archiviazione.
2. Domande e assistenza
   
   * [Forum MSDN: Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
   * [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
   * [Archiviazione in azure.microsoft.com](https://azure.microsoft.com/services/storage/)
   * [Documentazione di archiviazione in azure.microsoft.com](https://azure.microsoft.com/documentation/services/storage/)




<!--HONumber=Nov16_HO3-->


