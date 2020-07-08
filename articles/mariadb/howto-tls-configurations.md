---
title: Configurazione TLS-portale di Azure-database di Azure per MariaDB
description: Informazioni su come impostare la configurazione di TLS usando portale di Azure per il database di Azure per MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 9ebaca8720eb923c2373c45ac605146bbcdbc4a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848000"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Configurazione delle impostazioni TLS nel database di Azure per MariaDB con portale di Azure

Questo articolo descrive come configurare un database di Azure per il server MariaDB per applicare la versione minima di TLS per le connessioni e negare tutte le connessioni con una versione di TLS inferiore rispetto alla versione minima configurata di TLS, migliorando in tal modo la sicurezza della rete.

È possibile applicare la versione TLS per la connessione al database di Azure per MariaDB impostando la versione minima di TLS per il server di database. Se ad esempio si imposta la versione minima delle impostazioni TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. In alternativa, l'impostazione di questa opzione su 1,2 significa che è possibile consentire solo le connessioni dai client che usano TLS 1.2 + e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

## <a name="prerequisites"></a>Prerequisiti

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

Informazioni su [come creare avvisi](howto-alert-metric.md) per le metriche