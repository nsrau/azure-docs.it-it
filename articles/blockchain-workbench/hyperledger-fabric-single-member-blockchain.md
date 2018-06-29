---
title: Hyperledger Fabric Consortium
description: Usare il modello di soluzione Hyperledger Fabric Consortium per distribuire e configurare una rete con singolo membro
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 28561f5f94044d19cfd07e99d7f7a736ec470cf1
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960356"
---
# <a name="hyperledger-fabric-single-member-network"></a>Rete con singolo membro Hyperledger Fabric

È possibile usare il modello di soluzione Hyperledger Fabric Consortium per distribuire e configurare una rete (multinodo) con singolo membro Hyperledger Fabric.

Dopo avere letto l'articolo, si sarà in grado di:

- Disporre di conoscenze operative di blockchain, Hyperledger Fabric e architetture di rete di consorzio più complicate
- Informazioni su come distribuire e configurare una rete di consorzio Hyperledger Fabric a singolo membro dall'interno del portale di Azure

## <a name="about-blockchain"></a>Informazioni sulle blockchain

Per gli utenti che non hanno familiarità con la community blockchain, questa è una grande opportunità per conoscere la tecnologia in modo facile e configurabile su Azure. Blockchain è la tecnologia sottostante a Bitcoin, ma è molto di più un agente di attivazione per una valuta virtuale. È una combinazione di tecnologie esistenti per database, sistema distribuiti e crittografia, che consente calcoli multiparty sicuri con garanzie per immutabilità, verificabilità, capacità di controllo e resilienza agli attacchi. Protocolli diversi adottano meccanismi diversi per fornire questi attributi. [Hyperledger Fabric](https://github.com/hyperledger/fabric) è uno di tali protocolli.

## <a name="consortium-architecture-on-azure"></a>Architettura di consorzio in Azure

Questo modello consente di distribuire una topologia per semplificare i test e simulare la produzione per gli utenti all'interno di una singola organizzazione (membro singolo). Questa distribuzione è costituita da una rete multinodo in una singola area, che verrà presto estesa a più aree.

La rete è costituita da tre tipi di nodi:

1. **Nodo membro**: un nodo che esegue il servizio di appartenenza Fabric che registra e gestisce i membri della rete. Questo nodo può essere inserito in un cluster ai fini della scalabilità e della disponibilità elevata, tuttavia in questo lab verrà usato un nodo a singolo membro.
2. **Nodi di ordinamento**: un nodo che esegue il servizio di comunicazione che implementa una garanzia di recapito, ad esempio la trasmissione del totale degli ordini o le transazioni atomiche.
3. **Nodi peer**: un nodo che esegue il commit delle transazioni e mantiene lo stato e una copia del libro mastro distribuito.

## <a name="getting-started"></a>Introduzione

Per iniziare, è necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali e account di archiviazione standard. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/).

Per impostazione predefinita, la maggior parte dei tipi di sottoscrizione supporta una topologia di distribuzione di piccole dimensioni senza la necessità di aumentare la quota. La distribuzione più piccola possibile per un singolo membro dovrà includere:

- 5 macchine virtuali (5 core)
- 1 rete virtuale
- 1 bilanciamento del carico
- 1 indirizzo IP pubblico

Dopo aver creato una sottoscrizione, passare al [portale di Azure](https://portal.azure.com). Selezionare **+**, selezionare **Blockchain** e selezionare **Hyperledger Fabric Single Member Blockchain**.

![Modello del marketplace Hyperledger Fabric Single Member Blockchain](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Distribuzione

Per iniziare, selezionare **Hyperledger Fabric Single Member Blockchain** e fare clic su **Crea**. Verrà aperto il pannello **Informazioni di base** nella procedura guidata.

La distribuzione del modello consente di configurare in modo guidato la rete multinodo. Il flusso di distribuzione è suddiviso in tre passaggi: informazioni di base, configurazione della rete di rete e configurazione dell'infrastruttura.

### <a name="basics"></a>Nozioni di base

Nel pannello **Informazioni di base**, specificare i valori dei parametri standard per qualsiasi distribuzione, ad esempio sottoscrizione, gruppo di risorse e proprietà di base della macchina virtuale.

![Nozioni di base](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Nome parametro| DESCRIZIONE| Valori consentiti|Default Value
---|---|---|---
**Resource prefix** (Prefisso della risorsa)| Stringa usata come base per la denominazione delle risorse distribuite.|6 caratteri o meno|ND
**VM user name** (Nome utente macchina virtuale)| Nome utente dell'amministratore per ognuna delle macchine virtuali distribuite per questo membro.|1-64 caratteri|azureuser
**Tipo di autenticazione**| Metodo per l'autenticazione per la macchina virtuale.|Password o chiave pubblica SSH|Password
**Password (tipo di autenticazione = password)**|Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. La password deve contenere 3 dei requisiti seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale.<br /><br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile modificarla dopo il provisioning.|12-72 caratteri|ND
**Chiave SSH (tipo di autenticazione = chiave pubblica)**|Chiave Secure Shell usata per l'accesso remoto.||ND
**Restrict access by IP address** (Limita l'accesso in base all'indirizzo IP)|Impostazione per determinare se l'accesso all'endpoint client è limitato o meno.|Sì/No| No 
**Allowed IP address or subnet (restrict access by IP address = Yes)** (Indirizzo IP o subnet consentiti - limita l'accesso in base all'indirizzo IP = Sì)|Indirizzo IP o set di indirizzi IP autorizzati ad accedere all'endpoint client quando è abilitato il controllo di accesso.||ND
**Sottoscrizione** |Sottoscrizione in cui eseguire la distribuzione.
**Gruppo di risorse** |Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio.||ND
**Posizione** |L'area di Azure in cui distribuire il footprint di rete del primo membro.

### <a name="network-size-and-performance"></a>Dimensioni e prestazioni della rete

In **Network size and performance** (Dimensioni e prestazioni della rete) specificare gli input per le dimensioni della rete di consorzio, ad esempio il numero di nodi di appartenenza, di ordinamento e peer. Scegliere le opzioni di infrastruttura e le dimensioni della macchina virtuale.

![Dimensioni e prestazioni della rete](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Nome parametro| DESCRIZIONE| Valori consentiti|Default Value
---|---|---|---
**Number of Membership Nodes** (Numero di nodi di appartenenza)|Numero di nodi che eseguono il servizio di appartenenza. Per informazioni dettagliate sul servizio di appartenenza, vedere gli articoli dedicati a sicurezza e servizi di appartenenza nella [documentazione](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf) di Hyperledger.<br /><br />Questo valore è attualmente limitato a un nodo, ma si prevede di supportare la scalabilità orizzontale tramite clustering in una versione successiva.|1| 1
**Number of Orderer Nodes** (Numero di nodi di ordinamento) |Numero di nodi che ordinano (organizzano) le transazioni in un blocco.--> Questa frase è prolissa e confusa. Per altri dettagli sul servizio di ordinamento, vedere la [documentazione](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) di Hyperledger.<br /><br />Questo valore è attualmente limitato a 1 nodo. |1 |1
**Number of Peer Nodes** (Numero di nodi peer)| Nodi di proprietà dei membri del consorzio che eseguono le transazioni e mantengono lo stato e una copia del libro mastro.<br /><br />Per altri dettagli sul servizio di ordinamento, vedere la [documentazione](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html) di Hyperledger.|3| 3 - 9
**Storage performance** (Prestazioni di archiviazione)|Tipo di archiviazione a supporto di ognuno dei nodi distribuiti. Per altre informazioni sull'archiviazione, vedere [Introduzione ad Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) e [Archiviazione Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard o Premium|Standard
**Virtual machine size** (Dimensioni della macchina virtuale) |Dimensioni della macchina virtuale usate per tutti i nodi nella rete|Standard A,<br />Standard D,<br />Standard D-v2,<br />Standard serie F,<br />Standard DS<br />e Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Impostazioni specifiche dell'infrastruttura

Infine, in **Fabric Settings** (Impostazioni dell'infrastruttura) specificare le impostazioni di configurazione relative all'infrastruttura.

![Impostazioni dell'infrastruttura](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Nome parametro| DESCRIZIONE| Valori consentiti|Default Value
---|---|---|---
**Bootstrap User Name** (Nome utente bootstrap)| Utente autorizzato iniziale che verrà registrato con i servizi membro nella rete distribuita.|Massimo 9 caratteri|admin
**Bootstrap User Password for Fabric CA** (Password utente bootstrap per Fabric CA)|Password dell'amministratore usata per proteggere l'account Fabric CA importato in ogni nodo di appartenenza.<br /><br />La password deve contenere un carattere maiuscolo, un carattere minuscolo e un numero.|12 o più caratteri|ND

### <a name="deploy"></a>Distribuire

In **Riepilogo** controllare l'input specificato ed eseguire la convalida pre-distribuzione di base.

![Summary](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Rivedere i termini legali e sulla privacy e fare clic su **Acquista** per avviare la distribuzione. A seconda del numero di macchine virtuali di cui viene eseguito il provisioning, il tempo di distribuzione può variare da pochi minuti a decine di minuti.

### <a name="accessing-nodes"></a>Accesso ai nodi

Al termine della distribuzione viene visualizzata una **panoramica**.

![Deployments](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Se la schermata non viene visualizzata automaticamente (ad esempio perché ci si è spostati all'interno del portale di gestione durante l'esecuzione della distribuzione), è sempre possibile trovarla nella scheda Gruppi di risorse della barra di spostamento a sinistra. Fare clic sul nome del gruppo di risorse specificato nel passaggio 1 per passare alla pagina **Panoramica**.

Nella panoramica sono elencate tutte le risorse che sono state distribuite tramite il modello di soluzione. È possibile esplorarle quando è necessario, ma da questa schermata è possibile accedere anche ai _parametri di output_ generati dal modello. Questi parametri di output forniranno informazioni utili per la connessione alla rete Hyperledger Fabric.

Per accedere ai parametri di output, fare clic prima sulla scheda **Distribuzioni** nel pannello Gruppo di risorse. Viene visualizzata la cronologia di distribuzione.

![Cronologia di distribuzione](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Dalla cronologia di distribuzione fare clic sulla prima distribuzione nell'elenco per esaminare i dettagli.

![Dettagli della distribuzione](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Nella schermata dei dettagli viene visualizzato un riepilogo della distribuzione, seguito da tre parametri di output utili:

- Il parametro _API-ENDPOINT_ può essere usato quando si distribuisce un'applicazione nella rete.
- Il parametro _PREFIX_, noto anche come _prefisso di distribuzione_ , identifica in modo univoco le risorse e la distribuzione. Verrà usato quando si usano strumenti basati sulla riga di comando.
- Il parametro _SSH-TO-FIRST-VM_ offre un comando ssh pre-assemblato con tutti i parametri corretti necessari per connettersi alla prima macchina virtuale nella rete. Nel caso di Hyperledger Fabric si tratterà del nodo Fabric-CA.

È possibile connettersi in remoto alle macchine virtuali per ogni nodo tramite SSH con il nome utente e la password/chiave SSH dell'amministratore specificati. Poiché le macchine virtuali del nodo non hanno indirizzi IP pubblici propri, è necessario passare attraverso il sistema di bilanciamento del carico e specificare il numero di porta. Il comando SSH per accedere al primo nodo di transazione è il terzo parametro di output del modello, **SSH-TO-FIRST-VM (per la distribuzione di esempio: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Per ottenere nodi di transazione aggiuntivi, incrementare il numero di porta di uno (ad esempio, il primo nodo di transazione è sulla porta 3000, il secondo sulla porta 3001, il terzo sulla porta 3002 e così via).

## <a name="next-steps"></a>Passaggi successivi

Ora si è pronti a concentrarsi sullo sviluppo di applicazioni e chaincode per la propria rete blockchain di consorzio Hyperledger.
