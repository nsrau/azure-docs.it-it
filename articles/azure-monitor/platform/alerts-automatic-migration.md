---
title: Informazioni sul funzionamento del processo di migrazione automatica per gli avvisi classici di monitoraggio di Azure
description: Informazioni sul funzionamento del processo di migrazione automatica.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 6a2d032c6aa33b72fe422638df45ca48bf8b1036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87847283"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Informazioni sul processo di migrazione automatica per le regole di avviso classiche

Come [annunciato in precedenza](monitoring-classic-retirement.md), gli avvisi classici in monitoraggio di Azure vengono ritirati, anche se ancora in uso limitato per le risorse che non supportano ancora i nuovi avvisi. Nell'ambito del processo di ritiro, [uno strumento di migrazione](alerts-using-migration-tool.md) è disponibile nel portale di Azure per i clienti per attivare la migrazione.
Questo articolo illustra il processo di migrazione automatica e consente di risolvere eventuali problemi che potrebbero verificarsi.

  > [!NOTE]
  > Questo articolo si applica solo al cloud pubblico di Azure. Il processo di ritiro per gli avvisi classici di monitoraggio di Azure nel cloud di Azure per enti pubblici e in Azure Cina 21Vianet verrà annunciato alla data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Cosa accade durante il processo di migrazione automatica?

- A partire dal **1 ° settembre 2019**, i clienti non saranno in grado di creare nuove regole di avviso classiche, ad eccezione di [determinate metriche](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).
- Per le eccezioni, il cliente può continuare a creare nuove regole di avviso classiche e usare gli avvisi classici fino a un altro annuncio.
- A partire dal **1 ° settembre 2019**, la migrazione degli avvisi classici verrà attivata in batch per tutti i clienti che hanno avvisi classici.
- Come per lo strumento di migrazione volontaria, alcune regole di avviso classiche non migrazione nei verranno lasciate così come sono. Queste regole di avviso classiche continueranno a essere supportate fino a un ulteriore annuncio. Tuttavia, tutte le regole di avviso classiche non valide verranno eliminate perché non sono funzionali.
Eventuali regole di avviso classiche che monitorano le risorse di destinazione eliminate o su [metriche non più supportate](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) sono considerate non valide.
- Una volta avviata la migrazione per la sottoscrizione, a meno che non si verifichino problemi, la migrazione dovrebbe essere completata entro un'ora. I clienti possono monitorare lo stato della migrazione [nel pannello migrazione di monitoraggio di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- I proprietari della sottoscrizione riceveranno un messaggio di posta elettronica al termine della migrazione.
- Se si verificano problemi durante la migrazione, i proprietari della sottoscrizione riceveranno anche un messaggio di posta elettronica che li informa dello stesso. I clienti possono usare il pannello migrazione per visualizzare i dettagli completi del problema.
- Nel caso in cui sia necessaria un'azione da clienti, ad esempio la disabilitazione temporanea di un blocco di risorsa o la modifica di un'assegnazione di criteri, i clienti dovranno risolvere tali problemi. Se i problemi non vengono risolti da allora, non è possibile garantire una corretta migrazione degli avvisi classici.

    > [!NOTE]
    > Se non si desidera attendere l'avvio del processo di migrazione automatica, è comunque possibile attivare la migrazione volontariamente utilizzando lo strumento di migrazione.

## <a name="important-things-to-note"></a>Aspetti importanti da notare

Il processo di migrazione converte le regole di avviso classiche in nuove regole di avviso equivalenti e crea gruppi di azioni. In preparazione, tenere presenti i punti seguenti:

- I formati di payload delle notifiche per le nuove regole di avviso sono diversi da quelli delle regole di avviso classiche perché supportano più funzionalità. Se sono presenti app per la logica, manuali operativi o webhook attivati dalla regola di avviso classica, potrebbero smettere di funzionare come previsto al termine della migrazione a causa di differenze nei payload di notifica. [Informazioni su come prepararsi per la migrazione](alerts-prepare-migration.md).

- Non è possibile eseguire la migrazione di alcune regole di avviso classiche utilizzando lo strumento. [Informazioni sulle regole di cui non è possibile eseguire la migrazione e sulle operazioni da eseguire con esse](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Il processo di migrazione non influirà sulla valutazione delle regole di avviso classiche. Continueranno a essere eseguiti e invieranno avvisi fino a quando non verranno migrati e le nuove regole di avviso avranno effetto.

## <a name="what-if-the-automatic-migration-fails"></a>Cosa accade se la migrazione automatica ha esito negativo?

Quando il processo di migrazione automatica ha esito negativo, i proprietari delle sottoscrizioni riceveranno un messaggio di posta elettronica per notificare il problema. È possibile usare il pannello migrazione in monitoraggio di Azure per visualizzare i dettagli completi del problema.

Per informazioni sui problemi che potrebbero verificarsi durante la migrazione, vedere la [Guida alla risoluzione dei](alerts-understand-migration.md#common-problems-and-remedies) problemi.

  > [!NOTE]
  > Nel caso in cui sia necessaria un'azione da clienti, ad esempio la disabilitazione temporanea di un blocco di risorsa o la modifica di un'assegnazione di criteri, i clienti dovranno risolvere tali problemi. Se i problemi non vengono risolti da allora, non è possibile garantire una corretta migrazione degli avvisi classici.

## <a name="next-steps"></a>Passaggi successivi

- [Prepararsi per la migrazione](alerts-prepare-migration.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)
