---
title: Valutare l'impatto di una nuova definizione di Criteri di Azure
description: Comprendere il processo da seguire quando si introduce una nuova definizione dei criteri nell'ambiente di Azure.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: d9844e8435452b388c934c5969898fe01d23fb47
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684311"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Valutare l'impatto di una nuova definizione di Criteri di Azure

Criteri di Azure è uno strumento potente che consente di gestire le risorse di Azure in base agli standard aziendali e di soddisfare le esigenze di conformità. Quando utenti, processi o pipeline creano o aggiornano le risorse, Criteri di Azure esamina la richiesta. Quando il risultato della definizione dei criteri è [Accoda](./effects.md#deny) o [DeployIfNotExists](./effects.md#deployifnotexists), Criteri di Azure modifica o aggiunge la richiesta. Quando il risultato della definizione dei criteri è [Controlla](./effects.md#audit) o [AuditIfNotExists](./effects.md#auditifnotexists), Criteri di Azure crea una voce del log attività. Quando il risultato della definizione dei criteri è [Nega](./effects.md#deny), Criteri di Azure interrompe la creazione o la modifica della richiesta.

Questi sono i risultati desiderati quando i criteri sono definiti correttamente. È, tuttavia, importante verificare che un nuovo criterio funzioni come previsto prima di consentire la modifica o il blocco dell'attività. La convalida deve garantire che vengano determinate come non conformi solo le risorse desiderate e che nei risultati non vengano incluse erroneamente le risorse non conformi (_falsi positivi_).

L'approccio consigliato per la convalida di una nuova definizione dei criteri consiste nella procedura seguente:

- Definire i criteri in modo preciso
- Controllare le risorse esistenti
- Controllare le richieste di risorse nuove o aggiornate
- Distribuire i criteri alle risorse
- Monitoraggio continuo

## <a name="tightly-define-your-policy"></a>Definire i criteri in modo preciso

È importante comprendere come vengono implementati i criteri di business come definizione dei criteri e la relazione tra le risorse di Azure e gli altri servizi di Azure. Questo passaggio viene eseguito [identificando i requisiti](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando le proprietà delle risorse](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
È, tuttavia, importante andare anche oltre la definizione stretta dei criteri di business. Il criterio dichiara, ad esempio, "Tutte le macchine virtuali devono..."? E per quanto riguarda gli altri servizi di Azure che usano macchine virtuali, ad esempio HDInsight o il servizio Azure Kubernetes? Quando si definisce un criterio, è necessario considerare il modo in cui influisce sulle risorse usate da altri servizi.

Per questo motivo, è importante che le definizioni dei criteri siano quanto più possibile precise e incentrate sulle risorse e le proprietà da valutare.

## <a name="audit-existing-resources"></a>Controllare le risorse esistenti

Prima di cercare di gestire risorse nuove o aggiornate con la nuova definizione dei criteri, è preferibile analizzare come quest'ultima valuta un subset limitato di risorse esistenti, ad esempio un gruppo di risorse di test. Usare la [modalità di imposizione](./assignment-structure.md#enforcement-mode) _Disabilitato_ (DoNotEnforce) nell'assegnazione dei criteri per evitare l'attivazione del [risultato](./effects.md) o la creazione delle voci del log attività.

Questo passaggio consente di valutare i risultati di conformità del nuovo criterio sulle risorse esistenti senza influire sul flusso di lavoro. Verificare che non siano presenti risorse conformi contrassegnate come non conformi (_falsi positivi_) e che tutte le risorse che si prevede siano non conformi siano contrassegnate correttamente.
Dopo che il subset iniziale di risorse viene convalidato come previsto, espandere lentamente la valutazione a tutte le risorse esistenti.

La valutazione delle risorse esistenti in questo modo consente anche di correggere le risorse non conformi prima dell'implementazione completa del nuovo criterio. Questa pulitura può essere eseguita manualmente o tramite un'[attività di correzione](../how-to/remediate-resources.md), se l'effetto della definizione dei criteri è _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Controllare le risorse nuove o aggiornate

Dopo aver verificato che la nuova definizione dei criteri segnali correttamente le risorse esistenti, è il momento di esaminare l'effetto dei criteri quando le risorse vengono create o aggiornate. Se la definizione dei criteri supporta la parametrizzazione del risultato, usare [Controlla](./effects.md#audit). Questa configurazione consente di monitorare la creazione e l'aggiornamento delle risorse per verificare se la nuova definizione dei criteri attiva una voce nel log attività di Azure per una risorsa non conforme senza alcun effetto sulle richieste o sull'attività esistente.

È consigliabile aggiornare e creare nuove risorse corrispondenti alla definizione dei criteri per verificare che il risultato _Controlla_ venga attivato correttamente quando previsto. Prestare attenzione alle richieste di risorse che non devono essere interessate dalla nuova definizione dei criteri che attiva il risultato _Controlla_.
Queste risorse interessate rappresentano un altro esempio di _falsi positivi_ e devono essere corrette nella definizione dei criteri prima dell'implementazione completa.

Nel caso in cui la definizione dei criteri venga modificata in questa fase di test, è consigliabile iniziare il processo di convalida con il controllo delle risorse esistenti. Una modifica alla definizione dei criteri per un _falso positivo_ sulle risorse nuove o aggiornate potrebbe anche avere un impatto sulle risorse esistenti.

## <a name="deploy-your-policy-to-resources"></a>Distribuire i criteri alle risorse

Dopo aver completato la convalida della nuova definizione dei criteri con risorse esistenti e richieste di risorse nuove o aggiornate, si inizia il processo di implementazione dei criteri. È consigliabile creare innanzitutto l'assegnazione dei criteri per la nuova definizione dei criteri a un subset di tutte le risorse, ad esempio un gruppo di risorse. Dopo aver convalidato la distribuzione iniziale, estendere l'ambito dei criteri a livelli sempre più ampi, ad esempio le sottoscrizioni e i gruppi di gestione. Questa espansione viene eseguita rimuovendo l'assegnazione e creandone una nuova negli ambiti di destinazione, fino a quando non viene assegnata all'ambito completo delle risorse destinate a essere interessate dalla nuova definizione dei criteri.

Durante l'implementazione, se si trovano risorse che devono essere esentate dalla nuova definizione dei criteri, risolverle in uno dei modi seguenti:

- Aggiornare la definizione dei criteri in modo che sia più esplicita per ridurre l'effetto imprevisto
- Modificare l'ambito dell'assegnazione dei criteri (rimuovendo l'assegnazione e creandone una nuova)
- Aggiungere il gruppo di risorse all'elenco di esclusione per l'assegnazione dei criteri

Tutte le modifiche apportate all'ambito (livello o esclusioni) devono essere completamente convalidate e comunicate alle organizzazioni di sicurezza e conformità per assicurarsi che la copertura sia completa.

## <a name="monitor-your-policy-and-compliance"></a>Monitorare criteri e conformità

L'implementazione e l'assegnazione della definizione dei criteri non è il passaggio finale. Monitorare costantemente il livello di [conformità](../how-to/get-compliance-data.md) delle risorse nella nuova definizione dei criteri e configurare [avvisi e notifiche di Monitoraggio di Azure](../../../azure-monitor/platform/alerts-overview.md) appropriati in caso di identificazione di dispositivi non conformi. È inoltre consigliabile valutare la definizione dei criteri e le assegnazioni correlate in base a una pianificazione, per verificare che la definizione dei criteri soddisfi le esigenze di conformità e i criteri aziendali. I criteri devono essere rimossi se non sono più necessari. I criteri richiedono anche un aggiornamento periodico, in quanto le risorse di Azure sottostanti si evolvono e aggiungono nuove proprietà e funzionalità.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [struttura delle definizioni dei criteri](./definition-structure.md).
- Informazioni sulla [struttura di assegnazione dei criteri](./assignment-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).