---
title: Pubblicazione di un Servizio cloud con gli Strumenti di Azure | Documentazione Microsoft
description: Informazioni su come pubblicare progetti di servizi cloud di Azure tramite Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2017
ms.author: kraigb
ms.openlocfilehash: e854c46329f092388531a73ada50829c4579806d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Pubblicazione di un servizio cloud con Visual Studio

Visual Studio può pubblicare un'applicazione direttamente in Azure, con il supporto per entrambi gli ambienti di staging e produzione di un servizio cloud. Al momento della pubblicazione, si selezionano l'ambiente di distribuzione e un account di archiviazione da usare temporaneamente per il pacchetto di distribuzione.

Durante lo sviluppo e il test di un'applicazione Azure, è possibile usare Distribuzione Web per pubblicare le modifiche in modo incrementale per i ruoli web. Dopo aver pubblicato l'applicazione in un ambiente di distribuzione, Distribuzione Web consente di distribuire le modifiche apportate direttamente alla macchina virtuale che esegue il ruolo web. Non è necessario assemblare e pubblicare l'intera applicazione Azure ogni volta che si desidera aggiornare il ruolo web per testare le modifiche. Con questo approccio, è possibile avere le modifiche del ruolo Web disponibili nel cloud per il test senza dover attendere la pubblicazione dell'applicazione in un ambiente di distribuzione.

Utilizzare le procedure seguenti per pubblicare l'applicazione Azure e per aggiornare un ruolo web tramite Distribuzione Web:

- Pubblicare o Assemblare un'applicazione Azure da Visual Studio
- Aggiornare un ruolo web come parte del ciclo di sviluppo e test

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Pubblicare o assemblare un'applicazione Azure da Visual Studio

Quando si pubblica l'applicazione Azure, è possibile eseguire una delle seguenti operazioni:

- Creare un pacchetto del servizio: è possibile usare questo pacchetto e il file di configurazione del servizio per pubblicare l'applicazione in un ambiente di distribuzione dal [portale di Azure](https://portal.azure.com).

- Pubblicare il progetto Azure da Visual Studio: per pubblicare l'applicazione direttamente in Azure, si utilizza la Pubblicazione guidata. Per altre informazioni, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Per creare un pacchetto del servizio da Visual Studio

1. Quando si è pronti per pubblicare l'applicazione, aprire Esplora soluzioni, aprire il menu di scelta rapida per il progetto Azure contenente i ruoli e scegliere Pubblica.

1. Per creare un pacchetto del servizio, attenersi alla procedura seguente:

   a. Sul menu di scelta rapida per il progetto Azure, scegliere **Pacchetto**.

   b. Nella finestra di dialogo **Crea pacchetto dell’applicazione Azure** scegliere la configurazione del servizio per il quale si desidera creare un pacchetto e quindi scegliere la configurazione di compilazione.

   c. (Facoltativo) Per attivare Desktop remoto per il servizio cloud dopo la pubblicazione, selezionare **Abilita Desktop remoto per tutti i ruoli**, quindi selezionare **Impostazioni** per configurare le credenziali di Desktop remoto. Per altre informazioni, vedere [Enable Remote Desktop Connection for a Role in Azure Cloud Services using Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md) (Abilitare una connessione Desktop remoto per un ruolo in Servizi cloud di Azure con Visual Studio).

      Se si desidera eseguire il debug del servizio cloud dopo la pubblicazione, attivare il debug remoto selezionando **Abilita Debugger remoto per tutti i ruoli**.

   d. Per creare il pacchetto, scegliere il collegamento **Pacchetto** .

      Esplora file mostra il percorso del file del pacchetto appena creato. È possibile copiare questo percorso per usarlo dal portale di Azure.

   e. Per pubblicare il pacchetto in un ambiente di distribuzione, è necessario usare questo percorso del pacchetto quando si crea un servizio cloud e si distribuisce il pacchetto in un ambiente con il portale di Azure.

1. (Facoltativo) Per annullare il processo di distribuzione, nel menu di scelta rapida per la voce nel registro attività, scegliere **Annulla e Rimuovi**. Questo comando arresta il processo di distribuzione ed elimina l'ambiente di distribuzione da Azure. Per rimuovere l'ambiente dopo la distribuzione, usare il portale di Azure.

1. (Facoltativo) Dopo l'avvio delle istanze del ruolo, Visual Studio mostrerà automaticamente l'ambiente di distribuzione nel nodo **Servizi cloud** in Esplora server. Da qui è possibile visualizzare lo stato delle singole istanze del ruolo. Vedere [Gestione delle risorse di Azure con Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). La figura seguente mostra le istanze del ruolo mentre si trovano ancora nello stato di inizializzazione:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Aggiornare un ruolo web come parte del ciclo di sviluppo e test

Se l'infrastruttura back-end dell'app è stabile, ma i ruoli Web richiedono aggiornamenti più frequenti, è possibile usare Distribuzione Web per aggiornare un solo ruolo Web nel progetto. Distribuzione Web è utile quando si vuole evitare di creare e distribuire di nuovo i ruoli di lavoro back-end o se si hanno più ruoli Web e si vuole aggiornarne solo uno.

### <a name="requirements-for-using-web-deploy"></a>Requisiti per l'uso di Distribuzione Web

- **Solo per scopi di sviluppo e test:** le modifiche vengono apportate direttamente alla macchina virtuale in cui è in esecuzione il ruolo web. Se è necessario riciclare questa macchina virtuale, le modifiche andranno perse perché il pacchetto originale pubblicato viene utilizzato per ricreare la macchina virtuale per il ruolo. Pubblicare nuovamente l'applicazione per ottenere le modifiche più recenti per il ruolo Web.

- **Possono essere aggiornati solo i ruoli Web:** i ruoli di lavoro non possono essere aggiornati. Inoltre, non è possibile aggiornare `RoleEntryPoint` in `web role.cs`.

- **Può supportare solo una singola istanza di un ruolo Web:** non è possibile avere più istanze di qualsiasi ruolo Web nell'ambiente di distribuzione. Tuttavia, sono supportati più ruoli web con una sola istanza.

- **Abilitare le connessioni Desktop remoto:** questa operazione è necessaria per consentire a Distribuzione Web di usare il nome utente e la password per connettersi alla macchina virtuale e distribuire le modifiche al server che esegue IIS (Internet Information Services). Inoltre, potrebbe essere necessario connettersi alla macchina virtuale per aggiungere un certificato attendibile a IIS su questa macchina virtuale. Con questo certificato si ha la certezza che la connessione remota per IIS usata da Distribuzione Web sia protetta.

La procedura seguente presuppone che si stia utilizzando la procedura guidata **Pubblica applicazione di Azure** .

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Abilitare Distribuzione Web quando si pubblica l'applicazione

1. Per abilitare l'opzione **Abilita Distribuzione Web per tutti i ruoli Web** , è prima necessario configurare le connessioni di Desktop remoto. Selezionare **Abilita Desktop remoto per tutti i ruoli** e quindi specificare le credenziali usate per connettersi in remoto nella casella **Configurazione Desktop remoto** visualizzata. Vedere [Abilitare una connessione Desktop remoto per un ruolo in Servizi cloud di Azure con Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Per abilitare Distribuzione Web per tutti i ruoli web nell'applicazione, selezionare **Abilita Distribuzione Web per tutti i ruoli web**.

    Viene visualizzato un triangolo giallo. Distribuzione Web utilizza un certificato autofirmato non attendibile per impostazione predefinita, che non è consigliato per il caricamento dei dati riservati. Se è necessario proteggere questo processo per i dati riservati, è possibile aggiungere un certificato SSL da usare per le connessioni di Distribuzione Web. Questo certificato deve essere un certificato attendibile. Per altre informazioni, vedere [Proteggere Distribuzione Web](#make-web-deploy-secure).

1. Scegliere **Avanti** per mostrare la schermata **Riepilogo** e quindi scegliere **Pubblica** per distribuire il servizio cloud.

    Il servizio cloud viene pubblicato. La macchina virtuale creata dispone di connessioni remote abilitate per IIS in modo che Distribuzione Web possa essere utilizzato per aggiornare i ruoli web senza ripubblicarli.

   > [!NOTE]
   > Se per un ruolo Web sono configurate più istanze, viene visualizzato un messaggio di avviso per segnalare che ogni ruolo Web è limitato a una sola istanza nel pacchetto creato per pubblicare l'applicazione. Selezionare **OK** per continuare. Come indicato nella sezione Requisiti, è possibile disporre di più di un ruolo web ma di solo un'istanza di ogni ruolo.

### <a name="update-your-web-role-by-using-web-deploy"></a>Aggiornare il ruolo Web tramite Distribuzione Web

1. Per utilizzare Distribuzione Web, apportare modifiche al codice del progetto per uno dei ruoli web in Visual Studio che si desidera pubblicare, quindi fare doppio clic su questo nodo del progetto nella soluzione e scegliere **Pubblica**. La finestra di dialogo **Pubblica sito Web** viene visualizzata.

1. (Facoltativo) Se è stato aggiunto un certificato SSL attendibile da utilizzare per le connessioni remote per IIS, è possibile deselezionare la casella di controllo **Consenti certificato non attendibile** . Per informazioni su come aggiungere un certificato per proteggere Distribuzione Web, vedere la sezione **Proteggere Distribuzione Web** più avanti in questo articolo.

1. Per usare Distribuzione Web, il meccanismo di pubblicazione richiede il nome utente e la password impostati per la connessione Desktop remoto alla prima pubblicazione del pacchetto.

   a. In **Nome utente**, immettere il nome utente.

   b. In **Password**, immettere la password.

   c. (Facoltativo) Se si desidera salvare la password in questo profilo, scegliere **Salva password**.

1. Per pubblicare le modifiche al proprio ruolo web, scegliere **Pubblica**.

    La riga di stato mostra **Pubblicazione avviata**. Quando la pubblicazione è stata completata, lo stato mostra **Pubblicazione completata** . Le modifiche a questo punto sono state distribuite al ruolo web nella macchina virtuale. Ora è possibile avviare l'applicazione Azure nell'ambiente di Azure per verificare le modifiche apportate.

### <a name="make-web-deploy-secure"></a>Proteggere Distribuzione Web

1. Distribuzione Web utilizza un certificato autofirmato non attendibile per impostazione predefinita, che non è consigliato per il caricamento dei dati riservati. Se è necessario proteggere questo processo per i dati riservati, è possibile aggiungere un certificato SSL da usare per le connessioni di Distribuzione Web. Questo certificato deve essere un certificato attendibile, ottenuto da un'autorità di certificazione (CA).

    Per proteggere Distribuzione Web per ogni macchina virtuale relativa a ciascuno dei ruoli Web, è necessario caricare il certificato attendibile che si vuole usare per Distribuzione Web nel portale di Azure. In questo modo si ha la certezza che il certificato venga aggiunto alla macchina virtuale creata per il ruolo Web quando si pubblica l'applicazione.

1. Per aggiungere un certificato SSL attendibile a IIS da utilizzare per le connessioni remote, attenersi alla seguente procedura:

   a. Per connettersi alla macchina virtuale che esegue il ruolo Web, selezionare l'istanza del ruolo Web in **Cloud Explorer** o **Esplora server**, quindi scegliere il comando **Connessione tramite desktop remoto**. Per informazioni dettagliate su come connettersi alla macchina virtuale, vedere [Abilitare una connessione Desktop remoto per un ruolo in Servizi cloud di Azure con Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). Il browser chiederà di scaricare un file con estensione `.rdp`.

   b. Per aggiungere un certificato SSL, aprire il servizio di gestione in Gestione IIS. In Gestione IIS abilitare SSL aprendo il collegamento **Binding** nel riquadro **Azione**. La finestra di dialogo **Aggiungi binding del sito** verrà visualizzata. Fare clic su **Aggiungi** e quindi selezionare HTTPS dall'elenco a discesa **Tipo**. Nell'elenco **Certificato SSL** selezionare il certificato SSL firmato da una CA che è stato caricato nel portale di Azure. Per altre informazioni, vedere [Configurare le impostazioni di connessione per il servizio di gestione](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Se si aggiunge un certificato SSL attendibile, il triangolo giallo di avviso non viene visualizzato nella **Pubblicazione guidata**.

## <a name="include-files-in-the-service-package"></a>Includere file nel pacchetto del servizio

Potrebbe essere necessario includere file specifici nel pacchetto di servizio in modo che siano disponibili nella macchina virtuale creata per un ruolo. Può ad esempio essere opportuno aggiungere un file con estensione `.exe` o `.msi` che viene usato da uno script di avvio per il pacchetto del servizio. Oppure potrebbe essere necessario aggiungere un assembly richiesto da un progetto di ruolo web o ruolo di lavoro. Per includere file, è necessario aggiungerli alla soluzione relativa all'applicazione Azure.

1. Per aggiungere un assembly a un pacchetto del servizio, utilizzare la procedura seguente:

   a. In **Esplora soluzioni** aprire il nodo del progetto in cui non è presente l'assembly di riferimento.
   b. Per aggiungere l'assembly al progetto, aprire il menu di scelta rapida per la cartella **Riferimenti** e quindi scegliere **Aggiungi riferimento**. Viene visualizzata la finestra di dialogo Aggiungi riferimento.
   c. Scegliere il riferimento che si vuole aggiungere e quindi fare clic su **OK**. Il riferimento viene aggiunto all'elenco sotto la cartella **Riferimenti** .
   d. Aprire il menu di scelta rapida per l'assembly aggiunto e scegliere **Proprietà**. La finestra **Proprietà** verrà visualizzata.

      Per includere questo assembly nel pacchetto del servizio, nell'elenco **Copia locale** scegliere **True**.
1. In **Esplora soluzione** aprire il nodo del progetto per il progetto in cui manca l'assembly di riferimento.

1. Per aggiungere l'assembly al progetto, aprire il menu di scelta rapida per la cartella **Riferimenti** e quindi scegliere **Aggiungi riferimento**. Viene visualizzata la finestra di dialogo **Aggiungi riferimento** .

1. Scegliere il riferimento che si desidera aggiungere e quindi scegliere il pulsante **OK** .

    Il riferimento viene aggiunto all'elenco sotto la cartella **Riferimenti** .

1. Aprire il menu di scelta rapida per l'assembly aggiunto e scegliere **Proprietà**. La finestra Proprietà verrà visualizzata.

1. Per includere questo assembly nel pacchetto del servizio, nell'elenco **Copia locale** scegliere **True**.

1. Per includere file nel pacchetto del servizio che sono stati aggiunti al progetto del ruolo web, aprire il menu di scelta rapida per il file e quindi scegliere **Proprietà**. Dalla finestra **Proprietà** scegliere **Contenuto** nella casella di riepilogo **Azione di compilazione**.

1. Per includere file nel pacchetto del servizio che sono stati aggiunti al progetto del ruolo di lavoro, aprire il menu di scelta rapida per il file e quindi scegliere **proprietà**. Dalla finestra **Proprietà** scegliere **Copia se più recente** nella casella di riepilogo **Copia nella directory di output**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla pubblicazione in Azure da Visual Studio, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).
