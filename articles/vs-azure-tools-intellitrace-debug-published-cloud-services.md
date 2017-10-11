---
title: Debug di un servizio cloud di Azure pubblicato con Visual Studio e IntelliTrace| Microsoft Docs
description: Informazioni su come eseguire il debug di un servizio cloud con Visual Studio e IntelliTrace
services: visual-studio-online
documentationcenter: n/a
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 7905dfb97cbd7578a8422567fe674839d00c21ef
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Debug di un servizio cloud di Azure pubblicato con Visual Studio e IntelliTrace
Con IntelliTrace è possibile registrare informazioni di debug approfondite per un'istanza del ruolo quando è in esecuzione in Azure. Se è necessario individuare la causa di un problema, è possibile usare i log di IntelliTrace per esaminare il codice da Visual Studio come se fosse in esecuzione in Azure. In effetti, IntelliTrace registra i dati fondamentali dell’esecuzione del codice e dell’ambiente quando l'applicazione Azure è in esecuzione come servizio cloud in Azure e consente di riprodurre i dati registrati da Visual Studio. 

È possibile usare IntelliTrace se Visual Studio Enterprise è installato e l'applicazione Azure è destinata a .NET Framework 4 o versione successiva. IntelliTrace raccoglie informazioni per i ruoli Azure. Le macchine virtuali per questi ruoli eseguono sempre sistemi operativi a 64 bit.

In alternativa, è possibile usare il [debug remoto](http://go.microsoft.com/fwlink/p/?LinkId=623041) per connettersi direttamente a un servizio cloud in esecuzione in Azure.

> [!IMPORTANT]
> IntelliTrace è destinato esclusivamente a scenari di debug e non deve essere usato per una distribuzione di produzione.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Configurare un'applicazione Azure per IntelliTrace
Per abilitare IntelliTrace per un'applicazione Azure, è necessario creare e pubblicare l'applicazione da un progetto Azure di Visual Studio. È necessario configurare IntelliTrace per l'applicazione Azure prima di pubblicarla in Azure. Se si pubblica l'applicazione senza configurare IntelliTrace, è necessario ripubblicare il progetto. Per altre informazioni, vedere [Pubblicazione di progetti di servizi cloud di Azure con Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando si è pronti distribuire l'applicazione Azure, verificare che le destinazioni di compilazione del progetto siano impostate su **Debug**.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica** dal menu di scelta rapida.
   
1. Nella finestra di dialogo **Pubblica applicazione Azure** selezionare la sottoscrizione di Azure e scegliere **Avanti**.

1. Nella pagina **Impostazioni** selezionare la scheda **Impostazioni avanzate**.

1. Per raccogliere i log di IntelliTrace per l'applicazione quando viene pubblicata nel cloud, selezionare l'opzione **Abilita IntelliTrace**.
   
1. Per personalizzare la configurazione di base di IntelliTrace, selezionare **Impostazioni** accanto ad **Abilita IntelliTrace**.

    ![Collegamento alle impostazioni di IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. Nella finestra di dialogo **Impostazioni di IntelliTrace** è possibile specificare gli eventi da registrare, se si desidera raccogliere informazioni sulle chiamate, i moduli e i processi di cui raccogliere i log e quanto spazio allocare per la registrazione. Per ulteriori informazioni su IntelliTrace, vedere [Debug con IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Impostazioni di IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Il log di IntelliTrace è un file di log circolare delle dimensioni massime specificate nelle impostazioni di IntelliTrace (le dimensioni predefinite sono 250 MB). I log di IntelliTrace vengono raccolti in un file nel file system della macchina virtuale. Quando si richiedono i log, a questo punto uno snapshot viene creato e scaricato nel computer locale.

Dopo che il servizio cloud di Azure è stato pubblicato in Azure, è possibile determinare se IntelliTrace è stato abilitato dal nodo di Azure in **Esplora server**, come illustrato nella figura seguente:

![Esplora server - IntelliTrace abilitato](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Scaricare i log di IntelliTrace per un'istanza del ruolo
Tramite Visual Studio, è possibile scaricare i log di IntelliTrace per un'istanza del ruolo attenendosi alla procedura seguente:

1. In **Esplora Server**, espandere il nodo **Servizi cloud** e quindi individuare l'istanza del ruolo di cui si desidera scaricare i log. 

1. Fare clic con il pulsante destro del mouse sull'istanza del ruolo e dal menu di scelta rapida scegliere **Visualizza log IntelliTrace**. 

    ![Opzione del menu Visualizza log IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. I log di IntelliTrace vengono scaricati in un file in una directory nel computer locale. Ogni volta che si richiedono i log di IntelliTrace, viene creato un nuovo snapshot. Quando i log vengono scaricati, Visual Studio mostra lo stato di avanzamento dell'operazione nella finestra **Log attività di Azure**. Come illustrato nella figura riportata di seguito, è possibile espandere la voce dell'operazione per visualizzare altri dettagli.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

È possibile continuare a lavorare in Visual Studio durante il download dei log di IntelliTrace. Quando il download del log è terminato si apre automaticamente in Visual Studio.

> [!NOTE]
> I log di IntelliTrace possono contenere eccezioni generate e poi gestite dal framework. Codice interno del framework genera queste eccezioni come parte normale di avvio di un ruolo, pertanto può essere ignorato.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
- [Opzioni per il debug dei servizi cloud di Azure](vs-azure-tools-debugging-cloud-services-overview.md)
- [Pubblicazione di un servizio cloud di Azure con Visual Studio](vs-azure-tools-publishing-a-cloud-service.md)