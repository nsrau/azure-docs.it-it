<properties
   pageTitle="Ottenere la stessa esperienza di Office 365 su qualsiasi dispositivo con Azure RemoteApp"
   description="Informazioni su come condividere con gli utenti qualsiasi applicazione di Office 365 tramite Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="07/07/2015"
   ms.author="guscatal;elizapo"/>


# Ottenere la stessa esperienza di Office 365 su qualsiasi dispositivo

Questo articolo illustra come distribuire Office 365 in qualsiasi dispositivo della società. Gli utenti possono ottenere le stesse funzionalità e la stessa esperienza dell'interfaccia utente su Android, Apple e Windows.

A questo scopo, si userà Azure RemoteApp mediante l’hosting di Office 365 in macchine virtuali scalabili di Azure a cui gli utenti possono connettersi. Questo set di macchine virtuali è detto "raccolta nel cloud".

## Creare una raccolta nel cloud

Subito dopo aver creato un account Azure passare a **RemoteApp** facendo clic sul collegamento presente sul lato sinistro. ![Visualizzazione di Azure RemoteApp nel portale di Azure](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/1-menu.png)

Continuare facendo clic su **Nuovo** nella parte inferiore e quindi su "Creazione rapida" di una raccolta. Specificare un nome, l'area, la sottoscrizione, il piano e l'immagine di "Office Professional 2013" fornita. ![Finestra di dialogo Crea](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/2-quickcreate.PNG)

Dopo aver completato il form, verrà avviato il processo di creazione della raccolta. L'operazione potrebbe richiedere un'ora circa.

![In attesa](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/3-waiting.PNG)

Al termine del processo, la schermata sarà simile alla seguente. Se si fa clic su **Pubblicazione**, si noterà che la maggior parte delle applicazioni di Office è già stata pubblicata. ![Raccolta creata](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/4-done.PNG)

![App pubblicate](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/5-publish.PNG)

A questo punto è anche possibile aggiungere altri utenti che potranno accedere a questa raccolta facendo clic su **Accesso utente**. ![Configurare l'accesso utente](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/6-user.PNG)

Ora si proverà a connettersi a Office 365.

## Connettersi a Office 365

Accedere a [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), scorrere verso il basso e fare clic su **Download del client** per installare il client di Azure RemoteApp nel dispositivo attivo. Le schermate seguenti si riferiscono a Windows.

Dopo l'avvio dell'applicazione verrà chiesto di accedere con il proprio account Microsoft (in precedenza denominato Live ID) . Per ora, usare lo stesso account usato per accedere ad Azure. Dopo avere eseguito l'accesso dovrebbe essere visualizzata una notifica relativa a nuovi inviti. Fare clic sulla notifica per visualizzare un elenco come il seguente. Accettare l'invito corrispondente all'indirizzo di posta elettronica del proprietario dell'account Azure.

![Nuovo invito](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/7-araclient.PNG)

Ecco come appare la schermata quando sono presenti nuovi inviti.

![Accettare un'applicazione](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/8-invitation.PNG)

Dopo avere accettato l'invito verranno visualizzate tutte le app di Office nel client Azure RemoteApp.

![Elenco di app](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/9-work.PNG)

Quando si fa clic su una di esse, l'applicazione verrà avviata nella macchina virtuale di Azure, pronta per essere usata.

![Avvio](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/10-arastart.PNG)

![PowerPoint](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/11-pp.PNG)
 

<!---HONumber=August15_HO6-->