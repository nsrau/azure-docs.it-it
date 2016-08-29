<properties 
   pageTitle="Procedura guidata Pubblica l'applicazione Azure | Microsoft Azure"
   description="Procedura guidata Pubblica l'applicazione Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Procedura guidata Pubblica l'applicazione Azure

## Overview

Un'applicazione Web sviluppata in Visual Studio può essere pubblicata più facilmente in un servizio cloud di Azure mediante la procedura guidata **Pubblica l'applicazione Azure**. La prima sezione illustra i passaggi da completare per poter usare la procedura guidata, mentre le sezioni successive descrivono le funzionalità di tale procedura guidata.

>[AZURE.NOTE] Questo argomento illustra la distribuzione in servizi cloud, non in siti Web. Per informazioni sulla distribuzione in siti Web, vedere l'argomento relativo alla [Come distribuire un sito Web di Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## Prerequisiti

Per poter pubblicare l'applicazione Web in Azure, è necessario avere un account Microsoft e una sottoscrizione di Azure ed è necessario associare l'applicazione Web a un servizio cloud di Azure. Se queste attività sono già state completate, è possibile passare alla sezione successiva.

1. Ottenere un account Microsoft e una sottoscrizione di Azure. Una sottoscrizione di valutazione di Azure gratuita per un mese è disponibile [qui](https://azure.microsoft.com/pricing/free-trial/)

1. Creare un servizio cloud e un account di archiviazione in Azure. È possibile eseguire questa operazione in Esplora server in Visual Studio oppure usando il [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Abilitare l'applicazione Web per Azure. Per consentire la pubblicazione dell'applicazione Web in Azure da Visual Studio, è necessario associarla a un progetto servizio cloud di Azure in Visual Studio. Per creare il progetto servizio cloud associato, aprire il menu di scelta rapida relativo al progetto per l'applicazione Web, quindi scegliere Converti, **Converti in progetto servizio cloud di Azure**.

1. Dopo aver aggiunto il progetto servizio cloud alla soluzione, aprire di nuovo lo stesso menu di scelta rapida e scegliere **Pubblica**. Per altre informazioni sull'abilitazione delle applicazioni per Azure, vedere [Procedura: Eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE] Assicurarsi di avviare Visual Studio con le credenziali di amministratore (Esegui come amministratore).

1. Quando si è pronti a pubblicare l'applicazione, aprire il menu di scelta rapida per il progetto servizio cloud di Azure e quindi scegliere **Pubblica**. Di seguito è illustrata la procedura guidata Pubblica l'applicazione Azure.

## Scelta della sottoscrizione

### Per scegliere una sottoscrizione

1. Prima di usare la procedura guidata per la prima volta, è necessario effettuare l'accesso. Fare clic sul collegamento **Accedi**. Accedere al portale di Azure quando richiesto e immettere il nome utente e la password di Azure.

    ![Una delle schermate della procedura guidata di pubblicazione](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    L'elenco delle sottoscrizioni viene popolato con le sottoscrizioni associate all'account. Potrebbero essere visualizzate anche le sottoscrizioni contenute in file di sottoscrizione importati in precedenza.

1. Dall'elenco **Scegliere la sottoscrizione** scegliere la sottoscrizione da usare per la distribuzione.

   Se si sceglie **<Gestisci…>**, viene visualizzata la finestra di dialogo **Gestisci sottoscrizioni** e sarà possibile scegliere la sottoscrizione e l'account utente da usare. La scheda **Account** mostra tutti gli account e la scheda **Sottoscrizioni** mostra tutte le sottoscrizioni associate agli account. È anche possibile scegliere un'area da cui usare le risorse di Azure, oltre a creare o importare certificati per la sottoscrizione dal portale di Azure. Se sono state importate sottoscrizioni da un file di sottoscrizione, i certificati associati verranno visualizzati nella scheda **Certificati**. Al termine, scegliere il pulsante **Chiudi**.

    ![Manage subscriptions](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] A subscription file can contain more than one subscription.

1. Per continuare, scegliere **Avanti**.

    Se nella sottoscrizione non sono presenti servizi cloud, è necessario creare un servizio cloud in Azure per ospitare il progetto. Verrà visualizzata la finestra di dialogo **Crea servizio cloud e account di archiviazione**.

    Specificare un nuovo nome per il servizio cloud. Il nome deve essere univoco in Azure. Specificare quindi un'area o un set di affinità per un data center situato nelle vicinanze dell'utente o della maggior parte dei propri clienti. Questo nome verrà usato anche per il nuovo account di archiviazione che Azure crea per il servizio cloud.

1. Modificare tutte le impostazioni desiderate per questa distribuzione, quindi pubblicarla scegliendo **Pubblica**. La sezione successiva fornisce dettagli aggiuntivi sulle diverse impostazioni. Per verificare le impostazioni prima della pubblicazione, scegliere **Avanti**.

    >[AZURE.NOTE] Se in questo passaggio è stato scelto il pulsante Pubblica, sarà possibile monitorare lo stato della distribuzione in Visual Studio.

È possibile modificare le impostazioni comuni e avanzate di una distribuzione tramite la procedura guidata **Pubblica l'applicazione Azure**. Ad esempio, è possibile scegliere un'impostazione per distribuire l'applicazione in un ambiente di test prima del rilascio. La figura seguente mostra la scheda **Impostazioni comuni** per una distribuzione di Azure.

![Impostazioni comuni](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## Configurazione delle impostazioni di pubblicazione

### Per configurare le impostazioni di pubblicazione

1. Nell'elenco **Servizio cloud** eseguire una delle procedure seguenti:

   1. Nell'elenco a discesa scegliere un servizio cloud esistente. Verrà visualizzata la posizione del data center per il servizio. È consigliabile annotare questa posizione e assicurarsi che l'account di archiviazione si trovi nello stesso data center.

    1. Scegliere **Crea nuovo** per creare un servizio cloud ospitato da Azure. Nella finestra di dialogo **Crea servizio cloud** specificare un nome per il servizio e quindi specificare un'area o un set di affinità per indicare la posizione del data center in cui si vuole ospitare il servizio cloud. Il nome deve essere univoco in Azure.

1. Nell'elenco **Ambiente** scegliere **Produzione** o **Gestione temporanea**. Scegliere l'ambiente di gestione temporanea se si vuole distribuire l'applicazione in un ambiente di test. È possibile spostare l'applicazione nell'ambiente di produzione in un secondo momento.

1. Nell'elenco **Configurazione della build** scegliere **Debug** o **Rilascio**.

1. Nell'elenco **Configurazione servizio** scegliere **Cloud** o **Locale**.

    Selezionare la casella di controllo **Abilita Desktop remoto per tutti i ruoli** se si vuole consentire la connessione remota al servizio. Questa opzione viene usata principalmente per la risoluzione dei problemi. Quando si seleziona questa casella di controllo, viene visualizzata la finestra di dialogo **Configurazione Desktop remoto**. Per modificare la configurazione, scegliere il collegamento Impostazioni.

    Selezionare la casella di controllo **Abilita Distribuzione Web per tutti i ruoli Web** per abilitare la distribuzione Web per il servizio. Per usare questa funzionalità, è necessario abilitare Desktop remoto. Per altre informazioni, vedere [[Pubblicazione di un servizio cloud con gli strumenti di Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). Per altre informazioni sulla distribuzione Web, vedere [[Pubblicazione di un servizio cloud con gli strumenti di Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Scegliere la scheda **Impostazioni avanzate**. Nel campo **Etichetta distribuzione** accettare il nome predefinito o immettere un nome personalizzato. Per aggiungere la data all'etichetta di distribuzione, lasciare selezionata la casella di controllo.

    ![Terza schermata della procedura guidata di pubblicazione](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. Nell'elenco **Account di archiviazione** scegliere l'account di archiviazione da usare per la distribuzione. Confrontare le posizioni dei data center per il servizio cloud e l'account di archiviazione. Idealmente, dovrebbero coincidere.

    >[AZURE.NOTE] L'account di archiviazione di Azure archivia il pacchetto per la distribuzione dell'applicazione. Dopo la distribuzione dell'applicazione, il pacchetto viene rimosso dall'account di archiviazione.

1. Selezionare la casella di controllo **Aggiornamento distribuzione** se si vuole distribuire solo componenti aggiornati. Questo tipo di distribuzione risulta più rapida della distribuzione completa. Scegliere il collegamento **Impostazioni** per aprire la finestra di dialogo **Impostazioni aggiornamento distribuzione**, mostrata nella figura seguente.

    ![Impostazioni distribuzione](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    È possibile scegliere una di due opzioni per la distribuzione dell'aggiornamento, ovvero incrementale o simultanea. La distribuzione incrementale aggiorna un'istanza distribuita alla volta. In questo modo, l'applicazione rimane online e disponibile agli utenti. Una distribuzione simultanea aggiorna tutte le istanze distribuite in una sola volta. L'aggiornamento simultaneo è più rapido di quello incrementale. Tuttavia, se si sceglie questa opzione, l'applicazione potrebbe non essere disponibile durante il processo di aggiornamento.

    Se quando la distribuzione di un aggiornamento non riesce si vuole che venga eseguita automaticamente la distribuzione completa, selezionare la casella di controllo Se la distribuzione non viene aggiornata, esegui una distribuzione completa. Una distribuzione completa reimposta l'indirizzo IP virtuale (VIP) per il servizio cloud. Per altre informazioni, vedere [Procedura: Mantenere un indirizzo IP virtuale costante per un servizio cloud](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Per eseguire il debug del servizio, selezionare la casella di controllo **Abilita IntelliTrace**. Se invece si intende distribuire una configurazione di **Debug** e si vuole eseguire il debug del servizio cloud in Azure, selezionare la casella di controllo **Abilita Debugger remoto per tutti i ruoli** per distribuire i servizi di debug remoto.

2. Per profilare l'applicazione, selezionare la casella di controllo **Abilita profilatura**, quindi scegliere il collegamento **Impostazioni** per visualizzare le opzioni di profilatura.


    >[AZURE.NOTE] È necessario usare Visual Studio Ultimate per abilitare IntelliTrace o la profilatura interazione tra livelli e non è possibile abilitare entrambi contemporaneamente.

    Per altre informazioni, vedere [Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx) e [Test delle prestazioni di un servizio cloud](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Scegliere **Avanti** per visualizzare la pagina di riepilogo per l'applicazione.

## Pubblicazione dell'applicazione

1. È possibile scegliere di creare un profilo di pubblicazione dalle impostazioni scelte. È ad esempio possibile creare un profilo per un ambiente di test e un altro per l'ambiente di produzione. Per salvare il profilo, fare clic sull'icona **Salva**. La procedura guidata crea e salva il profilo nel progetto Visual Studio. Per modificare il nome del profilo, aprire l'elenco **Profilo di destinazione**, quindi scegliere **<Gestisci…>**.

    ![Schermata di riepilogo della procedura guidata di pubblicazione](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] Il profilo di pubblicazione viene visualizzato in Esplora soluzioni di Visual Studio e le impostazioni del profilo vengono scritte in un file con estensione azurePubxml. Le impostazioni vengono salvate come attributi dei tag XML.

1. Scegliere **Pubblica** per pubblicare l'applicazione. È possibile monitorare lo stato del processo nella finestra **Output** in Visual Studio.

## Vedere anche

[Procedura: Eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Pubblicazione di un servizio cloud con gli strumenti di Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Test delle prestazioni di un servizio cloud](https://msdn.microsoft.com/library/azure/hh369930.aspx)

<!---HONumber=AcomDC_0817_2016-->