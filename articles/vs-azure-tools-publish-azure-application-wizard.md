---
title: Uso della procedura guidata Pubblica l'applicazione Azure di Visual Studio | Microsoft Docs
description: Informazioni su come configurare le varie impostazioni della procedura guidata Pubblica l'applicazione Azure di Visual Studio
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 25b3ca9af2639860d9cfcb1492aef745fb47beb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>Uso della procedura guidata Pubblica l'applicazione Azure di Visual Studio
Un'applicazione Web sviluppata in Visual Studio può essere pubblicata in un servizio cloud di Azure mediante la procedura guidata **Pubblica l'applicazione Azure**. 

> [!NOTE]
> Questo argomento illustra la distribuzione in servizi cloud, non in siti Web. Per informazioni sulla distribuzione in siti Web, vedere l'argomento relativo alla [Come distribuire un sito Web di Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).
> 
> 

## <a name="accessing-the-publish-azure-application-wizard"></a>Accesso alla procedura guidata Pubblica l'applicazione Azure

È possibile accedere alla procedura guidata Pubblica l'applicazione Azure in due modi diversi, a seconda del tipo di progetto di Visual Studio di cui si dispone.

**Se si dispone di un progetto servizio cloud di Azure:**

1. Creare o aprire un progetto servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica** dal menu di scelta rapida.

**Se si dispone di un progetto applicazione Web non abilitato per Azure:**

1. Creare o aprire un progetto servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Converti** > **Converti in progetto servizio cloud di Azure** dal menu di scelta rapida. 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto di Azure appena creato e scegliere **Pubblica** dal menu di scelta rapida.

## <a name="sign-in-page"></a>Pagina di accesso

![Pagina di accesso](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Account** - Selezionare un account o fare clic su **Aggiungi un account** nell'elenco a discesa degli account.

**Scegliere la sottoscrizione** - Scegliere la sottoscrizione da usare per la distribuzione.
   
## <a name="settings-page---common-settings-tab"></a>Pagina Impostazioni - Scheda Impostazioni comuni   

![Impostazioni comuni](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**Servizio cloud** - Usando l'elenco a discesa, selezionare un servizio cloud esistente oppure fare clic su **&lt;Crea nuovo&gt;** e creare un servizio cloud. Per ogni servizio cloud viene visualizzato tra parentesi il data center. È consigliabile che la posizione del data center per il servizio cloud corrisponda a quella del data center per l'account di archiviazione (Impostazioni avanzate).  

**Ambiente** - Selezionare **Produzione** o **Gestione temporanea**. Scegliere l'ambiente di gestione temporanea se si vuole distribuire l'applicazione in un ambiente di test. 

**Configurazione compilazione** - Selezionare **Debug** o **Rilascio**.

**Configurazione servizio** - Selezionare **Cloud** o **Locale**.
   
**Abilita Desktop remoto per tutti i ruoli** - Selezionare questa opzione se si vuole consentire la connessione remota al servizio. Questa opzione viene usata principalmente per la risoluzione dei problemi. Quando si seleziona questa casella di controllo, viene visualizzata la finestra di dialogo **Configurazione Desktop remoto** . Per modificare la configurazione, scegliere il collegamento **Impostazioni**.
   
**Abilita Distribuzione Web per tutti i ruoli Web** - Selezionare questa opzione per abilitare la distribuzione Web per il servizio. È necessario selezionare l'opzione **Abilita Desktop remoto per tutti i ruoli** per usare questa funzionalità. Per altre informazioni, vedere [[Pubblicazione di un servizio cloud di Azure con Visual Studio](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). 

## <a name="settings-page---advanced-settings-tab"></a>Pagina Impostazioni - Scheda Impostazioni avanzate

![Impostazioni avanzate](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Etichetta distribuzione** - Accettare il nome predefinito o immettere un nome personalizzato. Per aggiungere la data all'etichetta di distribuzione, lasciare selezionata la casella di controllo. 
   
**Account di archiviazione** - Selezionare l'account di archiviazione da usare per questa distribuzione, **&lt;Crea nuovo> per creare un nuovo account di archiviazione. Per ogni account di archiviazione viene visualizzato tra parentesi il data center. È consigliabile che la posizione del data center per l'account di archiviazione corrisponda a quella del data center per il servizio cloud (Impostazioni comuni).  
   
L'account di archiviazione di Azure archivia il pacchetto per la distribuzione dell'applicazione. Dopo la distribuzione dell'applicazione, il pacchetto viene rimosso dall'account di archiviazione.

**Elimina distribuzione in caso di errore** - Selezionare questa opzione per eliminare la distribuzione se si verificano errori durante la pubblicazione. Questa opzione deve essere deselezionata se si vuole mantenere un indirizzo IP virtuale costante per il servizio cloud.

**Aggiornamento distribuzione** - Selezionare questa opzione se si vogliono distribuire solo componenti aggiornati. Questo tipo di distribuzione risulta più rapida della distribuzione completa. Questa opzione deve essere selezionata se si vuole mantenere un indirizzo IP virtuale costante per il servizio cloud. 

**Impostazioni aggiornamento distribuzione** - Questa finestra di dialogo viene usata per specificare ulteriormente come aggiornare i ruoli. Se si sceglie **Aggiornamento incrementale**, le istanze dell'applicazione vengono aggiornate una dopo l'altra, in modo che l'applicazione sia sempre disponibile. Se si sceglie **Simultaneous update** (Aggiornamento simultaneo), tutte le istanze dell'applicazione vengono aggiornate contemporaneamente. L'aggiornamento simultaneo è più veloce, ma è possibile che il servizio non sia disponibile durante il processo di aggiornamento. 

![Impostazioni di distribuzione](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**Abilita IntelliTrace** - Specificare se si vuole abilitare IntelliTrace. Con IntelliTrace è possibile registrare informazioni di debug approfondite per un'istanza del ruolo quando è in esecuzione in Azure. Se è necessario individuare la causa di un problema, è possibile usare i log di IntelliTrace per esaminare il codice da Visual Studio come se fosse in esecuzione in Azure. Per altre informazioni sull'uso di IntelliTrace, vedere [Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio](./vs-azure-tools-intellitrace-debug-published-cloud-services.md). 

**Abilita profilatura** - Specificare se si vuole abilitare la profilatura delle prestazioni. Il profiler di Visual Studio consente di ottenere un'analisi approfondita degli aspetti computazionali dell'esecuzione del servizio cloud. Per altre informazioni sull'uso del profiler di Visual Studio, vedere [Test delle prestazioni di un servizio cloud](./vs-azure-tools-performance-profiling-cloud-services.md).

**Abilita debug remoto per tutti i ruoli** - Specificare se si vuole abilitare il debug remoto. Per altre informazioni sul debug dei servizi cloud con Visual Studio, vedere [Debug di un servizio cloud o di una macchina virtuale di Azure in Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Pagina Impostazioni di diagnostica

![Impostazioni di diagnostica](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

La diagnostica consente di risolvere i problemi relativi a un servizio cloud di Azure (o a una macchina virtuale di Azure). Per informazioni sulla diagnostica, vedere [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Per informazioni su Application Insights, vedere [Informazioni su Azure Application Insights](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Pagina Riepilogo

![Riepilogo](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Profilo di destinazione** - È possibile scegliere di creare un profilo di pubblicazione dalle impostazioni scelte. È ad esempio possibile creare un profilo per un ambiente di test e un altro per l'ambiente di produzione. Per salvare il profilo, fare clic sull'icona **Salva** . La procedura guidata crea e salva il profilo nel progetto Visual Studio. Per modificare il nome del profilo, aprire l'elenco **Profilo di destinazione**, quindi scegliere **<Gestisci…>**.
   
   > [!NOTE]
   > Il profilo di pubblicazione viene visualizzato in Esplora soluzioni di Visual Studio e le impostazioni del profilo vengono scritte in un file con estensione azurePubxml. Le impostazioni vengono salvate come attributi dei tag XML.
   > 
   > 

## <a name="publishing-your-application"></a>Pubblicazione dell'applicazione

Dopo avere configurato tutte le impostazioni per la distribuzione del progetto, selezionare **Pubblica** nella parte inferiore della finestra di dialogo. È possibile monitorare lo stato del processo nella finestra **Output** in Visual Studio.

## <a name="next-steps"></a>Passaggi successivi
- [Eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)
- [Imparare a usare Visual Studio per pubblicare un servizio cloud di Azure](./vs-azure-tools-publishing-a-cloud-service.md)
- [Debug di un servizio cloud di Azure pubblicato con Visual Studio e IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)
- [Test delle prestazioni di un servizio cloud di Azure](./vs-azure-tools-performance-profiling-cloud-services.md)
- [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) 
- [Informazioni su Azure Application Insights](./application-insights/app-insights-overview.md)