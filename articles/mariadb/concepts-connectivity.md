---
title: Errori di connettività temporanei-database di Azure per MariaDB
description: Informazioni su come gestire gli errori di connettività temporanei per Database di Azure per MariaDB.
keywords: connessione mysql,stringa di connessione,problemi di connettività,errore temporaneo,errore di connessione
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 3e6c5c8b6c3f118f1b19c5e2b3455f1f66f7e70e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100805"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Gestione degli errori di connettività temporanei per Database di Azure per MariaDB

Questo articolo descrive come gestire gli errori temporanei nella connessione a Database di Azure per MariaDB.

## <a name="transient-errors"></a>Errori temporanei

Un errore temporaneo è un errore che si risolve in modo autonomo. Questi errori generalmente si manifestano come una connessione al server di database che viene interrotta. Inoltre non è possibile aprire nuove connessioni a un server. Possono verificarsi errori temporanei, ad esempio nel caso di un errore hardware o di rete. Un altro motivo potrebbe essere una nuova versione di un servizio PaaS che viene implementato. La maggior parte di questi eventi viene automaticamente attenuata dal sistema in meno di 60 secondi. Una procedura consigliata per la progettazione e lo sviluppo di applicazioni nel cloud è la previsione di errori temporanei, presupponendo che possano accadere in qualsiasi componente e in qualunque momento e che si disponga della logica appropriata per affrontare tali situazioni.

## <a name="handling-transient-errors"></a>Gestione degli errori temporanei

Gli errori temporanei devono essere gestiti usando la logica di ripetizione dei tentativi. Situazioni da considerare:

* Si verifica un errore quando si tenta di aprire una connessione
* Una connessione inattiva viene eliminata sul lato server. Se si prova a eseguire un comando non si riesce a eseguirlo
* Una connessione attiva che sta attualmente eseguendo un comando viene eliminata.

Il primo e il secondo caso sono abbastanza semplici da gestire. Provare ad aprire di nuovo la connessione. Quando l'operazione riesce, l'errore temporaneo è stato attenuato dal sistema. È possibile usare di nuovo Database di Azure per MariaDB. È consigliabile attendere prima di riprovare a effettuare la connessione. Interrompere temporaneamente l'operazione se i tentativi iniziali hanno esito negativo. In questo modo il sistema può usare tutte le risorse disponibili per superare la situazione di errore. È consigliabile seguire questa procedura:

* Attendere 5 secondi prima di riprovare.
* Per ogni tentativo successivo, aumentare l'attesa in modo esponenziale, fino a 60 secondi.
* Impostare un numero massimo di tentativi nel punto in cui l'applicazione considera l'operazione non riuscita.

Quando una connessione con una transazione attiva ha esito negativo, la gestione corretta del ripristino è più difficile. Possono presentarsi due casi. Se la transazione era di sola lettura, è opportuno riaprire la connessione e riprovare la transazione. Se invece la transazione stava anche scrivendo nel database occorre stabilire se è stata sottoposta a rollback o se è riuscita prima che si verificasse l'errore temporaneo. In tal caso, è possibile che non sia stato ricevuto il riconoscimento del commit dal server di database.

Un modo per superare il problema consiste nel generare un ID univoco sul client usato per tutti i tentativi. Passare questo ID univoco come parte della transazione al server e archiviarlo in una colonna con un vincolo univoco. In questo modo è possibile riprovare a eseguire la transazione in tutta sicurezza. Il tentativo ha esito positivo se la transazione precedente è stata sottoposta a rollback e l'ID univoco generato dal client non esiste ancora nel sistema. Il tentativo ha esito negativo quando indica una violazione della chiave duplicata se l'ID univoco è stato già archiviato, perché la transazione precedente è stata completata correttamente.

Se il programma comunica con Database di Azure per MariaDB tramite middleware di terze parti, chiedere al fornitore se il middleware include la logica di ripetizione dei tentativi per gli errori temporanei.

Verificare di testare la logica di ripetizione dei tentativi. Provare, ad esempio, a eseguire il codice ridimensionando le risorse di calcolo del server di Database di Azure per MariaDB. L'applicazione deve gestire senza problemi il breve tempo di inattività che si verifica durante questa operazione.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di connessione a database di Azure per MariaDB](howto-troubleshoot-common-connection-issues.md)
