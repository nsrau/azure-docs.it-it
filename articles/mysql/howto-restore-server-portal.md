---
title: Come ripristinare un server nel Database di Azure per MySQL | Microsoft Docs
description: In questo articolo viene descritta la procedura per ripristinare un server nel Database di Azure per MySQL usando il portale di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6c1c0f8a0c0e59661b70b787b551b8cfdb024cda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Come eseguire la procedura di backup e ripristino di un server in Database di Azure per MySQL tramite il portale di Azure

## <a name="backup-happens-automatically"></a>Il backup viene eseguito automaticamente
Quando si usa Database di Azure per MySQL, il servizio di database esegue automaticamente il backup del servizio ogni 5 minuti. 

La disponibilità dei backup è di 7 giorni per il livello Basic e 35 giorni per il livello Standard. Per altre informazioni, vedere [Livelli di servizio del Database di Azure per MySQL](concepts-service-tiers.md)

L'uso di questa funzionalità di backup automatico permette di ripristinare il server e tutti i suoi database in un nuovo server a un precedente punto temporizzato.

## <a name="restore-in-the-azure-portal"></a>Ripristino nel portale di Azure
Database di Azure per MySQL consente di ripristinare il server a un punto temporizzato specifico e in una nuova copia del server. È possibile usare questo nuovo server per ripristinare i dati. 

Ad esempio, se oggi una tabella è stata accidentalmente eliminata a mezzogiorno, è possibile eseguire il ripristino a un'ora prima di mezzogiorno per recuperare la tabella e i dati mancanti dalla nuova copia del server.

La procedura seguente consente di ripristinare il server di esempio a un punto temporizzato specifico:

1. Accedere al [portale di Azure](https://portal.azure.com/)

2. Individuare il Database di Azure per il server MySQL. Nel riquadro a sinistra selezionare **All resources** (Tutte le risorse) e il server dall'elenco.

3.  Nella parte superiore del pannello Panoramica server, fare clic su **Ripristina** nella barra degli strumenti. Viene aperto il pannello Ripristina.
![fare clic sul pulsante Ripristina](./media/howto-restore-server-portal/click-restore-button.png)

4. Compilare il modulo Ripristina con le informazioni obbligatorie:

- **Punto di ripristino (UTC)**: usando i controlli Selezione data e Selezione ora, scegliere un punto di ripristino temporizzato. L'ora specificata è nel formato UTC, pertanto è probabile che si debba convertire l'ora locale in UTC.
- **Ripristina al nuovo server**: specificare il nome del nuovo server in cui ripristinare il server esistente.
- **Percorso**: la scelta dell'area viene eseguita automaticamente ricalcando l'area del server di origine e non può essere modificata.
- **Piano tariffario**: la scelta del piano tariffario viene effettuata automaticamente con lo stesso livello di prezzo del server di origine e non può essere modificata qui. 
![Ripristino temporizzato](./media/howto-restore-server-portal/pitr-restore.png)

5. Fare clic su **OK** per ripristinare il server al punto di ripristino temporizzato specificato. 

6. Al termine dell'operazione di ripristino, individuare il nuovo server creato e verificare che il ripristino dei database sia avvenuto come previsto.

## <a name="next-steps"></a>Passaggi successivi
- [Raccolte connessioni per il Database di Azure per MySQL](concepts-connection-libraries.md).