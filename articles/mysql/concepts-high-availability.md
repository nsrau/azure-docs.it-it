---
title: Concetti relativi alla disponibilità elevata in Database di Azure per MySQL
description: In questo argomento vengono offerte informazioni relative alla disponibilità elevata quando si usa Database di Azure per MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 055727695bfa1ce8a6bb160a7e071c2a161afb3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837830"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Concetti relativi alla disponibilità elevata in Database di Azure per MySQL
Il servizio Database di Azure per MySQL offre un livello di disponibilità elevata garantito. Il contratto di servizio con copertura finanziaria è del 99,99% in concomitanza con la disponibilità a livello generale. I tempi di inattività dell'applicazione sono praticamente nulli quando si usa questo servizio.

## <a name="high-availability"></a>Disponibilità elevata
Il modello di disponibilità elevata si basa sui meccanismi di failover predefiniti quando si verifica un'interruzione a livello di nodo. Un'interruzione a livello di nodo potrebbe verificarsi a causa di un errore hardware o in risposta a una distribuzione del servizio.

In qualsiasi momento, le modifiche apportate a un server di database del Database di Azure per MySQL si verificano nel contesto di una transazione. Le modifiche vengono registrate in modo sincrono nel servizio di archiviazione di Azure quando viene eseguito il commit della transazione. Se si verifica un'interruzione a livello di nodo, il server di database crea automaticamente un nuovo nodo e collega l'archiviazione dei dati al nuovo nodo. Vengono eliminate tutte le connessioni attive e non viene eseguito il commit delle transazioni in fase di elaborazione.

## <a name="application-retry-logic-is-essential"></a>La logica di ripetizione dei tentativi dell'applicazione è essenziale
È importante che le applicazioni di database MySQL vengano compilate in modo da rilevare e ripetere i tentativi di connessione interrotti e le transazioni non riuscite. Quando viene ripetuto il tentativo dell'applicazione, la connessione dell'applicazione viene reindirizzata in modo trasparente all'istanza appena creata, che subentra all'istanza non riuscita.

All'interno di Azure viene usato un gateway per reindirizzare le connessioni alla nuova istanza. Dopo un'interruzione, l'intero processo di failover richiede in genere decine di secondi. Poiché il reindirizzamento viene gestito internamente dal gateway, la stringa di connessione esterna rimane la stessa per le applicazioni client.

## <a name="scaling-up-or-down"></a>Ridimensionamento di un server
Come avviene per il modello a disponibilità elevata, quando un'istanza di Database di Azure per MySQL viene ridimensionata verso l'alto o verso il basso viene creata una nuova istanza del server con le dimensioni specificate. L'archiviazione dati esistente viene scollegata dall'istanza originale e associata alla nuova istanza.

Durante l'operazione di ridimensionamento, si verifica un'interruzione delle connessioni al database. Le applicazioni client vengono disconnesse e le transazioni aperte non sottoposte a commit vengono annullate. Dopo che l'applicazione client ritenta la connessione o crea una nuova connessione, il gateway indirizza la connessione all'istanza appena ridimensionata. 

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulla [gestione degli errori di connettività temporanei](concepts-connectivity.md)
- Informazioni sulle modalità di [replica dei dati con le repliche in lettura](howto-read-replicas-portal.md)
