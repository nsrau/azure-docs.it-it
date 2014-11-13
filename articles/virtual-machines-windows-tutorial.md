<properties urlDisplayName="Create a virtual machine" pageTitle="Creare una macchina virtuale che esegue Windows in Azure" metaKeywords="Azure capture image vm, capturing vm" description="Come creare una macchina virtuale (VM) Windows in Azure, eseguire l'accesso e collegare un disco dati" metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav, rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="kathydav" />

# Creare una macchina virtuale che esegue Windows

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Portale di Azure</a><a href="/it-it/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Portale di anteprima di Azure</a></div>

Questa esercitazione mostra come è facile creare una macchina virtuale di Azure che esegue Windows usando come esempio un'immagine di Windows Server dalla Raccolta immagini nel portale di gestione di Azure. Nella Raccolta immagini è disponibile un'ampia gamma di immagini, ad esempio immagini di sistemi operativi Windows, sistemi operativi basati su Linux e di applicazioni.

> [WACOM.NOTE] Per completare questa esercitazione, non è necessario essere esperti di macchine virtuali di Azure. È invece richiesto un account di Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creare un account Azure][Creare un account Azure].

In questa esercitazione si apprenderà come:

-   [Come creare la macchina virtuale][Come creare la macchina virtuale]
-   [Come accedere alla macchina virtuale dopo averla creata][Come accedere alla macchina virtuale dopo averla creata]
-   [Come collegare un disco dati alla nuova macchina virtuale][Come collegare un disco dati alla nuova macchina virtuale]

Per altre informazioni, vedere [Macchine virtuali][Macchine virtuali].

## <span id="createvirtualmachine"></span> </a>Come creare la macchina virtuale

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

> [WACOM.NOTE] Il numero e il tipo di immagini disponibili nella raccolta dipende dal tipo di sottoscrizione dell'utente. Questa esercitazione usa un'immagine di Windows Server, ma una sottoscrizione MSDN potrebbe avere immagini aggiuntive disponibili, incluse le immagini desktop.

> È anche possibile provare il [portale di anteprima di Azure][portale di anteprima di Azure], più avanzato e personalizzabile, per creare una macchina virtuale, automatizzare la distribuzione di modelli di applicazioni su più macchine virtuali, usare le funzionalità di diagnostica e monitoraggio delle macchine virtuali avanzate e altro ancora. Le opzioni di configurazione per le macchine virtuali disponibili nei due portali sono sostanzialmente simili ma non identiche.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <span id="logon"></span> </a>Come accedere alla macchina virtuale dopo averla creata

Questa sezione descrive come accedere alla macchina virtuale per gestirne le impostazioni e le applicazioni che verranno eseguite al suo interno.

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <span id="attachdisk"></span> </a>Come collegare un disco dati alla nuova macchina virtuale

Questa sezione descrive come collegare un disco dati vuoto alla macchina virtuale. Per altre informazioni su come collegare dischi vuoti e dischi esistenti, vedere l'esercitazione [Collegamento di un disco dati][Collegamento di un disco dati].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale **MyTestVM**.

    ![Select MyTestVM][Select MyTestVM]

3.  L'utente potrebbe essere indirizzato alla pagina Avvio rapido. In questo caso, fare clic su **Dashboard** nella parte superiore.

    ![Selezione del Dashboard][Selezione del Dashboard]

4.  Sella barra dei comandi fare clic su **Connetti** e quindi su **Connetti disco vuoto**.

    ![Selezione di Connetti sulla barra dei comandi][Selezione di Connetti sulla barra dei comandi]

5.  Le impostazioni **Virtual Machine Name**, **Storage Location**, **File Name** e **Host Cache Preference** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Size**. Fare quindi clic sul segno di spunta per connettere il disco vuoto alla macchina virtuale.

    > [WACOM.NOTE] Vale la pena sottolineare che le immagini del disco in Azure vengono archiviate come BLOB di pagine nell'Archiviazione di Azure. Esternamente ad Azure, per i dischi rigidi virtuali può essere usato un formato VHD o VHDX. I dischi possono inoltre essere fissi, espansi dinamicamente o dischi differenze. Azure supporta dischi fissi di formato VHD. In caso di formato fisso, il disco logico viene disposto in modo lineare all'interno del file, in modo che l'offset del disco X venga archiviato in corrispondenza dell'offset del BLOB X. In un piè di pagina alla fine del BLOB vengono descritte le proprietà del disco rigido virtuale. Spesso, con il formato fisso si verifica uno spreco di spazio poiché nella maggior parte dei dischi sono presenti intervalli inutilizzati di grandi dimensioni. Tuttavia, in Azure i file con estensione vhd vengono archiviati in un formato di tipo sparse, pertanto si ottengono contemporaneamente i vantaggi sia dei dischi fissi sia di quelli dinamici. Altre informazioni sono disponibili in [Informazioni sui dischi rigidi virtuali in Azure][Informazioni sui dischi rigidi virtuali in Azure]

    ![Definizione delle dimensioni del disco vuoto][Definizione delle dimensioni del disco vuoto]

    > [WACOM.NOTE] Tutti i dischi vengono creati da un file VHD in Archiviazione di Azure. In **Nome file** è possibile specificare un nome per il file VHD aggiunto alla risorsa di archiviazione, tuttavia il nome del disco viene generato automaticamente in Azure.

6.  Tornare al dashboard per verificare che il disco dati sia stato connesso correttamente alla macchina virtuale. Verrà visualizzato nell'elenco **Dischi** come secondo disco, insieme al disco del sistema operativo.

    ![Attach Empty Disk][Attach Empty Disk]

    Quando viene collegato alla macchina virtuale, il disco dati è offline e non inizializzato. Prima di poterlo usare per l'archiviazione dei dati, sarà necessario accedere alla macchina virtuale e inizializzarlo.

7.  Per connettersi alla macchina virtuale, attenersi ai passaggi descritti nella sezione precedente, [Come accedere alla macchina virtuale dopo averla creata][Come accedere alla macchina virtuale dopo averla creata] (\#logon).

8.  Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.

    ![Espandere Servizi file e archiviazione in Server Manager][Espandere Servizi file e archiviazione in Server Manager]

9.  Selezionare **Dischi** nel menu espanso.

    ![Espandere Servizi file e archiviazione in Server Manager][1]

10. Nella sezione **Dischi** è visualizzato un elenco con tre dischi: disco 0, disco 1 e disco 2. Disco 0 è il disco del sistema operativo, disco 1 è un disco di risorse temporaneo (che non deve essere usato per l'archiviazione dei dati) e disco 2 è il disco dati connesso alla macchina virtuale. Si noti che il disco dati ha una capacità di 5 GB come specificato in precedenza. Fare clic con il pulsante destro del mouse sul disco 2 e quindi scegliere **Inizializza**.

    ![Avviare l'inizializzazione][Avviare l'inizializzazione]

11. Fare clic su **Sì** per avviare il processo di inizializzazione.

    ![Continuare l'inizializzazione][Continuare l'inizializzazione]

12. Fare di nuovo clic con il pulsante destro del mouse sul disco 2 e scegliere **Nuovo volume**.

    ![Creare il volume][Creare il volume]

13. Completare la procedura guidata usando i valori predefiniti specificati. Al termine della procedura guidata, nella sezione **Volumi** verrà visualizzato un nuovo disco.

    ![Creare il volume][2]

    Il disco sarà ora online e pronto per l'uso con una nuova lettera di unità.

## Passaggi successivi

Per altre informazioni sulla configurazione di macchine virtuali Windows in Azure, vedere gli articoli seguenti:

[Come connettere macchine virtuali in un servizio cloud][Come connettere macchine virtuali in un servizio cloud]

[Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server][Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server]

[Gestione della disponibilità delle macchine virtuali][Gestione della disponibilità delle macchine virtuali]

[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure][Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]

[VIDEO: Introduzione ai dischi rigidi virtuali][VIDEO: Introduzione ai dischi rigidi virtuali]

[VIDEO: Risposte alle domande frequenti sull'esecuzione di Windows in Azure con Mark Russinovich][VIDEO: Risposte alle domande frequenti sull'esecuzione di Windows in Azure con Mark Russinovich]

[VIDEO: Aggiunta di una nuova macchina virtuale a una Web farm creando immagini riutilizzabili][VIDEO: Aggiunta di una nuova macchina virtuale a una Web farm creando immagini riutilizzabili]

[VIDEO: Aggiunta di dischi rigidi virtuali, account di archiviazione e ridimensionamento di macchine virtuali][VIDEO: Aggiunta di dischi rigidi virtuali, account di archiviazione e ridimensionamento di macchine virtuali]

[VIDEO: Scott Guthrie parla delle macchine virtuali][VIDEO: Scott Guthrie parla delle macchine virtuali]

[VIDEO: Nozioni di base su archiviazione e dischi con le macchine virtuali di Azure][VIDEO: Nozioni di base su archiviazione e dischi con le macchine virtuali di Azure]

  [Creare un account Azure]: http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/
  [Come creare la macchina virtuale]: #createvirtualmachine
  [Come accedere alla macchina virtuale dopo averla creata]: #logon
  [Come collegare un disco dati alla nuova macchina virtuale]: #attachdisk
  [Macchine virtuali]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [portale di anteprima di Azure]: https://portal.azure.com
  [Collegamento di un disco dati]: http://www.windowsazure.com/it-it/documentation/articles/storage-windows-attach-disk/
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Select MyTestVM]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Selezione del Dashboard]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Selezione di Connetti sulla barra dei comandi]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Informazioni sui dischi rigidi virtuali in Azure]: http://msdn.microsoft.com/it-it/library/azure/dn790344.aspx
  [Definizione delle dimensioni del disco vuoto]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [Attach Empty Disk]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [Espandere Servizi file e archiviazione in Server Manager]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [1]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [Avviare l'inizializzazione]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [Continuare l'inizializzazione]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [Creare il volume]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [2]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [Come connettere macchine virtuali in un servizio cloud]: http://www.windowsazure.com/it-it/documentation/articles/cloud-services-connect-virtual-machine/
  [Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server]: http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Gestione della disponibilità delle macchine virtuali]: http://www.windowsazure.com/it-it/documentation/articles/manage-availability-virtual-machines/
  [Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
  [VIDEO: Introduzione ai dischi rigidi virtuali]: http://azure.microsoft.com/it-it/documentation/videos/getting-started-with-azure-virtual-machines
  [VIDEO: Risposte alle domande frequenti sull'esecuzione di Windows in Azure con Mark Russinovich]: http://azure.microsoft.com/it-it/documentation/videos/mark-russinovich-windows-on-azure
  [VIDEO: Aggiunta di una nuova macchina virtuale a una Web farm creando immagini riutilizzabili]: http://azure.microsoft.com/it-it/documentation/videos/adding-virtual-machines-web-farm
  [VIDEO: Aggiunta di dischi rigidi virtuali, account di archiviazione e ridimensionamento di macchine virtuali]: http://azure.microsoft.com/it-it/documentation/videos/adding-drives-scaling-virtual-machines
  [VIDEO: Scott Guthrie parla delle macchine virtuali]: http://azure.microsoft.com/it-it/documentation/videos/virtual-machines-scottgu
  [VIDEO: Nozioni di base su archiviazione e dischi con le macchine virtuali di Azure]: http://azure.microsoft.com/it-it/documentation/videos/storage-and-disks-virtual-machines
