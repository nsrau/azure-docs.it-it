<properties 
   pageTitle="Accesso ai cloud privati di Azure con Visual Studio"
   description="Informazioni su come accedere alle risorse del cloud privato con Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# Accesso ai cloud privati di Azure con Visual Studio

##Panoramica

Per impostazione predefinita, Visual Studio supporta gli endpoint REST del cloud pubblico di Azure. Tuttavia, ciò può rappresentare un problema se si usa Visual Studio con un cloud privato di Azure. È possibile usare i certificati per configurare Visual Studio per l'accesso agli endpoint REST di un cloud privato di Azure. È possibile ottenere questi certificati tramite un file delle impostazioni di pubblicazione di Azure.

## Per accedere a un cloud privato di Azure in Visual Studio

1. Nel portale di gestione del cloud privato scaricare il file delle impostazioni di pubblicazione o contattare l'amministratore per ottenere un file delle impostazioni di pubblicazione. Nella versione pubblica di Azure, il collegamento da cui scaricare questo file è [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/) (l'estensione di tale file deve essere publishsettings).

1. In **Esplora server** in Visual Studio scegliere il nodo**Azure** e nel menu di scelta rapida scegliere il comando **Gestisci sottoscrizioni**.

    ![Comando Gestisci sottoscrizioni](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Nella finestra di dialogo **Gestisci sottoscrizioni Microsoft Azure** scegliere la scheda **Certificati**, quindi scegliere il pulsante **Importa**.

    ![Importazione di certificati di Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Nella finestra di dialogo **Importa sottoscrizioni Microsoft Azure** trovare la cartella in cui è stato salvato il file delle impostazioni di pubblicazione e scegliere il file, quindi scegliere il pulsante **Importa**. In questo modo i certificati verranno importati nel file delle impostazioni di pubblicazione in Visual Studio. Dovrebbe ora essere possibile interagire con le risorse del cloud privato.

    ![Importazione delle impostazioni di pubblicazione](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## Passaggi successivi

[Pubblicazione in un servizio cloud di Azure da Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Procedura: Scaricare e importare le impostazioni di pubblicazione e le informazioni sulla sottoscrizione](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

<!---HONumber=Oct15_HO3-->