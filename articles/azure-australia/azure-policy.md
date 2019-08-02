---
title: Conformità di sicurezza con i criteri di Azure e i progetti di Azure
description: Garanzia della conformità e dell'applicazione della sicurezza con i criteri di Azure e i progetti di Azure per agenzie governative australiane in relazione all'ASD ISM ed Essential 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571744"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Conformità di sicurezza con i criteri di Azure e i progetti di Azure

Il problema di applicare la governance all'interno dell'ambiente IT, indipendentemente dal fatto che si tratti di un ambiente locale, nativo o ibrido, esiste per tutte le organizzazioni. È necessario disporre di un Framework di governance tecnico affidabile per garantire la conformità dell'ambiente Microsoft Azure ai requisiti di progettazione, normativi e sicurezza.

Per gli enti pubblici australiani, i controlli chiave da considerare quando si valutano i rischi si trovano in [Australian Cyber Security Center (ACSC) Information Security Manual](https://acsc.gov.au/infosec/ism/index.htm) (ISM). La maggior parte dei controlli descritti in dettaglio nell'ISM richiede che l'applicazione di governance tecnica venga gestita e applicata in modo efficace. È importante avere gli strumenti appropriati per valutare e applicare la configurazione negli ambienti.

Microsoft Azure offre due servizi gratuiti per supportare questi problemi, criteri di Azure e progetti di Azure.

## <a name="azure-policy"></a>Criteri di Azure

Criteri di Azure consente di applicare gli elementi tecnici della governance IT di un'organizzazione. Criteri di Azure contiene una raccolta in continua crescita di criteri predefiniti. Ogni criterio impone regole ed effetti sulle risorse di Azure di destinazione.

Una volta assegnati i criteri alle risorse, è possibile valutare la conformità complessiva rispetto a tali criteri e correggerli, se necessario.

Questa libreria di criteri di Azure predefiniti consente a un'organizzazione di applicare rapidamente i tipi di controlli disponibili in ACSC ISM. Esempi di controlli includono:

* Monitoraggio delle macchine virtuali per gli aggiornamenti del sistema mancanti
* Controllo degli account con autorizzazioni elevate per l'autenticazione a più fattori
* Identificazione di database SQL non crittografati
* Monitoraggio dell'uso del controllo degli accessi in base al ruolo di Azure personalizzato
* Limitazione delle aree di Azure in cui è possibile creare le risorse

Se la governance o i controlli normativi non sono soddisfatti da una definizione di criteri di Azure incorporata, è possibile creare e assegnare una definizione personalizzata. Tutte le definizioni di criteri di Azure sono definite in JSON e seguono una [struttura di definizione](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)standard. Le definizioni di criteri di Azure esistenti possono anche essere duplicate e usate per formare la base di una definizione di criteri personalizzata.

L'assegnazione di singoli criteri di Azure alle risorse, soprattutto in ambienti complessi o in ambienti con requisiti normativi severi, può creare un sovraccarico elevato per gli amministratori. Per supportare tali problemi, è possibile raggruppare un set di criteri di Azure per creare un'iniziativa di criteri di Azure. Le iniziative per i criteri vengono usate per combinare i criteri di Azure correlati che, quando vengono applicati insieme come gruppo, costituiscono la base di un obiettivo di sicurezza o conformità specifico. Microsoft aggiunge definizioni predefinite di Azure Policy Initiative, incluse le definizioni progettate per soddisfare requisiti normativi specifici:

![Iniziative per i criteri di conformità alle normative](media/regulatory-initiatives.png)

Tutti i criteri e le iniziative di Azure vengono assegnati a un ambito di assegnazione. Questo ambito viene definito a livello di sottoscrizione di Azure, gruppo di gestione di Azure o gruppo di risorse di Azure. Dopo aver assegnato i criteri di Azure o le iniziative per i criteri richiesti, un'organizzazione sarà in grado di applicare i requisiti di configurazione per tutte le nuove risorse di Azure create.

L'assegnazione di un nuovo criterio o iniziativa di Azure non influirà sulle risorse di Azure esistenti. Criteri di Azure può; Tuttavia, consentire a un'organizzazione di visualizzare la conformità delle risorse di Azure esistenti. Tutte le risorse che sono state identificate come non conformi possono essere corrette a discrezione dell'organizzazione

### <a name="azure-policy-and-initiatives-in-action"></a>Criteri e iniziative di Azure in azione

Le definizioni dei criteri e delle iniziative predefinite di Azure disponibili si trovano nel nodo definizione della sezione criteri della portale di Azure:

![Definizioni predefinite dei criteri di Azure](media/policy-definitions.png)

Utilizzando la libreria di definizioni predefinite, è possibile cercare rapidamente i criteri che soddisfano i requisiti dell'organizzazione, rivedere la definizione dei criteri e assegnare i criteri alle risorse appropriate. Ad esempio, ISM richiede l'autenticazione a più fattori per tutti gli utenti con privilegi e per tutti gli utenti con accesso a repository di dati importanti. In criteri di Azure è possibile cercare "multi-factor authentication" tra le definizioni di criteri di Azure:

![Criteri di autenticazione a più fattori di Azure](media/mfa-policies.png)

Una volta identificati i criteri appropriati, assegnare i criteri all'ambito desiderato. Se non sono presenti criteri predefiniti che soddisfano i requisiti, è possibile duplicare i criteri esistenti e apportare le modifiche desiderate:

![Criteri di Azure esistenti duplicati](media/duplicate-policy.png)

Microsoft fornisce anche una raccolta di esempi di criteri di Azure su [GitHub](https://github.com/Azure/azure-policy) come guida introduttiva per la creazione di criteri personalizzati di Azure. Questi esempi di criteri possono essere copiati direttamente nell'Editor criteri di Azure all'interno del portale di Azure.

Quando si creano le iniziative di criteri di Azure, è possibile ordinare l'elenco delle definizioni dei criteri disponibili, sia predefinite che personalizzate, aggiungendo le definizioni richieste.

Ad esempio, è possibile cercare nell'elenco delle definizioni di criteri di Azure disponibili per tutti i criteri relativi alle macchine virtuali Windows. Quindi, le definizioni di un'iniziativa progettata per applicare le procedure consigliate per la protezione avanzata delle macchine virtuali:

![Elenco di criteri di Azure](media/initiative-definitions.png)

Quando si assegna un criterio o un'iniziativa di criteri di Azure a un ambito di assegnazione, è possibile escludere le risorse di Azure dagli effetti dei criteri escludendo Azure Gruppi di gestione o i gruppi di risorse di Azure.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Valutazione della conformità e dell'applicazione in tempo reale

Le analisi di conformità dei criteri di Azure delle risorse di Azure nell'ambito vengono eseguite quando vengono soddisfatte le condizioni seguenti:

* Quando viene assegnato un criterio di Azure o un'iniziativa di criteri di Azure
* Quando viene modificato l'ambito di un'iniziativa o di un criterio di Azure esistente
* Su richiesta tramite l'API fino a un massimo di 10 analisi all'ora
* Ogni 24 ore, il comportamento predefinito

Un'analisi della conformità dei criteri per una singola risorsa di Azure viene eseguita 15 minuti dopo la modifica apportata alla risorsa.

Una panoramica della conformità dei criteri di Azure delle risorse può essere esaminata all'interno del portale di Azure tramite il dashboard di conformità dei criteri:

![Punteggio di conformità criteri di Azure](media/simple-compliance.png)

La cifra complessiva della percentuale di conformità delle risorse è un'aggregazione della conformità di tutte le risorse distribuite nell'ambito rispetto a tutti i criteri di Azure assegnati. In questo modo è possibile identificare le risorse all'interno di un ambiente che non sono conformi e definire il piano per correggere meglio queste risorse.

Il dashboard di conformità dei criteri include anche la cronologia delle modifiche per ogni risorsa. Se una risorsa viene identificata perché non è più conforme ai criteri assegnati e la correzione automatica non è abilitata, è possibile visualizzare chi ha apportato la modifica, cosa è stato modificato e quando sono state apportate le modifiche a tale risorsa.

## <a name="azure-blueprints"></a>Azure Blueprint

I progetti di Azure estendono le funzionalità dei criteri di Azure combinando gli elementi seguenti:

* Controllo degli accessi in base al ruolo Azure
* Gruppi di risorse di Azure
* [Modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

I progetti consentono la creazione di progetti di ambiente che distribuiscono risorse di Azure da modelli di Gestione risorse, configurano RBAC e impongono e controllano la configurazione assegnando criteri di Azure. I progetti costituiscono un modello di ambiente modificabile e ridistribuibile. Una volta creato, il progetto può essere assegnato a una sottoscrizione di Azure. Una volta assegnate, verranno create tutte le risorse di Azure definite nel progetto e i criteri di Azure applicati. La distribuzione e la configurazione delle risorse definite in un Azure Blueprint possono essere monitorate dalla console di Azure Blueprint nel portale di Azure.

I progetti di Azure modificati devono essere ripubblicati nella portale di Azure. Ogni volta che un progetto viene ripubblicato, il numero di versione del progetto viene incrementato. Il numero di versione consente di determinare quale versione specifica di un progetto è stata distribuita alle sottoscrizioni di Azure di un'organizzazione. Se lo si desidera, la versione attualmente assegnata del progetto può essere aggiornata alla versione più recente.

Le risorse distribuite tramite un Azure Blueprint possono essere configurate con [blocchi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) al momento della distribuzione. I blocchi delle risorse impediscono che le risorse vengano accidentalmente modificate o eliminate.

Microsoft sta sviluppando Azure Blueprint modelli per un'ampia gamma di settori e requisiti normativi. La libreria corrente delle definizioni di Azure Blueprint disponibili può essere visualizzata nella pagina portale di Azure o [progetto di sicurezza e conformità di Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) nel portale di attendibilità del servizio.

### <a name="azure-blueprint-artifacts"></a>Elementi di Azure Blueprint

Per creare una Azure Blueprint, è possibile iniziare con un modello di progetto vuoto oppure usare uno dei progetti di esempio esistenti come punto di partenza. È possibile aggiungere elementi al progetto che verrà configurato come parte della distribuzione:

![Elementi di Azure Blueprint](media/blueprint-artifacts.png)

Questi elementi possono includere il gruppo di risorse e le risorse di Azure e le iniziative criteri e criteri di Azure associate per applicare la configurazione necessaria affinché l'ambiente sia conforme ai requisiti normativi, ad esempio i controlli ISM. per la protezione avanzata dei sistemi.

Ognuno di questi elementi può essere configurato anche con parametri. Questi valori vengono forniti quando il progetto è stato assegnato a una sottoscrizione di Azure e distribuito. I parametri consentono di creare e usare un singolo progetto per distribuire le risorse in ambienti diversi senza dover modificare il progetto sottostante.

Microsoft sta sviluppando Azure PowerShell e i cmdlet dell'interfaccia della riga di comando per creare e gestire i progetti di Azure con l'intenzione che un progetto possa essere gestito e distribuito da un'organizzazione tramite una pipeline CI/CD.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato in che modo è possibile applicare governance e sicurezza con i criteri di Azure e i progetti di Azure. Ora che è stato esposto a un livello elevato, informazioni su come usare ogni servizio in modo più dettagliato:

* [Panoramica di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview)
* [Panoramica sui progetti di Azure](https://azure.microsoft.com/services/blueprints/)
* [Esempi di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Repository degli esempi di criteri di Azure](https://github.com/Azure/azure-policy)
* [Struttura delle definizioni di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Effetti dei criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
