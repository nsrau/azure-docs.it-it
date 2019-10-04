---
title: 'Scegliere le colonne di distribuzione nel database di Azure per PostgreSQL: iperscalabilità (CITUS)'
description: Scelte valide per le colonne di distribuzione in scenari con iperscalabilità comune
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: b0d1f343aa9b125ab0a5a9ab559d0788253037aa
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998181"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Scegliere le colonne di distribuzione nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

La scelta della colonna di distribuzione di ogni tabella è una delle decisioni di modellazione più importanti da eseguire. Azure database per PostgreSQL – iperscale (CITUS) Preview archivia le righe nelle partizioni in base al valore della colonna di distribuzione Rows.

La scelta corretta raggruppa i dati correlati sugli stessi nodi fisici, rendendo rapidamente le query e aggiunge il supporto per tutte le funzionalità SQL. Una scelta non corretta rende l'esecuzione lenta del sistema e non supporta tutte le funzionalità SQL tra i nodi.

Questo articolo fornisce suggerimenti sulle colonne di distribuzione per i due scenari più comuni di iperscalabilità (CITUS).

### <a name="multi-tenant-apps"></a>App multi-tenant

L'architettura multi-tenant utilizza una forma di modellazione di database gerarchica per distribuire query tra nodi del gruppo di server. La parte superiore della gerarchia dei dati è nota come *ID tenant* e deve essere archiviata in una colonna in ogni tabella.

Iperscale (CITUS) esamina le query per individuare l'ID tenant che coinvolgono e trova la partizione della tabella corrispondente. Instrada la query a un singolo nodo di lavoro che contiene la partizione. L'esecuzione di una query con tutti i dati rilevanti posizionati nello stesso nodo viene chiamata condivisione percorso.

Il diagramma seguente illustra la condivisione percorso nel modello di dati multi-tenant. Contiene due tabelle, account e campagne, ognuno distribuito da `account_id`. Le caselle ombreggiate rappresentano le partizioni. Le partizioni verdi vengono archiviate insieme in un nodo di lavoro e le partizioni blu vengono archiviate in un altro nodo di lavoro. Si noti come una query di join tra account e campagne includa tutti i dati necessari insieme in un nodo quando entrambe le tabelle sono limitate\_allo stesso ID account.

![Condivisione percorso multi-tenant](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Per applicare questa progettazione nello schema, identificare ciò che costituisce un tenant nell'applicazione. Le istanze comuni includono società, account, organizzazione o cliente. Il nome della colonna sarà simile a `company_id` o `customer_id`. Esaminare ogni query e chiedersi se sono presenti clausole WHERE aggiuntive per limitare tutte le tabelle incluse nelle righe con lo stesso ID tenant.
Le query nel modello multi-tenant hanno come ambito un tenant. Ad esempio, l'ambito delle query sulle vendite o sull'inventario rientra in un determinato archivio.

#### <a name="best-practices"></a>Procedure consigliate

-   **Partizionare le tabelle distribuite in\_base a una colonna ID tenant comune.** Ad esempio, in un'applicazione SaaS in cui i tenant sono società, è\_probabile che l'ID tenant sia l'\_ID società.
-   **Convertire le tabelle di piccole dimensioni tra tenant in tabelle di riferimento.** Quando più tenant condividono una piccola tabella di informazioni, è possibile distribuirla come tabella di riferimento.
-   **Limita filtro per tutte le query dell'\_applicazione in base all'ID tenant.** Ogni query deve richiedere informazioni per un tenant alla volta.

Per un esempio di creazione di questo tipo di applicazione, vedere l' [esercitazione multi-tenant](./tutorial-design-database-hyperscale-multi-tenant.md) .

### <a name="real-time-apps"></a>App in tempo reale

L'architettura multi-tenant introduce una struttura gerarchica e usa la condivisione percorso dati per instradare le query per ogni tenant. Al contrario, le architetture in tempo reale dipendono da proprietà di distribuzione specifiche dei dati per ottenere un'elaborazione altamente parallela.

Viene usato "ID entità" come termine per le colonne di distribuzione nel modello in tempo reale. Le entità tipiche sono utenti, host o dispositivi.

Le query in tempo reale richiedono le aggregazioni numeriche raggruppate in base alla data o alla categoria. Iperscale (CITUS) Invia queste query a ogni partizione per ottenere risultati parziali e assembla la risposta finale sul nodo coordinatore. Le query vengono eseguite più velocemente quando il numero di nodi è più possibile e quando nessun singolo nodo deve eseguire una quantità di lavoro sproporzionata.

#### <a name="best-practices"></a>Procedure consigliate

-   **Scegliere una colonna con cardinalità elevata come colonna di distribuzione.** Per il confronto, un campo di stato in una tabella Order con valori nuovi, a pagamento e spedito è una scelta di colonna di distribuzione non sufficiente. Si presuppone solo questi pochi valori, che limitano il numero di partizioni che possono conservare i dati e il numero di nodi che possono elaborarli. Tra le colonne con cardinalità elevata è inoltre consigliabile scegliere le colonne utilizzate di frequente nelle clausole Group-by o come chiavi di join.
-   **Scegliere una colonna con una distribuzione uniforme.** Se si distribuisce una tabella in una colonna inclinata a determinati valori comuni, i dati nella tabella tendono ad accumularsi in determinate partizioni. I nodi che contengono le partizioni finiscono più lavoro rispetto ad altri nodi.
-   **Distribuire le tabelle dei fatti e delle dimensioni nelle colonne comuni.**
    La tabella dei fatti può avere una sola chiave di distribuzione. Le tabelle che si uniscono a un'altra chiave non verranno condivise con la tabella dei fatti. Scegliere una dimensione per il percorso in base alla frequenza con cui viene unita e le dimensioni delle righe di join.
-   **Modificare alcune tabelle delle dimensioni in tabelle di riferimento.** Se una tabella della dimensione non può essere colocata con la tabella dei fatti, è possibile migliorare le prestazioni delle query distribuendo le copie della tabella della dimensione in tutti i nodi sotto forma di tabella di riferimento.

Per un esempio di creazione di questo tipo di applicazione, vedere l' [esercitazione dashboard in tempo reale](./tutorial-design-database-hyperscale-realtime.md) .

### <a name="time-series-data"></a>Dati di serie temporali

In un carico di lavoro di serie temporali, le applicazioni eseguono query sulle informazioni recenti mentre archiviano informazioni obsolete.

L'errore più comune nella modellazione delle informazioni della serie temporale in iperscalabilità (CITUS) consiste nell'usare il timestamp stesso come colonna di distribuzione. Una distribuzione hash basata sul tempo distribuisce i tempi apparentemente in modo casuale in partizioni diverse anziché tenere insieme intervalli di tempo nelle partizioni. Le query che coinvolgono il tempo in genere fanno riferimento a intervalli di tempo, ad esempio i dati più recenti. Questo tipo di distribuzione hash comporta un sovraccarico di rete.

#### <a name="best-practices"></a>Procedure consigliate

-   **Non scegliere un timestamp come colonna di distribuzione.** Scegliere una colonna di distribuzione diversa. In un'app multi-tenant usare l'ID tenant o in un'app in tempo reale usare l'ID entità.
-   **Usare invece il partizionamento delle tabelle PostgreSQL per il tempo.** Usare il partizionamento delle tabelle per suddividere una tabella di grandi dimensioni di dati ordinati in più tabelle ereditate con ogni tabella contenente intervalli di tempo diversi. La distribuzione di una tabella partizionata Postgres in iperscala (CITUS) Crea partizioni per le tabelle ereditate.

Per un esempio di creazione di questo tipo di applicazione, vedere l'esercitazione relativa alle [serie temporali](https://aka.ms/hyperscale-tutorial-timeseries) .

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [come la](concepts-hyperscale-colocation.md) condivisione tra i dati distribuiti consente di eseguire rapidamente le query.
