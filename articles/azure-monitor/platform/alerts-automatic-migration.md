---
title: Informazioni sul funzionamento del processo di migrazione automatica per gli avvisi classici di monitoraggio di Azure
description: Informazioni sul funzionamento del processo di migrazione automatica.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: e0ac349554fa580c4ac88b26e76d0bea1ecf738b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932717"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Informazioni sul processo di migrazione automatica per le regole di avviso classiche

Come [annunciato in precedenza](monitoring-classic-retirement.md), gli avvisi classici in monitoraggio di Azure verranno ritirati a settembre 2019 (in origine il 2019 luglio). Nell'ambito del processo di ritiro, [uno strumento di migrazione](alerts-using-migration-tool.md) è disponibile nel portale di Azure per i clienti per attivare la migrazione. Se non è stato usato lo strumento di migrazione entro il 31 agosto 2019, monitoraggio di Azure avvierà il processo di migrazione automatica degli avvisi classici a partire dal 1 ° settembre 2019.
Questo articolo illustra il processo di migrazione automatica e consente di risolvere eventuali problemi che potrebbero verificarsi.

  > [!NOTE]
  > Questo articolo si applica solo al cloud pubblico di Azure. Il processo di ritiro per gli avvisi classici di monitoraggio di Azure nel cloud di Azure per enti pubblici e in Azure Cina 21Vianet verrà annunciato alla data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Cosa accade durante il processo di migrazione automatica?

- A partire dal **1 ° settembre 2019**, i clienti non saranno in grado di creare nuove regole di avviso classiche, ad eccezione di [determinate metriche](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).
  - Per le eccezioni, il cliente può continuare a creare nuove regole di avviso classiche e usare gli avvisi classici fino al 2020 giugno.
- A partire dal **1 ° settembre 2019**, la migrazione degli avvisi classici verrà attivata in batch per tutti i clienti che hanno avvisi classici.
- Come per lo strumento di migrazione volontaria, alcune regole di avviso classiche non migrazione nei verranno lasciate così come sono. Queste regole di avviso classiche continueranno a essere supportate fino al 2020 giugno. Tuttavia, tutte le regole di avviso classiche non valide verranno eliminate perché non sono funzionali.
Eventuali regole di avviso classiche che monitorano le risorse di destinazione eliminate o su [metriche non più supportate](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) sono considerate non valide.
- Una volta avviata la migrazione per la sottoscrizione, a meno che non si verifichino problemi, la migrazione dovrebbe essere completata entro un'ora. I clienti possono monitorare lo stato della migrazione [nel pannello migrazione di monitoraggio di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- I proprietari della sottoscrizione riceveranno un messaggio di posta elettronica al termine della migrazione.
- Se si verificano problemi durante la migrazione, i proprietari della sottoscrizione riceveranno anche un messaggio di posta elettronica che li informa dello stesso. I clienti possono usare il pannello migrazione per visualizzare i dettagli completi del problema.
- Se è necessaria un'azione da parte del cliente, ad esempio la disabilitazione temporanea di un blocco di risorsa o la modifica di un'assegnazione di criteri, i clienti dovranno risolvere eventuali problemi entro il 31 ottobre 2019. Se i problemi non vengono risolti da allora, non è possibile garantire una corretta migrazione degli avvisi classici.

    > [!NOTE]
    > Se non si desidera attendere l'avvio del processo di migrazione automatica, è comunque possibile attivare la migrazione volontariamente utilizzando lo strumento di migrazione.

## <a name="important-things-to-note"></a>Aspetti importanti da notare

Il processo di migrazione converte le regole di avviso classiche in nuove regole di avviso equivalenti e crea gruppi di azioni. In preparazione, tenere presenti i punti seguenti:

- I formati di payload delle notifiche per le nuove regole di avviso sono diversi da quelli delle regole di avviso classiche perché supportano più funzionalità. Se sono presenti app per la logica, manuali operativi o webhook attivati dalla regola di avviso classica, potrebbero smettere di funzionare come previsto al termine della migrazione a causa di differenze nei payload di notifica. [Informazioni su come prepararsi per la migrazione](alerts-prepare-migration.md).

- Non è possibile eseguire la migrazione di alcune regole di avviso classiche utilizzando lo strumento. [Informazioni sulle regole di cui non è possibile eseguire la migrazione e sulle operazioni da eseguire con esse](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Il processo di migrazione non influirà sulla valutazione delle regole di avviso classiche. Continueranno a essere eseguiti e invieranno avvisi fino a quando non verranno migrati e le nuove regole di avviso avranno effetto.

## <a name="what-if-the-automatic-migration-fails"></a>Cosa accade se la migrazione automatica ha esito negativo?

Quando il processo di migrazione automatica ha esito negativo, i proprietari delle sottoscrizioni riceveranno un messaggio di posta elettronica per notificare il problema. È possibile usare il pannello migrazione in monitoraggio di Azure per visualizzare i dettagli completi del problema.

Per informazioni sui problemi che potrebbero verificarsi durante la migrazione, vedere la [Guida alla risoluzione dei](alerts-understand-migration.md#common-problems-and-remedies) problemi.

  > [!NOTE]
  > Se è necessaria un'azione da parte del cliente, ad esempio la disabilitazione temporanea di un blocco di risorsa o la modifica di un'assegnazione di criteri, i clienti dovranno risolvere eventuali problemi entro il 31 ottobre 2019. Se i problemi non vengono risolti da allora, non è possibile garantire una corretta migrazione degli avvisi classici.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la migrazione](alerts-prepare-migration.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)
