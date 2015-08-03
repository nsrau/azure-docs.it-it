<properties
	pageTitle="Creazione di una macchina virtuale per un progetto Web con Visual Studio"
	description="Creare una macchina virtuale per un sito Web"
	services="virtual-machines"
	documentationCenter=""
	authors="kempb"
	manager="douge"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="kempb"/>

# Creazione di una macchina virtuale per un sito Web con Visual Studio

Quando si crea un progetto Web per un sito Web di Azure, è possibile eseguire il provisioning di una macchina virtuale in Azure. È quindi possibile configurare la macchina virtuale con software aggiuntivo oppure usarla a scopo di diagnostica o debug.

Per creare una macchina virtuale quando si crea un sito Web, eseguire la procedura seguente:

1. In Visual Studio scegliere **File**, **Nuovo progetto**, quindi **Web** e infine **Applicazione Web ASP.NET** (sotto i nodi **Visual C** o **Visual Basic**).
2. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il tipo di applicazione Web desiderata e quindi, nella sezione Azure nell'angolo inferiore destro della finestra di dialogo, verificare che sia selezionata la casella di controllo **Ospita nel cloud**. In alcune installazioni questa casella di controllo è denominata **Crea risorse remote**.

	![][0]

3. Nell'elenco a discesa sotto Microsoft Azure scegliere **Macchina virtuale** e fare clic su **OK**.
4. Accedere a Azure se richiesto. Verrà visualizzata la finestra di dialogo **Crea macchina virtuale**.

	![][2]

5. Nella casella Nome DNS immettere un nome per la macchina virtuale. Il nome DNS deve essere univoco in Azure. Se il nome immesso non è disponibile, compare un punto esclamativo rosso.
6. Nell'elenco **Immagine** scegliere l'immagine VM su cui basare la macchina virtuale. È possibile scegliere una qualsiasi delle immagini VM standard oppure un'immagine personalizzata caricata in Azure.
7. Lasciare selezionata la casella di controllo **Abilita IIS e Distribuzione Web** a meno che non si preveda di installare un server Web diverso. Se non si abilita Distribuzione Web, non sarà possibile eseguire la pubblicazione da Visual Studio. È possibile aggiungere IIS e Distribuzione Web a qualsiasi immagine di Windows Server inclusa nel pacchetto, comprese le immagini personalizzate.
8. Nell'elenco **Dimensioni** selezionare le dimensioni della macchina virtuale.
9. Specificare le credenziali di accesso per la macchina virtuale. Prendere nota di tali credenziali perché saranno necessarie per accedere al computer tramite Desktop remoto.
10. Nell'elenco **Località** scegliere l'area che ospiterà la macchina virtuale.
11. Scegliere **OK** per avviare la creazione della macchina virtuale. È possibile seguire l'avanzamento dell'operazione nella **finestra Output**.
	![][3]

12. Dopo il provisioning della macchina virtuale, gli script di pubblicazione vengono creati in un nodo **PublishScripts**nella soluzione. Lo script di pubblicazione viene eseguito ed esegue il provisioning di una macchina virtuale in Azure. Nella finestra **Output** viene visualizzato lo stato. Lo script esegue le azioni seguenti per configurare la macchina virtuale:

	* Crea la macchina virtuale se non esiste già.
	* Crea un account di archiviazione con un nome che inizia con `devtest`, ma solo se nell'area specificata non esiste già un account di archiviazione di questo tipo.
	* Crea un servizio cloud come contenitore per la macchina virtuale e crea un ruolo Web per il sito Web.
	* Configura Distribuzione Web nella macchina virtuale.
	* Configura IIS e ASP.NET nella macchina virtuale.

	![][4]

<br/> 13. (Facoltativo) È possibile connettersi alla nuova macchina virtuale. In **Esplora server** espandere il nodo **Macchine virtuali**, scegliere il nodo per la macchina virtuale creata e, nel relativo menu di scelta rapida, scegliere **Esegui connessione con Desktop remoto**.

 ![][5]


## Passaggi successivi

Per personalizzare gli script di pubblicazione creati, vedere le informazioni più dettagliate disponibili [qui](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/VS_Create_VM_Connect.png

<!---HONumber=July15_HO4-->