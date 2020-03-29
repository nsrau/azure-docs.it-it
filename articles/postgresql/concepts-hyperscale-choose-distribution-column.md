---
title: Scegliere le colonne di distribuzione - Hyperscale (Citus) - Database di Azure per PostgreSQLChoose distribution columns – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Informazioni su come scegliere le colonne di distribuzione in scenari di iperscalabili comuni nel database di Azure per PostgreSQL.Learn how to choose distribution columns in common hyperscale scenarios in Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975670"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Scegliere le colonne di distribuzione nel database di Azure per PostgreSQL - Hyperscale (Citus)Choose distribution columns in Azure Database for PostgreSQL – Hyperscale (Citus)

La scelta della colonna di distribuzione di ogni tabella è una delle decisioni di modellazione più importanti da prendere. Database di Azure per PostgreSQL: Hyperscale (Citus) archivia le righe in partizioni in base al valore della colonna di distribuzione delle righe.

La scelta corretta raggruppa i dati correlati sugli stessi nodi fisici, il che velocizza le query e aggiunge il supporto per tutte le funzionalità SQL. Una scelta non corretta rende il sistema a esecuzione lenta e non supporta tutte le funzionalità SQL tra i nodi.

In questo articolo vengono forniti suggerimenti per le due scenari di Hyperscale (Citus) più comuni.

### <a name="multi-tenant-apps"></a>App multi-tenant

L'architettura multi-tenant utilizza una forma di modellazione gerarchica del database per distribuire le query tra i nodi del gruppo di server. La parte superiore della gerarchia dei dati è nota come *ID tenant* e deve essere archiviata in una colonna in ogni tabella.

Hyperscale (Citus) controlla le query per vedere quale ID tenant coinvolgono e trova la partizione di tabella corrispondente. Indirizza la query a un singolo nodo di lavoro che contiene la partizione. L'esecuzione di una query con tutti i dati rilevanti inseriti nello stesso nodo è denominata colocation.

Il diagramma seguente illustra la colocation nel modello di dati multi-tenant. Contiene due tabelle, Account e Campagne, ciascuna distribuita da `account_id`. Le caselle ombreggiate rappresentano le partizioni. Le partizioni verdi vengono archiviate insieme in un nodo di lavoro e le partizioni blu in un altro nodo di lavoro. Si noti come una query di join tra account e campagne disponga di\_tutti i dati necessari in un nodo quando entrambe le tabelle sono limitate allo stesso ID account.

![Colocation multi-tenant](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Per applicare questa progettazione nel proprio schema, identificare ciò che costituisce un tenant nell'applicazione. Le istanze comuni includono società, account, organizzazione o cliente.Common instances include company, account, organization, or customer. Il nome della colonna `company_id` `customer_id`sarà simile o . Esaminare ciascuna delle query e chiedersi, funzionerebbe se avesse ulteriori clausole WHERE per limitare tutte le tabelle coinvolte alle righe con lo stesso ID tenant?
Le query nel modello multi-tenant hanno come ambito un tenant. Ad esempio, l'ambito delle query sulle vendite o sull'inventario è compreso in un determinato punto vendita.

#### <a name="best-practices"></a>Procedure consigliate

-   **Partizionare le tabelle\_distribuite in base a una colonna ID tenant comune.** Ad esempio, in un'applicazione SaaS in\_cui i tenant sono\_società, è probabile che l'ID tenant sia l'ID società.
-   **Convertire piccole tabelle tra tenant in tabelle di riferimento.** Quando più tenant condividono una piccola tabella di informazioni, distribuirla come tabella di riferimento.
-   **Limitare il filtro\_di tutte le query di applicazione in base all'ID tenant.** Ogni query deve richiedere informazioni per un tenant alla volta.

Leggere [l'esercitazione multi-tenant](./tutorial-design-database-hyperscale-multi-tenant.md) per un esempio di come creare questo tipo di applicazione.

### <a name="real-time-apps"></a>App in tempo reale

L'architettura multi-tenant introduce una struttura gerarchica e utilizza la colocation dei dati per instradare le query per tenant. Al contrario, le architetture in tempo reale dipendono da proprietà di distribuzione specifiche dei dati per ottenere un'elaborazione altamente parallela.

Utilizziamo "ID entità" come termine per le colonne di distribuzione nel modello in tempo reale. Le entità tipiche sono utenti, host o dispositivi.Typical entities are users, hosts, or devices.

Le query in tempo reale in genere specificano aggregazioni numeriche raggruppate per data o categoria. Hyperscale (Citus) invia queste query a ogni partizione per ottenere risultati parziali e assembla la risposta finale nel nodo del coordinatore. Le query vengono eseguite più velocemente quando il maggior numero possibile di nodi contribuisce e quando nessun singolo nodo deve eseguire una quantità di lavoro sproporzionata.

#### <a name="best-practices"></a>Procedure consigliate

-   **Scegliere una colonna con cardinalità elevata come colonna di distribuzione.** Per confronto, un campo Stato in una tabella ordini con i valori Nuovo, Pagato e Spedito è una scelta di distribuzione inequione. Presuppone solo i pochi valori, che limita il numero di partizioni che possono contenere i dati e il numero di nodi che possono elaborarli. Tra le colonne con cardinalità elevata, è anche consigliabile scegliere le colonne che vengono spesso utilizzate nelle clausole di raggruppamento o come chiavi di join.
-   **Scegliere una colonna con distribuzione uniforme.** Se si distribuisce una tabella in una colonna distorta a determinati valori comuni, i dati nella tabella tendono ad accumularsi in determinate partizioni. I nodi che contengono tali partizioni finiscono per eseguire più lavoro rispetto ad altri nodi.
-   **Distribuire le tabelle dei fatti e delle dimensioni nelle colonne comuni.**
    La tabella dei fatti può avere una sola chiave di distribuzione. Le tabelle che si uniscono a un'altra chiave non verranno colocate con la tabella dei fatti. Scegliere una dimensione da collocare in base alla frequenza con cui viene unita e alle dimensioni delle righe di unione.
-   **Modificare alcune tabelle delle dimensioni in tabelle di riferimento.** Se una tabella delle dimensioni non può essere collocata con la tabella dei fatti, è possibile migliorare le prestazioni delle query distribuendo copie della tabella delle dimensioni a tutti i nodi sotto forma di tabella di riferimento.

Leggere [l'esercitazione](./tutorial-design-database-hyperscale-realtime.md) sul dashboard in tempo reale per un esempio di come creare questo tipo di applicazione.

### <a name="time-series-data"></a>Dati di serie temporali

In un carico di lavoro di una serie temporale, le applicazioni interrogano le informazioni recenti mentre archiviano le vecchie informazioni.

L'errore più comune nella modellazione delle informazioni sulle serie temporali in Hyperscale (Citus) consiste nell'utilizzare il timestamp stesso come colonna di distribuzione. Una distribuzione hash basata sul tempo distribuisce orari apparentemente casualmente in partizioni diverse anziché mantenere insieme intervalli di tempo nelle partizioni. Le query che coinvolgono il tempo in genere fanno riferimento a intervalli di tempo, ad esempio i dati più recenti. Questo tipo di distribuzione hash comporta un sovraccarico di rete.

#### <a name="best-practices"></a>Procedure consigliate

-   **Non scegliere un timestamp come colonna di distribuzione.** Scegliere una colonna di distribuzione diversa. In un'app multi-tenant usare l'ID tenant o in un'app in tempo reale usare l'ID entità.
-   **Usare invece il partizionamento della tabella PostgreSQL per il partizionamento temporale.** Usare il partizionamento delle tabelle per suddividere una tabella di grandi dimensioni di dati ordinati nel tempo in più tabelle ereditate con ogni tabella contenente intervalli di tempo diversi. La distribuzione di una tabella partizionata da Postgres in Hyperscale (Citus) crea partizioni per le tabelle ereditate.

Leggere [l'esercitazione sulla serie temporale](https://aka.ms/hyperscale-tutorial-timeseries) per un esempio di come creare questo tipo di applicazione.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come la [colocation](concepts-hyperscale-colocation.md) tra dati distribuiti consente l'esecuzione rapida delle query.
