---
title: Scegliere le colonne di distribuzione nel Database di Azure per PostgreSQL con Iperscalabilità (Citus) (anteprima)
description: Ottima scelta per le colonne di distribuzione negli scenari più comuni su scala molto vasta
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65078986"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Scegliere le colonne di distribuzione nel Database di Azure per PostgreSQL con Iperscalabilità (Citus) (anteprima)

La scelta di colonna di distribuzione di ogni tabella **uno dei più importanti** decisioni di modellazione. Con Iperscalabilità archivia le righe nelle partizioni in base al valore della colonna di distribuzione delle righe.

I gruppi scelta corretta correlati i dati negli stessi nodi fisici, rendendo veloce e aggiungendo il supporto per tutte le funzionalità SQL esegue una query. Rende una scelta non corretta, il sistema verrà eseguito lentamente e non supporterà tutte le funzionalità SQL tra i nodi.

In questa sezione offre distribuzione suggerimenti di colonna per i due scenari più comuni su scala molto vasta.

### <a name="multi-tenant-apps"></a>App multi-Tenant

L'architettura multi-tenant Usa un modulo di gerarchica del modello per distribuire le query tra i nodi nel gruppo di server di database.  La parte superiore della gerarchia dei dati è noto come il *tenant ID*e devono essere archiviati in una colonna in ogni tabella.

Con Iperscalabilità controlla le query per visualizzare l'ID tenant si comportano e consente di trovare la partizione di tabella corrispondente. La query viene instradato a un singolo nodo di lavoro che contiene la partizione. L'esecuzione di una query con tutti i dati pertinenti posizionati nello stesso nodo viene definita con più sedi.

Il diagramma seguente illustra la condivisione nel modello di dati multi-tenant. Tale traccia contiene due tabelle, gli account e le campagne, ognuno distribuito da `account_id`. Le caselle in grigio rappresentano le partizioni, ognuna delle quali il colore rappresenta un nodo di lavoro che lo contiene. Verde partizioni vengono archiviate insieme in un nodo di lavoro e blu in un altro. Si noti come una query di join tra gli account e le campagne avrebbe tutti i dati necessari contemporaneamente in un nodo quando si limita a entrambe le tabelle allo stesso account\_id.

![condivisione percorso multi-tenant](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Per applicare questa struttura in uno schema personalizzato, identificare cosa costituisce un tenant nell'applicazione. Le istanze comuni includono aziendale, account, organizzazione o clienti. Il nome della colonna sarà simile a `company_id` o `customer_id`. Esaminare ciascuna delle query e chiedersi: funzionerebbe se includesse altre clausole WHERE per limitare tutte le tabelle coinvolte alle righe con lo stesso ID tenant?
Le query nel modello multi-tenant sono limitate a un tenant, ad esempio le query in vendita o inventario sarebbero essere limitate all'interno di un determinato punto vendita.

#### <a name="best-practices"></a>Procedure consigliate

-   **Tabelle di un tenant più comune distribuite partizione\_colonna id.** Ad esempio, in un'applicazione SaaS in cui i tenant sono aziende, il tenant\_id sarà probabilmente aziendale\_id.
-   **Convertire tabelle di piccole dimensioni tra tenant in tabelle di riferimento.** Quando più tenant condividono una piccola tabella di informazioni, è possibile distribuirlo come una tabella di riferimento.
-   **Limitare filtro esegue una query tutte le applicazioni dal tenant\_id.** Ogni query deve richiedere le informazioni per un tenant alla volta.

Leggere il [esercitazione di multi-tenant](./tutorial-design-database-hyperscale-multi-tenant.md) per un esempio di creazione di questo tipo di applicazione.

### <a name="real-time-apps"></a>App in tempo reale

L'architettura multi-tenant introduce una struttura gerarchica e Usa la condivisione percorso dati per instradare le query per ogni tenant. Al contrario, le architetture in tempo reale variano a seconda delle proprietà di distribuzione specifico dei dati per ottenere l'elaborazione parallela elevata.

Usiamo "ID entità" come un termine per le colonne di distribuzione del modello in tempo reale. Le entità tipiche sono utenti, gli host o dispositivi.

Le query in tempo reale è in genere chiedono per funzioni di aggregazione numeriche raggruppati in base alla data o categoria. Con Iperscalabilità invia queste query per ogni partizione per i risultati parziali e Assembla la risposta finale nel nodo coordinatore. Le query eseguite in modo più veloce quando molti nodi di contribuire con minor e nessun nodo singolo è necessario eseguire una quantità sproporzionata di lavoro.

#### <a name="best-practices"></a>Procedure consigliate

-   **Scegliere una colonna con una cardinalità elevata come colonna di distribuzione.** Per il confronto, un \"stato\" campo in una tabella di ordini con i valori "nuovi", "a pagamento" e "spedito" è una scelta di una riduzione della colonna di distribuzione. Si presuppone che solo i valori di alcuni, che limita il numero di partizioni che possono contenere i dati e il numero di nodi che possa elaborarlo. Tra le colonne con cardinalità elevata, è consigliabile inoltre di scegliere quelli che vengono spesso usate nelle clausole group by o come chiavi di join.
-   **Scegliere una colonna con una distribuzione uniforme.** Se si distribuisce una tabella in una colonna appropriata per alcuni valori comuni, i dati nella tabella tenderanno ad accumularsi in alcune partizioni. I nodi che contengono tali partizioni finirà più operazioni che gli altri nodi.
-   **Distribuire le tabelle dei fatti e delle dimensioni nella relative colonne comuni.**
    La tabella dei fatti può avere solo una chiave di distribuzione. Le tabelle che uniscono in join in un'altra chiave non sarà con percorso condiviso con la tabella dei fatti. Scegliere una dimensione, usare la condivisione percorso basata sulla frequenza con cui viene unita in join e le dimensioni delle righe unita tramite join.
-   **Modificare alcune tabelle delle dimensioni in tabelle di riferimento.** Se una tabella delle dimensioni non può essere con percorso condiviso con la tabella dei fatti, è possibile migliorare le prestazioni delle query distribuendo le copie della tabella della dimensione per tutti i nodi sotto forma di una tabella di riferimento.

Leggere il [esercitazione dashboard in tempo reale](./tutorial-design-database-hyperscale-realtime.md) per un esempio di creazione di questo tipo di applicazione.

### <a name="timeseries-data"></a>Dati delle serie temporali

In un carico di lavoro di serie temporali, applicazioni, eseguire query sulle informazioni recenti e archivia le informazioni obsolete.

L'errore più comune quando modellano le informazioni delle serie temporali in con Iperscalabilità utilizza il timestamp di se stesso come una colonna di distribuzione. Una distribuzione hash in base al tempo distribuirà volte apparentemente in modo casuale in partizioni diverse, anziché mantenere insieme gli intervalli di tempo in partizioni. Le query che comportano tempo in genere fanno riferimento agli intervalli di tempo (ad esempio i dati più recenti), pertanto, tale distribuzione hash potrebbe causare rete sovraccarico.

#### <a name="best-practices"></a>Procedure consigliate

-   **Non si sceglie un timestamp come colonna di distribuzione.** Scegliere una colonna di distribuzione diversi. In un'app multi-tenant, usare l'ID tenant o in un'app in tempo reale, usare l'ID entità.
-   **Usa PostgreSQL partizionamento delle tabelle per volta invece.** Usare il partizionamento delle tabelle per suddividere una tabella di grandi dimensioni dei dati temporale in più tabelle ereditate con ognuno dei quali contiene diversi intervalli di tempo.  Distribuzione di una tabella partizionata Postgres in con Iperscalabilità consente di creare partizioni per le tabelle ereditate.

Leggere il [timeseries esercitazione](https://aka.ms/hyperscale-tutorial-timeseries) per un esempio di creazione di questo tipo di applicazione.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [colocation](concepts-hyperscale-colocation.md) tra le query vengono eseguite rapidamente consente di dati distribuiti
