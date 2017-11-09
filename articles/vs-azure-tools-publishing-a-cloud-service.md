---
title: Pubblicazione di un Servizio cloud con gli Strumenti di Azure | Documentazione Microsoft
description: Informazioni su come pubblicare progetti di servizi cloud di Azure tramite Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/14/2017
ms.author: kraigb
ms.openlocfilehash: e617d600dbc8287eea737fc4969833e873365288
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Pubblicazione di un servizio cloud con gli strumenti di Azure
Utilizzando gli Strumenti di Azure per Microsoft Visual Studio, è possibile pubblicare l'applicazione Azure direttamente da Visual Studio. Visual Studio supporta la pubblicazione integrata per la gestione temporanea o l’ambiente di produzione di un servizio cloud.

Per poter pubblicare un'applicazione Azure è necessario avere una sottoscrizione di Azure. È necessario anche impostare un servizio cloud e un account di archiviazione che possano essere utilizzati dall'applicazione. È possibile impostarli nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!IMPORTANT]
> Quando si pubblica, è possibile selezionare l'ambiente di distribuzione per il servizio cloud. E’ necessario inoltre selezionare un account di archiviazione utilizzato per archiviare il pacchetto dell'applicazione durante la distribuzione. Dopo la distribuzione dell'applicazione, il pacchetto viene rimosso dall'account di archiviazione.
> 
> 

Durante lo sviluppo e il test di un'applicazione Azure, è possibile utilizzare Distribuzione Web per pubblicare le modifiche in modo incrementale per i ruoli web. Dopo aver pubblicato l'applicazione in un ambiente di distribuzione, Distribuzione Web consente di distribuire le modifiche apportate direttamente alla macchina virtuale che esegue il ruolo web. Non è necessario assemblare e pubblicare l'intera applicazione Azure ogni volta che si desidera aggiornare il ruolo web per testare le modifiche. Con questo approccio è possibile che le modifiche di un ruolo web siano disponibili nel cloud per il test senza attendere la pubblicazione dell’applicazione in un ambiente di distribuzione.

Utilizzare le procedure seguenti per pubblicare l'applicazione Azure e per aggiornare un ruolo web tramite Distribuzione Web:

* Pubblicare o Assemblare un'applicazione Azure da Visual Studio
* Aggiornare un ruolo web come parte del ciclo di sviluppo e test

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Pubblicare o Assemblare un'applicazione Azure da Visual Studio
Quando si pubblica l'applicazione Azure, è possibile eseguire una delle seguenti operazioni:

* Creare un pacchetto del servizio: è possibile usare questo pacchetto e il file di configurazione del servizio per pubblicare l'applicazione in un ambiente di distribuzione dal [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).
* Pubblicare il progetto Azure da Visual Studio: per pubblicare l'applicazione direttamente in Azure, si utilizza la Pubblicazione guidata. Per altre informazioni, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Per creare un pacchetto del servizio da Visual Studio
1. Quando si è pronti per pubblicare l'applicazione, aprire Esplora soluzioni, aprire il menu di scelta rapida per il progetto Azure contenente i ruoli e scegliere Pubblica.
2. Per creare un pacchetto del servizio, attenersi alla procedura seguente:  
   
   1. Sul menu di scelta rapida per il progetto Azure, scegliere **Pacchetto**.
   2. Nella finestra di dialogo **Crea pacchetto dell’applicazione Azure** scegliere la configurazione del servizio per il quale si desidera creare un pacchetto e quindi scegliere la configurazione di compilazione.
   3. (facoltativo) Per attivare Desktop remoto per il servizio cloud dopo la pubblicazione, selezionare la casella di controllo **Abilita Desktop remoto per tutti i ruoli**, quindi selezionare **Impostazioni** per configurare Desktop remoto. Se si desidera eseguire il debug del servizio cloud dopo la pubblicazione, attivare il debug remoto selezionando **Abilita Debugger remoto per tutti i ruoli**.
      
      Per altre informazioni, vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md).
   4. Per creare il pacchetto, scegliere il collegamento **Pacchetto** .
      
      Esplora file mostra il percorso del file del pacchetto appena creato. È possibile copiare questo percorso per usarlo dal [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).
   5. Per pubblicare il pacchetto in un ambiente di distribuzione, è necessario usare questo percorso come percorso del pacchetto quando si crea un servizio cloud e si distribuisce il pacchetto in un ambiente con il [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).
3. (Facoltativo) Per annullare il processo di distribuzione, nel menu di scelta rapida per la voce nel registro attività, scegliere **Annulla e Rimuovi**. Questo arresta il processo di distribuzione ed elimina l'ambiente di distribuzione da Azure.
   
   > [!NOTE]
   > Per rimuovere questo ambiente di distribuzione dopo che è stato distribuito, è necessario usare il [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).
   > 
   > 
4. (Facoltativo) Dopo l'avvio delle istanze del ruolo, Visual Studio mostrerà automaticamente l'ambiente di distribuzione nel nodo **Servizi cloud** in Esplora server. Da qui è possibile visualizzare lo stato delle singole istanze del ruolo. Vedere [Gestione delle risorse di Azure con Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). La figura seguente mostra le istanze del ruolo mentre si trovano ancora nello stato di inizializzazione:
   
    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Aggiornare un ruolo web come parte del ciclo di sviluppo e test
Se l'infrastruttura dell'applicazione back-end è stabile, ma i ruoli web necessitano di un aggiornamento più frequente, è possibile utilizzare Distribuzione Web per aggiornare solo un ruolo web nel progetto. Ciò è utile quando non si desidera ricompilare e ridistribuire i ruoli di lavoro back-end, o se si dispone di più ruoli web e si desidera aggiornare solo uno dei ruoli web.

### <a name="requirements"></a>Requisiti
Di seguito sono riportati i requisiti per utilizzare Distribuzione Web per aggiornare il ruolo web:

* **Solo per scopi di sviluppo e test:** Le modifiche vengono apportate direttamente alla macchina virtuale in cui è in esecuzione il ruolo web. Se è necessario riciclare questa macchina virtuale, le modifiche andranno perse perché il pacchetto originale pubblicato viene utilizzato per ricreare la macchina virtuale per il ruolo. È necessario ripubblicare l'applicazione per ottenere le modifiche più recenti per il ruolo web.
* **Possono essere aggiornati solo i ruoli web:** I ruoli di lavoro non possono essere aggiornati. Inoltre, non è possibile aggiornare RoleEntryPoint in web role.cs.
* **Può supportare solo una singola istanza di un ruolo web:** Non è possibile avere più istanze di qualsiasi ruolo web nell'ambiente di distribuzione. Tuttavia, sono supportati più ruoli web con una sola istanza.
* **E’ necessario abilitare le connessioni desktop remoto:** Questa operazione è necessaria in modo che Distribuzione Web possa utilizzare l'utente e la password per connettersi alla macchina virtuale per distribuire le modifiche al server in cui è in esecuzione Internet Information Services (IIS). Inoltre, potrebbe essere necessario connettersi alla macchina virtuale per aggiungere un certificato attendibile a IIS su questa macchina virtuale. (In questo modo la connessione remota per IIS utilizzata da Distribuzione Web è protetta).

La procedura seguente presuppone che si stia utilizzando la procedura guidata **Pubblica applicazione di Azure** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Per abilitare Distribuzione Web quando si pubblica l'applicazione
1. Per abilitare **Abilita Distribuzione Web** per tutte la casella di controllo tutti i ruoli web, è innanzitutto necessario configurare le connessioni desktop remoto. Selezionare **Abilita Desktop remoto per tutti i ruoli** e quindi specificare le credenziali usate per connettersi in remoto nella casella **Configurazione Desktop remoto** visualizzata. Vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md) per ulteriori informazioni.
2. Per abilitare Distribuzione Web per tutti i ruoli web nell'applicazione, selezionare **Abilita Distribuzione Web per tutti i ruoli web**.
   
    Viene visualizzato un triangolo giallo. Distribuzione Web utilizza un certificato autofirmato non attendibile per impostazione predefinita, che non è consigliato per il caricamento dei dati riservati. Se è necessario proteggere questo processo per i dati riservati, è possibile aggiungere un certificato SSL da utilizzare per le connessioni di Distribuzione Web. Questo certificato deve essere un certificato attendibile. Per informazioni su come eseguire questa operazione, vedere la sezione **Per proteggere Distribuzione Web** più avanti in questo argomento.
3. Scegliere **Avanti** per mostrare la schermata **Riepilogo** e quindi scegliere **Pubblica** per distribuire il servizio cloud.
   
    Il servizio cloud viene pubblicato. La macchina virtuale creata dispone di connessioni remote abilitate per IIS in modo che Distribuzione Web possa essere utilizzato per aggiornare i ruoli web senza ripubblicarli.
   
   > [!NOTE]
   > Se si dispone di più di un'istanza configurata per un ruolo web, viene visualizzato un messaggio di avviso che informa che ogni ruolo web sarà limitato a una sola istanza nel pacchetto creato per pubblicare l'applicazione. Selezionare **OK** per continuare. Come indicato nella sezione Requisiti, è possibile disporre di più di un ruolo web ma di solo un'istanza di ogni ruolo.
   > 
   > 

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Per aggiornare il ruolo Web tramite Distribuzione Web
1. Per utilizzare Distribuzione Web, apportare modifiche al codice del progetto per uno dei ruoli web in Visual Studio che si desidera pubblicare, quindi fare doppio clic su questo nodo del progetto nella soluzione e scegliere **Pubblica**. La finestra di dialogo **Pubblica sito Web** viene visualizzata.
2. (Facoltativo) Se è stato aggiunto un certificato SSL attendibile da utilizzare per le connessioni remote per IIS, è possibile deselezionare la casella di controllo **Consenti certificato non attendibile** . Per informazioni su come aggiungere un certificato per rendere Distribuzione Web sicuro, vedere la sezione **Per proteggere Distribuzione Web** più avanti in questo argomento.
3. Per utilizzare Distribuzione Web, il meccanismo di pubblicazione richiede il nome utente e la password impostati per la connessione desktop remoto alla prima pubblicazione del pacchetto.
   
   1. In **Nome utente**, immettere il nome utente.
   2. In **Password**, immettere la password.
   3. (Facoltativo) Se si desidera salvare la password in questo profilo, scegliere **Salva password**.
4. Per pubblicare le modifiche al proprio ruolo web, scegliere **Pubblica**.
   
    La riga di stato mostra **Pubblicazione avviata**. Quando la pubblicazione è stata completata, lo stato mostra **Pubblicazione completata** . Le modifiche a questo punto sono state distribuite al ruolo web nella macchina virtuale. Ora è possibile avviare l'applicazione Azure nell'ambiente di Azure per verificare le modifiche apportate.

### <a name="to-make-web-deploy-secure"></a>Per proteggere Distribuzione Web
1. Distribuzione Web utilizza un certificato autofirmato non attendibile per impostazione predefinita, che non è consigliato per il caricamento dei dati riservati. Se è necessario proteggere questo processo per i dati riservati, è possibile aggiungere un certificato SSL da utilizzare per le connessioni di Distribuzione Web. Questo certificato deve essere un certificato attendibile, ottenuto da un'autorità di certificazione (CA).
   
    Per proteggere Distribuzione Web per ogni macchina virtuale per ognuno dei ruoli Web, è necessario caricare il certificato attendibile che si vuole usare per Distribuzione Web nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885). Ciò garantisce che il certificato venga aggiunto alla macchina virtuale creata per il ruolo web quando si pubblica l'applicazione.
2. Per aggiungere un certificato SSL attendibile a IIS da utilizzare per le connessioni remote, attenersi alla seguente procedura:
   
   1. Per connettersi alla macchina virtuale che esegue il ruolo Web, selezionare l'istanza del ruolo Web in **Cloud Explorer** o **Esplora server**, quindi scegliere il comando **Connessione tramite desktop remoto**. Per la procedura dettagliata su come connettersi alla macchina virtuale, vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md).
      
      Il browser richiederà di scaricare un file RDP.
   2. Per aggiungere un certificato SSL, aprire il servizio di gestione in Gestione IIS. In Gestione IIS abilitare SSL aprendo il collegamento **Binding** nel riquadro **Azione**. La finestra di dialogo **Aggiungi binding del sito** verrà visualizzata. Scegliere **Aggiungi**, quindi scegliere HTTPS nell'elenco a discesa **Tipo**. Nell'elenco **Certificato SSL** scegliere il certificato SSL firmato da una CA e caricato nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885). Per altre informazioni, vedere [Configurare le impostazioni di connessione per il servizio di gestione](http://go.microsoft.com/fwlink/?LinkId=215824).
      
      > [!NOTE]
      > Se si aggiunge un certificato SSL attendibile, il triangolo giallo di avviso non viene visualizzato nella **Pubblicazione guidata**.
      > 
      > 

## <a name="include-files-in-the-service-package"></a>Includere file nel pacchetto del servizio
Potrebbe essere necessario includere file specifici nel pacchetto di servizio in modo che siano disponibili nella macchina virtuale creata per un ruolo. Ad esempio, si potrebbe desiderare aggiungere un file con estensione exe o msi utilizzato da uno script di avvio per il pacchetto del servizio. Oppure potrebbe essere necessario aggiungere un assembly richiesto da un progetto di ruolo web o ruolo di lavoro. Per includere i file essi devono essere aggiunti alla soluzione per l'applicazione Azure.

### <a name="to-include-files-in-the-service-package"></a>Per includere file nel pacchetto del servizio
1. Per aggiungere un assembly a un pacchetto del servizio, utilizzare la procedura seguente:
   
   1. In **Esplora soluzione** aprire il nodo del progetto per il progetto in cui manca l'assembly di riferimento.
   2. Per aggiungere l'assembly al progetto, aprire il menu di scelta rapida per la cartella **Riferimenti** e quindi scegliere **Aggiungi riferimento**. Viene visualizzata la finestra di dialogo Aggiungi riferimento.
   3. Scegliere il riferimento che si desidera aggiungere e quindi scegliere il pulsante **OK** .
      
      Il riferimento viene aggiunto all'elenco sotto la cartella **Riferimenti** .
   4. Aprire il menu di scelta rapida per l'assembly aggiunto e scegliere **Proprietà**. La finestra **Proprietà** verrà visualizzata.
      
      Per includere questo assembly nel pacchetto del servizio, nell'elenco **Copia locale** scegliere **True**.
2. In **Esplora soluzione** aprire il nodo del progetto per il progetto in cui manca l'assembly di riferimento.
3. Per aggiungere l'assembly al progetto, aprire il menu di scelta rapida per la cartella **Riferimenti** e quindi scegliere **Aggiungi riferimento**. Viene visualizzata la finestra di dialogo **Aggiungi riferimento** .
4. Scegliere il riferimento che si desidera aggiungere e quindi scegliere il pulsante **OK** .
   
    Il riferimento viene aggiunto all'elenco sotto la cartella **Riferimenti** .
5. Aprire il menu di scelta rapida per l'assembly aggiunto e scegliere **Proprietà**. La finestra Proprietà verrà visualizzata.
6. Per includere questo assembly nel pacchetto del servizio, nell'elenco **Copia locale** scegliere **True**.
7. Per includere file nel pacchetto del servizio che sono stati aggiunti al progetto del ruolo web, aprire il menu di scelta rapida per il file e quindi scegliere **Proprietà**. Dalla finestra **Proprietà** scegliere **Contenuto** nella casella di riepilogo **Azione di compilazione**.
8. Per includere file nel pacchetto del servizio che sono stati aggiunti al progetto del ruolo di lavoro, aprire il menu di scelta rapida per il file e quindi scegliere **proprietà**. Dalla finestra **Proprietà** scegliere **Copia se più recente** nella casella di riepilogo **Copia nella directory di output**.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla pubblicazione in Azure da Visual Studio, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).

