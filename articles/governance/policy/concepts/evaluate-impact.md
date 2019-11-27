---
title: Valutare l'effetto di un nuovo criterio di Azure
description: Comprendere il processo da seguire quando si introduce una nuova definizione dei criteri nell'ambiente Azure.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463517"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Valutare l'effetto di un nuovo criterio di Azure

Criteri di Azure è uno strumento potente per gestire le risorse di Azure in base agli standard aziendali e soddisfare le esigenze di conformità. Quando persone, processi o pipeline creano o aggiornano risorse, i criteri di Azure esaminano la richiesta. Quando l'effetto della definizione dei criteri è [Append](./effects.md#deny) o [DeployIfNotExists](./effects.md#deployifnotexists), Policy modifica la richiesta o la aggiunge. Quando l'effetto della definizione dei criteri è [Audit](./effects.md#audit) o [AuditIfNotExists](./effects.md#auditifnotexists), Policy causa la creazione di una voce del log attività. Quando l'effetto della definizione dei criteri è [Deny](./effects.md#deny), il criterio interrompe la creazione o l'alterazione della richiesta.

Questi risultati sono esattamente quelli desiderati quando si è certi che i criteri siano definiti correttamente. Tuttavia, è importante convalidare un nuovo criterio funziona come previsto prima di consentire la modifica o bloccare il lavoro. La convalida deve garantire che vengano determinate solo le risorse desiderate come non conformi e che nel risultato non sia inclusa erroneamente alcuna risorsa conforme (nota come _falso positivo_).

L'approccio consigliato per la convalida di una nuova definizione dei criteri consiste nella procedura seguente:

- Definire strettamente i criteri
- Controllare le risorse esistenti
- Controlla richieste di risorse nuove o aggiornate
- Distribuire i criteri alle risorse
- Monitoraggio continuo

## <a name="tightly-define-your-policy"></a>Definire strettamente i criteri

È importante comprendere come vengono implementati i criteri di business come definizione dei criteri e la relazione delle risorse di Azure con altri servizi di Azure. Questo passaggio viene eseguito [identificando i requisiti](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando le proprietà delle risorse](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Tuttavia, è anche importante vedere oltre la definizione stretta dei criteri di business. Lo stato del criterio, ad esempio "tutte le macchine virtuali deve..."? Quali sono gli altri servizi di Azure che usano macchine virtuali, ad esempio HDInsight o AKS? Quando si definiscono i criteri, è necessario considerare il modo in cui questo criterio influisca sulle risorse utilizzate da altri servizi.

Per questo motivo, le definizioni dei criteri devono essere definite strettamente e incentrate sulle risorse e sulle proprietà necessarie per valutare la conformità.

## <a name="audit-existing-resources"></a>Controllare le risorse esistenti

Prima di cercare di gestire risorse nuove o aggiornate con la nuova definizione dei criteri, è consigliabile vedere come valuta un subset limitato di risorse esistenti, ad esempio un gruppo di risorse di test. Utilizzare la [modalità di imposizione](./assignment-structure.md#enforcement-mode)
_disabilitato_ (DoNotEnforce) nell' [assegnazione dei criteri](./effects.md) per impedire che venga creato il trigger o le voci del log attività.

Questo passaggio consente di valutare i risultati di conformità del nuovo criterio sulle risorse esistenti senza influire sul flusso di lavoro. Verificare che non siano presenti risorse conformi contrassegnate come non conformi (_falso positivo_) e che tutte le risorse che si prevede siano non conformi siano contrassegnate correttamente.
Dopo che il subset iniziale di risorse è stato convalidato come previsto, espandere lentamente la valutazione per tutte le risorse esistenti.

La valutazione delle risorse esistenti in questo modo consente anche di monitorare e aggiornare le risorse non conformi prima dell'implementazione completa del nuovo criterio. Questa pulitura può essere eseguita manualmente o tramite un' [attività di monitoraggio e aggiornamento](../how-to/remediate-resources.md) se l'effetto della definizione dei criteri è _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Controllare le risorse nuove o aggiornate

Una volta convalidata la nuova definizione dei criteri, la creazione di report avviene correttamente per le risorse esistenti, è il momento di esaminare l'effetto del criterio quando le risorse vengono create o aggiornate. Se la definizione dei criteri supporta la parametrizzazione dell'effetto, utilizzare [Audit](./effects.md#audit). Questa configurazione consente di monitorare la creazione e l'aggiornamento delle risorse per verificare se la nuova definizione dei criteri attiva una voce nel log attività di Azure per una risorsa non conforme senza alcun effetto sulle richieste o sul lavoro esistente.

È consigliabile aggiornare e creare nuove risorse corrispondenti alla definizione dei criteri per verificare che l'effetto di _controllo_ venga attivato correttamente quando previsto. Trovarsi in Lookout per le richieste di risorse che non devono essere interessate dalla nuova definizione dei criteri che attiva l'effetto di _controllo_ .
Queste risorse interessate sono un altro esempio di _falsi positivi_ e devono essere corrette nella definizione dei criteri prima dell'implementazione completa.

Nel caso in cui la definizione dei criteri venga modificata in questa fase di test, è consigliabile iniziare il processo di convalida con il controllo delle risorse esistenti. Una modifica alla definizione dei criteri per un _falso positivo_ sulle risorse nuove o aggiornate potrebbe avere anche un impatto sulle risorse esistenti.

## <a name="deploy-your-policy-to-resources"></a>Distribuire i criteri alle risorse

Dopo aver completato la convalida della nuova definizione dei criteri con risorse esistenti e richieste di risorse nuove o aggiornate, si inizia il processo di implementazione del criterio. È consigliabile creare l'assegnazione dei criteri per la nuova definizione dei criteri a un subset di tutte le risorse, ad esempio un gruppo di risorse. Dopo aver convalidato la distribuzione iniziale, estendere l'ambito dei criteri a livelli più ampi e più ampi, ad esempio le sottoscrizioni e i gruppi di gestione. Questa espansione viene eseguita rimuovendo l'assegnazione e creandone una nuova negli ambiti di destinazione fino a quando non viene assegnata all'ambito completo delle risorse destinate a essere coperte dalla nuova definizione dei criteri.

Durante l'implementazione, se si trovano risorse che devono essere esentate dalla nuova definizione dei criteri, risolverle in uno dei modi seguenti:

- Aggiornare la definizione dei criteri in modo che sia più esplicita per ridurre l'effetto imprevisto
- Modificare l'ambito dell'assegnazione di criteri (rimuovendo e creando una nuova assegnazione)
- Aggiungere il gruppo di risorse all'elenco di esclusione per l'assegnazione dei criteri

Tutte le modifiche apportate all'ambito (livello o esclusioni) devono essere completamente convalidate e comunicate con le organizzazioni di sicurezza e conformità per assicurarsi che non vi siano gap nel code coverage.

## <a name="monitor-your-policy-and-compliance"></a>Monitorare i criteri e la conformità

L'implementazione e l'assegnazione della definizione dei criteri non è il passaggio finale. Monitora costantemente il livello di [conformità](../how-to/get-compliance-data.md) delle risorse alla nuova definizione dei criteri e imposta [avvisi e notifiche di monitoraggio di Azure](../../../azure-monitor/platform/alerts-overview.md) appropriati per l'identificazione di dispositivi non conformi. È inoltre consigliabile valutare la definizione dei criteri e le assegnazioni correlate in base a una pianificazione per convalidare che la definizione dei criteri soddisfi le esigenze di conformità e i criteri aziendali. I criteri devono essere rimossi se non sono più necessari. Anche i criteri richiedono l'aggiornamento di tanto in tanto quando le risorse di Azure sottostanti si evolvono e aggiungono nuove proprietà e funzionalità.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [struttura della definizione dei criteri](./definition-structure.md).
- Informazioni sulla [struttura di assegnazione dei criteri](./assignment-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/get-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).