---
title: Preparare la pubblicazione o la distribuzione di un&quot;applicazione di Azure da Visual Studio | Documentazione Microsoft
description: Informazioni sulle procedure per la configurazione di servizi cloud e dell&quot;account di archiviazione e per la configurazione dell&quot;applicazione Azure.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/15/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 53480aca2ebe3f9ef6221fbdcd3b9ec13c03d31a


---
# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Preparare la pubblicazione o la distribuzione di un'applicazione di Azure da Visual Studio
## <a name="overview"></a>Overview
Per potere pubblicare un progetto di servizio cloud, è prima di tutto necessario configurare i servizi seguenti:

* Un **servizio cloud** per l'esecuzione dei ruoli nell'ambiente Azure.
* Un **account di archiviazione** che fornisce l'accesso ai servizi BLOB, code e tabelle.

Usare le procedure seguenti per impostare questi servizi e configurare l'applicazione.

## <a name="create-a-cloud-service"></a>Creare un servizio cloud
Per pubblicare un servizio cloud in Azure, è prima di tutto necessario creare un servizio cloud, che esegue i ruoli nell'ambiente Azure. È possibile creare un servizio cloud nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885)come descritto nella sezione **Per creare un servizio cloud usando il portale di Azure classico**, più avanti in questo argomento. È anche possibile creare un servizio cloud in Visual Studio usando la Pubblicazione guidata.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Per creare un servizio cloud usando Visual Studio
1. Aprire il menu di scelta rapida per il progetto Azure, quindi scegliere **Pubblica**.
   
    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)
2. Se non è ancora stato fatto, accedere con il nome utente e la password dell'account Microsoft o dell'account aziendale associato alla sottoscrizione di Azure.
3. Scegliere il pulsante **Avanti** per passare alle pagina **Impostazioni**.
   
    ![Impostazioni comuni della Pubblicazione guidata](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)
4. Nell'elenco **Servizi cloud** scegliere **Crea nuovo**. La finestra di dialogo **Crea Servizi di Azure** .
5. Immettere il nome del servizio cloud. Il nome fa parte dell'URL per il servizio, quindi deve essere globalmente univoco. Nel nome non viene fatta distinzione tra maiuscole e minuscole.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Per creare un servizio cloud usando il portale di Azure classico
1. Accedere al [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkId=253103) nel sito Web Microsoft.
2. (Facoltativo) Per visualizzare un elenco di servizi cloud già creati, selezionare il collegamento Servizi cloud sul lato sinistro della pagina.
3. Fare clic sull'icona **+** nell'angolo inferiore sinistro, quindi scegliere **Servizio cloud** dal menu visualizzato. Verrà visualizzata un'altra schermata con due opzioni disponibili, **Creazione rapida** e **Creazione personalizzata**. L'opzione **Creazione rapida**consente di creare un servizio cloud semplicemente specificandone l'URL e l'area in cui sarà ospitato fisicamente. L'opzione **Creazione personalizzata**consente di pubblicare immediatamente un servizio cloud specificando un pacchetto (file con estensione .cspkg), un file di configurazione (file con estensione .cscfg) e un certificato. La Creazione personalizzata non è necessaria se si intende pubblicare il servizio cloud usando il comando **Pubblica** in un progetto Azure. Il comando **Pubblica** è disponibile nel menu di scelta rapida per un progetto Azure.
4. Scegliere l'opzione **Creazione rapida** per pubblicare il servizio cloud in un secondo momento usando Visual Studio.
5. Specificare un nome per il servizio cloud. Accanto al nome verrà visualizzato l'URL completo.
6. Nell'elenco scegliere l'area in cui si trova la maggior parte degli utenti.
7. Nella parte inferiore della finestra fare clic sul collegamento **Crea servizio cloud** .

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Un account di archiviazione fornisce l'accesso ai servizi BLOB, code e tabelle. È possibile creare un account di archiviazione usando Visual Studio o il [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Per creare un account di archiviazione usando Visual Studio
1. In **Esplora soluzioni** aprire il menu di scelta rapida per il nodo **Archiviazione**, quindi scegliere **Crea account di archiviazione**.
   
    ![Creare un nuovo account di archiviazione di Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)
2. Selezionare o immettere le informazioni seguenti per il nuovo account di archiviazione nella finestra di dialogo **Crea account di archiviazione** .
   
   * Sottoscrizione di Azure a cui aggiungere l'account di archiviazione.
   * Nome da usare per il nuovo account di archiviazione.
   * Area o set di affinità (ad esempio Stati Uniti occidentali o Asia orientale).
   * Tipo di replica da usare per l'account di archiviazione, ad esempio Con ridondanza geografica.
3. Al termine dell'operazione scegliere **Crea**. Il nuovo account di archiviazione viene visualizzato nell'elenco **Archiviazione** in **Esplora server**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Per creare un account di archiviazione usando il portale di Azure classico
1. Accedere al [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkId=253103) nel sito Web Microsoft.
2. (Facoltativo) Per visualizzare gli account di archiviazione, fare clic sul collegamento **Archiviazione** nel riquadro sul lato sinistro della pagina.
3. Nell'angolo inferiore sinistro della pagina fare clic sull'icona **+** .
4. Nel menu visualizzato scegliere **Archiviazione**, quindi **Creazione rapida**.
5. Assegnare un nome all'account di archiviazione che rappresenterà un URL univoco.
6. Assegnare un nome al servizio cloud. Accanto al nome verrà visualizzato l'URL completo.
7. Nell'elenco delle aree scegliere l'area in cui si trova la maggior parte degli utenti.
8. Specificare se si vuole abilitare la replica geografica. Se la si abilita, i dati saranno salvati in più ubicazioni fisiche per ridurre i rischi di perdita dei dati. Questa funzionalità rende più costoso il processo di archiviazione. Tuttavia è possibile ridurre il costo abilitando la georilevazione quando si crea l'account di archiviazione anziché aggiungere la funzionalità in un secondo momento. Per altre informazioni, vedere [Replica geografica](http://go.microsoft.com/fwlink/?LinkId=253108).
9. Nella parte inferiore della finestra fare clic sul collegamento **Crea account di archiviazione** .

Dopo la creazione dell'account di archiviazione, saranno visualizzati gli URL che è possibile usare per accedere alle risorse di ogni servizio di archiviazione di Azure, nonché la chiavi di accesso primaria e secondaria per l'account. Questi tasti possono essere usati per autenticare le richieste eseguite in relazione ai servizi di archiviazione.

> [!NOTE]
> La chiave di accesso secondaria fornisce lo stesso accesso all'account di archiviazione offerto da quella primaria e viene generata come backup nel caso in cui la chiave di accesso primaria dovesse essere compromessa. È anche consigliabile rigenerare regolarmente le chiavi di accesso. È possibile modificare l'impostazione di una stringa di connessione per usare la chiave secondaria mentre si rigenera quella primaria, quindi procedere nuovamente alla modifica per usare la chiave primaria rigenerata mentre si rigenera quella secondaria.
> 
> 

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configurare l'app per usare servizi forniti dall'account di archiviazione
È necessario configurare un ruolo che acceda ai servizi di archiviazione per usare i servizi di archiviazione di Azure creati. A questo scopo, è possibile usare più configurazioni del servizio per il progetto Azure. Per impostazione predefinita, nel progetto Azure vengono create due configurazioni. L'uso di più configurazioni del servizio consente di usare la stessa stringa di connessione nel codice, ma la stringa di connessione in ogni configurazione del servizio avrà un valore diverso. Ad esempio, è possibile usare una configurazione del servizio per eseguire ed effettuare il debug dell'applicazione in locale usando l'emulatore di archiviazione di Azure e una configurazione del servizio diversa per pubblicare l'applicazione in Azure. Per altre informazioni sulle configurazioni del servizio, vedere [Configurazione di un progetto Azure usando configurazioni del servizio multiple](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Per configurare l'applicazione per usare i servizi forniti dall'account di archiviazione
1. In Visual Studio aprire la soluzione Azure. In Esplora soluzioni aprire il menu di scelta rapida per ogni ruolo nel progetto Azure che accede ai servizi di archiviazione e scegliere **Proprietà**. Una pagina con il nome del ruolo viene visualizzata nell'editor di Visual Studio. La pagina visualizza i campi per la scheda **Configurazione** .
2. Nelle pagine delle proprietà per il ruolo scegliere **Impostazioni**.
3. Nell'elenco **Configurazione del servizio** scegliere il nome della configurazione del servizio da modificare. Per apportare modifiche a tutte le configurazioni del servizio per questo ruolo, è possibile scegliere **Tutte le configurazioni**.  Per altre informazioni su come aggiornare le configurazioni del servizio, vedere la sezione **Gestire le stringhe di connessione per gli account di archiviazione** nell'argomento [Configurare i ruoli per un servizio cloud di Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
4. Per modificare un'impostazione della stringa di connessione, scegliere il pulsante **…**  accanto all'impostazione. Verrà visualizzata la finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione** .
5. In **Connetti tramite** scegliere l'opzione **Sottoscrizione**.
6. Nell'elenco **Sottoscrizione** scegliere la sottoscrizione da usare. Se l'elenco delle sottoscrizioni non include quella desiderata, selezionare il collegamento **Download impostazioni di pubblicazione** .
7. Scegliere il nome dell'account di archiviazione dall'elenco **Nome account** . Gli strumenti di Azure ottengono le credenziali dell'account di archiviazione automaticamente tramite il file con estensione publishsettings. Per specificare le credenziali dell'account di archiviazione manualmente, scegliere l'opzione **Credenziali immesse manualmente** e quindi continuare la procedura. È possibile ottenere la chiave primaria e il nome dell'account di archiviazione dal [portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=213885). Se non si vogliono specificare le impostazioni dell'account di archiviazione manualmente, fare clic sul pulsante **OK** per chiudere la finestra di dialogo.
8. Fare clic sul collegamento **Immettere le credenziali dell'account di archiviazione** .
9. Nella casella **Nome account** immettere il nome dell'account di archiviazione.
   
   > [!NOTE]
   > Accedere al [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885)e quindi fare clic su **Archiviazione** . Il portale mostra un elenco di account di archiviazione. Se si sceglie un account, viene aperta una pagina corrispondente. È possibile copiare il nome dell'account di archiviazione da questa pagina. Se si usa una versione precedente del portale classico, il nome dell'account di archiviazione viene indicato nella visualizzazione **Account di archiviazione** . Per copiare questo nome, evidenziarlo nella finestra **Proprietà** della visualizzazione e premere CTRL+C. Per incollare il nome in Visual Studio, fare clic nella casella di testo **Nome account** e premere CTRL+V.
   > 
   > 
10. Nella casella **Chiave account** immettere la chiave primaria oppure copiarla dal [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885)e incollarla.
     Per copiare la chiave:
    
    1. Nella parte inferiore della pagina per l'account di archiviazione appropriato selezionare il pulsante **Gestisci chiavi** .
    2. Nella pagina **Gestisci chiavi di accesso** selezionare il testo della chiave di accesso primaria, quindi premere CTRL+C.
    3. Negli strumenti di Azure incollare la chiave nella casella **Chiave account** .
    4. È necessario selezionare una delle opzioni seguenti per determinare le modalità di accesso del servizio all'account di archiviazione:
       
       * **Usa HTTP**. Si tratta dell'opzione standard. Ad esempio: `http://<account name>.blob.core.windows.net`.
       * **Usa HTTPS** per una connessione sicura. Ad esempio: `https://<accountname>.blob.core.windows.net`.
       * **Specifica endpoint personalizzati** per ciascuno dei tre servizi. È quindi possibile digitare questi endpoint nel campo per il servizio specifico.
         
         > [!NOTE]
         > Se si creano endpoint personalizzati, sarà possibile creare una stringa di connessione più complessa. Quando si usa questo formato stringa, è possibile specificare endpoint del servizio di archiviazione che includono un nome di dominio personalizzato registrato per l'account di archiviazione con il servizio BLOB. È anche possibile concedere l'accesso solo alle risorse BLOB in un singolo contenitore, tramite una firma di accesso condiviso. Per altre informazioni sulla creazione di endpoint personalizzati, vedere [Configurare le stringhe di connessione di archiviazione di Azure](storage/storage-configure-connection-string.md).
         > 
         > 
11. Per salvare queste modifiche della stringa di connessione, scegliere **OK**, quindi scegliere il pulsante **Salva**. Dopo avere salvato queste modifiche, è possibile ottenere il valore di questa stringa di connessione nel codice tramite [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Quando si pubblica l'applicazione in Azure, scegliere la configurazione del servizio che contiene l'account di archiviazione di Azure per la stringa di connessione. Dopo la pubblicazione, verificare che l'applicazione funzioni come previsto rispetto ai servizi di archiviazione Azure.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla pubblicazione di app in Azure da Visual Studio, vedere [Pubblicazione di un servizio Cloud con gli strumenti di Azure](vs-azure-tools-publishing-a-cloud-service.md).




<!--HONumber=Nov16_HO3-->


