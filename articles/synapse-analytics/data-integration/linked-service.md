---
title: Effettuare il provisioning e proteggere un servizio collegato in Azure Synapse AnalyticsProvision and secure a linked service in Azure Synapse Analytics
description: Informazioni su come eseguire il provisioning e proteggere un servizio collegato con Managed Vnet
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430552"
---
# <a name="securing-a-linked-service-with-private-links"></a>Protezione di un servizio collegato con collegamenti privati 

In questo articolo verrà illustrato come proteggere un servizio collegato in Synapse con un endpoint privato.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure:** se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di Archiviazione di Azure:** si usa Azure Data Lake Gen 2 come archivio dati *di origine.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione](../../storage/blobs/data-lake-storage-quickstart-create-account.md) di Azure per la procedura per crearne uno. Assicurarsi che l'account di archiviazione disponga del filtro IP di Synapse Studio per l'accesso e che sia consentito solo alle **reti selezionate** di accedere all'account di archiviazione. L'impostazione nel pannello **I firewall e le reti virtuali** dovrebbero essere simili all'immagine seguente.

![Account di archiviazione protetto](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Creare un servizio collegato con collegamenti privatiCreate a linked service with Private Links

In Azure Synapse Analytics, un servizio collegato consente di definire le informazioni di connessione ad altri servizi. In questa sezione verranno aggiunti Azure Synapse Analytics e Azure Data Lake Gen 2 come servizi collegati.

1. Aprire Azure Synapse Studio e passare alla scheda **Gestisci.Open** the Azure Synapse Studio and go to the Manage tab.
1. In **Connessioni esterne**selezionare Servizi **collegati**.
1. Per aggiungere un servizio collegato, fare clic su **Nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e fare clic su **Continua**.
1. Assicurarsi di abilitare **la creazione interattiva**. L'abilitazione potrebbe richiedere circa 1 minuto. 
1. Immettere le credenziali di autenticazione. La chiave account, l'entità servizio e l'identità gestita sono tipi di autenticazione attualmente supportati. Fare clic su Test connessione per verificare che le credenziali siano corrette.
1. Selezionare **Verifica connessione**, deve avere esito negativo perché l'account di archiviazione non consente l'accesso a essa senza la creazione e l'approvazione di un endpoint privato. Nel messaggio di errore dovrebbe essere visualizzato un collegamento per creare un **endpoint privato** che è possibile seguire per passare alla parte successiva. Se si segue questo collegamento, ignorare la parte successiva.
1. Selezionare **Create** (Crea) al termine.

## <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestitoCreate a managed private endpoint

Nel caso in cui, non è stato fatto clic nel collegamento ipertestuale durante il test della connessione precedente, seguire il seguente percorso. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato in precedenza.

1. Vai alla scheda **Gestisci.**
1. Passare alla sezione **Reti virtuali gestite.**
1. In Endpoint privato gestito, selezionare **Nuovo.**
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e selezionare **Continua**.
1. Immettere il nome dell'account di archiviazione creato in precedenza.
1. Selezionare **Crea**
1. Si dovrebbe vedere dopo l'attesa di alcuni secondi che il collegamento privato creato richiede un'approvazione.

## <a name="approval-of-a-private-link"></a>Approvazione di un collegamento privato
1. Selezionare l'endpoint privato creato in precedenza. È possibile visualizzare un collegamento ipertestuale che consente di approvare l'endpoint privato a livello di account di archiviazione. *Un'alternativa consiste nell'accedere direttamente all'account di archiviazione del portale di Azure e passare al pannello **Connessioni endpoint privati.***
1. Selezionare l'endpoint privato creato in Studio e selezionare **Approva**.
1. Aggiungi una descrizione e fai clic su **Sì**
1. Tornare a Synapse Studio nella sezione **Reti virtuali gestite** della scheda **Gestisci.**
1. Per ottenere l'approvazione per l'endpoint privato, dovrebbe essere necessario circa 1 minuto.

## <a name="check-the-connection-works"></a>Controllare che la connessione funzioni
1. Passare alla scheda **Gestisci** e selezionare il servizio collegato creato.
1. Assicurarsi che **la creazione interattiva** sia attiva.
1. Selezionare **Test connessione**. La connessione dovrebbe essere riuscita.

Ora hai stabilito una connessione sicura e privata tra Synapse e il tuo servizio collegato!

## <a name="next-steps"></a>Passaggi successivi

Per sviluppare un'ulteriore comprensione dell'endpoint privato gestito in Synapse Analytics, vedere l'articolo [Concetto intorno all'endpoint privato gestito di Synapse.To](data-integration-data-lake.md) develop further understanding of Managed private endpoint in Synapse Analytics, see the Concept around Synapse Managed private endpoint article.

Per altre informazioni sull'integrazione dei dati per Synapse Analytics, vedere l'articolo Inserimento dei dati in un Data Lake.For more information on data integration for Synapse Analytics, see the [Ingesting data into a Data Lake](data-integration-data-lake.md) article.