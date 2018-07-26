---
title: Usare il progetto di database U-SQL per lo sviluppo di database U-SQL per Azure Data Lake | Microsoft Docs
description: Informazioni su come sviluppare un database U-SQL usando Strumenti Azure Data Lake per Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890821"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Usare il progetto di database U-SQL per lo sviluppo di database U-SQL per Azure Data Lake

Database U-SQL fornisce visualizzazioni strutturate dei dati non strutturati, gestisce i dati strutturati in tabelle e fornisce un sistema di catalogo dei metadati generali per organizzare i dati strutturati e i codici personalizzati. Il database è il concetto che raggruppa questi oggetti correlati.

Altre informazioni su [database U-SQL e DDL (Data Definition Language)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

Progetto di database U-SQL è un tipo di progetto di Visual Studio che consente agli sviluppatori di sviluppare, gestire e distribuire i relativi database U-SQL in modo rapido e semplice.

## <a name="create-a-u-sql-database-project"></a>Creare un progetto di database U-SQL

Strumenti Azure Data Lake per Visual Studio ha aggiunto un nuovo modello di progetto denominato progetto del database U-SQL dopo la versione 2.3.3000.0. Per creare un progetto U-SQL, aprire **File > Nuovo > progetto**. Il progetto di database U-SQL è reperibile in **Azure Data Lake > nodo U-SQL**.

![Creare un progetto di database U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>Usare il progetto di database per sviluppare oggetti del database U-SQL

Fare clic con il pulsante destro del mouse sul progetto di database U-SQL, quindi fare clic su **Aggiungi > Nuovo elemento**. Tutti i tipi di oggetto supportati sono reperibili nella Procedura guidata per l'aggiunta di nuovi elementi. 

1.  Per gli oggetti non assembly, ad esempio un funzione con valori di tabella, viene creato un nuovo script U-SQL dopo l'aggiunta di un nuovo elemento. È possibile iniziare a sviluppare l'istruzione DDL per l'oggetto nell'editor.
2.  Per gli oggetti assembly, lo strumento fornisce un editor dell'interfaccia utente intuitivo che consente di registrare l'assembly, la distribuzione. dll e file aggiuntivi. Seguire la procedura seguente per aggiungere la definizione di un oggetto assembly al progetto di database U-SQL:

1.  Aggiungere i riferimenti del progetto C#, inclusa l'opzione UDO/UDAG/UDF, al progetto di database U-SQL.

    ![Aggiungere un riferimento a un progetto di database U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Aggiungere un riferimento a un progetto di database U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  Nella visualizzazione di progettazione assembly, scegliere l'assembly di riferimento dall'elenco a discesa **Create assembly from reference** (Crea assembly da riferimento).

    ![Creare un assembly da un riferimento con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Aggiungere eventuali **Dipendenze gestite** e **File aggiuntivi**. Quando si aggiungono file aggiuntivi, lo strumento userà il percorso relativo per assicurarsi di trovare gli assembly sia nel computer locale che nel computer di compilazione in un secondo momento. @_DeployTempDirectory è una variabile predefinita che fa riferimento allo strumento per la cartella di output di compilazione. In output di compilazione, ogni assembly presenta una sottocartella denominata come l'assembly. Tutte le DLL e i file sono reperibili nella sottocartella. 
 
## <a name="build-u-sql-database-project"></a>Compilare un progetto di database U-SQL

L'output della compilazione per il progetto di database U-SQL è un pacchetto di distribuzione del database U-SQL, denominato con il suffisso `.usqldbpack`. Il pacchetto `.usqldbpack` è un file ZIP che include tutte le istruzioni DDL in un unico script U-SQL nella cartella **DDL** e tutti i file con estensione DLL e i file aggiuntivi per gli assembly nella cartella **Temp**.

Altre informazioni su [come compilare il progetto di database U-SQL con la riga di comando MSBuild e attività di compilazione di Visual Studio Team Service](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>Distribuire un database U-SQL

Il pacchetto .usqldbpack può essere distribuito su account sia locali che di Azure Data Lake Analytics usando Visual Studio o l'SDK di distribuzione. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>Distribuire un database U-SQL in Visual Studio

È possibile distribuire un database U-SQL tramite un progetto di database U-SQL o un pacchetto .usqldbpack in Visual Studio.

#### <a name="deploy-through-u-sql-database-project"></a>Distribuzione tramite un progetto di database U-SQL

1.  Fare clic con il pulsante destro sul progetto del database U-SQL e selezionare **Distribuisci**.
2.  Nella procedura guidata per distribuire un database U-SQL, selezionare l'**account ADLA** in cui implementare il database. Sono supportati sia l'account (locale) che gli account Azure Data Lake Analytics.
3.  L'**origine database** viene compilata automaticamente e indirizzata al pacchetto .usqldbpack nella cartella di output compilazione del progetto
4.  Inserire un **Nome database** per creare un database. Se esiste già un database con lo stesso nome nell'account di destinazione di Azure Data Lake Analytics, tutti gli oggetti definiti nel progetto di database verranno creati senza ricreare il database.
5.  Fare clic su **Invia** per distribuire il database U-SQL. Verranno caricate tutte le risorse (assembly e file aggiuntivi) e verrà inviato un processo U-SQL comprendente tutte le istruzioni DDL.

    ![Distribuire un progetto di database U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Distribuire una procedura guidata per un progetto di database U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Distribuire tramite il pacchetto di distribuzione di database U-SQL

1.  Aprire **Esplora server** ed espandere l'**account Azure Data Lake Analytics** in cui distribuire il database.
2.  Fare clic con il pulsante destro su Database U-SQL e scegliere **Distribuisci database**.
3.  Impostare **Database Source** (Origine database) sul percorso del pacchetto di distribuzione del database U-SQL (file .usqldbpack).
4.  Inserire un **Nome del base** per creare un database. Se esiste già un database con lo stesso nome nell'account di destinazione di Azure Data Lake Analytics, tutti gli oggetti definiti nel progetto di database verranno creati senza ricreare il database.

    ![Distribuire un pacchetto di database U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Distribuire una procedura guidata per un pacchetto di database U-SQL con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>Distribuire un database U-SQL tramite SDK

`PackageDeploymentTool.exe` offre le interfacce di programmazione e della riga di comando che consentono di distribuire database U-SQL. L'SDK è incluso nel [pacchetto Nuget di SDK di U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), disponibile su `build/runtime/PackageDeploymentTool.exe`.

[Altre informazioni sull'SDK e su come configurare la pipeline CI/CD per la distribuzione di database U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Riferimento a un progetto di database U-SQL

Un progetto U-SQL può fare riferimento a un progetto di database U-SQL. Il riferimento interessa due carichi di lavoro:

- Compilazione del progetto: gli ambienti del database di riferimento vengono impostati prima di compilare gli script U-SQL. 
- Esecuzione con account locale (Local-project): gli ambienti del database di riferimento vengono distribuiti sull'account (Local-project) prima dell'esecuzione dello script U-SQL. [Altre informazioni sull'esecuzione locale e sulle differenze tra l'account (Local-machine) e l'account (Local-project) sono disponibili qui](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>Come aggiungere un riferimento al database U-SQL

1. Fare clic con il pulsante destro del mouse sul progetto U-SQL in **Esplora soluzioni** e scegliere **Aggiungi riferimento al database U-SQL...**.

    ![Aggiungere un riferimento a un progetto di database con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configurare un riferimento al database da un progetto di database U-SQL nella soluzione corrente o in un file di pacchetto database U-SQL.
3. Specificare un nome per il database.

    ![Aggiungere una procedura guidata di riferimento a un progetto di database con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Passaggi successivi

- [How to set up CI/CD pipeline for Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md) (Come configurare la pipeline CI/CD per Azure Data Lake Analytics)
- [How to test your Azure Data Lake Analytics code](data-lake-analytics-cicd-test.md) (Come testare il codice di Azure Data Lake Analytics)
- [Eseguire lo script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md)
