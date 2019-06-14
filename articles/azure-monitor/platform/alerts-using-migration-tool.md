---
title: Eseguire la migrazione gli avvisi classici in Monitoraggio di Azure usando lo strumento di migrazione volontaria
description: Informazioni su come usare lo strumento di migrazione volontaria in modo per eseguire la migrazione le regole di avviso di classiche.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 00229cca1d7fb238b330ec98cd35d0bb59bc821a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66015630"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Usare lo strumento di migrazione volontaria in modo per eseguire la migrazione delle regole di avviso classiche

Come [annunciate in precedenza](monitoring-classic-retirement.md), gli avvisi classici in Monitoraggio di Azure verranno ritirati nel mese di settembre 2019 (è stato originariamente 2019 luglio). Uno strumento di migrazione è disponibile nel portale di Azure per i clienti che usano le regole di avviso di classiche e che desiderano migrazione si attivano automaticamente. Questo articolo illustra come usare lo strumento di migrazione per migrare volontariamente le regole di avviso classiche prima dell'avvio della migrazione automatica in settembre 2019.

> [!NOTE]
> A causa di un ritardo nell'implementazione dello strumento di migrazione, è stata la data di ritiro per la migrazione di avvisi classici [esteso al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data di originariamente annunciata del 30 giugno 2019.

## <a name="benefits-of-new-alerts"></a>Vantaggi di nuovi avvisi

Gli avvisi classici verranno sostituiti dai nuovi e unificata gli avvisi in Monitoraggio di Azure. Nuova piattaforma avvisi offre i vantaggi seguenti:

- È possibile inviare un avviso su una vasta gamma di metriche multidimensionali per [molti servizi di Azure più](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- La nuova metrica degli avvisi di supporto [regole di avviso a più risorse](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) che ridurre notevolmente il sovraccarico di gestione di molte regole.
- Il meccanismo di notifica unificato, che supporta:
  - [I gruppi di azioni](action-groups.md), un meccanismo di notifica modulare che funziona con tutti i nuovi tipi di avviso (metriche, log e log attività).
  - Nuovi meccanismi di notifica, ad esempio SMS, voce e il connettore ITSM.
- Il [avviso esperienza unificata](alerts-overview.md) porta tutti gli avvisi su segnali diversi (metriche, log e log attività) in un'unica posizione.

## <a name="before-you-migrate"></a>Prima della migrazione

Il processo di migrazione converte le regole di avviso classica per le regole di avviso di nuovo, equivalente e crea i gruppi di azioni. Per prepararsi, tenere presente quanto riportato di seguito:

- Il formato di payload di notifica sia le API per creare e gestire le nuove regole di avviso sono diverse da quelle delle regole di avviso di classico perché supportano più funzionalità. [Informazioni su come preparare la migrazione](alerts-prepare-migration.md).

- Impossibile eseguire la migrazione di alcune regole di avviso classici utilizzando lo strumento. [Informazioni su quali regole non possono essere migrate e che cosa farne](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Il processo di migrazione non influisca sulla valutazione delle regole di avviso di classico. Continueranno a eseguire e inviare avvisi fino a quando la migrazione e le nuove regole di avviso avrà effetto.

## <a name="how-to-use-the-migration-tool"></a>Come usare lo strumento di migrazione

Per attivare la migrazione delle regole di avviso classiche nel portale di Azure, seguire questa procedura:

1. Nelle [portale di Azure](https://portal.azure.com), selezionare **Monitor**.

1. Selezionare **gli avvisi**e quindi selezionare **gestire le regole di avviso** oppure **visualizzare gli avvisi classici**.

1. Selezionare **eseguire la migrazione a nuove regole** per passare alla pagina di destinazione della migrazione. Questa pagina viene visualizzato un elenco di tutte le sottoscrizioni e il relativo stato di migrazione:

    ![destinazione di migrazione](media/alerts-migration/migration-landing.png "eseguire la migrazione delle regole")

    Tutte le sottoscrizioni che possono essere migrate utilizzando lo strumento sono contrassegnate come **pronto per la migrazione**.

    > [!NOTE]
    > Lo strumento di migrazione è l'implementazione in fasi a tutte le sottoscrizioni che utilizzano le regole di avviso di classiche. Nelle fasi iniziali dell'implementazione di, è possibile notare alcune sottoscrizioni contrassegnati come non pronto per la migrazione.

1. Selezionare una o più sottoscrizioni e quindi selezionare **anteprima migrazione**.

    La pagina risultante mostra i dettagli delle regole di avviso classiche che verranno eseguita la migrazione di una sottoscrizione alla volta. È anche possibile selezionare **Scarica i dettagli della migrazione per questa sottoscrizione** per ottenere i dettagli in un formato CSV.

    ![migrazione-preview](media/alerts-migration/migration-preview.png "anteprima migrazione")

1. Specificare uno o più indirizzi di posta elettronica per ricevere una notifica di stato di migrazione. Si riceverà la posta elettronica quando la migrazione è completata o se è necessaria alcuna azione da parte dell'utente.

1. Selezionare **avviare la migrazione**. Leggere le informazioni visualizzate nella finestra di dialogo di conferma e confermare che si è pronti per avviare il processo di migrazione.

    > [!IMPORTANT]
    > Dopo aver avviato la migrazione di una sottoscrizione, sarà possibile modificare o creare regole di avviso classiche per la sottoscrizione. Questa restrizione garantisce che nessuna modifica per le regole di avviso classiche vada persa durante la migrazione alle nuove regole. Anche se sarà possibile modificare le regole di avviso classiche, continueranno comunque a eseguire e per fornire avvisi fino a quando non si è stata eseguita la migrazione. Al termine della migrazione per la sottoscrizione, è non possibile usare più regole di avviso classica.

    ![Verificare la migrazione](media/alerts-migration/migration-confirm.png "conferma avvia la migrazione")

1. Una volta completata la migrazione o se non è richiesta da parte dell'utente azione, si riceverà un messaggio di posta elettronica all'indirizzo di fornito in precedenza. È possibile controllare anche periodicamente lo stato nella pagina di destinazione migrazione nel portale.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Il motivo per cui l'abbonamento è elencato come non pronto per la migrazione?

Lo strumento di migrazione è l'implementazione per i clienti nelle fasi. Nelle prime fasi, la maggior parte o tutte le sottoscrizioni potrebbero essere contrassegnate come **non è pronto per la migrazione**. 

Quando una sottoscrizione diventa pronta per la migrazione, il proprietario della sottoscrizione verrà visualizzato un messaggio di posta elettronica che indica che lo strumento è disponibile. Non perderti per questo messaggio.

### <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Gli utenti che dispongono del ruolo di collaboratore al monitoraggio loro assegnato a livello di sottoscrizione sono in grado di attivare la migrazione. [Altre informazioni su controllo degli accessi in base al ruolo per il processo di migrazione](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Quanto tempo la migrazione richiederà?

Migrazione completata per la maggior parte delle sottoscrizioni in meno di un'ora. È possibile tenere traccia dell'avanzamento della migrazione nella pagina di destinazione della migrazione. Durante la migrazione, è possibile garantire che gli avvisi sono ancora in esecuzione nel sistema di avvisi classici o in quello nuovo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Cosa può fare se si verifica un problema durante la migrazione?

Vedere le [Guida alla risoluzione dei](alerts-understand-migration.md#common-problems-and-remedies) per informazioni sui problemi che possono verificarsi durante la migrazione. Se è necessaria alcuna azione da parte dell'utente per completare la migrazione, riceve una notifica all'indirizzo di posta elettronica specificato quando si configura lo strumento.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la migrazione](alerts-prepare-migration.md)
- [Comprendere il funzionamento dello strumento di migrazione](alerts-understand-migration.md)
