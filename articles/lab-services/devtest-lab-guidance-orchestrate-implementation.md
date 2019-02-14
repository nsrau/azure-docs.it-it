---
title: Orchestrare l'implementazione di Azure DevTest Labs
description: Questo articolo contiene le indicazioni per l'orchestrazione dell'implementazione di Azure DevTest Labs nella propria organizzazione.
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
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242992"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orchestrare l'implementazione di Azure DevTest Labs
Questo articolo descrive un approccio consigliato per la distribuzione e l'implementazione rapide di Azure DevTest Labs. L'immagine seguente evidenzia il processo complessivo come indicazione prescrittiva e al contempo osserva la flessibilità necessaria per supportare vari scenari e requisiti di settore.

![Passaggi per l'implementazione di Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Presupposti
Questo articolo presuppone la presenza degli elementi seguenti prima di implementare un progetto pilota di DevTest Labs:

- **Sottoscrizione di Azure**: il team del progetto pilota ha accesso alla distribuzione delle risorse in una sottoscrizione di Azure. Se i carichi di lavoro sono solo per sviluppo e test, è consigliabile selezionare l'offerta Sviluppo/test Enterprise, che include immagini disponibili aggiuntive e tariffe ridotte per macchine virtuali Windows.
- **Accesso in locale**: se necessario, l'accesso in locale è già stato configurato. Per l'accesso in locale è possibile configurare una connessione VPN da sito a sito o una connessione ExpressRoute. L'installazione di una connettività tramite ExpressRoute richiede in genere molte settimane, è pertanto consigliato installarla prima di iniziare il progetto.
- **Team del progetto pilota**: I team del progetto di sviluppo iniziale che usano DevTest Labs sono stati identificati insieme alle attività di sviluppo e test applicabili e sono stati stabiliti i requisiti, gli scopi e gli obiettivi per questi team.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Attività cardine 1: stabilire il progetto e la topologia di rete iniziali
La prima area su cui concentrarsi quando si distribuisce una soluzione Azure DevTest Labs riguarda l'installazione della connettività pianificata per le macchine virtuali. I passaggi seguenti delineano le procedure necessarie:

1. Definire gli **intervalli di indirizzi IP iniziali** assegnati alla sottoscrizione di DevTest Labs in Azure. Questo passaggio richiede di prevedere l'utilizzo atteso del numero di macchine virtuali in modo che sia possibile specificare un blocco di dimensioni sufficientemente grandi che consenta una futura espansione.
2. Identificare le **modalità di accesso desiderate** in DevTest Labs (ad esempio l'accesso esterno o interno). Un punto chiave in questo passaggio consiste nel determinare se le macchine virtuali hanno indirizzi IP pubblici, vale a dire se è possibile accedervi da Internet direttamente.
3. Identificare e stabilire le **modalità di connettività** con il resto dell'ambiente cloud di Azure e l'ambiente locale. Se è abilitato il routing forzato con ExpressRoute, è probabile che le macchine virtuali necessitino di configurazioni proxy appropriate per attraversare il firewall aziendale.
4. Se le macchine virtuali devono essere **aggiunte a un dominio**, determinare se vengono aggiunte a un dominio basato sul cloud (ad esempio Servizi directory di AAD) o a un dominio locale. Per l'ambiente locale determinare quale unità organizzativa (OU) in Active Directory unisce in join le macchine virtuali. Verificare anche che gli utenti abbiano accesso per eseguire un join (o stabilire un account del servizio che abbia la possibilità di creare record di macchina nel dominio)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Attività cardine 2: distribuire il lab pilota
Dopo avere determinato la topologia di rete, è possibile creare il primo lab o lab pilota seguendo questi passaggi:

1. Creare un ambiente DevTest Labs iniziale (le istruzioni dettagliate sono reperibili [qui](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md))
2. Determinare le immagini di macchine virtuali consentite e le dimensioni per l'uso con il lab. Decidere se è possibile caricare immagini personalizzate in Azure da usare con DevTest Labs.
3. Proteggere l'accesso al lab creando Controlli degli accessi in base al ruolo per il lab (proprietari del lab e utenti del lab). È consigliabile usare account di Active Directory sincronizzati con Azure Active Directory per l'identità con DevTest Labs.
4. Configurare DevTest Labs per l'uso di criteri, ad esempio le pianificazioni, gestione dei costi, macchine virtuali richiedibili, immagini personalizzate o formule.
5. Stabilire un repository online, ad esempio Azure Repos/Git.
6. Decidere se usare repository pubblici o privati o una combinazione di entrambi. Organizzare modelli JSON per le distribuzioni e la manutenzione a lungo termine.
7. Creare artefatti personalizzati, se necessario. Questo passaggio è facoltativo. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Attività cardine 3: documentazione, supporto, apprendimento e miglioramento
Il team del progetto pilota iniziale può avere bisogno di un supporto approfondito per affrontare le attività iniziali. Usare la propria esperienza per garantire la documentazione e il supporto appropriati per l'implementazione continuata di Azure DevTest Labs.

1. Introdurre il team del progetto pilota alle nuove risorse di DevTest Labs (demo, documentazione)
2. In base alle esperienze dei team del progetto pilota pianificare e distribuire la documentazione a seconda delle esigenze
3. Formalizzare processo di onboarding di nuovi team (creazione e configurazione di lab, concessione dell'accesso e così via)
4. In base all'utilizzo iniziale verificare se la previsione originale relativa allo spazio di indirizzi IP è ancora sensata e accurata
5. Verificare che siano state completate le verifiche di conformità e sicurezza appropriate

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo successivo di questa serie: [Governance dell'infrastruttura di Azure DevTest Labs](devtest-lab-guidance-governance-resources.md)
