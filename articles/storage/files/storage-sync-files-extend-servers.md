---
title: 'Esercitazione: Estendere i file server Windows con Sincronizzazione file di Azure | Microsoft Docs'
description: Informazioni su come estendere i file server Windows con Sincronizzazione file di Azure, dall'inizio alla fine.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f71a27ea4da6bce5832287e948e0731672280196
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699496"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Esercitazione: Estendere i file server Windows con Sincronizzazione file di Azure

L'articolo illustra i passaggi di base per estendere la capacità di archiviazione di un Windows Server con Sincronizzazione file di Azure. Anche se l'esercitazione utilizza Windows Server come macchina virtuale di Azure, in genere questa procedura andrebbe eseguita per i server locali. Le istruzioni sulla distribuzione di Sincronizzazione file di Azure nel proprio ambiente sono disponibili nell'articolo [Distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Distribuire il servizio di sincronizzazione archiviazione
> * Preparare Windows Server per l'uso con Sincronizzazione file di Azure
> * Installare l'agente Sincronizzazione file di Azure
> * Registrare Windows Server con il servizio di sincronizzazione archiviazione
> * Creare un gruppo di sincronizzazione e un endpoint cloud
> * Creare un endpoint server

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparare l'ambiente

Ai fini di questa esercitazione, è necessario procedere come segue prima di distribuire Sincronizzazione file di Azure:

- Creare un account di archiviazione di Azure e condivisione file
- Configurare una macchina virtuale Windows Server 2016 Datacenter
- Preparare la VM Windows Server per la Sincronizzazione file di Azure

### <a name="create-a-folder-and-txt-file"></a>Creare una cartella e un file .txt

Nel computer locale, creare una nuova cartella denominata _FilesToSync_ e aggiungere un file di testo denominato _mytestdoc.txt_, che verrà caricato nella condivisione file più avanti in questa esercitazione.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Creare una condivisione file

Dopo aver distribuito un account di archiviazione di Azure, creare una condivisione file.

1. Nel portale di Azure selezionare **Vai alla risorsa**.
1. Nel riquadro dell'account di archiviazione selezionare **File**.

    ![Selezione dei file](./media/storage-sync-files-extend-servers/click-files.png)

1. Selezionare **+ Condivisione file**.

    ![Selezionare il pulsante Aggiungi condivisione file](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Denominare la nuova condivisione file _afsfileshare_. Immettere "1" per la **Quota**, quindi selezionare **Crea**. Le dimensioni della quota non possono superare i 5 TiB, ma ai fini di questa esercitazione è necessario solo 1 GB.

    ![Specificare un nome e una quota per la nuova condivisione file](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selezionare la nuova condivisione file. Nel percorso della condivisione file, selezionare **Carica**.

    ![Caricare un file](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Passare alla cartella _FilesToSync_ in cui è stato creato il file .txt, selezionare _mytestdoc.txt_ e selezionare **Carica**.

    ![Esplorare una condivisione file](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

A questo punto, sono stati creati un account di archiviazione e una condivisione file contenente un solo file. Quindi, si distribuirà una macchina virtuale di Azure con Windows Server 2016 Datacenter per rappresentare il server locale in questa esercitazione.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Distribuire una macchina virtuale e collegare un disco dati

1. Passare al portale di Azure ed espandere il menu a sinistra. Scegliere **Crea una risorsa** nell'angolo superiore sinistro.
1. Nella casella di ricerca sopra l'elenco delle risorse di **Azure Marketplace**, cercare **Windows Server 2016 Datacenter** e selezionarlo nei risultati. Scegliere **Create**.
1. Passare alla scheda **Informazioni di base**. In **Dettagli del progetto** selezionare il gruppo di risorse creato per questa esercitazione.

   ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. In **Dettagli dell'istanza** indicare un nome di macchina virtuale. Ad esempio, usare _myVM_.
1. Non modificare le impostazioni predefinite per **Area**, **Opzioni di disponibilità**, **Immagine** e **Dimensioni**.
1. In **Account amministratore** indicare un **Nome utente** e una **Password** per la macchina virtuale.
1. Sotto **Regole porta in ingresso**, scegliere **Consentire porte selezionate**, quindi selezionare **RDP (3389)** e **HTTP** dal menu a discesa.

1. Prima di creare la macchina virtuale, è necessario creare un disco dati.

   1. Selezionare **Next:Disks**.

      ![Aggiungere dischi di dati](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Nella scheda **Dischi** in **Opzioni disco** lasciare le impostazioni predefinite.
   1. In **DISCHI DATI** selezionare **Create and attach a new disk** (Crea e collega un nuovo disco).

   1. Usare le impostazioni predefinite, ad eccezione di **Dimensioni (GiB)** , che è possibile modificare in **1GB** per questa esercitazione.

      ![Dettagli del disco dati](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Selezionare **OK**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Create** (Crea).

   Selezionare l'icona **Notifiche** per guardare lo **stato di avanzamento della distribuzione**. Il completamento della creazione di una nuova macchina virtuale potrebbe richiedere alcuni minuti.

1. Una volta completata la distribuzione della macchina virtuale, selezionare **Vai alla risorsa**.

   ![Vai alla risorsa](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

A questo punto, è stata creata una nuova macchina virtuale e collegato un disco dati. Quindi, connettersi alla VM.

### <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

1. Nel portale di Azure selezionare **Connetti** nella pagina delle proprietà della macchina virtuale.

   ![Connettersi a una macchina virtuale di Azure dal portale](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Nella pagina **Connetti a macchina virtuale** mantenere le opzioni predefinite per la connessione tramite **indirizzo IP** sulla porta 3389. Selezionare **Scarica file RDP**.

   ![Scaricare il file RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Aprire il file RDP scaricato e selezionare **Connetti** quando richiesto.
1. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare il nome utente come *localhost\nomeutente*, immettere la password creata per la macchina virtuale e quindi selezionare **OK**.

   ![Altre opzioni](./media/storage-sync-files-extend-servers/local-host2.png)

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** o **Continua** per procedere con la connessione.

### <a name="prepare-the-windows-server"></a>Preparare il Windows Server

Per il Windows Server 2016 Datacenter disabilitare Configurazione sicurezza avanzata IE. Questo passaggio è necessario solo per la registrazione iniziale del server e l'opzione può essere riabilitata dopo che il server è stato registrato.

Nella macchina virtuale Windows Server 2016 Datacenter, Server Manager si apre automaticamente.  Se Server Manager non si apre per impostazione predefinita, cercarlo in Explorer.

1. In **Server Manager** selezionare **Server locale**.

   !["Server locale" sul lato sinistro dell'interfaccia utente di Server Manager](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Nel riquadro **Proprietà** selezionare il collegamento per **Configurazione sicurezza avanzata IE**.  

    ![Riquadro "Configurazione sicurezza avanzata IE" nell'interfaccia utente di Server Manager](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Nella finestra di dialogo **Configurazione sicurezza avanzata IE** selezionare **Disattivato** per **Amministratori** e **Utenti**.

    ![Finestra popup Configurazione sicurezza avanzata IE con opzione "Disattivato" selezionata](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Ora è possibile aggiungere un disco dati alla VM.

### <a name="add-the-data-disk"></a>Aggiungere il disco dati

1. Mentre si è ancora nella macchina virtuale **Windows Server 2016 Datacenter**, selezionare **Servizi file e archiviazione** > **Volumi** > **Dischi**.

    ![Disco dati](media/storage-sync-files-extend-servers/your-disk.png)

1. Fare clic con il pulsante destro del mouse sul disco da 1 GB denominato **Disco virtuale Msft** e selezionare **Nuovo volume**.
1. Completare la procedura guidata. Usare le impostazioni predefinite e prendere nota della lettera unità assegnata.
1. Selezionare **Create** (Crea).
1. Selezionare **Chiudi**.

   A questo punto, il disco è stato portato online ed è stato creato un volume. Aprire Esplora File nella VM Windows Server per verificare la presenza del disco dati aggiunto di recente.

1. In Explorer nella macchina virtuale, espandere **Questo PC** e aprire la nuova unità. Si tratta dell'unità F: in questo esempio.
1. Fare clic con il pulsante destro del mouse e scegliere **Nuova** > **Cartella**. Denominare la cartella _FilesToSync_.
1. Aprire la cartella **FilesToSync**.
1. Fare clic con il pulsante destro del mouse e scegliere **Nuovo** > **Documento di testo**. Denominare il file di testo _MyTestFile_.

    ![Aggiungere un nuovo file di testo](media/storage-sync-files-extend-servers/new-file.png)

1. Chiudere **Esplora file** e **Server Manager**.

### <a name="download-the-azure-powershell-module"></a>Scaricare il modulo di Azure PowerShell

Successivamente, nella macchina virtuale Windows Server 2016 Datacenter installare il modulo di Azure PowerShell nel server.

1. Nella VM, aprire una finestra di Windows PowerShell con privilegi elevati.
1. Eseguire il comando seguente:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Se si ha una versione di NuGet precedente alla versione 2.8.5.201, viene richiesto di scaricare e installare la versione più recente di NuGet.

   Per impostazione predefinita, PowerShell Gallery non è configurata come archivio attendibile per PowerShellGet. La prima volta che si usa PSGallery verrà visualizzato il messaggio seguente:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Rispondere **Sì** o **Sì per tutti** per continuare l'installazione.

Il modulo `Az` è un modulo di rollup per i cmdlet di Azure PowerShell. Con la sua installazione vengono scaricati tutti i moduli di Azure Resource Manager disponibili e vengono messi a disposizione i relativi cmdlet.

A questo punto, l'ambiente è stato configurato per l'esercitazione. È ora possibile distribuire il servizio di sincronizzazione archiviazione.

## <a name="deploy-the-service"></a>Distribuire il servizio

Per distribuire Sincronizzazione file di Azure, inserire prima di tutto una risorsa del **servizio di sincronizzazione archiviazione** in un gruppo di risorse per la sottoscrizione selezionata. Il servizio di sincronizzazione archiviazione eredita le autorizzazioni di accesso dalla relativa sottoscrizione e dal gruppo di risorse.

1. Nel portale di Azure, selezionare **Crea una risorsa**, quindi cercare **Sincronizzazione file di Azure**.
1. Nei risultati della ricerca selezionare **Sincronizzazione file di Azure**.
1. Selezionare **Crea** per aprire la scheda **Distribuisci sincronizzazione archiviazione**.

   ![Distribuisci sincronizzazione archiviazione](media/storage-sync-files-extend-servers/afs-info.png)

   Nel pannello che viene visualizzato immettere le informazioni seguenti:

   | Valore | DESCRIZIONE |
   | ----- | ----- |
   | **Nome** | Un nome univoco (per ogni sottoscrizione) per il servizio di sincronizzazione archiviazione.<br><br>Usare _afssyncservice02_ per questa esercitazione. |
   | **Sottoscrizione** | Sottoscrizione di Azure usata per questa esercitazione. |
   | **Gruppo di risorse** | il gruppo di risorse che contiene il servizio di sincronizzazione archiviazione.<br><br>Usare _afsresgroup101918_ per questa esercitazione. |
   | **Posizione** | Stati Uniti orientali |

1. Al termine, selezionare **Crea** per distribuire il **servizio di sincronizzazione di archiviazione**.
1. Selezionare la scheda **Notifiche** -> **Vai alla risorsa**.

## <a name="install-the-agent"></a>Installare l'agente

L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare Windows Server con una condivisione file di Azure.

1. Nella VM **Windows Server 2016 Datacenter** aprire **Internet Explorer**.
1. Passare all'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Scorrere verso il basso fino alla sezione **Agente Sincronizzazione file di Azure** e selezionare **Scarica**.

   ![Download di Agente di sincronizzazione](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Selezionare la casella di controllo **StorageSyncAgent_V3_WS2016.EXE** e selezionare **Avanti**.

   ![Selezionare l'agente](media/storage-sync-files-extend-servers/select-agent.png)

1. Selezionare **Consenti una sola volta** > **Esegui** > **Apri**.
1. Se non è già stato fatto, chiudere la finestra di PowerShell.
1. Accettare i valori predefiniti nell'**installazione guidata dell'agente di sincronizzazione archiviazione**.
1. Selezionare **Installa**.
1. Selezionare **Fine**.

È stato distribuito il servizio di sincronizzazione Azure e installato l'agente nella VM Windows Server 2016 Datacenter. A questo punto è necessario registrare la macchina virtuale con il servizio di sincronizzazione archiviazione.

## <a name="register-windows-server"></a>Registrare Windows Server

La registrazione di Windows Server con un servizio di sincronizzazione archiviazione consente di stabilire una relazione di trust tra il server, o il cluster, in uso e il servizio di sincronizzazione archiviazione. Un server può essere registrato in un solo servizio di sincronizzazione archiviazione. Può eseguire la sincronizzazione con altri server e condivisioni file di Azure associati allo stesso servizio di sincronizzazione archiviazione.

L'interfaccia utente di Registrazione Server viene visualizzata automaticamente al termine dell'installazione dell'agente Sincronizzazione file di Azure. In caso contrario, è possibile aprirla manualmente dal percorso file: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Dopo avere aperto l'interfaccia utente di Registrazione server selezionare **OK**.
1. Selezionare **Accedi** per iniziare.
1. Accedere con le credenziali dell'account Azure e selezionare **Accedi**.
1. Specificare le informazioni seguenti:

   ![Schermata dell'interfaccia utente di Registrazione Server](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Valore | DESCRIZIONE |
   | **Sottoscrizione di Azure** | La sottoscrizione contenente il servizio di sincronizzazione archiviazione per questa esercitazione. |
   | **Gruppo di risorse** | il gruppo di risorse che contiene il servizio di sincronizzazione archiviazione. Usare _afsresgroup101918_ per questa esercitazione. |
   | **Servizio di sincronizzazione archiviazione** | Nome del Servizio di sincronizzazione archiviazione. Usare _afssyncservice02_ per questa esercitazione. |

1. Selezionare **Registra** per completare la registrazione del server.
1. Come parte del processo di registrazione, viene richiesto un accesso aggiuntivo. Eseguire l'accesso e selezionare **Avanti**.
1. Selezionare **OK**.

## <a name="create-a-sync-group"></a>Creare un gruppo di sincronizzazione

Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Un gruppo di sincronizzazione deve contenere un endpoint cloud, che rappresenta una condivisione file di Azure. Un gruppo di sincronizzazione deve anche contenere uno o più endpoint server. Un endpoint server rappresenta un percorso in un server registrato. Per creare un gruppo di sincronizzazione:

1. nel [portale di Azure](https://portal.azure.com/) selezionare **+ Gruppo di sincronizzazione** dal servizio di sincronizzazione archiviazione. Usare *afssyncservice02* per questa esercitazione.

   ![Creare un nuovo gruppo di sincronizzazione nel portale di Azure](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Immettere le informazioni seguenti per creare un gruppo di sincronizzazione con un endpoint cloud:

   | Valore | DESCRIZIONE |
   | ----- | ----- |
   | **Nome gruppo di sincronizzazione** | Questo nome deve essere univoco all'interno del servizio di sincronizzazione archiviazione, ma può essere qualsiasi nome logico per l'utente. Usare *afssyncgroup* per questa esercitazione.|
   | **Sottoscrizione** | La sottoscrizione in cui è stato distribuito il servizio di sincronizzazione archiviazione per questa esercitazione. |
   | **Account di archiviazione** | Scegliere **Selezionare l'account di archiviazione**. Nel riquadro visualizzato selezionare l'account di archiviazione con la condivisione file di Azure creata. Usare *afsstoracct101918* per questa esercitazione. |
   | **Condivisione file di Azure** | Il nome della condivisione file di Azure creato. Usare *afsfileshare* per questa esercitazione. |

1. Selezionare **Create** (Crea).

Se si seleziona il gruppo di sincronizzazione, si noterà che ora si ha un solo **endpoint cloud**.

## <a name="add-a-server-endpoint"></a>Aggiungere un endpoint server

Un endpoint server rappresenta una posizione specifica in un server registrato. Ad esempio, una cartella in un volume del server. Per aggiungere un endpoint server:

1. selezionare il gruppo di sincronizzazione appena creato e fare clic su **Aggiungi endpoint server**.

   ![Aggiungere un nuovo endpoint server nel riquadro del gruppo di sincronizzazione](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Nel riquadro **Aggiungi endpoint server** immettere le informazioni seguenti per creare un endpoint server:

   | | |
   | ----- | ----- |
   | Valore | DESCRIZIONE |
   | **Server registrato** | Il nome del server creato. Usare *afsvm101918* per questa esercitazione. |
   | **Percorso** | Il nome del percorso di Windows Server all'unità creato. Usare *f:\filestosync* in questa esercitazione. |
   | **Suddivisione in livelli nel cloud** | Lasciare disattivato per questa esercitazione. |
   | **Spazio disponibile volume** | Lasciare vuoto per questa esercitazione. |

1. Selezionare **Create** (Crea).

I file sono ora sincronizzati tra la condivisione file di Azure e Windows Server.

![Sincronizzazione Archiviazione di Azure riuscita](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati appresi i passaggi di base per estendere la capacità di archiviazione di un Windows Server con Sincronizzazione file di Azure. Per informazioni dettagliate sulla pianificazione di una distribuzione di Sincronizzazione file di Azure, vedere:

> [!div class="nextstepaction"]
> [Pianificare una distribuzione di Sincronizzazione file di Azure](./storage-sync-files-planning.md)
