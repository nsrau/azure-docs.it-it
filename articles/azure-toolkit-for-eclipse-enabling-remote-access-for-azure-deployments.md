---
title: Abilitare l&quot;accesso remoto per le distribuzioni di Azure in Eclipse
description: "Informazioni su come abilitare l’accesso remoto per le distribuzioni Azure usando il Toolkit di Azure per Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: b6150006-9a7f-4d83-be18-d35ec780c7c5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 7d547ee39c4fe2bd52dd1fedce9c61d63b4e3011


---
# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Abilitare l'accesso remoto per le distribuzioni di Azure in Eclipse
Per risolvere i problemi relativi alle distribuzioni, è possibile abilitare e utilizzare l'accesso remoto per connettersi alla macchina virtuale che ospita la distribuzione. La funzionalità di accesso remoto si basa sul Remote Desktop Protocol (RDP). È possibile configurare l’accesso remoto per la distribuzione dopo averlo pubblicato in Azure o se si utilizza Eclipse con un sistema operativo Windows, è possibile configurare l’accesso remoto prima di pubblicare in Azure. Si noti che è necessario un client di desktop remoto compatibile con il sistema operativo per potersi connettere alla macchina virtuale della distribuzione in Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Come abilitare l'accesso remoto prima della distribuire in Azure
> [!NOTE]
> Per abilitare l'accesso remoto prima di distribuire l'applicazione in Azure, è necessario eseguire Eclipse in Windows.
> 
> 

La figura seguente mostra la finestra delle proprietà **Accesso remoto** utilizzate per abilitare l'accesso remoto.

![][ic719494]

Esistono due modi per visualizzare la finestra delle proprietà **Accesso remoto** :

* Fare clic sul collegamento **Avanzate** nella sezione **Accesso remoto** della finestra di dialogo **Pubblica in Azure**.
* Aprire la finestra di dialogo **Proprietà** del progetto di Azure.

Quando si crea un nuovo progetto di distribuzione di Azure, il progetto non disporrà dell’accesso remoto abilitato per impostazione predefinita. Tuttavia, è possibile abilitare facilmente l'accesso remoto specificando il nome utente e la password nella finestra di dialogo **Pubblica in Azure** . La password di accesso remoto viene crittografata mediante certificati X.509. Se solitamente non si fornisce il proprio certificato, la crittografia si basa su un certificato autofirmato fornito con il plug-in di Azure per Eclipse. Questo certificato autofirmato si trova nella cartella **cert** del progetto di Azure, archiviato sia come file di certificato pubblico (SampleRemoteAccessPublic) sia come un file di certificato Personal Information Exchange (PFX) (SampleRemoteAccessPrivate). Il secondo contiene la chiave privata del certificato e ha una password predefinita, **Password1**. Tuttavia, poiché questa password è pubblica, il certificato predefinito dovrebbe essere utilizzato solo a scopo illustrativo, non per una distribuzione di produzione. Quindi per fini diversi a quelli dell'apprendimento, quando si vuole abilitare le sessioni remote per le distribuzioni, è necessario fare clic sul collegamento **Avanzate** nella finestra di dialogo **Pubblica in Azure** per specificare il proprio certificato. Si noti che è necessario caricare la versione PFX del certificato nel servizio ospitato all'interno del portale di gestione di Azure, in modo che Azure possa decrittografare la password dell'utente.

Nel resto dell'esercitazione viene illustrato come abilitare l'accesso remoto per un progetto di distribuzione di Azure creato inizialmente con l’accesso remoto disabilitato. Ai fini di questa esercitazione, verrà creato un nuovo certificato autofirmato e il relativo file con estensione pfx avrà una password di propria scelta. È inoltre possibile utilizzare un certificato emesso da un'autorità di certificazione.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Come abilitare l'accesso remoto dopo la distribuzione in Azure
Per abilitare l'accesso remoto dopo aver distribuito in Azure, attenersi alla procedura seguente:

1. Accedere al portale di gestione di Azure con il proprio account.
2. Nell'elenco di **servizi Cloud**, selezionare il servizio cloud distribuito
3. Nella pagina web del servizio cloud, fare clic sul collegamento **Configura**
4. Nella parte inferiore della pagina di configurazione, fare clic sul collegamento **Remoto**
5. Quando viene visualizzata la finestra di dialogo popup:
   
   * Specificare il Ruolo per cui si desidera abilitare l'accesso remoto
   * Fare clic per selezionare la casella di controllo **Abilita Desktop remoto**
   * Specificare un nome utente e una password da utilizzare per l'accesso remoto
   * Selezionare il certificato da utilizzare
6. Fare clic su **OK** 

Verrà visualizzato un messaggio che informa che la modifica della configurazione è in corso. La modifica potrebbe richiedere alcuni minuti. Una volta completata la modifica della configurazione, seguire i passaggi nella sezione **Per accedere in remoto** più avanti in questo articolo.

## <a name="how-to-enable-remote-access-in-your-package"></a>Come abilitare l'accesso remoto nel pacchetto
1. Nel riquadro Project Explorer di Eclipse fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Properties**.
2. Nella finestra di dialogo **Properties** (Proprietà) espandere **Azure** nel riquadro a sinistra e fare clic su **Remote Access** (Accesso remoto).
3. Nella finestra di dialogo **Remote Access** (Accesso remoto), verificare che sia selezionato **Enable all roles to accept Remote Desktop Connections with these login credentials** (Consenti a tutti i ruoli di accettare connessioni Desktop remoto con queste credenziali di accesso).
4. Specificare un nome utente per la connessione Desktop remoto.
5. Specificare e confermare la password per l'utente. I valori nome e password utente impostati in questa finestra di dialogo verranno utilizzati quando si effettua una connessione Desktop remoto. (Si tratta di una password diversa dalla password PFX).
6. Specificare la data di scadenza per l'account utente.
7. Fare clic su **New** per creare un nuovo certificato autofirmato. In alternativa, è possibile selezionare un certificato dall'area di lavoro o nel file system tramite i pulsanti **Workspace** (Area di lavoro) o **FileSystem** (File system), rispettivamente, ma per questa esercitazione si creerà un nuovo certificato.
   
   * Nella finestra di dialogo **New Certificate** , specificare e confermare la password da utilizzare per il file PFX.
   * Accettare il valore fornito per **Name (CN)**, oppure utilizzare un nome personalizzato.
   * Specificare il percorso e il nome del file con cui il nuovo certificato verrà salvato, nel formato con estensione cer. Per questo passaggio e il passaggio successivo, è possibile utilizzare la cartella **cert** del progetto di Azure, ma si può scegliere un altro percorso. Ai fini di questa esercitazione, si userà **c:\mycert\mycert.cer**. Creare la cartella **c:\mycert** prima di continuare o, se si vuole, usare una cartella esistente.
   * Specificare il percorso e il nome del file con cui il nuovo certificato e la sua chiave privata verranno salvati, nel formato con estensione pfx. Ai fini di questa esercitazione, si userà **c:\mycert\mycert.pfx**. La finestra di dialogo **New Certificate** (Nuovo certificato) dovrebbe essere simile a quanto segue. Aggiornare i percorsi della cartella se non si usa **c:\mycert**:
     
       ![][ic712275]
   * Fare clic su **OK** per chiudere la finestra di dialogo **New Certificate** (Nuovo certificato).
8. La finestra di dialogo **Remote Access** (Accesso remoto) avrà un aspetto analogo al seguente:</p>
   
    ![][ic719495]
9. Fare clic su **OK** per chiudere la finestra di dialogo **Remote Access** (Accesso remoto).

Ricompilare l'applicazione, con la compilazione impostata per la distribuzione nel cloud.

## <a name="to-log-in-remotely"></a>Per accedere in modalità remota
Quando l'istanza del ruolo è pronta, è possibile accedere in remoto alla macchina virtuale che ospita l'applicazione.

* Se si usa Eclipse in Windows e si seleziona l’opzione **Start remote desktop on deploy** durante la distribuzione in Azure, si visualizzerà una schermata di accesso di connessione Desktop remoto all'avvio della distribuzione. Quando viene chiesto di immettere il nome utente e la password, immettere i valori specificati per l'utente remoto per effettuare l’accesso.
* È possibile accedere in remoto tramite il <a href="http://go.microsoft.com/fwlink/?LinkID=512959">portale di gestione di Azure</a>:
  
  * Nella visualizzazione **Servizi cloud** del portale di gestione di Azure fare clic sul servizio cloud, fare clic su **Istanze**, fare clic su un'istanza specifica e quindi fare clic sul pulsante **Connetti**. Il pulsante **Connetti** viene visualizzato come mostrato di seguito sulla barra dei comandi:
    
      ![][ic659273]
  * Dopo aver fatto clic sul pulsante **Connetti** , verrà richiesto di aprire un file RDP. Aprire il file e seguire le istruzioni. (È possibile inoltre salvare questo file nel computer locale e quindi eseguire il file facendovi doppio clic per accedere in modalità remota alla macchina virtuale senza la necessità di aprire prima il portale di gestione.)
  * Quando viene chiesto di immettere il nome utente e la password, immettere i valori specificati per l'utente remoto per effettuare l’accesso.

> [!NOTE]
> Se si utilizza un sistema operativo non Windows, è necessario utilizzare un client di Desktop remoto compatibile con il sistema operativo e seguire la procedura per configurare il client con le impostazioni nel file RDP scaricato.
> 
> 

## <a name="see-also"></a>Vedere anche
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Creare un'applicazione Hello World per Azure in Eclipse][Creating a Hello World Application for Azure in Eclipse]

[Installazione di Azure Toolkit for Eclipse][Installing the Azure Toolkit for Eclipse] 

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->



<!--HONumber=Jan17_HO1-->


