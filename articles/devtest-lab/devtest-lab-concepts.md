<properties
	pageTitle="Concetti relativi a DevTest Labs | Microsoft Azure"
	description="Informazioni sui concetti di base di DevTest Labs e su come possa semplificare la creazione, la gestione e il monitoraggio delle macchine virtuali di Azure"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="tarcher"/>

#Concetti di Lab di sviluppo e test

> [AZURE.NOTE]
Questo articolo è la parte 3 di una serie in 3 parti:
> 
> 1. [Che cosa sono i lab di sviluppo/test?](devtest-lab-overview.md)
> 1. [Perché Lab di sviluppo e test?](devtest-lab-why.md)
> 1. **[Concetti relativi a DevTest Labs](devtest-lab-concepts.md)**

##Overview

L'elenco seguente contiene le definizioni e i concetti chiave di Lab di sviluppo e test:

##Elementi
Gli elementi vengono usati per distribuire e configurare l'applicazione dopo il provisioning di una macchina virtuale. Gli elementi possono essere:

- Strumenti che si vuole installare nella VM, come agenti, Fiddler, Visual Studio.
- Azioni che si desidera eseguire sulla macchina virtuale, ad esempio la clonazione di un archivio.
- Applicazioni che si vuole testare.

Gli elementi sono file JSON basati su [Azure Resource Manager](../resource-group-overview.md) che contengono istruzioni per eseguire la distribuzione e applicare la configurazione.

##Repository di elementi
I repository di elementi sono repository git in cui vengono archiviati gli elementi. È possibile aggiungere gli stessi archivi elementi a più lab all'interno dell'organizzazione consentendone il riutilizzo e la condivisione.

## Immagini di base
Le immagini di base sono immagini di macchine virtuali con tutti gli strumenti e le impostazioni preinstallati e configurati per creare rapidamente una macchina virtuale. È possibile eseguire il provisioning di una macchina virtuale scegliendo una base esistente e aggiungendo un elemento per installare l'agente di test. È quindi possibile salvare la macchina virtuale di cui è stato effettuato il provisioning come base in modo che la base possa essere utilizzata senza la necessità di reinstallare l'agente di test per ogni processo di provisioning della macchina virtuale.

##Formule 
Le formule, oltre alle immagini di base, offrono un meccanismo per il provisioning rapido di VM. Una formula è un elenco di valori predefiniti di proprietà usati nei lab di sviluppo/test per creare macchine virtuali. Con le formule, è possibile creare VM con lo stesso insieme di proprietà (come l'immagine base, la dimensione della VM, la rete virtuale e gli elementi), senza il bisogno di specificare tali proprietà ogni volta. Quando si crea una VM da una formula, i valori predefiniti possono essere modificati o usati così come sono.

##Limiti
I limiti rappresentano un meccanismo per ridurre al minimo gli sprechi nel lab. Ad esempio, è possibile impostare un limite per limitare il numero di macchine virtuali che possono essere create per ogni utente o in un lab.

##Criteri
I criteri consentono di controllare i costi nel lab. Ad esempio, è possibile creare un criterio per arrestare automaticamente le macchine virtuali in base a una pianificazione definita.

##Livelli di sicurezza
L'accesso sicuro è determinato dal controllo degli accessi in base al ruolo di Azure. Per comprendere il funzionamento dell'accesso, è utile conoscere le differenze tra un'autorizzazione, un ruolo e un ambito definiti dal Controllo degli accessi in base al ruolo.

- Autorizzazione: un'autorizzazione è un accesso definito a un'azione specifica, ad esempio un accesso in lettura a tutte le macchine virtuali.
- Ruolo: un ruolo è un set di autorizzazioni che possono essere raggruppate e assegnate a un utente. Ad esempio, il ruolo *proprietario della sottoscrizione* ha accesso a tutte le risorse all'interno di una sottoscrizione.
- Ambito: un ambito è un livello nella gerarchia della risorsa di Azure, ad esempio un gruppo di risorse, un singolo lab o un'intera sottoscrizione.
 
Nell'ambito di DevTest Labs, esistono due tipi di ruoli per definire le autorizzazioni utente: proprietario del lab e utente del lab.

- Proprietario del lab: un proprietario del lab ha accesso a tutte le risorse all'interno del lab. Un proprietario del lab può quindi modificare i criteri, leggere e scrivere nelle VM, modificare la rete virtuale e così via.
- Utente del lab: può visualizzare tutte le risorse del lab, ad esempio VM, criteri e reti virtuali, ma non può modificare i criteri o le VM create da altri utenti.


Per informazioni sulla creazione di ruoli personalizzati in DevTest Labs, vedere [Concedere le autorizzazioni utente per specifici criteri di lab](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Poiché gli ambiti sono gerarchici, quando un utente ha le autorizzazioni per un determinato ambito, gli vengono automaticamente concesse tali autorizzazioni per ogni ambito di livello inferiore incluso. Ad esempio, se un utente è assegnato al ruolo di proprietario della sottoscrizione, ha accesso a tutte le risorse in una sottoscrizione, che includono tutte le macchine virtuali, tutte le reti virtuali e tutti i lab. Il proprietario di una sottoscrizione eredita quindi automaticamente il ruolo di proprietario del lab, ma non il contrario. Il proprietario di un lab ha accesso a un lab, che è un ambito più basso del livello della sottoscrizione. Il proprietario di un lab quindi non potrà visualizzare le macchine virtuali, le reti virtuali o nessuna altra risorsa esterna al lab.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

##Passaggi successivi

[Creare un lab in Azure DevTest Labs](devtest-lab-create-lab.md)

<!---HONumber=AcomDC_0831_2016-->