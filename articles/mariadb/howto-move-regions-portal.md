---
title: Spostare le aree di Azure-portale di Azure database di Azure per MariaDB
description: Spostare un database di Azure per il server MariaDB da un'area di Azure a un'altra usando una replica di lettura e il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: b2e4bc71a0883c6fef6f0115080a79a74ced92b0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542428"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Spostare un database di Azure per il server MariaDB in un'altra area usando il portale di Azure

Esistono diversi scenari per lo trasferimento di un database di Azure esistente per il server MariaDB da un'area all'altra. Ad esempio, potrebbe essere necessario spostare un server di produzione in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

Per completare lo spostamento in un'altra area, è possibile usare una [replica di lettura tra aree](concepts-read-replicas.md#cross-region-replication) per database di Azure per MariaDB. A tale scopo, creare prima di tutto una replica di lettura nell'area di destinazione. Arrestare quindi la replica al server di replica di lettura per renderlo un server autonomo che accetta il traffico di lettura e scrittura. 

> [!NOTE]
> Questo articolo è incentrato sullo spostare il server in un'area diversa. Per spostare il server in un gruppo di risorse o una sottoscrizione diversa, vedere l'articolo relativo allo [spostamento](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) . 

## <a name="prerequisites"></a>Prerequisiti

- La funzionalità di lettura della replica è disponibile solo per i server di database di Azure per MariaDB nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server di origine si trovi in uno di questi piani tariffari.

- Verificare che il database di Azure per il server di origine MariaDB si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

## <a name="prepare-to-move"></a>Preparare lo spostamento

Per creare un server di replica di lettura tra aree nell'area di destinazione usando il portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare il database di Azure per il server MariaDB esistente che si vuole usare come server di origine. Questa azione apre la pagina **Panoramica**.
1. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.
1. Selezionare **Aggiungi replica**.
1. Immettere un nome per il server di replica.
1. Selezionare la posizione per il server di replica. Il percorso predefinito è identico a quello del server di origine. Verificare di aver selezionato il percorso di destinazione in cui si vuole distribuire la replica.
1. Selezionare **OK** per confermare la creazione della replica. Durante la creazione della replica, i dati vengono copiati dal server di origine alla replica. L'ora di creazione può durare alcuni minuti o più, in proporzione alle dimensioni del server di origine.

>[!NOTE]
> Quando si crea una replica, non eredita gli endpoint di servizio VNet del server di origine. Queste regole devono essere configurate in modo indipendente per la replica.

## <a name="move"></a>Sposta

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Se si arresta la replica nel server di replica, quest'ultima diventa un server autonomo. Per arrestare la replica nella replica dalla portale di Azure, attenersi alla procedura seguente:

1. Dopo aver creato la replica, individuare e selezionare il database di Azure per il server di origine MariaDB. 
1. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.
1. Selezionare il server di replica.
1. Selezionare **Arresta replica**.
1. Confermare che si vuole arrestare la replica facendo clic su **OK**.

## <a name="clean-up-source-server"></a>Pulisci server di origine

Potrebbe essere necessario eliminare il database di Azure di origine per il server MariaDB. A tale scopo, seguire questa procedura:

1. Dopo aver creato la replica, individuare e selezionare il database di Azure per il server di origine MariaDB.
1. Nella finestra **Panoramica** selezionare **Elimina**.
1. Digitare il nome del server di origine per confermare che si desidera eliminare.
1. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un database di Azure per il server MariaDB da un'area a un'altra usando il portale di Azure e quindi ripulite le risorse di origine non necessarie. 

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
- Altre informazioni sulla [gestione delle repliche di lettura nel portale di Azure](howto-read-replicas-portal.md)
- Altre informazioni sulle opzioni di [continuità aziendale](concepts-business-continuity.md)
