---
title: Eseguire la migrazione delle regole di avviso di Monitoraggio di AzureMigrate Azure Monitor alert rules
description: Informazioni su come usare lo strumento di migrazione volontaria per eseguire la migrazione delle regole di avviso classiche.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114260"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Usare lo strumento di migrazione volontaria per eseguire la migrazione delle regole di avviso classicheUse the voluntary migration tool to migrate your classic alert rules

Come [annunciato in precedenza,](monitoring-classic-retirement.md)gli avvisi classici in Monitoraggio di Azure vengono ritirati a settembre 2019 (originariamente luglio 2019). Uno strumento di migrazione è disponibile nel portale di Azure per i clienti che usano le regole di avviso classiche e che vogliono attivare la migrazione autonomamente. Questo articolo spiega come usare lo strumento di migrazione per eseguire volontariamente la migrazione delle regole di avviso classiche prima dell'inizio della migrazione automatica a settembre 2019.This article explains how to use the migration tool to anodnontoaly your classic alert rules before the automatic migration starts in September 2019.

> [!NOTE]
> A causa del ritardo nell'implementazione dello strumento di migrazione, la data di pensionamento per la migrazione degli avvisi classici è stata [estesa al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data originariamente annunciata del 30 giugno 2019.

## <a name="benefits-of-new-alerts"></a>Vantaggi dei nuovi avvisi

Gli avvisi classici vengono sostituiti da nuovi avvisi unificati in Monitoraggio di Azure.Classic alerts are being replaced by new, unified alerting in Azure Monitor. La nuova piattaforma di avvisi presenta i seguenti vantaggi:

- È possibile avvisare un'ampia gamma di metriche multidimensionali per molti altri servizi di Azure.You can alert on a variety of multidimensional metrics for [many more Azure services](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- I nuovi avvisi di metrica supportano le regole di [avviso per più risorse](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) che riducono notevolmente l'overhead della gestione di molte regole.
- Il meccanismo di notifica unificato, che supporta:
  - [Gruppi](action-groups.md)di azioni, un meccanismo di notifica modulare che funziona con tutti i nuovi tipi di avviso (metrica, log e log attività).
  - Nuovi meccanismi di notifica come SMS, voce e ITSM Connector.
- L'esperienza di [avviso unificato](alerts-overview.md) porta tutti gli avvisi su segnali diversi (metrica, log e log attività) in un'unica posizione.

## <a name="before-you-migrate"></a>Prima di eseguire la migrazione

Il processo di migrazione converte le regole di avviso classiche in regole di avviso nuove e equivalenti e crea gruppi di azioni. In preparazione, tenere presente i seguenti punti:

- Sia il formato del payload di notifica che le API per creare e gestire nuove regole di avviso sono diversi da quelli delle regole di avviso classiche perché supportano più funzionalità. [Informazioni su come prepararsi per la migrazione](alerts-prepare-migration.md).

- Alcune regole di avviso classiche non possono essere migrate utilizzando lo strumento. [Scopri quali regole non possono essere migrate e cosa farne](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Il processo di migrazione non influirà sulla valutazione delle regole di avviso classiche. Continueranno a essere eseguiti e a inviare avvisi fino a quando non vengono migrati e le nuove regole di avviso avranno effetto.

## <a name="how-to-use-the-migration-tool"></a>Come usare lo strumento di migrazione

Per attivare la migrazione delle regole di avviso classiche nel portale di Azure, eseguire la procedura seguente:To trigger the migration of your classic alert rules in the Azure portal, follow these steps:

1. Nel [portale di Azure](https://portal.azure.com)selezionare **Monitora**.

1. Selezionare **Avvisi**e quindi **Gestisci regole di avviso** o Visualizza avvisi **classici**.

1. Selezionare **Esegui migrazione a nuove regole** per passare alla pagina di destinazione della migrazione. Questa pagina mostra un elenco di tutte le sottoscrizioni e il relativo stato di migrazione:This page shows a list of all your subscriptions and their migration status:

    ![migrazione-atterraggio](media/alerts-migration/migration-landing.png "Eseguire la migrazione delle regoleMigrate rules")

    Tutte le sottoscrizioni di cui è possibile eseguire la migrazione utilizzando lo strumento sono contrassegnate come **Pronto per la migrazione.**

    > [!NOTE]
    > Lo strumento di migrazione viene distribuito in fasi a tutte le sottoscrizioni che usano le regole di avviso classiche. Nelle prime fasi dell'implementazione, è possibile che alcune sottoscrizioni vengano contrassegnate come non pronte per la migrazione.

1. Selezionare una o più sottoscrizioni e quindi selezionare **Anteprima migrazione**.

    La pagina risultante mostra i dettagli delle regole di avviso classiche di cui verrà eseguita la migrazione per una sottoscrizione alla volta. È anche possibile selezionare **Scarica i dettagli di migrazione per questa sottoscrizione** per ottenere i dettagli in formato CSV.

    ![anteprima della migrazione](media/alerts-migration/migration-preview.png "Migrazione dell'anteprima")

1. Specificare uno o più indirizzi di posta elettronica per la notifica dello stato della migrazione. Riceverai un messaggio di posta elettronica al termine della migrazione o se è necessaria un'azione da parte dell'utente.

1. Selezionare **Avvia migrazione**. Leggere le informazioni visualizzate nella finestra di dialogo di conferma e verificare che si è pronti per avviare il processo di migrazione.

    > [!IMPORTANT]
    > Dopo aver avviato la migrazione per una sottoscrizione, non sarà possibile modificare o creare regole di avviso classiche per tale sottoscrizione. Questa restrizione garantisce che non vengano perse modifiche alle regole di avviso classiche durante la migrazione alle nuove regole. Anche se non sarà possibile modificare le regole di avviso classiche, continueranno a essere eseguite e a fornire avvisi fino a quando non ne sono state migrate. Al termine della migrazione per la sottoscrizione, non è più possibile usare le regole di avviso classiche.

    ![migrazione-confermare](media/alerts-migration/migration-confirm.png "Confermare l'avvio della migrazione")

1. Al termine della migrazione o se è necessaria un'azione da parte dell'utente, si riceverà un messaggio di posta elettronica agli indirizzi forniti in precedenza. È inoltre possibile controllare periodicamente lo stato nella pagina di destinazione della migrazione nel portale.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Perché la sottoscrizione è indicata come non pronta per la migrazione?

Lo strumento di migrazione viene distribuito ai clienti in fasi. Nelle prime fasi, la maggior parte o tutte le sottoscrizioni potrebbero essere contrassegnate come **Non pronte per**la migrazione. 

Quando una sottoscrizione diventa pronta per la migrazione, il proprietario della sottoscrizione riceverà un messaggio di posta elettronica che indica che lo strumento è disponibile. Tenete d'occhio questo messaggio.

### <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Gli utenti a cui è assegnato il ruolo Collaboratore monitoraggio a livello di sottoscrizione sono in grado di attivare la migrazione. [Ulteriori informazioni sul controllo](alerts-understand-migration.md#who-can-trigger-the-migration)degli accessi in base al ruolo per il processo di migrazione.

### <a name="how-long-will-the-migration-take"></a>Quanto tempo richiederà la migrazione?

La migrazione viene completata per la maggior parte delle sottoscrizioni in meno di un'ora. È possibile tenere traccia dello stato di avanzamento della migrazione nella pagina di destinazione della migrazione. Durante la migrazione, assicurarsi che gli avvisi siano ancora in esecuzione nel sistema di avvisi classico o in quello nuovo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Cosa posso fare se si verifica un problema durante la migrazione?

Per informazioni su problemi che potrebbero verificarsi durante la migrazione, consulta la guida alla [risoluzione dei problemi.](alerts-understand-migration.md#common-problems-and-remedies) Se è necessaria un'azione da parte dell'utente per completare la migrazione, si riceverà una notifica agli indirizzi di posta elettronica forniti durante la configurazione dello strumento.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la migrazione](alerts-prepare-migration.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)
