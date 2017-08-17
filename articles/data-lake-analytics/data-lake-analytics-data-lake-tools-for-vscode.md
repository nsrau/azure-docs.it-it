---
title: Strumenti Azure Data Lake - Usare gli strumenti Azure Data Lake per Visual Studio Code | Microsoft Docs
description: 'Informazioni su come usare gli strumenti di Azure Data Lake per Visual Studio Code per creare, testare ed eseguire gli script U-SQL. '
Keywords: VScode, Strumenti Azure Data Lake, Esecuzione locale, debug locale, Debug Locale, anteprima file di archiviazione, caricamento nel percorso di archiviazione
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Usare gli strumenti di Azure Data Lake per Visual Studio Code

Informazioni su come usare gli strumenti di Azure Data Lake per Visual Studio Code (VSCode) per creare, testare ed eseguire gli script U-SQL.  Le informazioni sono disponibili anche nel video seguente:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Prerequisiti

Gli strumenti di Azure Data Lake possono essere installati sulle piattaforme supportate da VSCode che includono Windows, Linux e MacOS. È possibile reperire i prerequisiti per piattaforme differenti

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). È necessario aggiungere il percorso del file java.exe alla variabile di ambiente path.  Per le istruzioni vedere [Come impostare o modificare la variabile di sistema Path]( https://www.java.com/download/help/path.xml) Il percorso è simile a c:\Programmi\Java\jdk1.8.0_77\jre\bin
    - [Runtime di .NET Core SDK 1.0.3 o .NET Core 1.1](https://www.microsoft.com/net/download).
    
- Linux (è consigliabile scegliere Ubuntu 14.04 LTS)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx). Immettere il comando seguente per installare:

        sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/). 

        - Aggiornare l'origine pacchetto deb con i comandi seguenti:

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - Installare mono eseguendo il comando:

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 non è supportato.  È necessario disinstallare completamente la versione 4.6 prima di installare la versione 4.2.x.  

        - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). Per istruzioni vedere [qui]( https://java.com/en/download/help/linux_x64_install.xml).
        - [Runtime di .NET Core SDK 1.0.3 o .NET Core 1.1](https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). Per istruzioni vedere [qui](https://java.com/en/download/help/mac_install.xml).
    - [Runtime di .NET Core SDK 1.0.3 o .NET Core 1.1](https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Installare gli strumenti di Data Lake

Dopo aver installato i prerequisiti, è possibile installare gli strumenti di Data Lake per VSCode.

**Installare gli strumenti di Data Lake**

1. Aprire **Visual Studio Code**.
2. Premere **CTRL+P** e immettere:
```
ext install usql-vscode-ext
```
È possibile visualizzare un elenco delle estensioni di codice di Visual Studio, tra cui gli **Strumenti Azure Data Lake**.

3. Fare clic su **Installa** accanto a **Azure Data Lake Tool** (Strumento di Azure Data Lake). Dopo alcuni secondi, al pulsante Installa si sostituirà il pulsante Ricarica.
4. Fare clic su **Ricarica** per attivare l'estensione.
5. Fare clic su **OK** per confermare. È possibile visualizzare gli strumenti di Azure Data Lake nel riquadro Estensioni.
    ![Installazione di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Attivare Strumenti Azure Data Lake
Creare un nuovo file USQL o aprire un file USQL esistente per attivare l'estensione. 

## <a name="connect-to-azure"></a>Connect to Azure

Prima di compilare ed eseguire gli script U-SQL in Azure Data Lake Analytics, è necessario connettersi al proprio account di Azure.

**Connettersi ad Azure**

1.  Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**. 
2.  Immettere **ADL: Login** (ADL: Accedi). Le informazioni di accesso sono visualizzate nel riquadro di output.

    ![Riquadro comandi di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Informazioni di accesso di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Premere CTRL e fare clic sull'URL di accesso: https://aka.ms/devicelogin per aprire la pagina Web di accesso. Copiare e incollare il codice G567LX42V nella casella di testo sottostante, fare clic su Continua per proseguire.

   ![Codice di accesso da incollare di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Seguire le istruzioni per accedere dalla pagina Web. Dopo essersi connessi, il nome dell'account di Azure viene visualizzato sulla barra di stato nella parte inferiore della finestra di VSCode. 

    > [!NOTE] 
    > Se l'account dispone di due fattori abilitati, è consigliabile usare l'autenticazione tramite telefono anziché tramite codice PIN.

Per disconnettersi, usare il comando **ADL: Logout** (ADL: Disconnetti)

## <a name="list-data-lake-analytics-accounts"></a>Elencare gli account di Data Lake Analytics

Per testare la connessione, è possibile elencare gli account di Data Lake Analytics:

**Elencare gli account di Data Lake Analytics sotto una sottoscrizione di Azure**

1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**.
2. Digitare **ADL: List Accounts** (ADL: Elenca account).  L'account viene visualizzato nel riquadro **Output**.

## <a name="open-sample-script"></a>Aprire uno script di esempio

Usare il riquadro comandi (**CTRL+MAIUSC+P**) e scegliere **ADL: Open Sample Script** (ADL: apri script di esempio). Verrà quindi aperta un'altra istanza per questo esempio. Anche in questa istanza è possibile modificare, configurare, inviare lo script.

## <a name="work-with-u-sql"></a>Uso di U-SQL

Per poter usare U-SQL è necessario aprire una cartella o un file U-SQL.

**Aprire una cartella per il progetto U-SQL**

1. Da Visual Studio Code fare clic sul menu **File** e selezionare **Apri cartella**.
2. Specificare una cartella e fare clic su **Seleziona cartella**.
3. Fare clic sul menu **File** e selezionare **Nuovo**. Un file **Untilted-1** viene aggiunto al progetto.
4. Copiare e incollare il codice seguente nel file Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO “/Output/departments.csv”

    Lo script crea un file departments.csv con alcuni dati nella cartella /output.

5. Salvare il file come **myUSQL.usql** nella cartella aperta. Si noti che al progetto viene aggiunto anche il file di configurazione **adltools_settings.json**.
4. Aprire e configurare il file **adltools_settings.json** con le proprietà seguenti:

    - Account: un account di Data Lake Analytics sotto la sottoscrizione di Azure.
    - Database: un database nel proprio account. Il database predefinito è il master.
    - Schema: uno schema nel database. Lo schema è dbo.
    - Impostazioni facoltative:
        - Priorità: L'intervallo di priorità va da 1 a 1000 dove la priorità più alta corrisponde al valore 1. Il valore predefinito è 1000.
        - Parallelismo: l'intervallo di parallelismo va da 1 a 150. Il valore predefinito è il parallelismo massimo consentito nell'account di ADLA. 
        
        > [!NOTE] 
        > Se le impostazioni non sono valide, vengono applicati i valori predefiniti.

    ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Si richiede un account di Data Lake Analytics di calcolo per poter compilare ed eseguire i processi di U-SQL.  È necessario configurare l'account del computer prima di compilare ed eseguire i processi di U-SQL.
    
    Dopo il salvataggio della configurazione, le informazioni account|database|schema vengono visualizzate sulla barra di stato nella parte inferiore sinistra del file USQL corrispondente. 
 
 

Rispetto a quando si apre un file, l'apertura della cartella consente di:

- Usare il file code-behind.  Code-behind non è supportato in modalità file unico.
- Usare il file di configurazione. Quando si apre una cartella, gli script nella cartella di lavoro condividono un file di configurazione.


La compilazione di script U-SQL viene eseguita da remoto da parte del servizio Data Lake Analytics.  Quando si esegue il comando di compilazione, lo script U-SQL viene inviato all'account di Data Lake Analytics. Il risultato di compilazione viene poi ricevuto da Visual Studio Code. Per via della compilazione remota, Visual Studio Code richiede le informazioni per connettersi all'account di Data Lake Analytics nel file di configurazione.

**Compilare uno script U-SQL**

1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**. 
2. Immettere **ADL: Compile Script**. I risultati di compilazione vengono mostrati nella finestra di output. È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Compile Script** per eseguire la compilazione di un processo U-SQL. Il risultato di compilazione viene visualizzato nel riquadro di output.
 

**Inviare uno script U-SQL**

1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**. 
2. Immettere **ADL: Submit Job**.  È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Submit Job** per inviare un processo U-SQL. 

Dopo l'invio di un processo U-SQL, i log di invio vengono visualizzati nella finestra di output in VSCode. Se l'invio ha esito positivo, viene visualizzato anche l'URL del processo, che può essere aperto in un Web browser per monitorare lo stato del processo in tempo reale.

Per abilitare i dettagli dei processi di output: impostare 'jobInformationOutputPath' nel file **vscode for u-sql_settings.json**.
 
## <a name="use-code-behind-file"></a>Usare il file code-behind

Il file code-behind è un file CSharp associato a uno script U-SQL. È possibile definire l'opzione UDO/UDA/UDT/UDF dedicata allo script nel file code-behind. L'opzione UDO/UDA/UDT/UDF può essere usata direttamente nello script senza dover prima registrare l'assembly. Il file code-behind viene inserito nella stessa cartella del suo file di script U-SQL associato. Se lo script viene denominato xxx.usql, code-behind assume il nome xxx.usql.cs. L'eliminazione manuale del file code-behind disabilita la funzionalità Code-behind per lo script U-SQL associato. Per altre informazioni sulla scrittura del codice cliente per lo script U-SQL, vedere [Scrivere e usare il codice personalizzato in U-SQL: funzioni definite dall'utente]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

Per supportare code-behind, è necessario aprire una cartella di lavoro. 

**Generare un file code-behind**

1. Aprire un file di origine. 
2. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**.
3. Immettere **ADL: Generate Code Behind**.  Viene creato un file code-behind nella stessa cartella. 

È anche possibile fare clic con il pulsante destro del mouse su un file di script e scegliere **ADL: Generate Code Behind** (ADL: genera code-behind) per generare un file code-behind. 

La procedura di compilazione e invio di uno script U-SQL con code-behind è la stessa usata per lo script U-SQL indipendente.

Le due schermate seguenti mostrano un file code-behind e il suo file di script U-SQL associato:
 
![Code-behind degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Code-behind degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>Usare gli assembly

Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).

Usando gli strumenti di Data Lake è possibile registrare gli assembly di codice personalizzato nel catalogo di Data Lake Analytics.

**Registrare un assembly**

È possibile registrare l'assembly tramite **ADL: Register Assembly** (ADL: Registra assembly) o **ADL: Register Assembly through Configuration** (ADL: Regista assembly da configurazione).

**ADL: Register Assembly**
1.  Premere **CTRL+MAIUSC+P** per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly** (ADL: Registra assembly). 
3.  Specificare il percorso dell'assembly locale. 
4.  Selezionare un account di Data Lake Analytics.
5.  Selezionare un database.

Risultati: il portale viene aperto nel browser e visualizza il processo di registrazione dell'assembly.  

Un altro modo pratico per attivare il comando **ADL: Register Assembly** (ADL: Registra assembly) consiste nel fare clic con il pulsante destro del mouse sul file con estensione dll in EXPLORER. 

**ADL: Register Assembly through Configuration**.
1.  Premere **CTRL+MAIUSC+P** per aprire il riquadro comandi.
2.  Immettere **ADL: Register Assembly through Configuration** (ADL: Registra assembly da configurazione). 
3.  Specificare il percorso dell'assembly locale. 
4.  Verrà visualizzato il file con estensione json. Esaminare e modificare le dipendenze e dell'assembly e i parametri delle risorse, se necessario. I risultati verranno visualizzati nella finestra di output. Salvare (**CTRL+S**) il file Json per procedere con la registrazione dell'assembly.

![Code-behind degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dipendenze dell'assembly: Strumenti Azure Data Lake rileva automaticamente se la DLL include delle dipendenze. Le dipendenze vengono visualizzate nel file Json dopo che vengono rilevate. 
>- Risorse: è possibile caricare le risorse DLL (ad esempio, txt, png, csv e così via) come parte della registrazione dell'assembly. 

Un altro modo pratico per attivare il comando **ADL: Register Assembly through Configuration** (ADL: Registra assembly da configurazione) consiste nel fare clic con il pulsante destro del mouse sul file con estensione dll in EXPLORER. 

Il codice U-SQL seguente illustra come chiamare un assembly. Nell'esempio il nome dell'assembly è *test*.

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```


## <a name="access-data-lake-analytics-catalog"></a>Accedere al catalogo di Data Lake Analytics

Dopo essersi connessi ad Azure, è possibile seguire questa procedura per accedere al catalogo di U-SQL:

**Per accedere ai metadati di Azure Data Lake Analytics**

1.  Premere **CTRL+MAIUSC+P** e digitare **ADL: List Tables** (ADL: Elenca tabelle).
2.  Fare clic su uno degli account di Data Lake Analytics.
3.  Fare clic su uno dei database di Data Lake Analytics.
4.  Fare clic su uno degli schemi. Le tabelle vengono visualizzate.

## <a name="show-data-lake-analytics-jobs"></a>Visualizzare processi di Data Lake Analytics

Usare il riquadro comandi (**CTRL+MAIUSC+P**) e scegliere **ADL: Show Job** (ADL: visualizza processo). 

1.  Selezionare un account ADLA o uno locale 
2.  Attendere che venga visualizzato l'elenco di processi per l'account
3.  Selezionare un processo nell'elenco. ADL Tools apre i dettagli del processo nel portale e visualizza il file JobInfo in VSCode.

![Tipi di oggetto IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Integrazione di Azure Data Lake Store (ADLS)

È possibile usare i comandi relativi ad ADLS per esplorare le risorse di ADLS, visualizzare in anteprima il file di ADLS e caricare il file in ADLS direttamente in VSCode.  
### <a name="list-storage-path"></a>Elencare il percorso di archiviazione

Usare il riquadro comandi (**CTRL+MAIUSC+P**) e scegliere **ADL: List Storage Path** (ADL: elenca percorso di archiviazione).
1.  Aprire il riquadro comandi e inserire il comando.

    ![Elencare il percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  Selezionare il modo preferito per elencare il percorso di archiviazione. Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio.

    ![List Storage Path (Elenca percorso di archiviazione) di Strumenti Data Lake per Visual Studio Code per selezionare un modo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- Visual Studio Code mantiene il percorso visitato per ultimo con ogni account ADLA. Esempio：/tt/ss.
    >- Browse from root path (Sfoglia da percorso radice): percorso radice elenco da cui si seleziona l'account ADLA o locale.
    >- Enter a path (Immetti un percorso): percorso specificato elenco da cui si seleziona l'account ADLA o locale.
    
3. Selezionare un account locale o ADLA.

    ![Selezionare altro in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Fare ancora clic per elencare altri account ADLA e selezionarne uno.

    ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Inserire un percorso di archiviazione di Azure. Ad esempio, /output/

       ![Inserire un percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  Risultati: il riquadro comandi elenca le informazioni sul percorso in base agli input.

    ![Risultato dell'inserimento del percorso di archiviazione di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

Un altro modo più pratico per elencare il percorso relativo consiste nell'usare il menu di scelta rapida.

1.  Fare clic con il pulsante destro del mouse sulla stringa del percorso e scegliere List Storage Path (Elenca percorso di archiviazione).

       ![Menu di scelta rapida di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. Il percorso relativo selezionato viene visualizzato nel riquadro comandi.

   ![Elenco percorso selezionato in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  Selezionare un account locale o ADLA.

       ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Risultati: il riquadro comandi elenca le cartelle e i file per il percorso corrente.

       ![Elencare il percorso corrente in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>Visualizzare in anteprima il file di archiviazione

Usare il riquadro comandi (**CTRL+MAIUSC+P**) e scegliere **ADL: Preview Storage File** (ADL: anteprima file di archiviazione).
1.  Aprire il riquadro comandi e inserire il comando.

       ![File di archiviazione di anteprima in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  Selezionare un account locale o ADLA.

       ![Elencare l'account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Fare ancora clic per elencare altri account ADLA e selezionarne uno.

       ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Inserire un file o un percorso di archiviazione di Azure. Ad esempio: /output/SearchLog.txt

       ![Inserimento di file o percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  Risultati: il riquadro comandi elenca le informazioni sul percorso in base agli input.

       ![Risultato del file di anteprima in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

Un altro modo più pratico per visualizzare l'anteprima di un file consiste nell'usare il menu di scelta rapida.

1.  Fare clic con il pulsante destro del mouse sul percorso di un file per visualizzare l'anteprima di un file.

       ![Menu di scelta rapida di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  Selezionare un account locale o ADLA.

       ![Selezionare un account in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Risultati: VSCode visualizza i risultati di anteprima del file.

       ![Risultato del file di anteprima in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>Caricare il file 

È possibile caricare il file usando il comando **ADL: Upload File** (ADL: Carica file) o **ADL: Upload File through Configuration** (ADL: Carica file da configurazione).

**ADL: Upload File**
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi o fare clic con il pulsante destro del mouse sull'editor di script, quindi immettere **Upload File** (Carica file).
2. Immettere un percorso locale per il caricamento.

    ![Inserimento di percorso locale in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. Selezionare una modalità per elencare il percorso di archiviazione. Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio.

    ![Elencare il percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- Visual Studio Code mantiene il percorso visitato per ultimo con ogni account ADLA. Esempio：/tt/ss.
    >- Browse from root path (Sfoglia da percorso radice): percorso radice elenco da cui si seleziona l'account ADLA o locale.
    >- Enter a path (Immetti un percorso): percorso specificato elenco da cui si seleziona l'account ADLA o locale.

4. Selezionare un account locale o ADLA.

    ![Archiviazione dal menu di scelta rapida di Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  Inserire un percorso di archiviazione di Azure. Ad esempio, /output/

       ![Inserire un percorso di archiviazione in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. Elenco di input del percorso di archiviazione di azure. Selezionare **Choose current folder** (Scegli cartella corrente).

    ![Selezionare una cartella in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  Risultati: la finestra di output visualizza lo stato di caricamento del file.

       ![Stato di caricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration**
1.  Premere CTRL+MAIUSC+P per aprire il riquadro comandi o fare clic con il pulsante destro del mouse sull'editor di script, quindi immettere **Upload File through Configuration** (Carica file da configurazione).
2.  VSCode visualizza un file json. È possibile immettere i percorsi di file e caricare più file contemporaneamente. I risultati verranno visualizzati nella finestra di output. Salvare (**CTRL+S**) il file Json per procedere con il caricamento del file.

       ![File di input in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Risultati: la finestra di output visualizza lo stato di caricamento del file.

       ![Stato di caricamento in Strumenti Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Un altro modo pratico per caricare file da archiviare è facendo clic con il pulsante destro del mouse sul percorso completo del file o sul percorso relativo del file nell'editor di script. Immettere il percorso file locale e scegliere l'account. Nella finestra di output verrà visualizzato lo stato di caricamento. 

### <a name="open-web-azure-storage-explorer"></a>Aprire Web Azure Storage Explorer
È possibile aprire **Web Azure Storage Explorer** tramite il comando **ADL: Open Web Azure Storage Explorer** (ADL: Apri Web Azure Storage Explorer) o dal menu di scelta rapida visualizzato facendo clic con il pulsante destro del mouse.

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer) o fare clic con il pulsante destro del mouse su un percorso relativo o un percorso completo nell'editor di script per scegliere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer).
3. Selezionare un account di Data Lake Analytics.

Strumenti ADL apre il percorso di archiviazione nel portale di Azure. È possibile visitare il percorso e il file anteprima dal Web.

### <a name="local-run-and-local-debug-windows-users"></a>Esecuzione e debug locale per utenti Windows
L'esecuzione locale U-SQL è stata implementata per testare i dati locali, convalidare lo script localmente prima di pubblicare codice in ADLA. La funzionalità di debug locale consente di eseguire il debug del code-behind C#, eseguire il codice passo passo, convalidare lo script in locale prima di inviarlo ad ADLA. Vedere le istruzioni: [Esecuzione e debug locali U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Funzionalità aggiuntive

Gli strumenti di Data Lake per VSCode supportano le funzionalità seguenti:

-   Completamento automatico di IntelliSense. I suggerimenti appaiono intorno a parole chiave, metodi, variabili e così via. Le icone differenti rappresentano tipi di oggetti diversi:

    - Tipo di dati Scala
    - Tipo di dati complesso
    - UDT integrati
    - Classi e raccolta .NET
    - Espressioni C#
    - UDF, UDO e UDAAG di C# integrati 
    - Funzioni di U-SQL
    - Funzione di windowing di U-SQL
 
    ![Tipi di oggetto IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Il completamento automatico IntelliSense sui metadati di Data Lake Analytics. Gli strumenti di Data Lake scaricano le informazioni sui metadati di Data Lake Analytics in locale.  La funzionalità IntelliSense popola automaticamente gli oggetti, tra cui database, schemi, tabelle, visualizzazioni, funzione con valori di tabella, procedure e assembly C# dai metadati di Data Lake Analytics.
 
    ![Metadati IntelliSense degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Indicatore di errore di IntelliSense. Gli strumenti di Data Lake rimarcano gli errori di modifica per U-SQL e C#. 
-   Sintassi in evidenza. Gli strumenti di Data Lake usano colori differenti per differenziare variabili, parole chiave, tipi di dati, funzioni e così via. 

    ![Sintassi in evidenza degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Passaggi successivi

- Per l'esecuzione e il debug locali U-SQL con Visual Studio Code, vedere [Esecuzione e debug locali U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Per le informazioni introduttive su Data Lake Analytics, vedere [Esercitazione: Introduzione a Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Per informazioni sull'uso degli strumenti di Data Lake per Visual Studio, vedere [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).




