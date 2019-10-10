---
title: Risolvere i problemi dei flussi di dati Azure Data Factory | Microsoft Docs
description: Informazioni su come risolvere i problemi del flusso di dati in Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5eafa0cc6284df5c969f63e2ab3fac4113fab417
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178618"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Risolvere i problemi di Azure Data Factory flussi di dati

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per i flussi di dati in Azure Data Factory.

## <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Messaggio di errore: DF-SYS-01: ombreggiato. databricks. org. Apache. Hadoop. FS. Azure. Azureexception: com. Microsoft. Azure. storage. StorageException: Il contenitore specificato non esiste.

- **Sintomi**: L'esecuzione del flusso di dati di anteprima, debug e pipeline non riesce perché il contenitore non esiste

- **Causa**: Quando il set di dati contiene un contenitore che non esiste nello spazio di archiviazione

- **Risoluzione**: Verificare che il contenitore a cui si fa riferimento nel set di dati esista

## <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Messaggio di errore: DF-SYS-01: Java. lang. AssertionError: asserzione non riuscita: Sono state rilevate strutture di directory in conflitto. Percorsi sospetti

- **Sintomi**: Quando si usano i caratteri jolly nella trasformazione origine con i file parquet

- **Causa**: Sintassi con caratteri jolly non corretta o non valida

- **Risoluzione**: Controllare la sintassi con caratteri jolly utilizzata nelle opzioni di trasformazione di origine

## <a name="error-message-df-src-002-container-container-name-is-required"></a>Messaggio di errore: DF-SRC-002:' container ' (nome contenitore) obbligatorio

- **Sintomi**: L'esecuzione del flusso di dati di anteprima, debug e pipeline non riesce perché il contenitore non esiste

- **Causa**: Quando il set di dati contiene un contenitore che non esiste nello spazio di archiviazione

- **Risoluzione**: Verificare che il contenitore a cui si fa riferimento nel set di dati esista

## <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Messaggio di errore: DF-UNI-001: PrimaryKeyValue presenta tipi incompatibili IntegerType e StringType

- **Sintomi**: L'esecuzione del flusso di dati di anteprima, debug e pipeline non riesce perché il contenitore non esiste

- **Causa**: Si verifica quando si tenta di inserire un tipo di chiave primaria errato nei sink di database

- **Risoluzione**: Utilizzare una colonna derivata per eseguire il cast della colonna utilizzata per la chiave primaria nel flusso di dati in modo che corrisponda al tipo di dati del database di destinazione

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Messaggio di errore: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: La connessione TCP/IP alla porta xxxxx.database.windows.net host 1433 non è riuscita. Errore: "xxxx.database.windows.net. Verificare le proprietà di connessione. Assicurarsi che un'istanza di SQL Server sia in esecuzione nell'host e accetti le connessioni TCP/IP sulla porta. Verificare che le connessioni TCP alla porta non siano bloccate da un firewall ".

- **Sintomi**: Non è possibile visualizzare in anteprima i dati o eseguire una pipeline con origine o sink del database

- **Causa**: Il database è protetto da firewall

- **Risoluzione**: Aprire l'accesso del firewall al database

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Messaggio di errore: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: Nel database esiste già un oggetto denominato "xxxxxx".

- **Sintomi**: Impossibile creare la tabella nel sink

- **Causa**: Nel database di destinazione è già presente un nome di tabella con lo stesso nome definito nell'origine o nel set di dati

- **Risoluzione**: Modificare il nome della tabella che si sta tentando di creare



## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)
