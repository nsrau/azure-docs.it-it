---
title: Eseguire lo script U-SQL di Azure Data Lake sul computer locale | Microsoft Docs
description: Informazioni su come usare Strumenti Azure Data Lake per Visual Studio per eseguire i processi U-SQL sul computer locale.
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
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888967"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>Eseguire lo script U-SQL sul computer locale

Quando si sviluppa uno script U-SQL, è comune eseguire lo script U-SQL in locale, perché questa operazione consente di risparmiare tempo e denaro. Strumenti Azure Data Lake per Visual Studio supporta l'esecuzione degli script U-SQL sul computer locale. 

## <a name="basic-concepts-for-local-run"></a>Concetti di base per l'esecuzione locale

Il grafico seguente mostra i componenti per l'esecuzione locale e il mapping di questi componenti per l'esecuzione su cloud.

|Componente|Esecuzione locale|Esecuzione su cloud|
|---------|---------|---------|
|Archiviazione|Cartella DataRoot locale|Account di Azure Data Lake Store predefinito|
|Calcolo|Motore di esecuzione locale di U-SQL|Servizio Azure Data Lake Analytics|
|Ambiente di esecuzione|Directory di lavoro sul computer locale|Cluster di Azure Data Lake Analytics|

Maggiori informazioni per i componenti di esecuzione locale:

### <a name="local-data-root-folder"></a>Cartella DataRoot locale

La cartella DataRoot è un "archivio locale" per l'account di calcolo locale. Qualsiasi cartella nel file system locale del computer locale può essere una cartella DataRoot locale. Equivale all'account Azure Data Lake Store predefinito di un account Data Lake Analytics. Il passaggio a un'altra cartella DataRoot è identico al passaggio a un account archivio predefinito diverso. 

La cartella DataRoot viene usata per:
- Archiviare i metadati, quali database, tabelle, funzioni con valori di tabella e assembly.
- Cercare i percorsi di input e output che sono definiti come percorsi relativi nello script U-SQL. L'uso di percorsi relativi semplifica la distribuzione degli script U-SQL in Azure.

### <a name="u-sql-local-run-engine"></a>Motore di esecuzione locale di U-SQL

Il motore di esecuzione locale di U-SQL è un "account di calcolo locale" per i processi U-SQL. Gli utenti possono eseguire i processi U-SQL in locale mediante Strumenti Azure Data Lake per Visual Studio. L'esecuzione locale viene supportata anche tramite le interfacce della riga di comando e di programmazione dell'SDK di U-SQL per Azure Data Lake. [Altre informazioni sull'SDK di U-SQL per Azure Data Lake](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directory"></a>Directory di lavoro

Quando si esegue uno script U-SQL, è necessaria una cartella directory di lavoro per memorizzare nella cache i risultati di compilazione, i log di esecuzione e così via. In Strumenti Azure Data Lake per Visual Studio la directory di lavoro è la directory di lavoro del progetto U-SQL che in genere si trova in `<U-SQL project root path>/bin/debug>`. La directory di lavoro verrà pulita ogni volta che si avvia una nuova esecuzione.

## <a name="local-run-in-visual-studio"></a>Esecuzione locale in Visual Studio

Strumenti Azure Data Lake per Visual Studio è dotato di motore di esecuzione locale predefinito che viene trattato come un account di calcolo locale. Per eseguire uno script U-SQL in locale, selezionare l'account (Local-machine) o (Local-project) nell'elenco a discesa a margine dell'editor nello script e fare clic su **Invia**.

![Script di invio degli Strumenti Data Lake per Visual Studio all'account locale](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>Esecuzione locale con l'account (Local-machine)

L'account (Local-machine) è un account di calcolo locale condiviso con una sola cartella DataRoot locale come account archivio locale. Per impostazione predefinita la cartella DataRoot si trova in "C:\Utenti\<nome utente > \AppData\Locale\USQLDataRoot", è configurabile anche attraverso **Strumenti > Data Lake > Opzioni e impostazioni**.

![Configurazione della cartella DataRoot locale di Strumenti Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Per l'esecuzione locale è necessario avere un progetto U-SQL. La directory di lavoro del progetto U-SQL viene usata per la directory di lavoro di esecuzione locale di U-SQL. I risultati di compilazione, i log di esecuzione e altri file relativi all'esecuzione del processo vengono generati e archiviati nella cartella directory di lavoro durante l'esecuzione locale. Si noti che ogni volta che si esegue di nuovo lo script, tutti i file nella directory di lavoro verranno puliti e rigenerati.

## <a name="local-run-with-local-project-account"></a>Esecuzione locale con l'account (Local-project)

L'account (Local-project) è un account di calcolo locale isolato dal progetto per ogni progetto con una cartella DataRoot locale isolata. Ogni progetto U-SQL attivo che viene aperto in Esplora soluzioni è dotato di un account `(Local-project: <project name>)` corrispondente elencato a margine dell'editor dello script sia in Esplora server che a margine dell'editor dello script U-SQL. 

L'account (Local-project) offre un ambiente di sviluppo pulito e isolato per gli sviluppatori. A differenza dell'account (Local-machine) che dispone di una cartella DataRoot locale condivisa per l'archiviazione dei metadati e dei dati di input/output per tutti i processi locali, l'account (Local-project) crea una cartella DataRoot locale temporanea nella directory di lavoro del progetto U-SQL ogni volta che uno script U-SQL viene eseguito. La cartella DataRoot temporanea viene pulita quando si avvia la ricompilazione o una nuova esecuzione. 

Il progetto U-SQL offre un'esperienza ottimale per la gestione di questo ambiente di esecuzione locale isolato mediante proprietà e riferimento al progetto. È possibile configurare sia le origini dati di input per gli script U-SQL nel progetto, che gli ambienti del database di riferimento.

### <a name="manage-input-data-source-for-local-project-account"></a>Gestire l'origine dati di input per l'account (Local-project)

Il progetto U-SQL crea la cartella DataRoot locale e imposta i dati per l'account (Local-project). Viene pulita e ricreata una cartella DataRoot temporanea nella directory di lavoro del progetto U-SQL ogni volta che si verifica la ricompilazione e l'esecuzione locale. Tutte le origini dati configurate dal progetto U-SQL vengono copiate in questa cartella DataRoot locale temporanea prima dell'esecuzione del processo locale. 

È possibile configurare la cartella radice delle origini dati **facendo clic con il pulsante destro del mouse sul progetto U-SQL > Proprietà > Test origine dati**. Quando si esegue lo script U-SQL nell'account (Local-project), tutti i file e le sottocartelle, inclusi i file contenuti nelle sottocartelle, della cartella **Test origine dati** vengono copiati nella cartella DataRoot locale temporanea. Al termine dell'esecuzione del processo locale, i risultati di output sono reperibili anche nella cartella DataRoot locale temporanea della directory di lavoro del progetto. Si noti che tutti questi output verranno eliminati e puliti quando il progetto viene ricompilato e pulito. 

![Configurazione dell'origine dati di test del progetto di Strumenti Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>Gestire l'ambiente del database di riferimento per l'account (Local-project) 

Se una query U-SQL usa o se si esegue una query con gli oggetti di database U-SQL, è necessario preparare gli ambienti del database in locale prima di eseguire questo script U-SQL in locale. Per l'account (Local-project), le dipendenze del database U-SQL possono essere gestite tramite i riferimenti al progetto U-SQL. È possibile aggiungere i riferimenti al progetto del database U-SQL al proprio progetto U-SQL. Prima di eseguire gli script U-SQL nell'account (Local-project), vengono distribuiti tutti i database di riferimento nella cartella DataRoot locale temporanea. Per ogni esecuzione, la cartella DataRoot temporanea viene pulita come un nuovo ambiente isolato.

Articoli correlati:
* [Informazioni su come gestire la definizione del database U-SQL tramite il progetto del database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [Informazioni su come gestire il riferimento al database U-SQL nel progetto U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>Differenza tra l'account (Local-machine) e l'account (Local-project)

L'account (Local-machine) mira a simulare un account Azure Data Lake Analytics nel computer locale degli utenti. Condivide la stessa esperienza con l'account Azure Data Lake Analytics. L'account (Local-project) mira a offrire un ambiente di sviluppo locale intuitivo che consente agli utenti di distribuire i riferimenti al database e i dati di input prima di eseguire lo script in locale. L'account (Local-machine) offre un ambiente permanente condiviso, a cui è possibile accedere da tutti i progetti. L'account (Local-project) offre un ambiente di sviluppo isolato per ogni progetto e viene aggiornato a ogni esecuzione. In base a quanto esposto sopra, l'account (Local-project) offre un'esperienza di sviluppo più veloce applicando rapidamente le nuove modifiche.

Altre differenze tra l'account (Local-machine) e l'account (Local-project) sono illustrate nel grafico seguente:

|Tipo di differenza|(Local-machine)|(Local-project)|
|----------------|---------------|---------------|
|Accesso locale|È accessibile da tutti i progetti|Solo il progetto corrispondente può accedere a questo account|
|Cartella DataRoot locale|Cartella locale permanente. Configurata da **Strumenti > Data Lake > Opzioni e impostazioni**|Una cartella temporanea creata per ogni esecuzione locale nella directory di lavoro del progetto U-SQL. La cartella viene pulita quando si avvia la ricompilazione o una nuova esecuzione|
|Dati di input per lo script U-SQL|Percorso relativo nella cartella DataRoot locale permanente|Impostare in **U-SQL project property (Proprietà del progetto U-SQL) > Test origine dati**. Tutti i file e le sottocartelle vengono copiati nella cartella DataRoot temporanea prima dell'esecuzione locale|
|Dati di output per lo script U-SQL|Percorso relativo nella cartella DataRoot locale permanente|Inviati alla cartella DataRoot temporanea. I risultati vengono puliti quando si avvia la ricompilazione o una nuova esecuzione.|
|Distribuzione del database di riferimento|I database di riferimento non vengono distribuiti automaticamente durante l'esecuzione per l'account (Local-machine). Uguale per l'invio all'account Azure Data Lake Analytics.|I database di riferimento vengono automaticamente distribuiti nell'account (Local-project) prima dell'esecuzione locale. Tutti gli ambienti del database vengono puliti e ridistribuiti quando si esegue la ricompilazione o una nuova esecuzione.|

## <a name="local-run-with-u-sql-sdk"></a>Esecuzione locale con l'SDK U-SQL

Oltre a eseguire gli script U-SQL in locale in Visual Studio, è anche possibile usare l'SDK U-SQL di Azure Data Lake per eseguire gli script U-SQL in locale con le interfacce della riga di comando e di programmazione. Tramite queste interfacce, è possibile automatizzare l'esecuzione locale e il test di U-SQL.

[Altre informazioni sull'SDK U-SQL di Azure Data Lake](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Passaggi successivi

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [How to set up CI/CD pipeline for Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md) (Come configurare la pipeline CI/CD per Azure Data Lake Analytics)
- [Use U-SQL database project to develop U-SQL database](data-lake-analytics-data-lake-tools-develop-usql-database.md) (Usare il progetto di database U-SQL per sviluppare il database U-SQL)
- [How to test your Azure Data Lake Analytics code](data-lake-analytics-cicd-test.md) (Come testare il codice di Azure Data Lake Analytics)
