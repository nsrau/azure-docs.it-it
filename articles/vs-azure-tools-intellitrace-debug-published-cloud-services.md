<properties 
   pageTitle="Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio | Microsoft Azure"
   description="Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio

##Overview

Con IntelliTrace è possibile registrare informazioni di debug approfondite per un'istanza del ruolo quando è in esecuzione in Azure. Se è necessario individuare la causa di un problema, è possibile usare i log di IntelliTrace per esaminare il codice da Visual Studio come se fosse in esecuzione in Azure. In effetti, IntelliTrace registra i dati fondamentali dell’esecuzione del codice e dell’ambiente quando l'applicazione Azure è in esecuzione come servizio cloud in Azure e consente di riprodurre i dati registrati da Visual Studio. In alternativa, è possibile usare il debug remoto per connettersi direttamente a un servizio cloud in esecuzione in Azure. Vedere [Debug dei servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] IntelliTrace è destinato esclusivamente a scenari di debug e non deve essere usato per una distribuzione di produzione.

>[AZURE.NOTE] È possibile usare IntelliTrace se Visual Studio Enterprise è installato e l'applicazione Azure è destinata a .NET Framework 4 o versione successiva. IntelliTrace raccoglie informazioni per i ruoli Azure. Le macchine virtuali per questi ruoli eseguono sempre sistemi operativi a 64 bit.

## Per configurare un'applicazione Azure per IntelliTrace

Per abilitare IntelliTrace per un'applicazione Azure, è necessario creare e pubblicare l'applicazione da un progetto Azure di Visual Studio. È necessario configurare IntelliTrace per l'applicazione Azure prima di pubblicarla in Azure. Se si pubblica l'applicazione senza configurare IntelliTrace, ma successivamente si decide che si desidera eseguire questa operazione, sarà necessario pubblicare nuovamente l'applicazione da Visual Studio. Per altre informazioni su questa opzione, vedere [Pubblicazione di un servizio cloud con gli strumenti di Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando si è pronti distribuire l'applicazione Azure, verificare che le destinazioni di compilazione del progetto siano impostate su **Debug**.

1. Aprire il menu di scelta rapida per il progetto Azure in Esplora soluzioni e scegliere **Pubblica**.
 
    Verrà visualizzata la Pubblicazione guidata di applicazione di Azure.

1. Per raccogliere i log di IntelliTrace per l'applicazione quando viene pubblicata nel cloud, selezionare la casella di controllo **Abilita IntelliTrace**.

    >[AZURE.NOTE] È possibile abilitare IntelliTrace o la profilatura quando si pubblica l'applicazione Azure. Non è possibile abilitare entrambi.

1. Per personalizzare la configurazione di base di IntelliTrace, scegliere il collegamento ipertestuale **Impostazioni**.

    Verrà visualizzata la finestra di dialogo Impostazioni di IntelliTrace, come illustrato nella figura riportata di seguito. È possibile specificare gli eventi da registrare, se si desidera raccogliere informazioni sulle chiamate, i moduli e i processi di cui raccogliere i log e quanto spazio allocare per la registrazione. Per ulteriori informazioni su IntelliTrace, vedere [Debug con IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST\_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Il log di IntelliTrace è un file di log circolare delle dimensioni massime specificate nelle impostazioni di IntelliTrace (le dimensioni predefinite sono 250 MB). I log di IntelliTrace vengono raccolti in un file nel file system della macchina virtuale. Quando si richiedono i log, a questo punto uno snapshot viene creato e scaricato nel computer locale.

Dopo che l'applicazione Azure è stata pubblicata in Azure, è possibile determinare se IntelliTrace è stato abilitato dal nodo di calcolo di Azure in Esplora server, come illustrato nella figura seguente:

![VST\_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## Download dei log di IntelliTrace per un'istanza del ruolo

È possibile scaricare i log di IntelliTrace per un'istanza del ruolo dal nodo **Servizi cloud** in **Esplora server**. Espandere il nodo **Servizi cloud** fino a individuare l'istanza a cui si è interessati, aprire il menu di scelta rapida per questa istanza e scegliere **Visualizza log di IntelliTrace**. I log di IntelliTrace vengono scaricati in un file in una directory nel computer locale. Ogni volta che si richiedono i log di IntelliTrace, viene creato un nuovo snapshot.

Quando i log vengono scaricati, Visual Studio visualizza lo stato di avanzamento dell'operazione nella finestra Log attività di Azure. Come illustrato nella figura riportata di seguito, è possibile espandere la voce dell'operazione per visualizzare altri dettagli.

![VST\_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

È possibile continuare a lavorare in Visual Studio durante il download dei log di IntelliTrace. Quando il download del log è terminato, esso verrà aperto automaticamente in Visual Studio.

>[AZURE.NOTE] I log di IntelliTrace possono contenere eccezioni che il framework genera e successivamente gestisce. Codice interno del framework genera queste eccezioni come parte normale di avvio di un ruolo, pertanto può essere ignorato.

## Vedere anche

[Debug di Servizi cloud](https://msdn.microsoft.com/library/ee405479.aspx)

<!---HONumber=AcomDC_0817_2016-->