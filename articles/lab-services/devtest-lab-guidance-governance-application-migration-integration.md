---
title: Governance dell'infrastruttura di Azure DevTest Labs
description: Questo articolo include indicazioni per la governance dell'infrastruttura di Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 75ce5d6a88b5398bd010cc363b4241bc90068f55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193000"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governance dell'infrastruttura di Azure DevTest Labs - Migrazione e integrazione delle applicazioni
Una volta definito l'ambiente lab di sviluppo/test, è opportuno porsi le domande seguenti:

- Come usare l'ambiente all'interno del team di progetto?
- Come è possibile garantire di seguire tutti i criteri aziendali richiesti e mantenere la flessibilità per aggiungere valore all'applicazione?

## <a name="azure-marketplace-images-vs-custom-images"></a>Immagini di Azure Marketplace e immagini personalizzate

### <a name="question"></a>Domanda
Quando è opportuno usare un'immagine di Azure Marketplace e quando un'immagine aziendale personalizzata?

### <a name="answer"></a>Risposta
Azure Marketplace deve essere usato per impostazione predefinita, se non in caso di dubbi specifici o requisiti aziendali. Alcuni esempi comuni sono:

- Configurazione software complessa che richiede di includere un'applicazione nell'immagine di base.
- L'installazione e la configurazione di un'applicazione possono richiedere diverse ore: un uso tutt'altro che efficiente del tempo di calcolo da aggiungere a un'immagine di Azure Marketplace.
- Sviluppatori e tester richiedono l'accesso rapido a una macchina virtuale e intendono ridurre al minimo il tempo di configurazione di una nuova macchina virtuale.
- Condizioni normative o di conformità, ad esempio criteri di sicurezza, che devono essere definite per tutti i computer.

L'uso di immagini personalizzate non deve essere considerato in modo superficiale. Apportano complessità aggiuntive, poiché sarebbe necessario gestire file di dischi rigidi virtuali per le immagini di base sottostanti. È anche necessario applicare periodicamente patch alle immagini di base con aggiornamenti software. Tra questi rientrano i nuovi aggiornamenti del sistema operativo e tutti gli aggiornamenti o le modifiche di configurazione necessarie per il pacchetto software stesso.

## <a name="formula-vs-custom-image"></a>Formula o immagine personalizzata

### <a name="question"></a>Domanda
Quando è opportuno usare una formula e quando un'immagine personalizzata?

### <a name="answer"></a>Risposta
I fattori decisivi in questo scenario sono in genere i costi e il riutilizzo.

In uno scenario con molti utenti/lab che richiedono un'immagine con molto software sull'immagine di base, è possibile ridurre i costi creando un'immagine personalizzata. L'immagine viene quindi creata una sola volta, riducendo il tempo di configurazione della macchina virtuale e i costi dovuti all'esecuzione della macchina virtuale durante la configurazione.

Un altro fattore da considerare è la frequenza delle modifiche al pacchetto software. Se si eseguono build giornaliere e il software deve trovarsi sulle macchine virtuali degli utenti, valutare una formula anziché un'immagine personalizzata.

## <a name="use-custom-organizational-images"></a>Usare immagini aziendali personalizzate

### <a name="question"></a>Domanda
Come è possibile configurare un processo facilmente ripetibile per introdurre le immagini aziendali personalizzate in un ambiente DevTest Labs?

### <a name="answer"></a>Risposta
Vedere [questo video sul criterio di factory di immagini](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Si tratta di uno scenario avanzato e gli script forniti sono solo di esempio. Se sono richieste modifiche, è necessario gestire gli script usati nell'ambiente.

Uso di DevTest Labs per creare una pipeline di immagine personalizzata in Azure Pipelines:

- [Introduction: Get VMs ready in minutes by setting up an image factory in Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/) (Introduzione: come rendere operative le VM in pochi minuti configurando una factory di immagini in Azure DevTest Labs)
- [Image Factory - Part 2! Setup Azure Pipelines and Factory Lab to Create VMs](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/) (Configurare Azure Pipelines e Factory Lab per creare macchine virtuali)
- [Image Factory - Part 3: Save Custom Images and Distribute to Multiple Labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/) (Factory di immagini - Parte 3. Salvare le immagini personalizzate e distribuirle in più lab)
- [Video: Custom Image Factory with Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) (Video: Factory di immagine personalizzata con Azure DevTest Labs)

## <a name="patterns-to-set-up-network-configuration"></a>Criteri per impostare la configurazione di rete

### <a name="question"></a>Domanda
Come è possibile garantire che le macchine virtuali di sviluppo e test non siano in grado di raggiungere la rete Internet pubblica? Esistono criteri consigliati per impostare la configurazione di rete?

### <a name="answer"></a>Risposta
Sì. Due sono gli aspetti da considerare: il traffico in ingresso e in uscita.

**Traffico in ingresso**: se la macchina virtuale non ha un indirizzo IP pubblico, non può essere raggiunta da Internet. Un approccio comune è quello di garantire che siano impostati criteri a livello di sottoscrizione che impediscano agli utenti di creare un indirizzo IP pubblico.

**Traffico in uscita**: per evitare che le macchine virtuali accedano direttamente alla rete Internet pubblica e forzare il traffico attraverso un firewall aziendale, è possibile instradare il traffico in locale tramite ExpressRoute o VPN, usando la configurazione del routing forzata.

> [!NOTE]
> In presenza di un server proxy che blocca il traffico senza le impostazioni del proxy, non dimenticare di aggiungere eccezioni all'account di archiviazione dell'artefatto del lab.

È anche possibile usare gruppi di sicurezza di rete per macchine virtuali o subnet. Questo passaggio aggiunge un livello di protezione per consentire o bloccare il traffico.

## <a name="new-vs-existing-virtual-network"></a>Rete virtuale nuova o esistente

### <a name="question"></a>Domanda
Quando è consigliabile creare una nuova rete virtuale per l'ambiente DevTest Labs e quando usare invece una rete virtuale esistente?

### <a name="answer"></a>Risposta
Se le macchine virtuali devono interagire con l'infrastruttura esistente, è consigliabile usare una rete virtuale esistente all'interno dell'ambiente DevTest Labs. In caso di utilizzo di ExpressRoute, è possibile ridurre al minimo la quantità di reti virtuali/subnet in modo da non frammentare lo spazio degli indirizzi IP assegnato per l'uso nelle sottoscrizioni. È anche consigliabile usare il criterio di peering di rete virtuale qui (modello Hub-Spoke). Questo approccio consente la comunicazione delle reti virtuali/subnet tra le sottoscrizioni all'interno di una determinata area, sebbene il peering tra aree sia una funzionalità non ancora introdotta nelle reti di Azure.

In caso contrario, ogni ambiente di DevTest Labs potrebbe avere una specifica rete virtuale. Tenere tuttavia presente che esistono [limiti](../azure-subscription-service-limits.md) al numero di reti virtuali per sottoscrizione. La quantità predefinita è 50, sebbene questo limite possa essere incrementato a 100.

## <a name="shared-public-or-private-ip"></a>IP condiviso, pubblico o privato

### <a name="question"></a>Domanda
Quando è opportuno usare un indirizzo IP condiviso, pubblico o privato?

### <a name="answer"></a>Risposta
Se si usa una VPN da sito a sito o ExpressRoute, è consigliabile usare indirizzi IP privati, in modo che i computer siano accessibili tramite la rete interna e inaccessibili sulla rete Internet pubblica.

> [!NOTE]
> I proprietari dei lab possono modificare questi criteri di subnet per garantire che nessuno crei accidentalmente indirizzi IP pubblici per le macchine virtuali. Il proprietario della sottoscrizione deve creare criteri di sottoscrizione che impediscano la creazione di indirizzi IP pubblici.

Quando si usano indirizzi IP pubblici condivisi, le macchine virtuali in un lab condividono un indirizzo IP pubblico. Questo approccio può essere utile se è necessario evitare di superare i limiti sugli indirizzi IP pubblici per una determinata sottoscrizione.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limiti nel numero di macchine virtuali per utente o lab

### <a name="question"></a>Domanda
Esiste una regola in termini di numero di macchine virtuali da impostare per utente o per lab?

### <a name="answer"></a>Risposta
Quando si considera il numero di macchine virtuali per utente o lab, sono tre i dubbi principali:

- Il **costo complessivo** che il team può impiegare per le risorse nel lab. È facile creare rapidamente numerosi computer. Per controllare i costi, un meccanismo consiste nel limitare il numero di VM per utente e/o lab
- Il numero totale di macchine virtuali in un lab è interessato dalle [quote a livello di sottoscrizione](../azure-subscription-service-limits.md) disponibili. Uno dei limiti superiori è 800 gruppi di risorse per sottoscrizione. DevTest Labs crea attualmente un nuovo gruppo di risorse per ogni VM (a meno che non vengano usati IP pubblici condivisi). Se sono presenti 10 labs in una sottoscrizione, laboratori potesse rientrare circa 79 macchine virtuali in ogni lab (800 limite massimo: 10 gruppi di risorse per i 10 laboratori se stessi) = 79 macchine virtuali per lab.
- Se ad esempio il lab è connesso in locale a ExpressRoute, sono **disponibili spazi di indirizzi IP definiti** per la rete virtuale/subnet. Per garantire la creazione di VM nel lab senza errori (errore: impossibile ottenere l'indirizzo IP), i proprietari dei lab possono specificare il numero massimo di macchine virtuali per lab allineato allo spazio di indirizzi IP disponibile.

## <a name="use-resource-manager-templates"></a>Usare i modelli di Resource Manager

### <a name="question"></a>Domanda
Come è possibile usare modelli di Resource Manager in un ambiente DevTest Labs?

### <a name="answer"></a>Risposta
Distribuire i modelli di Resource Manager in un ambiente DevTest Labs seguendo i passaggi indicati nell'articolo sulla [funzionalità degli ambienti in DevTest Labs](devtest-lab-test-env.md). In sostanza, si esegue il check-in dei modelli di Resource Manager in un repository Git (Azure Repos o GitHub) e si aggiunge un [repository privato per i modelli](devtest-lab-test-env.md) al lab.

Questo scenario potrebbe non essere utile se si usa DevTest Labs per ospitare computer di sviluppo, mentre può risultare utile se si crea un ambiente di gestione temporanea, rappresentativo dell'ambiente di produzione.

È anche importante notare che il numero di macchine virtuali per lab o per opzione utente limita solo il numero di computer creati in modalità nativa nel lab stesso e non per ambiente (modelli di Resource Manager).

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo sull'[uso degli ambienti in DevTest Labs](devtest-lab-test-env.md).