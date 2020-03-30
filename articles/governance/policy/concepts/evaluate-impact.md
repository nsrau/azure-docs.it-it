---
title: Valutare l'impatto di un nuovo criterio di AzureEvaluate the impact of a new Azure policy
description: Comprendere il processo da seguire quando si introduce una nuova definizione dei criteri nell'ambiente Azure.Understand the process to follow when introducing a new policy definition into your Azure environment.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463517"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Valutare l'impatto di un nuovo criterio di AzureEvaluate the impact of a new Azure policy

Criteri di Azure è uno strumento potente per la gestione delle risorse di Azure in base agli standard aziendali e per soddisfare le esigenze di conformità. Quando persone, processi o pipeline creano o aggiornano risorse, Criteri di Azure esamina la richiesta. Quando l'effetto di definizione dei criteri è [Append](./effects.md#deny) o [DeployIfNotExists](./effects.md#deployifnotexists), Policy modifica la richiesta o vi aggiunge. Quando l'effetto di definizione dei criteri è [Audit](./effects.md#audit) o [AuditIfNotExists](./effects.md#auditifnotexists), Policy determina la creazione di una voce del log attività. E quando l'effetto di definizione dei criteri è [Nega](./effects.md#deny), Criteri interrompe la creazione o l'alterazione della richiesta.

Questi risultati sono esattamente quelli desiderati quando si sa che il criterio è definito correttamente. Tuttavia, è importante convalidare un nuovo criterio funziona come previsto prima di consentire la modifica o il blocco del lavoro. La convalida deve garantire che solo le risorse previste siano non conformi e che non siano incluse in modo non corretto (noto come _falso positivo)_ nei risultati.

L'approccio consigliato per la convalida di una nuova definizione di criteri consiste nel seguire questi passaggi:The recommended approach to validating a new policy definition is by following these steps:

- Definire strettamente i criteri
- Controllare le risorse esistenti
- Controllare le richieste di risorse nuove o aggiornateAudit new or updated resource requests
- Distribuire i criteri nelle risorseDeploy your policy to resources
- Monitoraggio continuo

## <a name="tightly-define-your-policy"></a>Definire strettamente i criteri

È importante comprendere come vengono implementati i criteri di business come definizione dei criteri e la relazione delle risorse di Azure con altri servizi di Azure.It's important to understand how the business policy is implemented as a policy definition and the relationship of Azure resources with other Azure services. Questo passaggio viene eseguito [identificando i requisiti](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando le proprietà delle risorse.](../tutorials/create-custom-policy-definition.md#determine-resource-properties)
Ma è anche importante vedere oltre la definizione ristretta della politica aziendale. Lo stato dei criteri, ad esempio "Tutte le macchine virtuali,..."? Che dire di altri servizi di Azure che usano macchine virtuali, ad esempio HDInsight o AKS? Quando si definisce una politica, è necessario considerare l'impatto di questa politica sulle risorse utilizzate da altri servizi.

Per questo motivo, le definizioni dei criteri devono essere più strettamente definite e incentrate sulle risorse e sulle proprietà che è necessario valutare la conformità il più possibile.

## <a name="audit-existing-resources"></a>Controllare le risorse esistenti

Prima di cercare di gestire le risorse nuove o aggiornate con la nuova definizione dei criteri, è consigliabile vedere come valuta un sottoinsieme limitato di risorse esistenti, ad esempio un gruppo di risorse di test. Utilizzare
la modalità di [imposizione](./assignment-structure.md#enforcement-mode)_Disabled_ (DoNotEnforce) nell'assegnazione dei criteri per impedire l'attivazione dell'effetto o la creazione di voci del log attività. [effect](./effects.md)

Questo passaggio offre la possibilità di valutare i risultati di conformità dei nuovi criteri sulle risorse esistenti senza influire sul flusso di lavoro. Verificare che nessuna risorsa conforme sia contrassegnata come non conforme (_false positive_) e che tutte le risorse che si prevede non siano conformi siano contrassegnate correttamente.
Dopo che il sottoinsieme iniziale di risorse viene convalidato come previsto, espandere lentamente la valutazione a tutte le risorse esistenti.

La valutazione delle risorse esistenti in questo modo offre anche l'opportunità di correggere le risorse non conformi prima della piena attuazione della nuova politica. Questa pulizia può essere eseguita manualmente o tramite [un'attività](../how-to/remediate-resources.md) di correzione se l'effetto di definizione dei criteri è _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Controllare le risorse nuove o aggiornate

Dopo aver convalidato la nuova definizione di criteri, è necessario creare correttamente la creazione di report sulle risorse esistenti, è necessario esaminare l'impatto dei criteri quando le risorse vengono create o aggiornate. Se la definizione dei criteri supporta la parametrizzazione degli effetti, utilizzare [Audit](./effects.md#audit). Questa configurazione consente di monitorare la creazione e l'aggiornamento delle risorse per verificare se la nuova definizione dei criteri attiva una voce nel log attività di Azure per una risorsa non conforme senza influire sul lavoro o sulle richieste esistenti.

È consigliabile aggiornare e creare nuove risorse che corrispondono alla definizione dei criteri per verificare che l'effetto _di controllo_ venga attivato correttamente quando previsto. Prestare attenzione alle richieste di risorse che non dovrebbero essere influenzate dalla nuova definizione dei criteri che attivano l'effetto _di controllo._
Queste risorse interessate sono un altro esempio di _falsi positivi_ e devono essere fissate nella definizione dei criteri prima della piena implementazione.

Nel caso in cui la definizione dei criteri venga modificata in questa fase del test, è consigliabile avviare il processo di convalida con il controllo delle risorse esistenti. Una modifica alla definizione dei criteri per un _falso positivo_ sulle risorse nuove o aggiornate potrebbe avere un impatto anche sulle risorse esistenti.

## <a name="deploy-your-policy-to-resources"></a>Distribuire i criteri nelle risorseDeploy your policy to resources

Dopo aver completato la convalida della nuova definizione di criteri con risorse esistenti e richieste di risorse nuove o aggiornate, iniziare il processo di implementazione dei criteri. È consigliabile creare prima l'assegnazione dei criteri per la nuova definizione di criterio a un sottoinsieme di tutte le risorse, ad esempio un gruppo di risorse. Dopo aver convalidato la distribuzione iniziale, estendere l'ambito della politica a livelli più ampi e più ampi, ad esempio sottoscrizioni e gruppi di gestione. Questa espansione si ottiene rimuovendo l'assegnazione e creandone una nuova negli ambiti di destinazione fino a quando non viene assegnata all'intero ambito delle risorse destinate a essere coperte dalla nuova definizione dei criteri.

Durante l'implementazione, se vengono individuate risorse che devono essere esenti dalla nuova definizione di criteri, affrontarle in uno dei modi seguenti:

- Aggiornare la definizione dei criteri in modo che sia più esplicita per ridurre l'impatto indesiderato
- Modificare l'ambito dell'assegnazione dei criteri (rimuovendo e creando una nuova assegnazione)
- Aggiungere il gruppo di risorse all'elenco di esclusione per l'assegnazione dei criteri

Eventuali modifiche all'ambito (livello o esclusioni) devono essere completamente convalidate e comunicate con le organizzazioni di sicurezza e conformità per garantire che non vi siano lacune nella copertura.

## <a name="monitor-your-policy-and-compliance"></a>Monitorare i criteri e la conformità

L'implementazione e l'assegnazione della definizione dei criteri non è il passaggio finale. Monitorare continuamente il livello di [conformità](../how-to/get-compliance-data.md) delle risorse per la nuova definizione dei criteri e configurare [gli avvisi e le notifiche](../../../azure-monitor/platform/alerts-overview.md) di Monitoraggio di Azure appropriati per quando vengono identificati i dispositivi non conformi. È inoltre consigliabile valutare la definizione dei criteri e le assegnazioni correlate su base pianificata per verificare che la definizione dei criteri riscini alle esigenze di conformità e criteri aziendali. I criteri devono essere rimossi se non sono più necessari. I criteri devono inoltre essere aggiornati di tanto in tanto man mano che le risorse di Azure sottostanti si evolvono e aggiungono nuove proprietà e funzionalità.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla struttura di [definizione dei criteri](./definition-structure.md).
- Informazioni sulla struttura delle [assegnazioni dei criteri](./assignment-structure.md).
- Comprendere come creare criteri a livello di [codice.](../how-to/programmatically-create.md)
- Scopri come ottenere i dati di [conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](../how-to/remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).