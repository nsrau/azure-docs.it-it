---
title: Creare un'istanza gestita di SQL di Azure usando Azure Data Studio
description: Creare un'istanza gestita di SQL di Azure usando Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280466"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Creare un'istanza gestita di SQL: Azure ARC usando Azure Data Studio

Questo documento illustra i passaggi per l'installazione dell'istanza gestita di SQL di Azure-Azure Arc con Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Accedere al controller di dati di Azure Arc

Prima di poter creare un'istanza, accedere al controller dati di Azure Arc se non è già stato effettuato l'accesso.

```console
azdata login
```

Verrà quindi richiesto lo spazio dei nomi in cui viene creato il controller dati, il nome utente e la password per accedere al controller.  

> Se è necessario convalidare lo spazio dei nomi, è possibile eseguire ```kubectl get pods -A``` per ottenere un elenco di tutti gli spazi dei nomi nel cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Creare un'istanza gestita di SQL di Azure in Azure Arc

- Avvia Azure Data Studio
- Nella scheda connessioni fare clic sui tre puntini in alto a sinistra e scegliere "nuova distribuzione".
- Dalle opzioni di distribuzione selezionare **istanza gestita di SQL di Azure-Azure Arc** 
  > [!NOTE]
  > Se non è attualmente installato, potrebbe essere richiesto di installare [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] qui.
- Accetta le condizioni di licenza e privacy e fai clic su **Seleziona** nella parte inferiore



- Nel pannello Distribuisci istanza gestita di Azure SQL-Azure Arc immettere le informazioni seguenti:
  - Immettere un nome per l'istanza di SQL Server
  - Immettere e confermare una password per l'istanza di SQL Server
  - Selezionare la classe di archiviazione in base alle esigenze dei dati
  - Selezionare la classe di archiviazione in base alle esigenze dei log

- Fare clic sul pulsante **Distribuisci** .

- Questa operazione dovrebbe avviare la creazione dell'istanza gestita di SQL di Azure-Azure Arc sul controller dati.

- In pochi minuti la creazione dovrebbe essere completata correttamente

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Connettersi all'istanza gestita di SQL di Azure: Azure Arc da Azure Data Studio

- Accedere al controller di dati di Azure ARC specificando lo spazio dei nomi, il nome utente e la password per il controller dati: 
```console
azdata login
```

- Visualizzare tutte le istanze gestite di SQL di Azure di cui è stato effettuato il provisioning, usando i comandi seguenti:

```console
azdata arc sql mi list
```

L'output dovrebbe essere simile al seguente, copiare il ServerEndpoint (incluso il numero di porta) da qui.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- In Azure Data Studio, nella scheda **connessioni** , fare clic sulla **nuova connessione** nella visualizzazione **server** .
- Nel pannello **connessione** incollare ServerEndpoint nella casella di testo Server.
- Selezionare **account di accesso SQL** come tipo di autenticazione
- Immettere *sa* come nome utente
- Immettere la password per l' `sa` account
- Facoltativamente, immettere il nome del database specifico a cui connettersi
- Facoltativamente, selezionare/aggiungere un nuovo gruppo di server nel modo appropriato
- Selezionare **Connetti** per connettersi all'istanza gestita di SQL di Azure-Azure Arc




## <a name="next-steps"></a>Passaggi successivi

Ora provare a [monitorare l'istanza di SQL](monitor-grafana-kibana.md)
