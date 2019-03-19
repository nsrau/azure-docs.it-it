---
title: Rete per consorzi Hyperledger Fabric in Azure
description: Modello di soluzione per distribuire e configurare una rete per consorzi Hyperledger Fabric
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: ce1afbd5499e798888e77f52d7b652e7e1f548fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005825"
---
# <a name="hyperledger-fabric-consortium-network"></a>Rete per consorzi Hyperledger Fabric

È possibile usare il modello di soluzione per consorzi Hyperledger Fabric per distribuire e configurare una rete per consorzi Hyperledger Fabric in Azure.

Dopo avere letto l'articolo, si sarà in grado di:

- Disporre di conoscenze operative di blockchain, Hyperledger Fabric e architetture di rete di consorzio più complicate
- Informazioni su come distribuire e configurare una rete per consorzi Hyperledger Fabric dal portale di Azure

## <a name="about-blockchain"></a>Informazioni sulle blockchain

Per gli utenti che non hanno familiarità con la community blockchain, questo modello di soluzione rappresenta una grande opportunità per conoscere la tecnologia in modo facile e configurabile in Azure. Blockchain è la tecnologia sottostante a Bitcoin, ma è molto di più un agente di attivazione per una valuta virtuale. È una combinazione di tecnologie esistenti per database, sistema distribuiti e crittografia, che consente calcoli multiparty sicuri con garanzie per immutabilità, verificabilità, capacità di controllo e resilienza agli attacchi. Protocolli diversi adottano meccanismi diversi per fornire questi attributi. [Hyperledger Fabric](https://github.com/hyperledger/fabric) è uno di tali protocolli.

## <a name="consortium-architecture-on-azure"></a>Architettura di consorzio in Azure

Azure supporta due tipi di distribuzione principali per abilitare Hyperledger Fabric. Queste distribuzioni sono progettate per supportare diverse topologie, in base alla destinazione desiderata.

- **Server di sviluppo su singola macchina virtuale**. Questo tipo di distribuzione è progettato come ambiente di sviluppo usato per compilare e testare soluzioni basate su Hyperledger Fabric.
- **Distribuzione con scalabilità orizzontale con più macchine virtuali**. Questo tipo di distribuzione è progettato per gli ambienti che modellano un consorzio di diversi partecipanti che usano un ambiente condiviso.

Gli elementi fondamentali alla base di Hyperledger Fabric sono gli stessi in entrambi i tipi di distribuzione.  Le differenze tra le due distribuzioni consistono nel modo in cui questi componenti vengono ampliati.

- **Nodi CA**: nodo che esegue l'Autorità di certificazione usata per generare i certificati usati a loro volta per le identità nella rete.
- **Nodi di ordinamento**: nodo che esegue il servizio di comunicazione che implementa una garanzia di recapito, ad esempio la trasmissione del totale degli ordini o le transazioni atomiche.
- **Nodi peer**: nodo che esegue il commit delle transazioni e mantiene lo stato e una copia del libro mastro distribuito.
- **Nodi CouchDB**: nodo che può eseguire il servizio CouchDB che può contenere il database di stato e fornire funzionalità avanzate di query sui dati chaincode, estendendo la portata dalla semplice coppia chiave/valore all'archiviazione di tipo JSON.

### <a name="single-virtual-machine-architecture"></a>Architettura con singola macchina virtuale

Come già accennato, l'architettura con singola macchina virtuale è progettata per fornire agli sviluppatori un server con footprint ridotto da usare per lo sviluppo di applicazioni. Tutti i contenitori illustrati sono eseguiti in una sola macchina virtuale. Il servizio di ordinamento usa [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) per questa configurazione. Questa configurazione *non* è un servizio di ordinamento a tolleranza di errore, ma è progettata per essere leggera ai fini dello sviluppo.

![Architettura con singola macchina virtuale](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Architettura con più macchine virtuali

L'architettura con scalabilità orizzontale con più macchine virtuali è basata su disponibilità elevata e ridimensionamento di ogni componente. Questa architettura è molto più adatta per le distribuzioni a livello di produzione.

![Architettura con più macchine virtuali](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Introduzione

Per iniziare, è necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali e account di archiviazione standard. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/).

Dopo aver creato una sottoscrizione, passare al [portale di Azure](https://portal.azure.com). Selezionare **Crea una risorsa > Blockchain > Hyperledger Fabric Consortium**.

![Modello del marketplace Hyperledger Fabric Single Member Blockchain](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Distribuzione

Nel modello **Hyperledger Fabric Consortium** selezionare **Crea**.

La distribuzione del modello consente di configurare in modo guidato la rete multinodo [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/). Il flusso di distribuzione è suddiviso in quattro fasi: informazioni di base, impostazioni della rete per consorzi, configurazione dell'infrastruttura e componenti facoltativi.

### <a name="basics"></a>Nozioni di base

In **Basics** (Informazioni di base) specificare i valori dei parametri standard per qualsiasi distribuzione. Ad esempio, la sottoscrizione, il gruppo di risorse e le proprietà di base delle macchine virtuali.

![Nozioni di base](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Nome parametro | DESCRIZIONE | Valori consentiti |
|---|---|---|
**Resource prefix** (Prefisso della risorsa) | Prefisso delle risorse di cui è stato effettuato il provisioning nell'ambito della distribuzione |6 caratteri o meno |
**Nome utente** | Nome utente dell'amministratore di ognuna delle macchine virtuali distribuite per questo membro |1-64 caratteri |
**Tipo di autenticazione** | Metodo di autenticazione della macchina virtuale |Password o chiave pubblica SSH|
**Password (tipo di autenticazione = password)** |Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. La password deve contenere tre dei tipi di caratteri seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale<br /><br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile cambiarla dopo il provisioning|12-72 caratteri|
**Chiave SSH (tipo di autenticazione = chiave pubblica SSH)** |Chiave Secure Shell usata per l'accesso remoto ||
**Sottoscrizione** |Sottoscrizione in cui eseguire la distribuzione ||
**Gruppo di risorse** |Gruppo di risorse nel quale eseguire la distribuzione della rete per consorzi ||
**Posizione** |Area di Azure in cui distribuire il primo membro ||

Selezionare **OK**.

### <a name="consortium-network-settings"></a>Impostazioni della rete per consorzi

In **Impostazioni di rete** specificare i valori di input per la creazione di una rete per consorzi o l'aggiunta a una rete esistente e configurare le impostazioni dell'organizzazione.

![Impostazioni della rete per consorzi](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Nome parametro | DESCRIZIONE | Valori consentiti |
|---|---|---|
**Network configuration** |È possibile scegliere di creare una nuova rete o aggiungersi a una rete esistente. Se si sceglie *Join existing* (Aggiungi esistente), è necessario fornire valori aggiuntivi. |New network (Nuova rete) <br/> Join existing (Aggiungi esistente) |
**HLF CA password** (Password CA HLF) |Password usata per i certificati generati dalle autorità di certificazione create nell'ambito della distribuzione. La password deve contenere tre dei tipi di caratteri seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale.<br /><br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile cambiarla dopo il provisioning.|1-25 caratteri |
**Organization setup** (Configurazione organizzazione) |È possibile personalizzare il nome e il certificato dell'organizzazione oppure usare i valori predefiniti.|Predefinito <br/> Avanzate |
**VPN network settings** (Impostazioni rete VPN) | Provisioning di un gateway con tunnel VPN per l'accesso alle macchine virtuali | Sì <br/> No  |

Selezionare **OK**.

### <a name="fabric-specific-settings"></a>Impostazioni specifiche dell'infrastruttura

In **Fabric configuration** ( Configurazione dell'infrastruttura) è possibile configurare le dimensioni e le prestazioni della rete e specificare valori di input per la disponibilità della rete. Ad esempio, il numero di nodi di ordinamento e peer, il motore di persistenza usato da ogni nodo e le dimensioni delle macchine virtuali.

![Impostazioni dell'infrastruttura](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Nome parametro | DESCRIZIONE | Valori consentiti |
|---|---|---|
**Tipo di scala** |Tipo di distribuzione di una singola macchina virtuale con più contenitori o di più macchine virtuali in un modello di aumento del numero di istanze.|Single VM (Singola VM) o Multi VM (Più VM) |
**Tipo di disco della macchina virtuale** |Tipo di archiviazione a supporto di ognuno dei nodi distribuiti. <br/> Per altre informazioni sui tipi di dischi disponibili, vedere [Selezionare un tipo di disco](../../virtual-machines/windows/disks-types.md).|SSD Standard <br/> SSD Premium |

### <a name="multiple-vm-deployment-additional-settings"></a>Distribuzione su più macchine virtuali (impostazioni aggiuntive)

![Impostazioni dell'infrastruttura per distribuzioni su più macchine virtuali](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Nome parametro | DESCRIZIONE | Valori consentiti |
|---|---|---|
**Number of orderer nodes** (Numero di nodi di ordinamento) |Numero di nodi che ordinano (organizzano) le transazioni in un blocco. <br />Per altri dettagli sul servizio di ordinamento, vedere la [documentazione](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) di Hyperledger |1-4 |
**Orderer node virtual machine size** (Dimensioni della macchina virtuale dei nodi di ordinamento) |Dimensioni della macchina virtuale usata per i nodi di ordinamento nella rete|Standard Bs,<br />Standard Ds,<br />Standard FS |
**Number of Peer Nodes** (Numero di nodi peer) | Nodi di proprietà dei membri del consorzio che eseguono le transazioni e mantengono lo stato e una copia del libro mastro.<br />Per altri dettagli sul servizio di ordinamento, vedere la [documentazione](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html) di Hyperledger.|1-4 |
**Node state persistence** (Persistenza dello stato dei nodi) |Motore di persistenza usato dai nodi peer. È possibile configurare questo motore per nodo peer. Vedere i dettagli seguenti relativi a più nodi peer.|CouchDB <br />LevelDB |
**Peer node virtual machine size** (Dimensioni della macchina virtuale dei nodi di peer) |Dimensioni della macchina virtuale usate per tutti i nodi nella rete|Standard Bs,<br />Standard Ds,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Configurazione di più nodi peer

Questo modello consente di selezionare il motore di persistenza per nodo peer. Nel caso di tre nodi peer, ad esempio, è possibile usare CouchDB in un nodo e LevelDB negli altri due.

![Configurazione di più nodi peer](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Selezionare **OK**.

### <a name="deploy"></a>Distribuire

In **Riepilogo** controllare l'input specificato ed eseguire la convalida pre-distribuzione di base.

![Summary](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Rivedere le condizioni legali e sulla privacy e selezionare **Acquista** per avviare la distribuzione. A seconda del numero di macchine virtuali di cui viene eseguito il provisioning, il tempo di distribuzione può variare da pochi minuti a decine di minuti.

## <a name="next-steps"></a>Passaggi successivi

Ora si è pronti a concentrarsi sullo sviluppo di applicazioni e chaincode per la propria rete blockchain di consorzio Hyperledger.
