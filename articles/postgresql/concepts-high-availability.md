---
title: Concetti relativi alla disponibilità elevata in Database di Azure per PostgreSQL
description: Questo articolo offre informazioni relative alla disponibilità elevata quando si usa Database di Azure per PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 02/01/2019
ms.date: 02/18/2019
ms.openlocfilehash: d43647b57469efa4581dc1c74a842a51e9d54ad7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623817"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Concetti relativi alla disponibilità elevata in Database di Azure per PostgreSQL
Il servizio Database di Azure per PostgreSQL offre un livello di disponibilità elevata garantito. Il contratto di servizio con copertura finanziaria è del 99,99% in concomitanza con la disponibilità a livello generale. I tempi di inattività dell'applicazione sono praticamente nulli quando si usa questo servizio.

## <a name="high-availability"></a>Disponibilità elevata
Il modello di disponibilità elevata si basa sui meccanismi di failover predefiniti quando si verifica un'interruzione a livello di nodo. Un'interruzione a livello di nodo potrebbe verificarsi a causa di un errore hardware o in risposta a una distribuzione del servizio.

In qualsiasi momento, le modifiche apportate a un server di database del Database di Azure per PostgreSQL si verificano nel contesto di una transazione. Le modifiche vengono registrate in modo sincrono nel servizio di archiviazione di Azure quando viene eseguito il commit della transazione. Se si verifica un'interruzione a livello di nodo, il server di database crea automaticamente un nuovo nodo e collega l'archiviazione dei dati al nuovo nodo. Vengono eliminate tutte le connessioni attive e non viene eseguito il commit delle transazioni in fase di elaborazione.

## <a name="application-retry-logic-is-essential"></a>La logica di ripetizione dei tentativi dell'applicazione è essenziale
È importante che le applicazioni di database PostgreSQL vengano compilate in modo da rilevare e ripetere i tentativi di connessione interrotti e le transazioni non riuscite. Quando viene ripetuto il tentativo dell'applicazione, la connessione dell'applicazione viene reindirizzata in modo trasparente all'istanza appena creata, che subentra all'istanza non riuscita.

All'interno di Azure viene usato un gateway per reindirizzare le connessioni alla nuova istanza. Dopo un'interruzione, l'intero processo di failover richiede in genere decine di secondi. Poiché il reindirizzamento viene gestito internamente dal gateway, la stringa di connessione esterna rimane la stessa per le applicazioni client.

## <a name="scaling-up-or-down"></a>Ridimensionamento di un server
Come avviene per il modello a disponibilità elevata, quando un'istanza di Database di Azure per PostgreSQL viene ridimensionata verso l'alto o verso il basso viene creata una nuova istanza del server con le dimensioni specificate. L'archiviazione dati esistente viene scollegata dall'istanza originale e associata alla nuova istanza.

Durante l'operazione di ridimensionamento, si verifica un'interruzione delle connessioni al database. Le applicazioni client vengono disconnesse e le transazioni aperte non sottoposte a commit vengono annullate. Dopo che l'applicazione client ritenta la connessione o crea una nuova connessione, il gateway indirizza la connessione all'istanza appena ridimensionata. 

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulla [gestione degli errori di connettività temporanei](concepts-connectivity.md)
- Informazioni sulle modalità di [replica dei dati con le repliche in lettura](howto-read-replicas-portal.md)
