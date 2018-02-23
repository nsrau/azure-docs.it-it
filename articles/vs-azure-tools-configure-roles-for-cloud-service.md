---
title: Configurare i ruoli per un servizio cloud di Azure con Visual Studio | Documentazione Microsoft
description: Informazioni su come impostare e configurare i ruoli per i servizi cloud di Azure usando Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 17da71ac0c5ab9330b9244c0354e4d161d98229e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Configurare un ruolo per un servizio cloud di Azure con Visual Studio
Un servizio cloud di Azure può includere uno o più ruoli di lavoro o ruoli Web. Per ogni ruolo è necessario definire la modalità di configurazione e configurare la modalità di esecuzione. Per altre informazioni sui ruoli nei servizi cloud, vedere il video [Introduzione ai servizi cloud di Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

Le informazioni per il servizio cloud vengono archiviate nei file seguenti:

- **ServiceDefinition.csdef** - Il file di definizione del servizio definisce le impostazioni di runtime per il servizio cloud e include i ruoli richiesti, gli endpoint e le dimensioni della macchina virtuale. Nessun dato archiviato in `ServiceDefinition.csdef` può essere modificato durante l'esecuzione del ruolo.
- **ServiceConfiguration.cscfg** - Il file di configurazione del servizio configura il numero delle istanze di un ruolo che vengono eseguite e i valori delle impostazioni definiti per un ruolo. I dati archiviati in `ServiceConfiguration.cscfg` possono essere modificati durante l'esecuzione del ruolo.

Per archiviare valori diversi per le impostazioni che controllano l'esecuzione del ruolo, è possibile creare più configurazioni del servizio. È possibile usare una configurazione del servizio diversa per ogni ambiente di distribuzione. Ad esempio, è possibile configurare la stringa di connessione dell'account di archiviazione in modo che usi l'emulatore di archiviazione di Azure locale in una configurazione del servizio locale e creare un'altra configurazione del servizio in modo che usi l'archiviazione di Azure nel cloud.

Quando si crea un servizio cloud in Visual Studio, vengono automaticamente create due configurazioni del servizio che sono poi aggiunte al progetto Azure:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Configurare un servizio cloud di Azure
È possibile configurare un servizio cloud di Azure da Esplora soluzioni in Visual Studio, come illustrato nella procedura seguente:

1. Creare o aprire un progetto del servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Proprietà** dal menu di scelta rapida.
   
    ![Menu di scelta rapida di Esplora soluzioni](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Nella pagina delle proprietà del progetto, selezionare la scheda **Sviluppo**. 

    ![Pagina delle proprietà del progetto - Scheda Sviluppo](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. Nell'elenco **Configurazione del servizio** scegliere il nome della configurazione del servizio da modificare. Per apportare modifiche a tutte le configurazioni del servizio per questo ruolo, è possibile scegliere **Tutte le configurazioni**.
   
    > [!IMPORTANT]
    > Se si sceglie una configurazione del servizio specifica, alcune proprietà verranno disabilitate perché possono essere impostate solo per tutte le configurazioni. Per modificare queste proprietà, è necessario scegliere **Tutte le configurazioni**.
    > 
    > 
   
    ![Elenco Configurazione del servizio per un servizio cloud di Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Cambiare il numero di istanze del ruolo
Per migliorare le prestazioni del servizio cloud, è possibile cambiare il numero di istanze di un ruolo in esecuzione, in base al numero di utenti o al carico previsto per un ruolo specifico. Una macchina virtuale separata viene creata per ogni istanza di un ruolo quando il servizio cloud è in esecuzione in Azure. Ciò influirà sulla fatturazione per la distribuzione di questo servizio cloud. Per altre informazioni sulla fatturazione, vedere l'argomento contenente [informazioni sulla fatturazione per Microsoft Azure](billing/billing-understand-your-bill.md).

1. Creare o aprire un progetto di servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** espandere il nodo del progetto. Nel nodo **Ruoli**, fare clic con il pulsante destro del mouse sul ruolo da aggiornare e selezionare **Proprietà** dal menu di scelta rapida.

    ![Menu di scelta rapida di Esplora soluzioni di Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Scegliere la scheda **Configurazione**.

    ![Scheda Configurazione](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. Nell'elenco **Configurazione servizio** scegliere la configurazione del servizio da aggiornare.
   
    ![Elenco Configurazione servizio](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. Nella casella di testo **Conteggio istanze** immettere il numero di istanze da avviare per questo ruolo. Quando il servizio cloud viene pubblicato in Azure, ogni istanza viene eseguita in una macchina virtuale diversa.

    ![Aggiornamento del conteggio istanze](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Dalla barra degli strumenti di Visual Studio selezionare **Salva**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gestire le stringhe di connessione per gli account di archiviazione
È possibile aggiungere, rimuovere o modificare le stringhe di connessione per le configurazioni del servizio. Ad esempio, è possibile che si voglia creare una stringa di connessione locale per una configurazione del servizio locale con valore `UseDevelopmentStorage=true`. È anche possibile che si voglia definire una configurazione del servizio cloud che usa un account di archiviazione di Azure.

> [!WARNING]
> Quando si immettono le informazioni chiave dell'account di archiviazione di Azure per una stringa di connessione dell'account di archiviazione, tali informazioni vengono archiviate localmente nel file di configurazione del servizio. Queste informazioni, tuttavia, non vengono attualmente archiviate come testo crittografato.
> 
> 

Se si usa un valore diverso per ogni configurazione del servizio, non sarà necessario usare stringhe di connessione diverse nel servizio cloud o modificare il codice quando si pubblica il servizio cloud in Azure. È possibile usare lo stesso nome per la stringa di connessione nel codice e il valore sarà diverso in base alla configurazione del servizio selezionata quando si compila il servizio cloud o quando lo si pubblica.

1. Creare o aprire un progetto del servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** espandere il nodo del progetto. Nel nodo **Ruoli**, fare clic con il pulsante destro del mouse sul ruolo da aggiornare e selezionare **Proprietà** dal menu di scelta rapida.

    ![Menu di scelta rapida di Esplora soluzioni di Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Fare clic sulla scheda **Impostazioni**.

    ![Scheda Settings](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Nell'elenco **Configurazione servizio** scegliere la configurazione del servizio da aggiornare.

    ![Service Configuration](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Per aggiungere una stringa di connessione, scegliere **Aggiungi impostazione**.

    ![Aggiungere una stringa di connessione](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Dopo aver aggiunto la nuova impostazione all'elenco, aggiornare la riga nell'elenco con le informazioni necessarie.

    ![Nuova stringa di connessione](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nome**: digitare il nome da usare per la stringa di connessione.
    - **Tipo**: scegliere **Stringa di connessione** nell'elenco a discesa.
    - **Valore**: è possibile immettere la stringa di connessione direttamente nella cella **Valore** o selezionare i puntini di sospensione (...) per lavorare nella finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione**.  

1. Nella finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione**, selezionare un'opzione per **Connetti tramite**. Seguire quindi le istruzioni relative all'opzione selezionata:

    - **Emulatore di archiviazione di Microsoft Azure**: se si seleziona questa opzione, le impostazioni rimanenti nella finestra di dialogo vengono disabilitate, poiché sono valide solo per Azure. Selezionare **OK**.
    - **Sottoscrizione**: se si seleziona questa opzione, usare l'elenco a discesa per selezionare e accedere a un account Microsoft oppure aggiungere un account Microsoft. Selezionare una sottoscrizione e un account di archiviazione di Azure. Selezionare **OK**.
    - **Credenziali immesse manualmente**: immettere il nome dell'account di archiviazione e la chiave primaria o secondaria. Selezionare un'opzione per **Connessione**. Nella maggior parte dei casi è consigliato HTTPS. Selezionare **OK**.

1. Per eliminare una stringa di connessione, selezionarla e quindi scegliere **Rimuovi impostazione**.

1. Dalla barra degli strumenti di Visual Studio selezionare **Salva**.

## <a name="programmatically-access-a-connection-string"></a>Accedere a una stringa di connessione a livello di codice

La procedura seguente illustra come accedere a una stringa di connessione a livello di codice usando C#.

1. Aggiungere le seguenti direttive using al file C# in cui si vuole usare l'impostazione:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Il codice seguente è un esempio di come accedere a una stringa di connessione. Sostituire il segnaposto &lt;ConnectionStringName > con il valore appropriato. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Aggiungere impostazioni personalizzate da usare nel servizio cloud di Azure
Le impostazioni personalizzate nel file di configurazione del servizio consentono di aggiungere un nome e un valore per una stringa per una configurazione del servizio specifica. È possibile scegliere di usare questa impostazione per configurare una funzionalità nel servizio cloud leggendo il valore dell'impostazione e usando questo valore per controllare la logica nel codice. È possibile cambiare questi valori della configurazione del servizio senza dovere ricompilare il pacchetto del servizio o quando il servizio cloud è in esecuzione. Il codice può cercare notifiche in caso di modifiche di un'impostazione. Vedere l' [Evento RoleEnvironment.Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

È possibile aggiungere, rimuovere o modificare impostazioni personalizzate per le configurazioni del servizio. Potrebbero essere necessari diversi valori per queste stringhe per configurazioni del servizio diverse.

Se si usa un valore diverso per ogni configurazione del servizio, non sarà necessario usare stringhe diverse nel servizio cloud o modificare il codice quando si pubblica il servizio cloud in Azure. È possibile usare lo stesso nome per la stringa nel codice e il valore sarà diverso in base alla configurazione del servizio selezionata quando si compila il servizio cloud o quando lo si pubblica.

1. Creare o aprire un progetto del servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** espandere il nodo del progetto. Nel nodo **Ruoli**, fare clic con il pulsante destro del mouse sul ruolo da aggiornare e selezionare **Proprietà** dal menu di scelta rapida.

    ![Menu di scelta rapida di Esplora soluzioni di Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Fare clic sulla scheda **Impostazioni**.

    ![Scheda Settings](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Nell'elenco **Configurazione servizio** scegliere la configurazione del servizio da aggiornare.

    ![Elenco Configurazione servizio](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Per aggiungere un'impostazione personalizzata, selezionare **Aggiungi impostazione**.

    ![Aggiungere impostazioni personalizzate](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Dopo aver aggiunto la nuova impostazione all'elenco, aggiornare la riga nell'elenco con le informazioni necessarie.

    ![Nuova impostazione personalizzata](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nome**: inserire il nome dell'impostazione.
    - **Tipo**: scegliere **Stringa di connessione** dall'elenco a discesa.
    - **Valore**: inserire il valore dell'impostazione. È possibile immettere il valore direttamente nella cella **Valore** o selezionare i puntini di sospensione (...) per immettere il valore nella finestra di dialogo **Modifica stringa**.  

1. Per eliminare un'impostazione personalizzata, selezionarla e quindi scegliere **Rimuovi impostazione**.

1. Dalla barra degli strumenti di Visual Studio selezionare **Salva**.

## <a name="programmatically-access-a-custom-settings-value"></a>Accedere al valore dell'impostazione personalizzata a livello di codice
 
La procedura seguente illustra come accedere a un'impostazione personalizzata a livello di codice usando C#.

1. Aggiungere le seguenti direttive using al file C# in cui si vuole usare l'impostazione:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Il codice seguente è un esempio di come accedere a un'impostazione personalizzata. Sostituire il segnaposto &lt;SettingName> con il valore appropriato. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gestire le risorse di archiviazione locali per ogni istanza del ruolo
È possibile aggiungere una risorsa di archiviazione del file system locale per ogni istanza del ruolo. I dati archiviati nella risorsa di archiviazione non sono accessibili da altre istanze del ruolo per il quale sono archiviati, né da altri ruoli.  

1. Creare o aprire un progetto del servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** espandere il nodo del progetto. Nel nodo **Ruoli**, fare clic con il pulsante destro del mouse sul ruolo da aggiornare e selezionare **Proprietà** dal menu di scelta rapida.

    ![Menu di scelta rapida di Esplora soluzioni di Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selezionare la scheda **Risorsa di archiviazione locale**.

    ![Scheda Archiviazione locale](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. Nell'elenco **Configurazione del servizio**, verificare che l'opzione selezionata sia **Tutte le configurazioni**, poiché le impostazioni di archiviazione locale si applicano a tutte le configurazioni di servizio. Qualsiasi altro valore comporta la disabilitazione di tutti i campi di input della pagina. 

    ![Elenco Configurazione servizio](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Per aggiungere una voce relativa a una risorsa di archiviazione locale, selezionare **Aggiungi risorsa di archiviazione locale**.

    ![Aggiungi risorsa di archiviazione locale](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Dopo aver aggiunto la nuova risorsa di archiviazione locale all'elenco, aggiornare la riga nell'elenco con le informazioni necessarie.

    ![Nuova risorsa di archiviazione locale](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Nome**: digitare il nome da usare per la nuova risorsa di archiviazione locale.
    - **Dimensione (MB)**: immettere la dimensione in MB necessaria per la nuova risorsa di archiviazione locale.
    - **Pulisci a riciclo ruolo**: selezionare questa opzione per rimuovere i dati dalla nuova risorsa di archiviazione locale quando la macchina virtuale per il ruolo viene riciclata.

1. Per eliminare una risorsa di archiviazione locale, selezionarla e scegliere **Remove Local Storage** (Rimuovi risorsa di archiviazione locale).

1. Dalla barra degli strumenti di Visual Studio selezionare **Salva**.

## <a name="programmatically-accessing-local-storage"></a>Accedere alla risorsa di archiviazione locale a livello di codice

Questa sezione illustra come accedere alla risorsa di archiviazione locale a livello di codice usando C# per scrivere il file di testo di prova `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Scrivere un file di testo in una risorsa di archiviazione locale

Il codice seguente è un esempio di come scrivere un file di testo in una risorsa di archiviazione locale. Sostituire il segnaposto &lt;LocalStorageName> con il valore appropriato. 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>Trovare un file scritto in una risorsa di archiviazione locale

Per visualizzare il file creato dal codice nella sezione precedente, seguire questa procedura:
    
1.  Nell'area di notifica di Windows, fare clic con il pulsante destro del mouse sull'icona di Azure e, dal menu di scelta rapida, selezionare **Show Compute Emulator UI** (Mostra interfaccia utente dell'emulatore di calcolo). 

    ![Mostra emulatore di calcolo di Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Selezionare il ruolo Web.

    ![Emulatore di calcolo di Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. Dal menu **Emulatore di calcolo di Microsoft Azure**, selezionare **Strumenti** > **Open local store** (Apri risorsa di archiviazione locale).

    ![Voce di menu Apri risorsa di archiviazione locale](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Nella finestra di Esplora risorse visualizzata, immettere "MyLocalStorageTest.txt" nella casella di testo **Cerca** e premere **Invio** per avviare la ricerca. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui progetti Azure in Visual Studio, vedere [Configurazione di un progetto Azure](vs-azure-tools-configuring-an-azure-project.md). Per altre informazioni sullo schema del servizio cloud, vedere [Guida di riferimento agli schemi](https://msdn.microsoft.com/library/azure/dd179398).

