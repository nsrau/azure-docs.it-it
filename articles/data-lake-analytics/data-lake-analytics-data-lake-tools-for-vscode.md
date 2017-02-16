---
title: Usare gli strumenti di Azure Data Lake per Visual Studio Code | Microsoft Docs
description: 'Informazioni su come usare gli strumenti di Azure Data Lake per Visual Studio Code per creare, testare ed eseguire gli script U-SQL. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/22/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: fe5ef9bba31abdf9b29ad7c817a376407309f289
ms.openlocfilehash: 59f2e4473aac85a8476055d2d955f576c099d787

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Usare gli strumenti di Azure Data Lake per Visual Studio Code

Informazioni su come usare gli strumenti di Azure Data Lake per Visual Studio Code (VSCode) per creare, testare ed eseguire gli script U-SQL.


## <a name="prerequisites"></a>Prerequisiti

Gli strumenti di Azure Data Lake possono essere installati sulle piattaforme supportate da VSCode che includono Windows, Linux e MacOS. È possibile reperire i prerequisiti per piattaforme differenti

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). È necessario aggiungere il percorso java.exe al percorso della variabile dell'ambiente di sistema.  Per le istruzioni vedere [Come impostare o modificare la variabile di sistema Path]( https://www.java.com/download/help/path.xml) Il percorso è simile a c:\Programmi\Java\jdk1.8.0_77\jre\bin
    - [Runtime di .NET Core SDK 1.0.1-preview 2 o .NET Core 1.0.1]( https://www.microsoft.com/net/download).
    
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
            > Mono 4.6 non è supportato.  È necessario disinstallare completamente la versione 4.6 prima di poter installare la versione 4.2.x.  

        - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). Per istruzioni vedere [qui]( https://java.com/en/download/help/linux_x64_install.xml).
        - [Runtime di .NET Core SDK 1.0.1-preview 2 o .NET Core 1.0.1]( https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Aggiornamento 77 o successivo di Java SE Runtime Environment, versione 8](https://java.com/download/manual.jsp). Per istruzioni vedere [qui](https://java.com/en/download/help/mac_install.xml).
    - [Runtime di .NET Core SDK 1.0.1-preview 2 o .NET Core 1.0.1]( https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Installare gli strumenti di Data Lake

Dopo aver installato i prerequisiti, è possibile installare gli strumenti di Data Lake per VSCode.

**Installare gli strumenti di Data Lake**

1. Aprire **Visual Studio Code**.
2. Premere **CTRL+P** e immettere:

        ext install usql-vscode-ext
    È possibile visualizzare un elenco delle estensioni di codice di Visual Studio, tra cui **Strumento di Azure Data Lake (anteprima)**.
3. Fare clic su **Installa** accanto a **Strumento di Azure Data Lake (anteprima)**. Dopo alcuni secondi, al pulsante Installa si sostituirà il pulsante Ricarica.
4. Fare clic su **Ricarica** per attivare l'estensione.
5. Fare clic su **OK** per confermare. È possibile visualizzare gli strumenti di Azure Data Lake nel riquadro Estensioni.

    ![Installazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)


## <a name="connect-to-azure"></a>Connect to Azure

Prima di compilare ed eseguire gli script U-SQL, è necessario connettersi al proprio account di Azure.

**Connettersi ad Azure**

1.  Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**. 
2.  Immettere **ADL:Login**.

    ![Riquadro comandi degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

2.  Seguire le istruzioni per accedere dalla pagina Web. Dopo essersi connessi, il nome dell'account viene visualizzato sulla barra di stato nella parte inferiore della finestra.

> [!NOTE] 
> Se l'account dispone di due fattori abilitati, è consigliabile usare l'autenticazione tramite telefono anziché tramite codice PIN.

Per disconnettersi, usare il comando **ADL:Logout**

## <a name="list-data-lake-analytics-accounts"></a>Elencare gli account di Data Lake Analytics

Per testare la connessione, è possibile elencare gli account di Data Lake Analytics:

**Elencare gli account di Data Lake Analytics sotto una sottoscrizione di Azure**

1. Aprire il riquadro comandi premendo la combinazione di tasti **CTRL+MAIUSC+P**.
2. Digitare **ADL:List Accounts**.  L'account viene visualizzato nel riquadro **Output**.

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

5. Salvare il file con nome **myUSQL.usql** nella cartella. Si noti che al progetto viene aggiunto anche il file di configurazione **adltools_settings.json**.
4. Aprire e configurare il file **adltools_settings.json** con le proprietà seguenti:

    - Account: un account di Data Lake Analytics sotto la sottoscrizione di Azure.
    - Impostazioni facoltative:

        - Priorità: L'intervallo di priorità va da 1 a 1000 dove la priorità più alta corrisponde al valore 1. Il valore predefinito è 1000.
        - Parallelismo: l'intervallo di parallelismo va da 1 a 150. Il valore predefinito è 150. 

        > [!NOTE] 
        > Se le impostazioni non sono valide, vengono applicati i valori predefiniti.

     ![File di configurazione degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Si richiede un account di Data Lake Analytics di calcolo per poter compilare ed eseguire i processi di U-SQL.  È necessario configurare l'account del computer prima di compilare ed eseguire i processi di U-SQL.

Rispetto a quando si apre un file, aprendo una cartella è possibile:

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

È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Generate Code Behind** per inviare un processo U-SQL. 

La procedura di compilazione e invio di uno script U-SQL con code-behind è la stessa usata per lo script U-SQL indipendente.

Le due schermate seguenti mostrano un file code-behind e il suo file di script U-SQL associato:
 
![Code-behind degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Code-behind degli strumenti di Data Lake per Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="register-assemblies"></a>Registrazione di assembly

Usando gli strumenti di Data Lake è possibile registrare gli assembly di codice personalizzato nel metastore di Data Lake Analytics.

**Registrare un assembly**

1.  Premere **CTRL+MAIUSC+P** per aprire il riquadro comandi.
2.  Immettere **ADL:Register Assembly**.
3.  Selezionare un account di Data Lake Analytics.
4.  Selezionare un database.
5.  Specificare il percorso dell'assembly locale.

## <a name="access-data-lake-analytics-metadata"></a>Accedere ai metadati di Data Lake Analytics

Dopo essersi connessi ad Azure, è possibile attenersi alla procedura seguente per accedere ai metadati di U-SQL:

**Accedere ai metadati di U-SQL**

1.  Premere **CTRL+MAIUSC+P** e digitare **ADL:List Tables**.
2.  Fare clic su uno degli account di Data Lake Analytics.
3.  Fare clic su uno dei database di Data Lake Analytics.
4.  Fare clic su uno degli schemi. Le tabelle vengono visualizzate.

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

##<a name="next-steps"></a>Passaggi successivi:

- Per le informazioni introduttive su Data Lake Analytics, vedere [Esercitazione: Introduzione a Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Per informazioni sull'uso degli strumenti di Data Lake per Visual Studio, vedere [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).






<!--HONumber=Nov16_HO4-->


