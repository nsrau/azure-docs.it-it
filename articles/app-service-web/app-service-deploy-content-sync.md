<properties
	pageTitle="Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure"
	description="Informazioni su come distribuire l'app nel servizio app di Azure tramite la sincronizzazione dei contenuti da una cartella nel cloud."
	services="app-service"
	documentationCenter=""
	authors="dariac"
	manager="yochayk"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="dariac"/>
    
# Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure

Una delle opzioni per la distribuzione nel [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) è la sincronizzazione dei contenuti dai servizi di archiviazione cloud più diffusi come ad esempio Dropbox e OneDrive.

## <a name="overview"></a>Panoramica della distribuzione per la sincronizzazione dei contenuti

La distribuzione per la sincronizzazione dei contenuti on demand si basa sul [motore di distribuzione Kudu](https://github.com/projectkudu/kudu/wiki) integrato con il servizio app. Nel [portale di Azure](https://portal.azure.com) è possibile designare una cartella speciale nel servizio di archiviazione cloud, utilizzare il codice e il contenuto dell'applicazione disponibile in tale cartella e procedere alla sincronizzazione con il servizio app con un semplice clic del mouse. La sincronizzazione usa il processo Kudu per la compilazione e la distribuzione.
    
## <a name="contentsync"></a>Come abilitare la distribuzione per la sincronizzazione dei contenuti
Per abilitare la sincronizzazione dei contenuti dal portale di Azure, seguire questa procedura:

1. Passare al pannello **Impostazioni** nel contesto dell'app e selezionare l'opzione **Distribuzione continua**.

    ![Sincronizzazione dei contenuti](./media/app-service-deploy-content-sync/continous_deployment.png)

2. Selezionare **OneDrive** o **Dropbox** come origine per la distribuzione.

    ![Origine di distribuzione](./media/app-service-deploy-content-sync/onedrive.png)

3. Completare il flusso di lavoro di autorizzazione per consentire al servizio app di accedere a un determinato percorso designato predefinito per OneDrive o Dropbox in cui verranno archiviati tutti i contenuti del servizio app. Dopo l'autorizzazione la piattaforma del servizio app consentirà di creare una cartella dei contenuti nel percorso dei contenuti designato oppure di selezionare una cartella esistente in questo stesso percorso. I percorsi dei contenuti designati negli account di archiviazione cloud usati per la sincronizzazione del servizio app sono i seguenti:
    * **OneDrive**: `Apps\Azure Web Apps` 
    * **Dropbox**: `Dropbox\Apps\Azure`

4. Dopo la sincronizzazione dei contenuti iniziale, la sincronizzazione può essere avviata su richiesta dal portale di Azure. La cronologia di distribuzione è disponibile nel pannello **Distribuzioni**.

    ![Cronologia di distribuzione](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Altre informazioni sulla distribuzione con Dropbox sono disponibili in [Distribuzione tramite Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx).

<!---HONumber=AcomDC_0309_2016-->