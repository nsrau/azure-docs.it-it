---
title: Usare un progetto di database U-SQL per sviluppare un database U-SQL per Azure Data Lake
description: Ulteriori informazioni sullo sviluppo di un database U-SQL usando Strumenti Azure Data Lake per Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 47235fa5676acd8de8a7cc0d969b813837faf0af
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60628848"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Usare un progetto di database U-SQL per sviluppare un database U-SQL per Azure Data Lake

Il database U-SQL fornisce viste strutturate su dati non strutturati e dati strutturati gestiti in tabelle. Fornisce inoltre un sistema di catalogo di metadati generale per organizzare dati strutturati e codice personalizzato. Il database è il concetto che raggruppa questi oggetti correlati.

Altre informazioni su [database U-SQL e DDL (Data Definition Language)](/u-sql/data-definition-language-ddl-statements). 

Il progetto di database U-SQL è un tipo di progetto in Visual Studio che consente agli sviluppatori di sviluppare, gestire e distribuire database U-SQL in modo rapido e semplice.

## <a name="create-a-u-sql-database-project"></a>Creare un progetto di database U-SQL

Strumenti Azure Data Lake per Visual Studio ha aggiunto un nuovo modello di progetto denominato progetto del database U-SQL dopo la versione 2.3.3000.0. Per creare un progetto U-SQL, selezionare **File > Nuovo > Progetto**. Il progetto di database U-SQL è disponibile in **Azure Data Lake > nodo U-SQL**.

![Strumenti Data Lake per Visual Studio--crea progetto di database U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Sviluppare oggetti di database U-SQL usando un progetto di database

Fare clic con il pulsante destro del mouse sul progetto di database U-SQL. Selezionare **Aggiungi > Nuovo elemento**. È possibile trovare tutti i i nuovi tipi di oggetto supportati nella procedura guidata **Aggiungi nuovo elemento**. 

Per un oggetto non assembly, ad esempio una funzione con valori di tabella, un nuovo script U-SQL viene creato dopo l'aggiunta di un nuovo elemento. È possibile iniziare a sviluppare l'istruzione DDL per l'oggetto nell'editor.

Per un oggetto assembly, lo strumento fornisce un editor UI di uso intuitivo che consente di registrare l'assembly e di distribuire file DLL e altri file aggiuntivi. Di seguito viene illustrato come aggiungere una definizione di oggetto assembly al progetto di database U-SQL:

1.  Aggiungere riferimenti al progetto C# che includano UDO/UDAG/UDF per il progetto di database U-SQL.

    ![Strumenti Data Lake per Visual Studio--Aggiungi un riferimento al progetto di database U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Strumenti Data Lake per Visual Studio--Aggiungi un riferimento al progetto di database U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  Nella visualizzazione di progettazione dell'assembly scegliere l'assembly a cui si fa riferimento dal menu a discesa **Crea assembly da riferimento**.

    ![Strumenti Data Lake per Visual Studio--crea assembly da riferimento](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Aggiungere eventuali **dipendenze gestite** e **file aggiuntivi**. Quando si aggiungono file, lo strumento usa il percorso relativo per assicurarsi di poter trovare gli assembly sia sul computer locale sia sul computer di compilazione in un momento successivo. 

@_DeployTempDirectory è una variabile predefinita che punta allo strumento nella cartella di output della compilazione. Nella cartella di output della compilazione ogni assembly dispone di una sottocartella il cui nome fa riferimento a quello dell'assembly. Tutte le DLL e i file aggiuntivi si trovano in quella sottocartella. 
 
## <a name="build-a-u-sql-database-project"></a>Compilare un progetto di database U-SQL

L'output della compilazione di un progetto di database U-SQL è un pacchetto di distribuzione di database U-SQL, il cui nome contiene il suffisso `.usqldbpack`. Il pacchetto `.usqldbpack` è un file ZIP contenente tutte le istruzioni DDL in un singolo script U-SQL nella cartella **DDL** e tutte le DLL e i file aggiuntivi per gli assembly nella cartella **Temp**.

Altre informazioni sulle [come creare un progetto di database U-SQL con MSBuild della riga di comando e un servizi di Azure DevOps attività di generazione](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Distribuire un database U-SQL

Il pacchetto .usqldbpack può essere distribuito in un account locale o in un account Azure Data Lake Analytics usando Visual Studio o Deployment SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuire un database U-SQL in Visual Studio

È possibile distribuire un database U-SQL tramite un progetto di database U-SQL o un pacchetto .usqldbpack in Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Distribuire attraverso un progetto di database U-SQL

1.  Fare clic con il pulsante destro del mouse sul progetto di database U-SQL, quindi selezionare **Distribuisci**.
2.  Nella **Distribuzione guidata database U-SQL** selezionare l'**account ADLA** in cui distribuire il database. Gli account locali e gli account ADLA sono entrambi supportati.
3.  **Origine database** viene compilato automaticamente e punta al pacchetto .usqldbpack nella cartella di output della compilazione del progetto.
4.  Immettere un nome in **Nome database** per creare un database. Se esiste già un database con lo stesso nome nell'account Azure Data Lake Analytics, tutti gli oggetti definiti nel progetto di database vengono creati senza ricreare il database.
5.  Per distribuire il database U-SQL selezionare **Invia**. Vengono caricate tutte le risorse, assembly e file aggiuntivi, e viene inviato un progetto U-SQL che include tutti gli strumenti DDL.

    ![Strumenti Azure Data Lake per Visual Studio--Distribuisci progetto di database U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Strumenti Azure Data Lake per Visual Studio--Distribuzione guidata progetto di database U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Distribuire attraverso un pacchetto di distribuzione database U-SQL

1.  Aprire **Esplora server**. Espandere l'**account Azure Data Lake Analytics** in cui distribuire il database.
2.  Fare clic con il pulsante destro del mouse su **Database U-SQL**, quindi scegliere **Distribuisci database**.
3.  Impostare **Database Source** (Origine database) sul percorso del pacchetto di distribuzione del database U-SQL (file .usqldbpack).
4.  Immettere il **Nome database** per creare un database. Se esiste già un database con lo stesso nome nell'account Azure Data Lake Analytics di destinazione, tutti gli oggetti definiti nel progetto di database vengono creati senza ricreare il database.

    ![Strumenti Azure Data Lake per Visual Studio--Distribuisci pacchetto di database U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Strumenti Azure Data Lake per Visual Studio--Distribuzione guidata pacchetto di database U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Distribuire il database U-SQL usando l'SDK

`PackageDeploymentTool.exe` offre le interfacce di programmazione e della riga di comando che consentono di distribuire database U-SQL. L'SDK è incluso nel [pacchetto Nuget U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), disponibile in `build/runtime/PackageDeploymentTool.exe`.

[Altre informazioni sull'SDK e su come configurare la pipeline CI/CD per la distribuzione di database U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Riferimento a un progetto di database U-SQL

Un progetto U-SQL può fare riferimento a un progetto di database U-SQL. Il riferimento interessa due carichi di lavoro:

- *Compilazione progetto*: Configurare gli ambienti di database di riferimento prima di compilare gli script U-SQL. 
- *Account di esecuzione rispetto a (un progetto locale) locale*: Vengono distribuiti gli ambienti di database di riferimento (un progetto locale) account prima dell'esecuzione dello script U-SQL. [Ulteriori informazioni sulle esecuzioni locali e sulla differenza tra account (computer locale) e (progetto locale)](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Come aggiungere un riferimento di database U-SQL

1. Fare clic con il pulsante destro del mouse sul progetto U-SQL in **Esplora soluzioni**, quindi scegliere **Aggiungi riferimento al database U-SQL...** .

    ![Strumenti Azure Data Lake per Visual Studio -- Aggiungi riferimento al progetto di database](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configurare un riferimento al database da un progetto di database U-SQL nella soluzione corrente o in un file di pacchetto di database U-SQL.
3. Specificare un nome per il database.

    ![Aggiungere una procedura guidata di riferimento a un progetto di database con Strumenti Azure Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare una pipeline CI/CD per Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [How to test your Azure Data Lake Analytics code](data-lake-analytics-cicd-test.md) (Come testare il codice di Azure Data Lake Analytics)
- [Eseguire lo script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md)
