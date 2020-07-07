---
title: Effettuare il provisioning e proteggere un servizio collegato in Azure sinapsi Analytics
description: Informazioni su come eseguire il provisioning e proteggere un servizio collegato con VNET gestiti
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81430552"
---
# <a name="securing-a-linked-service-with-private-links"></a>Protezione di un servizio collegato con collegamenti privati 

In questo articolo si apprenderà come proteggere un servizio collegato in sinapsi con un endpoint privato.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: usare Azure Data Lake Gen 2 come archivio dati di *origine*. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md) per informazioni su come crearne uno. Verificare che l'account di archiviazione disponga del filtro IP di sinapsi Studio per accedervi e che si consentano solo le **reti selezionate** per accedere all'account di archiviazione. L'impostazione sotto il pannello **firewall e reti virtuali** dovrebbe essere simile all'immagine seguente.

![Account di archiviazione protetto](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Creare un servizio collegato con collegamenti privati

In Azure Synapse Analytics si usano i servizi collegati per definire le informazioni di connessione ad altri servizi. In questa sezione Azure Synapse Analytics e Azure Data Lake Gen 2 verranno aggiunti come servizi collegati.

1. Aprire Azure sinapsi studio e passare alla scheda **Gestisci** .
1. In **Connessioni esterne** selezionare **Servizi collegati**.
1. Per aggiungere un servizio collegato, fare clic su **Nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 nell'elenco e fare clic su **Continua**.
1. Assicurarsi di abilitare la **creazione interattiva**. L'abilitazione potrebbe richiedere circa 1 minuto. 
1. Immettere le credenziali di autenticazione. I tipi di autenticazione attualmente supportati sono la chiave dell'account, l'entità servizio e l'identità gestita. Fare clic su Test connessione per verificare che le credenziali siano corrette.
1. Selezionare **Test connessione**. l'operazione non riesce perché l'account di archiviazione non consente l'accesso al suo interno senza la creazione e l'approvazione di un endpoint privato. Nel messaggio di errore verrà visualizzato un collegamento per creare un **endpoint privato** che è possibile seguire per passare alla parte successiva. Se si segue tale collegamento, ignorare la parte successiva.
1. Selezionare **Create** (Crea) al termine.

## <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato fatto clic sul collegamento ipertestuale quando si testa la connessione precedente, seguire il percorso seguente. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato in precedenza.

1. Passare alla scheda **Gestisci** .
1. Passare alla sezione **reti virtuali gestite** .
1. Selezionare **+ nuovo** in endpoint privato gestito.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e selezionare **continua**.
1. Immettere il nome dell'account di archiviazione creato in precedenza.
1. Selezionare **Crea**
1. Si noterà che dopo aver atteso alcuni secondi il collegamento privato creato necessita di un'approvazione.

## <a name="approval-of-a-private-link"></a>Approvazione di un collegamento privato
1. Selezionare l'endpoint privato creato in precedenza. È possibile visualizzare un collegamento ipertestuale che consente di approvare l'endpoint privato a livello di account di archiviazione. *In alternativa, è possibile passare direttamente all'account di archiviazione portale di Azure e passare al pannello **connessioni endpoint privato** .*
1. Selezionare l'endpoint privato creato in studio e selezionare **approva**.
1. Aggiungere una descrizione e fare clic su **Sì**
1. Tornare a sinapsi studio nella sezione **reti virtuali gestite** della scheda **Gestisci**.
1. L'approvazione per l'endpoint privato dovrebbe richiedere circa 1 minuto.

## <a name="check-the-connection-works"></a>Verificare il funzionamento della connessione
1. Passare alla scheda **Gestisci** e selezionare il servizio collegato creato.
1. Assicurarsi che la **creazione interattiva** sia attiva.
1. Selezionare **Test connessione**. Si noterà che la connessione avrà esito positivo.

A questo punto è stata stabilita una connessione sicura e privata tra le sinapsi e il servizio collegato.

## <a name="next-steps"></a>Passaggi successivi

Per approfondire la conoscenza dell'endpoint privato gestito in sinapsi Analytics, vedere l'articolo relativo all' [endpoint privato gestito da sinapsi](data-integration-data-lake.md) .

Per altre informazioni sull'integrazione dei dati per l'analisi delle sinapsi, vedere l'articolo inserimento di [dati in un data Lake](data-integration-data-lake.md) .