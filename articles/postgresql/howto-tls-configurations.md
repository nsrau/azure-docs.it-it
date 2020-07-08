---
title: Configurazione TLS-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Informazioni su come impostare la configurazione di TLS usando portale di Azure per il server singolo del database di Azure per PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: ed5d46bbb5a520ee28013210ba44045ad59c2cc2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84705695"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Configurazione delle impostazioni TLS nel server singolo del database di Azure per PostgreSQL con portale di Azure

Questo articolo descrive come configurare un database di Azure per PostgreSQL per applicare la versione minima di TLS consentita per le connessioni e negare tutte le connessioni con una versione di TLS inferiore rispetto alla versione minima configurata di TLS, migliorando così la sicurezza della rete

È possibile applicare la versione TLS per la connessione al database di Azure per PostgreSQL. I clienti hanno ora la possibilità di impostare la versione minima di TLS per il server di database. Se ad esempio si imposta la versione minima delle impostazioni TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. Invece di impostare la versione minima di TLS su 1.2 + significa consentire solo le connessioni dai client che usano TLS 1,2 e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Impostare le configurazioni TLS per database di Azure per PostgreSQL-server singolo

Seguire questa procedura per impostare la versione minima TLS di PostgreSQL:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per PostgreSQL esistente.

1.  Nella pagina database di Azure per PostgreSQL-server singolo, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. Nella **versione minima di TLS**selezionare **1,2** per negare le connessioni con la versione tls inferiore a TLS 1,2 per il server singolo PostgreSQL.

    ![Configurazione di TLS a server singolo per database di Azure per PostgreSQL](./media/howto-tls-configurations/setting-tls-value.png)

1. È consigliabile fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Database di Azure per PostgreSQL-configurazione TLS a server singolo riuscita](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi](howto-alert-on-metric.md) per le metriche