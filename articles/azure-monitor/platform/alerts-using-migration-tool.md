---
title: Eseguire la migrazione di avvisi classici in Monitoraggio di Azure usando lo strumento di migrazione volontaria
description: Informazioni su come utilizzare lo strumento di migrazione volontaria per migrare le regole di avviso di classiche.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346064"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Usare lo strumento di migrazione volontaria in modo per eseguire la migrazione delle regole di avviso classiche

Come [annunciate in precedenza](monitoring-classic-retirement.md), gli avvisi classici in Monitoraggio di Azure verranno ritirati nel luglio 2019. Lo strumento di migrazione per attivare volontariamente la migrazione è disponibile nel portale di Azure e viene implementata per i clienti che usano le regole di avviso di classiche. Questo articolo illustrerà su come usare lo strumento di migrazione per migrare volontariamente le regole di avviso classiche prima dell'avvio della migrazione automatica in luglio 2019.

## <a name="benefits-of-new-alerts"></a>Vantaggi di nuovi avvisi

Gli avvisi classici verranno sostituiti dai nuovi unificato di avviso in Monitoraggio di Azure. Nuova piattaforma avvisi offre i vantaggi seguenti:

- Avviso su una vasta gamma di metriche multidimensionali per [molti altri servizi di Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Nuova metrica degli avvisi di supporto [regole di avviso a più risorse](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) cui ridurre notevolmente il sovraccarico di gestione di molte regole.
- Meccanismo di notifica unificato
  - [I gruppi di azioni](action-groups.md) è un meccanismo di notifica modulare che funziona con tutti i nuovi tipi di avviso (metriche, log e log attività)
  - Inoltre sarà in grado di sfruttare i vantaggi di nuovi meccanismi di notifica, ad esempio SMS, chiamate vocali e connettore ITSM
- Il [avviso esperienza unificata](alerts-overview.md) offre tutti gli avvisi su segnali diversi (attività delle metriche, log e log) in un'unica posizione

## <a name="before-you-migrate"></a>Prima della migrazione

Come parte della migrazione, le regole di avviso di classiche vengono convertite in equivalenti nuove regole di avviso e vengono creati gruppi di azioni.

- Il formato di payload di notifica, nonché le API per creare e gestire le nuove regole di avviso è diverso da quelli delle regole di avviso classiche perché supportano più funzionalità. Scopri [come preparare la migrazione](alerts-prepare-migration.md).

- Non è possibile eseguire la migrazione di alcune regole di avviso di classici utilizzando lo strumento. [Informazioni su quali regole non sono migrabile e informazioni su come eseguirne la migrazione](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Processo di migrazione non influirà sulla valutazione delle regole di avviso di classico. Continueranno a eseguire e inviare avvisi fino a quando non vengono migrati e nuove regole di avviso avvia la valutazione.


## <a name="how-to-use-the-migration-tool"></a>Come usare lo strumento di migrazione

La procedura seguente viene descritto come attivare la migrazione delle regole di avviso classiche nel portale di Azure:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Monitoraggio**.

2. Fare clic su **gli avvisi** quindi fare clic su **gestire le regole di avviso** oppure **visualizzare gli avvisi classici**.

3. Fare clic su **eseguire la migrazione a nuove regole** per passare alla pagina di destinazione della migrazione. Questa pagina mostra un elenco di tutte le sottoscrizioni e lo stato della migrazione per loro.

    ![destinazione di migrazione](media/alerts-migration/migration-landing.png "eseguire la migrazione delle regole")

4. Tutte le sottoscrizioni che possono essere migrate utilizzando lo strumento verranno contrassegnate come **pronto per la migrazione**.

    > [!NOTE]
    > Lo strumento di migrazione è l'implementazione in fasi a tutte le sottoscrizioni che utilizzano le regole di avviso di classiche. Nelle fasi iniziali dell'implementazione, è possibile notare alcune sottoscrizioni come non pronto per la migrazione.

5. Selezionare una o più sottoscrizioni e fare clic su **anteprima migrazione**

6. In questa pagina, è possibile visualizzare i dettagli delle regole di avviso classiche che verranno eseguita la migrazione di una sottoscrizione alla volta. È anche possibile **Scarica i dettagli della migrazione per questa sottoscrizione** in un formato CSV.

    ![migrazione-preview](media/alerts-migration/migration-preview.png "anteprima migrazione")

7. Specificare uno o più **indirizzi di posta elettronica** per ricevere una notifica di stato di migrazione. Microsoft invierà un messaggio di posta elettronica quando viene completata la migrazione o è necessaria un'azione da parte dell'utente.

8. Fare clic su **avviare la migrazione**. Leggere le informazioni visualizzate nella finestra di dialogo di conferma e confermare se si è pronti per avviare il processo di migrazione.

    >[!IMPORTANT]
    > Dopo aver avviato il processo di migrazione per una sottoscrizione, non sarà in grado di creazione/modifica le regole di avviso di classiche della sottoscrizione. Tuttavia, le regole di avviso classica continuerà in esecuzione e fornendo avvisi fino a quando non vengono migrati in. Questo modo si garantisce il livello di fedeltà tra le nuove regole create durante la migrazione e le regole di avviso di classiche. Al termine della migrazione per la sottoscrizione, è possibile non usare più regole di avviso classica.

    ![Verificare la migrazione](media/alerts-migration/migration-confirm.png "conferma avvia la migrazione")

9. Come è completare la migrazione o un'azione da parte dell'utente necessario, si riceverà un messaggio di posta elettronica sugli indirizzi di posta elettronica forniti nel passaggio 8. È possibile controllare anche periodicamente lo stato dalla pagina di destinazione della migrazione nel portale.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Perché sono mio sottoscrizioni indicate come non pronto per la migrazione?**

Lo strumento di migrazione è l'implementazione in fasi successive per tutti i clienti. Nelle prime fasi, la maggior parte o tutte le sottoscrizioni verranno contrassegnate **non è pronto per la migrazione**. Tuttavia, dalla metà di aprile, devono essere pronte per eseguire la migrazione di tutte le sottoscrizioni.

Quando una sottoscrizione diventa pronta per la migrazione, i proprietari delle sottoscrizioni riceverà un messaggio di posta elettronica che informa la disponibilità dello strumento. Tieni d'occhio per la notifica.

### <a name="who-can-trigger-the-migration"></a>**Chi può attivare la migrazione?**

Gli utenti che dispongono del ruolo di collaboratore al monitoraggio loro assegnato a livello di sottoscrizione sarà in grado di attivare la migrazione. Altre informazioni sulle [Role Based Access Control per il processo di migrazione](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**La durata della migrazione saranno necessarie?**

Per la maggior parte delle sottoscrizioni, la migrazione viene completata in genere meno di un'ora. È possibile tenere traccia dell'avanzamento della migrazione dalla pagina di destinazione della migrazione.  Durante questo periodo, per garantire che gli avvisi sono ancora in esecuzione nel sistema avvisi classici o quella nuova.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**Cosa può fare se si verifica un problema durante la migrazione?**

Seguire le [risoluzione dei problemi relativi alla Guida per vedere i passaggi di correzione di eventuali problemi che possono verificarsi durante la migrazione](alerts-understand-migration.md#common-issues-and-remediations). Se è necessaria alcuna azione da parte dell'utente per completare la migrazione, riceverà in indirizzi di posta elettronica fornito durante la migrazione.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la migrazione](alerts-prepare-migration.md)
- [Comprendere il funzionamento dello strumento di migrazione](alerts-understand-migration.md)
