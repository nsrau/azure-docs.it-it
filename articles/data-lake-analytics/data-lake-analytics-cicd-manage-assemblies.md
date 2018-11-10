---
title: Procedura consigliata per la gestione di assembly U-SQL nella pipeline di integrazione continua/recapito continuo per Azure Data Lake
description: Informazioni sulla procedura consigliata per la gestione di assembly C# U-SQL nella pipeline di integrazione continua/recapito continuo con Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670288"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Procedura consigliata per la gestione di assembly U-SQL nella pipeline di integrazione continua/recapito continuo

In questo articolo viene illustrato come gestire il codice sorgente degli assembly U-SQL con il nuovo progetto di database U-SQL introdotto. Viene anche illustrato come configurare una pipeline di integrazione continua e distribuzione continua (CI/CD) per la registrazione degli assembly tramite Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Usare il progetto di database U-SQL per gestire il codice sorgente degli assembly

[Il progetto di database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) è un tipo di progetto in Visual Studio che consente agli sviluppatori di sviluppare, gestire e distribuire database U-SQL in modo rapido e semplice. Tutti gli oggetti di database U-SQL (tranne le credenziali) possono essere gestiti con il progetto di database U-SQL. 

Per gestire il codice sorgente degli assembly C# e gli script U-SQL DDL di registrazione degli assembly, è consigliabile:

* Usare il **progetto di database U-SQL** per gestire gli script U-SQL di registrazione degli assembly.
* Usare la **libreria di classi (per l'applicazione U-SQL)** per gestire il codice sorgente C# e le dipendenze per gli operatori, le funzioni e gli aggregatori definiti dall'utente.
* Usare il progetto di database U-SQL per fare riferimento al progetto libreria di classi. 

Un progetto di database U-SQL può fare riferimento a un progetto libreria di classi (per l'applicazione U-SQL). Gli assembly registrati nel database U-SQL possono essere creati usando il codice sorgente C# di riferimento da questo progetto libreria di classi (per l'applicazione U-SQL).

È possibile seguire questa procedura di creazione dei progetti e di aggiunta dei riferimenti:
1. Creare un progetto libreria di classi (per l'applicazione U-SQL) tramite **File > Nuovo > Progetto**. Il progetto è sotto il nodo **Azure Data Lake > U-SQL**.
   ![Strumenti Data Lake per Visual Studio - Creare il progetto libreria di classi C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Aggiungere il codice C# definito dall'utente nel progetto libreria di classi (per l'applicazione U-SQL). 
3. Creare un progetto U-SQL tramite **File > Nuovo > Progetto**. Il progetto è sotto il nodo **Azure Data Lake > U-SQL**.
   ![Strumenti Data Lake per Visual Studio - Creare il progetto di database U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Aggiungere il riferimento al progetto libreria di classi C# per il progetto di database U-SQL.

    ![Strumenti Data Lake per Visual Studio--Aggiungi un riferimento al progetto di database U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Strumenti Data Lake per Visual Studio--Aggiungi un riferimento al progetto di database U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Creare uno script di assembly nel progetto di database U-SQL **facendo clic con il pulsante destro del mouse sul progetto > Aggiungi nuovo elemento**.
   ![Strumenti Data Lake per Visual Studio - Aggiungere lo script dell'assembly](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Aprire lo script dell'assembly, nella visualizzazione di progettazione dell'assembly scegliere l'assembly a cui si fa riferimento dal menu a discesa **Crea assembly da riferimento**.

    ![Strumenti Data Lake per Visual Studio--crea assembly da riferimento](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Aggiungere eventuali **dipendenze gestite** e **file aggiuntivi**. Quando si aggiungono file, lo strumento usa il percorso relativo per assicurarsi di poter trovare gli assembly sia sul computer locale sia sul computer di compilazione in un momento successivo. 

**@_DeployTempDirectory** nella finestra dell'editor nella parte inferiore è una variabile predefinita che punta allo strumento nella cartella di output della compilazione. Nella cartella di output della compilazione ogni assembly dispone di una sottocartella il cui nome fa riferimento a quello dell'assembly. Tutte le DLL e i file aggiuntivi si trovano in quella sottocartella. 

## <a name="build-a-u-sql-database-project"></a>Compilare un progetto di database U-SQL

L'output della compilazione di un progetto di database U-SQL è un pacchetto di distribuzione di database U-SQL, il cui nome contiene il suffisso `.usqldbpack`. Il pacchetto `.usqldbpack` è un file ZIP contenente tutte le istruzioni DDL in un singolo script U-SQL nella cartella **DDL** e tutte le DLL e i file aggiuntivi compilati per gli assembly nella cartella **Temp**.

## <a name="deploy-a-u-sql-database"></a>Distribuire un database U-SQL

Il pacchetto `.usqldbpack` può essere distribuito in un account locale o in un account Azure Data Lake Analytics usando Visual Studio o l'SDK di distribuzione. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuire un database U-SQL in Visual Studio

È possibile distribuire un database U-SQL tramite un progetto di database U-SQL o un pacchetto `.usqldbpack` in Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Distribuire attraverso un progetto di database U-SQL

1.  Fare clic con il pulsante destro del mouse sul progetto di database U-SQL, quindi selezionare **Distribuisci**.
2.  Nella **Distribuzione guidata database U-SQL** selezionare l'**account ADLA** in cui distribuire il database. Gli account locali e gli account ADLA sono entrambi supportati.
3.  **Origine database** viene compilato automaticamente e punta al pacchetto .usqldbpack nella cartella di output della compilazione del progetto.
4.  Immettere un nome in **Nome database** per creare un database. Se esiste già un database con lo stesso nome nell'account Azure Data Lake Analytics, tutti gli oggetti definiti nel progetto di database vengono creati senza ricreare il database.
5.  Per distribuire il database U-SQL selezionare **Invia**. Vengono caricate tutte le risorse, assembly e file aggiuntivi, e viene inviato un progetto U-SQL che include tutti gli strumenti DDL.

    ![Strumenti Azure Data Lake per Visual Studio--Distribuisci progetto di database U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Strumenti Azure Data Lake per Visual Studio--Distribuzione guidata progetto di database U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Distribuire un database U-SQL in Azure DevOps

`PackageDeploymentTool.exe` offre le interfacce di programmazione e della riga di comando che consentono di distribuire database U-SQL. L'SDK è incluso nel [pacchetto Nuget U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), disponibile in `build/runtime/PackageDeploymentTool.exe`.

In Azure DevOps è possibile usare l'attività della riga di comando e questo SDK per configurare la pipeline di automazione per l'aggiornamento del database SQL. [Altre informazioni sull'SDK e su come configurare la pipeline CI/CD per la distribuzione di database U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Passaggi successivi

* [Come configurare una pipeline CI/CD per Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [How to test your Azure Data Lake Analytics code](data-lake-analytics-cicd-test.md) (Come testare il codice di Azure Data Lake Analytics)
* [Eseguire lo script U-SQL sul computer locale](data-lake-analytics-data-lake-tools-local-run.md)
