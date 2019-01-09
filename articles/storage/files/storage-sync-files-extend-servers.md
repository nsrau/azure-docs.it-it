---
title: 'Esercitazione: Estendere i file server Windows con Sincronizzazione file di Azure | Microsoft Docs'
description: Informazioni su come estendere i file server Windows con Sincronizzazione file di Azure, dall'inizio alla fine.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 3ebf450f4e84fed572307a18f20f36013e32c7a5
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630700"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Esercitazione: Estendere i file server Windows con Sincronizzazione file di Azure
Questa esercitazione illustra i passaggi di base per estendere la capacità di archiviazione di un Windows Server con Sincronizzazione file di Azure. Anche se ai fini dell'esercitazione si usa una macchina virtuale Windows Server in Azure, in genere questa procedura andrebbe eseguita per i server locali. Se si è pronti a distribuire Sincronizzazione file di Azure nel proprio ambiente, consultare invece l'articolo [Distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Distribuire il servizio di sincronizzazione archiviazione
> * Preparare Windows Server per l'uso con Sincronizzazione file di Azure
> * Installare l'agente Sincronizzazione file di Azure
> * Registrare Windows Server con il servizio di sincronizzazione archiviazione
> * Creare un gruppo di sincronizzazione e un endpoint cloud
> * Creare un endpoint server

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="prepare-your-environment"></a>Preparare l'ambiente
Esistono alcuni aspetti da configurare per questa esercitazione prima di distribuire Sincronizzazione file di Azure. Oltre a creare un account di archiviazione e un file di condivisione di Azure, viene creata una macchina virtuale del data center di Windows Server 2016 e si prepara tale server per Sincronizzazione file di Azure.

### <a name="create-a-folder-and-txt-file"></a>Creare una cartella e un file .txt

Nel computer locale, creare una nuova cartella denominata *FilesToSync* e aggiungere un file di testo denominato *mytestdoc.txt*, che verrà caricato nella condivisione file più avanti in questa esercitazione.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Creare una condivisione file
Quindi, creare una condivisione file.

1. Una volta completata la distribuzione dell'account di archiviazione di Azure, fare clic su **Vai alla risorsa**.
1. Nel riquadro dell'account di archiviazione fare clic su **File**.

    ![Fare clic su File](./media/storage-sync-files-extend-servers/click-files.png)

1. Fare clic su **+ Condivisione file**.

    ![Fare clic sul pulsante Aggiungi condivisione file](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Denominare la nuova condivisione file *afsfileshare* e immettere "1" come **Quota**, quindi fare clic su **Crea**. Le dimensioni della quota non possono superare i 5 TiB, ma ai fini di questa esercitazione è necessario solo 1 GB.

    ![Specificare un nome e una quota per la nuova condivisione file](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selezionare la nuova condivisione file, quindi nel percorso della condivisione file, fare clic su **Carica**.

    ![Caricare un file](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Passare alla cartella *FilesToSync* in cui è stato creato il file .txt, selezionare *mytestdoc.txt* e fare clic su **Carica**.

    ![Esplorare una condivisione file](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

A questo punto, sono stati creati un account di archiviazione e una condivisione file di Azure contenente un solo file in Azure. A questo punto si creerà la macchina virtuale di Azure con Windows Server 2016 Datacenter per rappresentare il server locale in questa esercitazione.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Distribuire una macchina virtuale e collegare un disco dati

1. In seguito, espandere il menu a sinistra del portale e scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Nella casella di ricerca sopra l'elenco delle risorse di **Azure Marketplace**, cercare e selezionare **Windows Server 2016 Datacenter** e quindi scegliere **Crea**.
1. Nella scheda **Informazioni di base** in **Dettagli del progetto** selezionare il gruppo di risorse creato per questa esercitazione.

   ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. In **Dettagli dell'istanza** indicare un nome di macchina virtuale, ad esempio *myVM*.
1. Lasciare invariate le impostazioni predefinite per **Area**, **Opzioni di disponibilità**, **Immagine** e **Dimensioni**.
1. In **Account amministratore** indicare un **Nome utente** e una **Password** per la macchina virtuale.
1. Sotto **Regole porta in ingresso**, scegliere **Consentire porte selezionate**, quindi selezionare **RDP (3389)** e **HTTP** dall'elenco a discesa.

   Prima di creare la macchina virtuale, è necessario creare un disco dati.

1. Fare clic su **Next:Disks**

   ![Aggiungere dischi di dati](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. Nella scheda **Dischi** in **Opzioni disco** lasciare le impostazioni predefinite.
1. In **DISCHI DATI** fare clic su **Create and attach a new disk** (Crea e collega un nuovo disco).

1. Lasciare le impostazioni predefinite, ma modificare le **Dimensioni (GiB)** in **1 GB** per questa esercitazione.

   ![Dettagli del disco dati](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Fare clic su **OK**.
1. Fare clic su **Rivedi e crea**.
1. Fare clic su **Create**(Crea).

   Fare clic sull'icona **Notifiche** per guardare lo **stato di avanzamento della distribuzione**. Il completamento della creazione di una nuova macchina virtuale richiederà alcuni minuti.

1. Una volta completata la distribuzione della macchina virtuale, fare clic su **Vai alla risorsa**.

   ![Vai alla risorsa](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   A questo punto, è stata creata una nuova macchina virtuale e collegato un disco dati. Ora è necessario connettersi alla macchina virtuale.

### <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

1. Nel portale di Azure fare clic su **Connetti** nella pagina delle proprietà della macchina virtuale.

   ![Connettersi a una macchina virtuale di Azure dal portale](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Nella pagina **Connetti a macchina virtuale** mantenere le opzioni predefinite per la connessione con **Indirizzo IP** sulla porta 3389 e fare clic su **Scarica file RDP**.

   ![Scaricare il file RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Aprire il file RDP scaricato e fare clic su **Connetti** quando richiesto.
1. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare il nome utente come *localhost\nomeutente*, immettere la password creata per la macchina virtuale e quindi fare clic su **OK**.

   ![Altre opzioni](./media/storage-sync-files-extend-servers/local-host2.png)

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

### <a name="prepare-the-windows-server"></a>Preparare il Windows Server
Per il **Windows Server 2016 Datacenter** disabilitare **Configurazione sicurezza avanzata IE**. Questo passaggio è necessario solo per la registrazione iniziale del server e l'opzione può essere riabilitata dopo che il server è stato registrato.

Nella macchina virtuale **Windows Server 2016 Datacenter**, **Server Manager** si aprirà automaticamente.  Se **Server Manager** non si apre per impostazione predefinita, cercarlo in Explorer.

1. In **Server Manager** fare clic su **Server locale**.

   !["Server locale" sul lato sinistro dell'interfaccia utente di Server Manager](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Ne riquadro secondario **Proprietà** selezionare il collegamento per **Configurazione sicurezza avanzata IE**.  

    ![Riquadro "Configurazione sicurezza avanzata IE" nell'interfaccia utente di Server Manager](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Nella finestra di dialogo **Configurazione sicurezza avanzata IE** selezionare **Disattivato** per **Amministratori** e **Utenti**.

    ![Finestra popup Configurazione sicurezza avanzata IE con opzione "Disattivato" selezionata](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Ora è possibile aggiungere un disco dati alla VM.

### <a name="add-the-data-disk"></a>Aggiungere il disco dati

1. Mentre si è ancora nella macchina virtuale **Windows Server 2016 Datacenter**, fare clic su **Servizi file e archiviazione** > **Volumi** > **Dischi**.

    ![Disco dati](media/storage-sync-files-extend-servers/your-disk.png)

1. Fare clic con il pulsante destro del mouse sul disco da 1 GB denominato **Disco virtuale Msft** e fare clic su **Nuovo volume**.
1. Per completare la procedura guidata, lasciare le impostazioni predefinite invariate, annotare la lettera di unità assegnata e fare clic su **Crea**.
1. Fare clic su **Close**.

   A questo punto, il disco è stato portato online ed è stato creato un volume. Per confermare che l'aggiunta del disco dati è riuscita aprire Explorer nella macchina virtuale e confermare che sia presente la nuova unità.

1. In Explorer nella macchina virtuale, espandere **Questo PC** e fare doppio clic sulla nuova unità. Si tratta dell'unità F: in questo esempio.
1. Fare clic con il pulsante destro del mouse e scegliere **Nuova** > **Cartella**. Denominare la cartella *FilesToSync*.
1. Fare doppio clic sulla cartella **FilesToSync**.
1. Fare clic con il pulsante destro del mouse e scegliere **Nuovo** > **Documento di testo**. Denominare il file di testo *MyTestFile*.

    ![Aggiungere un nuovo file di testo](media/storage-sync-files-extend-servers/new-file.png)

1. Chiudere **Explorer** e **Server Manager**.

### <a name="download-the-azure-powershell-module"></a>Scaricare il modulo di Azure PowerShell
Successivamente, nella macchina virtuale **Windows Server 2016 Datacenter** installare il **modulo di Azure PowerShell** nel server.

1. Nella VM, aprire una finestra di Windows PowerShell con privilegi elevati
1. Eseguire il comando seguente:

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

   > [!NOTE]
   > Se si dispone di una versione precedente alla versione 2.8.5.201 di NuGet, viene richiesto di scaricare e installare la versione più recente di NuGet.

   Per impostazione predefinita, PowerShell Gallery non è configurata come archivio attendibile per PowerShellGet. La prima volta che si usa PSGallery verrà visualizzato il messaggio seguente:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Rispondere `Yes` o `Yes to All` per continuare l'installazione.

Il modulo `Az` è un modulo di rollup per i cmdlet di Azure PowerShell. Con la sua installazione vengono scaricati tutti i moduli di Azure Resource Manager disponibili e vengono messi a disposizione i relativi cmdlet.

A questo punto, è stata completata la configurazione dell'ambiente per l'esercitazione e si è pronti per iniziare a distribuire il **servizio di sincronizzazione archiviazione**.

## <a name="deploy-the-service"></a>Distribuire il servizio 
La distribuzione di Sincronizzazione file di Azure inizia con l'inserimento di una risorsa del **servizio di sincronizzazione archiviazione** in un gruppo di risorse per la sottoscrizione selezionata. Il servizio di sincronizzazione archiviazione eredita le autorizzazioni di accesso dalla sottoscrizione e dal gruppo di risorse in cui viene distribuito.

1. Nel portale di Azure, fare clic su **+ Crea una risorsa**, quindi cercare **Sincronizzazione file di Azure**.
1. Nei risultati della ricerca selezionare **Sincronizzazione file di Azure**.
1. Selezionare **Crea** per aprire la scheda **Distribuisci sincronizzazione archiviazione**.

   ![Distribuisci sincronizzazione archiviazione](media/storage-sync-files-extend-servers/afs-info.png)

   Nel pannello che viene visualizzato immettere le informazioni seguenti:

   | Valore | DESCRIZIONE |
   | ----- | ----- |
   | **Nome** | Un nome univoco (per ogni sottoscrizione) per il servizio di sincronizzazione archiviazione.<br><br>In questa esercitazione si usa *afssyncservice02*. |
   | **Sottoscrizione** | Sottoscrizione usata per questa esercitazione. |
   | **Gruppo di risorse** | Gruppo di risorse usato per questa esercitazione.<br><br>In questa esercitazione si usa *afsresgroup101918*. |
   | **Posizione** | Stati Uniti orientali |

1. Al termine, selezionare **Crea** per distribuire il **servizio di sincronizzazione di archiviazione**.
1. Fare clic sulla scheda **Notifiche** -> **Vai alla risorsa**.

## <a name="install-the-agent"></a>Installare l'agente
L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare Windows Server con una condivisione file di Azure.

1. Tornare alla VM **Windows Server 2016 Datacenter** e aprire **Internet Explorer**.
1. Passare all'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Scorrere verso il basso fino alla sezione **Agente Sincronizzazione file di Azure** e fare clic su **Scarica**.

   ![Download di Agente di sincronizzazione](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Selezionare la casella **StorageSyncAgent_V3_WS2016.EXE** e fare clic su **Avanti**.

   ![Selezionare l'agente](media/storage-sync-files-extend-servers/select-agent.png)

1. Selezionare **Consenti una sola volta** > **Esegui** > **Apri** per aprire il file.
1. Se non è già stato fatto, chiudere la finestra di PowerShell.
1. Accettare i valori predefiniti nell'**installazione guidata dell'agente di sincronizzazione archiviazione**.
1. Fare clic su **Installa**.
1. Fare clic su **Fine**.

È stato distribuito il servizio di sincronizzazione Azure e installato l'agente nella VM **Windows Server 2016 Datacenter**. A questo punto è necessario registrare la macchina virtuale con il **servizio di sincronizzazione archiviazione**.

## <a name="register-windows-server"></a>Registrare Windows Server
La registrazione di Windows Server con un servizio di sincronizzazione archiviazione consente di stabilire una relazione di trust tra il server, o il cluster, in uso e il servizio di sincronizzazione archiviazione. Un server può essere registrato solo in un servizio di sincronizzazione archiviazione e può eseguire la sincronizzazione con altri server e condivisioni file di Azure associati allo stesso servizio di sincronizzazione archiviazione.

L'interfaccia utente di Registrazione Server viene visualizzata automaticamente al termine dell'installazione dell'**agente Sincronizzazione file di Azure**. In caso contrario, è possibile aprirla manualmente dal percorso file: C:\Programmi\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Dopo avere aperto l'interfaccia utente di Registrazione server fare clic su **OK**.
1. Fare clic su **Accedi** per iniziare.
1. Accedere con le credenziali dell'account Azure e fare clic su **Accedi**.
1. Specificare le informazioni seguenti:

   ![Schermata dell'interfaccia utente di Registrazione Server](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Valore | DESCRIZIONE |
   | **Sottoscrizione di Azure** | La sottoscrizione contenente il servizio di sincronizzazione archiviazione per questa esercitazione. |
   | **Gruppo di risorse** | Il gruppo di risorse contenente il servizio di sincronizzazione archiviazione per questa esercitazione. In questa esercitazione è stato usato *afsresgroup101918*. |
   | **Servizio di sincronizzazione archiviazione** | Il nome del servizio di sincronizzazione archiviazione usato per questa esercitazione. In questa esercitazione è stato usato *afssyncservice02*. |

1. Fare clic su **Registra** per completare la registrazione del server.
1. Come parte del processo di registrazione, viene richiesto un accesso aggiuntivo. Accedere e fare clic su **Avanti**.
1. Fare clic su **OK**.

## <a name="create-a-sync-group"></a>Creare un gruppo di sincronizzazione
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Un gruppo di sincronizzazione deve contenere un endpoint cloud, che rappresenta una condivisione file di Azure, e uno o più endpoint server. Un endpoint server rappresenta un percorso in un server registrato.

1. Per creare un gruppo di sincronizzazione, nel [portale di Azure](https://portal.azure.com/) selezionare **+ Gruppo di sincronizzazione** dal servizio di sincronizzazione archiviazione creato per questa esercitazione. In questa esercitazione è stato usato *afssyncservice02* come esempio.

   ![Creare un nuovo gruppo di sincronizzazione nel portale di Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Nel riquadro che viene visualizzato immettere le informazioni seguenti per creare un gruppo di sincronizzazione con un endpoint cloud:

   | Valore | DESCRIZIONE |
   | ----- | ----- |
   | **Nome gruppo di sincronizzazione** | Questo nome deve essere univoco all'interno del servizio di sincronizzazione archiviazione, ma può essere qualsiasi nome logico per l'utente. In questa esercitazione si usa *afssyncgroup*.|
   | **Sottoscrizione** | La sottoscrizione in cui è stato distribuito il servizio di sincronizzazione archiviazione per questa esercitazione. |
   | **Account di archiviazione** |Fare clic su **Selezionare l'account di archiviazione**. Nel riquadro visualizzato selezionare l'account di archiviazione con la condivisione file di Azure creata per questa esercitazione. In questo caso è stato usato *afsstoracct101918*. |
   | **Condivisione file di Azure** | Il nome della condivisione file di Azure creato per questa esercitazione. È stato usato *afsfileshare*. |

1. Fare clic su **Create**(Crea).

Se si seleziona il gruppo di sincronizzazione, si noterà che ora si ha un solo **endpoint cloud**.

## <a name="add-a-server-endpoint"></a>Aggiungere un endpoint server
Un endpoint server rappresenta una posizione specifica in un server registrato, ad esempio una cartella in un volume del server.

1. Per aggiungere un endpoint server, selezionare il gruppo di sincronizzazione appena creato e fare clic su **Aggiungi endpoint server**.

   ![Aggiungere un nuovo endpoint server nel riquadro del gruppo di sincronizzazione](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Nel riquadro **Aggiungi endpoint server** immettere le informazioni seguenti per creare un endpoint server:

   | | |
   | ----- | ----- |
   | Valore | DESCRIZIONE |
   | **Server registrato** | Il nome del server creato per questa esercitazione. In questa esercitazione è stato usato *afsvm101918* |
   | **Percorso** | Il nome del percorso di Windows Server all'unità creato per questa esercitazione. Nell'esempio sarà *f:\filestosync*. |
   | **Suddivisione in livelli nel cloud** | Lasciare disattivato per questa esercitazione. |
   | **Spazio disponibile volume** | Lasciare vuoto per questa esercitazione. |

1. Fare clic su **Create**(Crea).

I file sono ora sincronizzati tra la condivisione file di Azure e Windows Server.

![Sincronizzazione Archiviazione di Azure riuscita](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati appresi i passaggi di base per estendere la capacità di archiviazione di un Windows Server con Sincronizzazione file di Azure. Seguire questo collegamento per informazioni dettagliate sulla pianificazione di una distribuzione di Sincronizzazione file di Azure.

> [!div class="nextstepaction"]
> [Pianificare una distribuzione di Sincronizzazione file di Azure](./storage-sync-files-planning.md)
