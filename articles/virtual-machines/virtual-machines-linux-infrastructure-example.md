<properties
	pageTitle="Procedura dettagliata per un'infrastruttura di esempio di Azure | Microsoft Azure"
	description="Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione di un'infrastruttura di esempio in Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# Procedura dettagliata per un'infrastruttura di esempio di Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Questo articolo illustra le modalità di compilazione di un'infrastruttura di applicazione di esempio. Sarà trattata la progettazione di un'infrastruttura per un semplice negozio online che riunisce tutte le linee guida e le decisioni sulle convenzioni di denominazione, i set di disponibilità, le reti virtuali e i servizi di bilanciamento del carico e l'effettiva distribuzione delle macchine virtuali.


## Carico di lavoro di esempio

Adventure Works Cycles desidera compilare un'applicazione per un negozio online in Azure, che è costituita da:

- Due server nginx che eseguono il client front-end in un livello Web
- Due server nginx che elaborano i dati e gli ordini in un livello applicazione
- Due server MongoDB che fanno parte di un cluster partizionato per l'archiviazione dei dati sui prodotti e degli ordini in un livello di database
- Due controller di dominio di Active Directory per gli account dei clienti e dei fornitori in un livello di autenticazione
- Tutti i server si trovano in due subnet:
	- una subnet front-end per i server Web
	- una subnet back-end per i server applicazioni, i cluster MongoDB e i controller di dominio

![Diagramma dei diversi livelli di infrastruttura dell'applicazione](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

Il traffico Web protetto in ingresso deve essere sottoposto al bilanciamento del carico tra i server Web mentre i clienti visitano il negozio online. Il traffico di elaborazione degli ordini sotto forma di richieste HTTP provenienti dai server Web deve essere bilanciato tra i server applicazioni. Inoltre, l'infrastruttura deve essere progettata per l'elevata disponibilità.

La progettazione risultante deve includere:

- Una sottoscrizione e un account di Azure
- Un unico gruppo di risorse
- Account di archiviazione
- Una rete virtuale con due subnet
- Set di disponibilità per le macchine virtuali con ruoli simili
- Macchine virtuali

Tutti gli elementi devono rispettare le convenzioni di denominazione seguenti:

- Adventure Works Cycles usa come prefisso **[carico di lavoro IT]-[località]-[risorsa di Azure]**
	- In questo esempio, "**azos**" (Azure online Store) è il nome del carico di lavoro IT e "**use**" (Stati Uniti orientali 2) è la località
- Gli account di archiviazione usano adventureazosusesa**[descrizione]**
	- 'adventure' è stato aggiunto al prefisso per garantire l'univocità, e i nomi account di archiviazione non supportano l'uso di trattini.
- Le reti virtuali usano AZOS-USE-VN**[numero]**
- I set di disponibilità usano azos-use-as-**[ruolo]**
- I nomi delle macchine virtuali usano azos-use-vm-**[nomevm]**


## Sottoscrizioni e account di Azure

Adventure Works Cycles usa la propria sottoscrizione aziendale, denominata Adventure Works Enterprise Subscription, per fornire la fatturazione per questo carico di lavoro IT.


## Account di archiviazione

Adventure Works Cycles ha stabilito che sono necessari due account di archiviazione:

- **adventureazosusesawebapp** per l’archiviazione standard di server Web, server applicazioni, controller di dominio e relativi dischi dati aggiuntivi
- **adventureazosusesadbclust** per l'archiviazione Premium dei server cluster partizionati MongoDB e dei relativi dischi dati


## Rete virtuale e subnet

Poiché la rete virtuale non necessita di connettività costante alla rete locale Adventure Work Cycles, l’azienda ha optato per una rete virtuale solo cloud.

Contoso ha creato una rete virtuale solo cloud con le impostazioni seguenti tramite il portale di Azure:

- Nome: AZOS-USE-VN01
- Sede: Stati Uniti orientali 2
- Spazio degli indirizzi della rete virtuale: 10.0.0.0/8
- Prima subnet:
	- Nome: FrontEnd
	- Spazio degli indirizzi: 10.0.1.0/24
- Seconda subnet:
	- Nome: BackEnd
	- Spazio degli indirizzi: 10.0.2.0/24


## Set di disponibilità

Per mantenere elevata la disponibilità di tutti i quattro i livelli del negozio online, Adventure Works Cycles ha optato per quattro set di disponibilità:

- **azos-use-as-web** per i server Web
- **azos-use-as-app** per i server applicazioni
- **azos-use-as-db** per i server nel cluster partizionato MongoDB
- **azos-use-as-dc** per i controller di dominio


## Macchine virtuali

Adventure Works Cycles ha optato per i seguenti nomi per le macchine virtuali di Azure:

- **azos-use-vm-web01** per il primo server Web
- **azos-use-vm-web02** per il secondo server Web
- **azos-use-vm-app01** per il primo server applicazioni
- **azos-use-vm-app02**, per il secondo server applicazioni
- **azos-use-vm-db01** per il primo server MongoDB nel cluster
- **azos-use-vm-db02** per il secondo server MongoDB nel cluster
- **azos-use-vm-dc01** per il primo controller di dominio
- **azos-use-vm-dc02** per il secondo controller di dominio

Di seguito è riportata la configurazione risultante.

![Infrastruttura dell'applicazione finale distribuita in Azure](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Questa configurazione include:

- Una rete virtuale solo cloud con due subnet (front-end e back-end)
- Due account di archiviazione
- Quattro set di disponibilità, uno per ciascun livello del negozio online
- Le macchine virtuali per i quattro livelli
- Un set esterno con bilanciamento del carico per il traffico Web basato su HTTPS da Internet ai server Web
- Un set interno con bilanciamento del carico per il traffico Web crittografato dai server Web ai server applicazioni
- Un unico gruppo di risorse


## Passaggi successivi

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->