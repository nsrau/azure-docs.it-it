---
title: Comprendere il funzionamento del processo di migrazione automatica per gli avvisi classici di Monitoraggio di AzureUnderstand how the automatic migration process for your Azure Monitor classic alerts works
description: Scopri come funziona il processo di migrazione automatica.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668248"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Comprendere il processo di migrazione automatica per le regole di avviso classicheUnderstand the automatic migration process for your classic alert rules

Come [annunciato in precedenza,](monitoring-classic-retirement.md)gli avvisi classici in Monitoraggio di Azure vengono ritirati a settembre 2019 (originariamente luglio 2019). Come parte del processo di pensionamento, nel portale di Azure è disponibile [uno strumento](alerts-using-migration-tool.md) di migrazione per i clienti per attivare la migrazione. Se non è stato usato lo strumento di migrazione entro il 31 agosto 2019, Monitoraggio di Azure avvierà il processo di migrazione automatica degli avvisi classici a partire dal 1 settembre 2019.If you haven't used the migration tool by August 31, 2019, Azure Monitor will start the process of automatic migration of your classic alerts starting September 1, 2019.
In questo articolo viene illustrato il processo di migrazione automatica e vengono risolti i problemi riscontrati.

  > [!NOTE]
  > Questo articolo si applica solo al cloud pubblico di Azure.This article only applies to Azure public cloud. Il processo di pensionamento per gli avvisi classici di Monitoraggio di Azure nel cloud di Azure per enti pubblici e Azure China 21Vianet verrà annunciato in una data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Cosa accadrà durante il processo di migrazione automatica?

- A partire dal **1 settembre 2019,** i clienti non saranno in grado di creare nuove regole di avviso classiche se non su [alcune metriche.](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)
  - Per le eccezioni, il cliente può continuare a creare nuove regole di avviso classiche e utilizzare gli avvisi classici fino a giugno 2020.
- A partire dal **1 settembre 2019,** la migrazione degli avvisi classici verrà attivata in batch per tutti i clienti con avvisi classici.
- Come con lo strumento di migrazione volontaria, alcune regole di avviso classiche che non sono migrabili verranno lasciate così come sono. Queste regole di avviso classiche continueranno a essere supportate fino a giugno 2020. Tuttavia, tutte le regole di avviso classiche non valide verranno eliminate in quanto non sono funzionali.
Tutte le regole di avviso classiche che monitorano le risorse di destinazione eliminate o le [metriche non più supportate](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) vengono considerate non valide.
- Una volta avviata la migrazione per la sottoscrizione, a meno che non si siano verificati problemi, la migrazione deve essere completata entro un'ora. I clienti possono monitorare lo stato della migrazione [nel pannello di migrazione in Monitoraggio di Azure.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)
- I proprietari delle sottoscrizioni riceveranno un messaggio di posta elettronica al termine della migrazione.
- In caso di problemi durante la migrazione, anche i proprietari della sottoscrizione riceveranno un messaggio di posta elettronica che li informa dello stesso. I clienti possono usare il pannello di migrazione per visualizzare i dettagli completi del problema.
- Nel caso in cui sia necessaria un'azione da parte del cliente, ad esempio la disattivazione temporanea di un blocco delle risorse o la modifica di un'assegnazione dei criteri, i clienti dovranno risolvere eventuali problemi entro il 31 ottobre 2019. Se i problemi non vengono risolti entro tale data, non è possibile garantire la corretta migrazione degli avvisi classici.

    > [!NOTE]
    > Se non si desidera attendere l'avvio del processo di migrazione automatica, è comunque possibile attivare la migrazione volontariamente utilizzando lo strumento di migrazione.

## <a name="important-things-to-note"></a>Cose importanti da notare

Il processo di migrazione converte le regole di avviso classiche in regole di avviso nuove e equivalenti e crea gruppi di azioni. In preparazione, tenere presente i seguenti punti:

- I formati di payload delle notifiche per le nuove regole di avviso sono diversi da quelli delle regole di avviso classiche perché supportano più funzionalità. Se si dispone di app per la logica, runbook o webhook attivati dalla regola di avviso classica, potrebbero smettere di funzionare come previsto una volta completata la migrazione a causa delle differenze nei payload delle notifiche. [Informazioni su come prepararsi per la migrazione](alerts-prepare-migration.md).

- Non è possibile eseguire la migrazione di alcune regole di avviso classiche utilizzando lo strumento. [Scopri quali regole non possono essere migrate e cosa fare con esse](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Il processo di migrazione non influirà sulla valutazione delle regole di avviso classiche. Continueranno a essere eseguiti e a inviare avvisi fino a quando non vengono migrati e le nuove regole di avviso avranno effetto.

## <a name="what-if-the-automatic-migration-fails"></a>Cosa succede se la migrazione automatica non riesce?

Quando il processo di migrazione automatica non riesce, i proprietari della sottoscrizione riceveranno un messaggio di posta elettronica che li informa del problema. È possibile usare il pannello di migrazione in Monitoraggio di Azure per visualizzare i dettagli completi del problema.

Per informazioni su problemi che potrebbero verificarsi durante la migrazione, consulta la guida alla [risoluzione dei problemi.](alerts-understand-migration.md#common-problems-and-remedies)

  > [!NOTE]
  > Nel caso in cui sia necessaria un'azione da parte del cliente, ad esempio la disattivazione temporanea di un blocco delle risorse o la modifica di un'assegnazione dei criteri, i clienti dovranno risolvere eventuali problemi entro il 31 ottobre 2019. Se i problemi non vengono risolti entro tale data, non è possibile garantire la corretta migrazione degli avvisi classici.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la migrazione](alerts-prepare-migration.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)
