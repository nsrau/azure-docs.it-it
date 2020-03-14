---
title: Configurazione TLS-portale di Azure-database di Azure per MariaDB
description: Informazioni su come impostare la configurazione di TLS usando portale di Azure per il database di Azure per MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 063fc82131a66012c7329f590c0fdf2ce4d4da79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375005"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Configurazione delle impostazioni TLS nel database di Azure per MariaDB con portale di Azure

Questo articolo descrive come configurare un database di Azure per il server MariaDB per applicare le connessioni per una versione minima di TLS e negare tutte le connessioni con una versione più bassa di TLS, migliorando così la sicurezza della rete.

I clienti hanno ora la possibilità di applicare la versione TLS per la connessione al database di Azure per MariaDB impostando la versione minima di TLS per il server di database. Se ad esempio si imposta la versione minima delle impostazioni TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. In alternativa, se si imposta questa opzione su 1,2, è possibile consentire solo le connessioni dai client che usano TLS 1,2 e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

## <a name="prerequisites"></a>Prerequisites

Per completare questa guida, è necessario:

* Un [database di Azure per MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Impostare le configurazioni TLS per il database di Azure per MariaDB

Per impostare la versione minima di TLS del server MariaDB, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server MariaDB esistente.

1. Nella pagina Server MariaDB, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. Nella **versione minima di TLS**selezionare **1,2** per negare le connessioni con la versione tls inferiore a TLS 1,2 per il server MariaDB.

    ![Configurazione TLS per database di Azure per MariaDB](./media/howto-tls-configurations/tls-configurations.png)

1. È consigliabile fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Configurazione TLS per database di Azure per MariaDB riuscita](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-metric.md).
