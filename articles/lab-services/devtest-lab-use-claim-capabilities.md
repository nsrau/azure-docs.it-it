---
title: Usare le funzionalità di attestazione in Azure DevTest Labs | Microsoft Docs
description: Informazioni sui diversi scenari per l'uso delle funzionalità Claim/Unclaim di Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861430"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Usare le funzionalità di attestazione in Azure DevTest Labs
Il servizio Azure DevTest Labs migliora l'efficacia e l'efficienza di sviluppatori e tester. Questo articolo è incentrato sulla possibilità di richiedere o annullare la richiesta di macchine virtuali in Azure DevTest Labs. Vengono inoltre elencati i vari modi in cui questa funzionalità migliora l'esperienza utente. Prima di esaminare i diversi scenari in cui è possibile usare questa funzionalità, è opportuno esaminare la **richiesta** e il suo funzionamento.

## <a name="claimable-machines"></a>Computer attestabili
Un computer attestabile è una macchina virtuale (VM) creata in un lab senza proprietario. Una volta richiesta la richiesta, l'utente dispone di una gamma completa di opzioni per la macchina virtuale. Quando un utente dichiara un computer, vengono apportate alcune modifiche. La VM viene spostata dall'elenco di **macchine virtuali** che è possibile richiedere all'elenco **macchine virtuali personali** nell'portale di Azure. 

L'utente può connettersi alla VM, personalizzare gli artefatti, riavviare, arrestare o annullare la richiesta del computer. Esistono due modi per rendere una macchina virtuale richiedibile:

- Creare un computer e non richiederlo in modo che venga spostato nel pool dichiarabile. 
- Creare una macchina virtuale e posizionarla nel pool condiviso usando [le impostazioni avanzate](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Esistono due casi in cui le funzionalità di attestazione/annullamento delle attestazioni possono essere utilizzate in modo efficiente. Il primo caso richiede una maggiore prevedibilità e una pianificazione, da progettare ed eseguire correttamente. E la seconda è più situazione. Di seguito sono riportati alcuni esempi dei diversi casi.

## <a name="designed-use-of-claimable-machines"></a>Uso progettato dei computer attestabili

- **Sviluppo/test del software:** Consentire agli sviluppatori o ai tester di essere più produttivi con la configurazione dei computer pronti e in uno stato non recuperato. Disporre di un set di macchine virtuali con configurazioni diverse, strumenti necessari e con il codice più recente consente agli utenti di richiedere una macchina virtuale e iniziare il lavoro senza dover dedicare tempo alla configurazione di un computer. Prima che le macchine virtuali vengano richieste, viene effettuato il provisioning dei computer, ma la riduzione del costo dei computer usati meno spesso. Quando le macchine virtuali sono necessarie, un utente dichiara semplicemente la VM, che avvia la macchina. L'opzione Unclaim non è utile in questo caso poiché la creazione di una nuova VM è spesso più semplice e conveniente.
- **Aule/laboratori:** Avere macchine virtuali preconfigurate per una classe o un Lab in modo che gli studenti possano connettersi immediatamente a un computer usando il portale di Azure.  Quando uno studente dichiara una macchina virtuale, il Lab garantisce che nessuno possa richiedere lo stesso computer. L'automazione di questo processo garantisce che sia disponibile il numero necessario di computer con l'ambiente specificato. Se gli studenti non vengono visualizzati o vengono eseguiti in ritardo, i computer non ritenuti possono essere mantenuti disponibili fino a quando la sessione non supera il costo minimo. L'opzione Unclaim non è valida in questo scenario perché la macchina virtuale è in uno stato sconosciuto quando viene eseguito l'utente precedente.
- **Dimostrazioni** Usare i computer per le dimostrazioni, in cui i computer nel Lab sono configurati con ambienti specifici. Questa funzionalità è utile quando più persone possono fornire una dimostrazione nello stesso momento o in momenti casuali, ad esempio in una conferenza. L'opzione Unclaim può essere utile in questa situazione perché la demo non deve modificare lo stato del computer, consentendo agli utenti di restituire una macchina virtuale al pool richiedabile per la dimostrazione successiva. Con il deprovisioning del computer non autorizzato e il costo minimo, le macchine virtuali possono essere lasciate nel Lab per periodi di tempo più lunghi.
- **Ruoli di lavoro temporanei/contratti:** Consente agli utenti di usare un computer. Quando lasciano, restituiscono la macchina virtuale al pool richiedibile senza perdita di dati. Con la macchina virtuale non richiesta, un altro utente può richiedere la macchina virtuale e continuare o rivedere il computer per ottenere ulteriori informazioni.
- **In generale:** La possibilità di configurare e distribuire automaticamente le macchine virtuali in base a una cadenza specifica è utile in molte situazioni diverse. Esistono diverse situazioni in cui la funzionalità di attestazione/Unclaim consente agli utenti di essere più efficienti grazie a un processo automatizzato per compilare le macchine virtuali in uno stato non recuperato con una configurazione set. Le configurazioni possono includere diversi sistemi operativi, lingue, dischi o [altri software (elementi)](devtest-lab-artifact-author.md) a seconda delle esigenze. La possibilità di richiedere una macchina virtuale dal Lab consente all'utente del Lab di ottenere un sistema configurato correttamente senza spendere tempo o sforzi per la configurazione del computer. Lab Manager può usare lo stato richiesto delle macchine virtuali per migliorare il numero di computer generati, pulire i computer e determinare la priorità delle configurazioni. [Image Factory](image-factory-create.md) è un valido esempio di processo automatizzato per la creazione di macchine virtuali e immagini per più Lab. Gli script possono essere modificati per eseguire una delle situazioni seguenti con le modifiche appropriate o essere usati come riferimento per la creazione di un sistema personalizzato.

## <a name="situational-use-of-claimable-machines"></a>Uso situazionale dei computer attestabili

- Usare la funzionalità di attestazione/annullamento delle attestazioni che consente agli utenti di passare il controllo dei computer da un a un altro e non deve conoscere in modo esplicito chi rileverà il computer avanti.
- Lo sviluppo, il test e il debug di uno scenario in cui una configurazione di computer specifica può riprodurre un bug, quindi è possibile che il computer non sia in grado di richiedere a un altro sviluppatore di richiedere il computer e continuare il lavoro. Questa funzionalità è particolarmente utile perché un maggior numero di persone lavora in remoto in aree diverse del mondo. 
- I membri del team possono usare un unico ambiente. Ad esempio, è possibile configurare manualmente un ambiente complesso che non può essere automatizzato o creare risorse in grado di gestire solo le modifiche per un singolo input come le immagini. In passato, questo problema veniva gestito con un computer dedicato funzionante. La funzionalità richiesta è un miglioramento del processo manuale, grazie al controllo degli accessi utente predefinito e all'identificazione visiva, quando disponibile. Se non è stato recuperato, viene effettuato il deprovisioning della macchina virtuale per ridurre i costi.
- Avere un disco dati collegato a una macchina virtuale. Ogni disco fino a circa 1 TB di dati consente di passare un volume elevato di dati senza dover copiare o duplicare i dati. La VM verrà inizialmente creata con un disco collegato con un volume elevato di dati.  Qualsiasi utente potrebbe quindi richiedere il computer e accedere ai dati. Al termine, annullare la richiesta della VM per consentire ad altri utenti di eseguire il computer.

Ci sono alcune avvertenze per l'uso di computer richiedibili, in genere per ottenere l'accesso al computer. Se il computer è aggiunto a un dominio, l'utente che richiede il computer dovrà disporre già dell'accesso, in genere viene concesso concedendo l'accesso a un gruppo che include tutti gli utenti nel Lab al momento della creazione della VM. Se il computer non è aggiunto a un dominio, sarà necessario eseguire l'artefatto **Reimposta password VM** nel repository pubblico per aggiungere l'utente come amministratore.  Gli artefatti possono essere applicati anche dopo che il computer è stato avviato o richiesto.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente: [Creare e gestire macchine virtuali richiedibili in Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
