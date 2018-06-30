---
title: Modello di soluzione Azure Ethereum dello Stack
description: Utilizzare i modelli di soluzione personalizzata per distribuire e configurare una rete Ethereum consortium nello Stack di Azure
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114729"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Modelli di soluzioni Azure Ethereum dello Stack

Il modello di soluzione Ethereum è progettato per renderlo più semplice e rapido distribuire e configurare una rete Ethereum multimembro consortium con le informazioni di Azure ed Ethereum minima.

Un numero limitato di input dell'utente e una distribuzione con singolo clic attraverso il portale dell'amministratore dello Stack di Azure, ogni membro può eseguire il provisioning rispettivi footprint di rete. La superficie della rete ogni membro è costituito da un set di nodi con bilanciamento del carico delle transazioni con cui un'applicazione o un utente può interagire per inviare un accessorio virtuale di rete (vulnerabilità), un set di nodi di data mining per registrare le transazioni e transazioni. Un passaggio di connessione successivi si connette NVAs per creare una rete blockchain multimembro completamente configurata.

## <a name="prerequisites"></a>Prerequisiti

Scaricare gli [tramite il Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS versione 16.04.201802220
* Windows Server 2016 
* Script personalizzato per Linux 2.0 
* Estensione di script personalizzati 

Per ulteriori informazioni sugli scenari blockchain in Azure, vedere [modello di soluzione proof-di-lavoro consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).

È necessaria una sottoscrizione Azure in grado di supportare la distribuzione di diverse macchine virtuali. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="deployment-architecture"></a>Architettura di distribuzione

Questo modello di soluzione è possibile distribuire una o più rete consortium Ethereum di membro. La rete virtuale è connesso in una topologia di catena usare risorse di rete dell'Appliance virtuale e connessione. 

## <a name="deployment-use-cases"></a>Casi d'uso di distribuzione

Il modello può distribuire consortium Ethereum per Guida e join di membro in una serie di modi, di seguito sono quelli che preventivamente testati:
- In uno Stack di Azure a più nodi, con Azure Active Directory o AD FS, distribuire lead e membro utilizzando la stessa sottoscrizione o con diverse sottoscrizioni.
- In uno Stack di Azure a nodo singolo (con Azure AD) distribuite lead e membro utilizzando la stessa sottoscrizione.

### <a name="standalone-and-consortium-leader-deployment"></a>Distribuzione Guida autonomo e consortium

Il modello di linea guida consortium Configura footprint del primo membro nella rete. 

1. Scaricare il [modello leader da GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. Nel portale di amministrazione di Stack di Azure, selezionare **nuovo > distribuzione modello** per la distribuzione da un modello personalizzato.
3. Selezionare **modifica modelli** per modificare il nuovo modello personalizzato.
4. Nel riquadro di modifica a destra, copiare e incollare il modello di linea guida JSON scaricato in precedenza.
    
    ![Modificare modello di riempimento](media/azure-stack-ethereum/edit-leader-template.png)

5. Selezionare **Salva**.
6. Selezionare **modificare i parametri** e completare i parametri del modello per la distribuzione.
    
    ![Modificare i parametri di modello di riempimento](media/azure-stack-ethereum/edit-leader-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Stringa utilizzata come base per la denominazione le risorse distribuite. | Caratteri alfanumerici con lunghezza da 1 a 6 | Eth
    AUTHTYPE | Metodo per l'autenticazione per la macchina virtuale. | Password o chiave pubblica SSH | Password
    ADMINUSERNAME | Nome utente dell'amministratore di ogni macchina virtuale distribuita | 1-64 caratteri | gethadmin
    ADMINPASSWORD (tipo di autenticazione = Password)| Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. La password deve contenere 3 dei requisiti seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale. <br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile modificarla dopo il provisioning.|12-72 caratteri|
    ADMINSSHKEY (tipo di autenticazione = parametri sshPublicKey) | Chiave Secure Shell usata per l'accesso remoto. | |
    GENESISBLOCK | Stringa JSON che rappresenta il blocco originale personalizzato. | |
    ETHEREUMACCOUNTPSSWD | Password dell'amministratore usata per proteggere Ethereum account. | |
    ETHEREUMACCOUNTPASSPHRASE | La passphrase utilizzata per generare la chiave privata associata all'account Ethereum. | |
    ETHEREUMNETWORKID | L'ID di rete del consortium. | Utilizzare qualsiasi valore compreso tra 5 e 999.999.999 | 72
    CONSORTIUMMEMBERID | L'ID associato a ogni membro della rete consortium.   | Questo ID deve essere univoco nella rete. | 0
    NUMMININGNODES | Numero di nodi di data mining. | Compreso tra 2 e 15. | 2
    MNNODEVMSIZE | Dimensione di macchina virtuale dei nodi di data mining. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Prestazioni di archiviazione dei nodi di data mining. | | Standard_LRS
    NUMTXNODES | Numero di nodi di transazione. | Compreso tra 1 e 5. | 1
    TXNODEVMSIZE | Dimensione di macchina virtuale dei nodi della transazione. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Prestazioni di archiviazione dei nodi della transazione. | | Standard_LRS
    BASEURL | URL di base per ottenere i modelli a seconda dal. | Usare il valore predefinito a meno che non si desidera personalizzare i modelli di distribuzione. | 

7. Selezionare **OK**.
8. In **distribuzione personalizzata**, specificare **sottoscrizione**, **gruppo di risorse**, e **percorso del gruppo di risorse**.
    
    ![Parametri della distribuzione della linea guida](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    Sottoscrizione | La sottoscrizione sul quale eseguire la distribuzione della rete di consorzio | | Sottoscrizione di consumo
    Gruppo di risorse | Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio. | | EthereumResources
    Località | La regione di Azure per gruppo di risorse. | | local

8. Selezionare **Create**.

La distribuzione può richiedere 20 minuti o più tempo.

Al termine della distribuzione, è possibile esaminare il riepilogo per la distribuzione **Microsoft. Modello** nella sezione distribuzione del gruppo di risorse. Il riepilogo contiene valori di output che possono essere usati per creare un join membri consortium.

Per verificare la distribuzione della Guida, visitare il sito di amministrazione della Guida. È possibile trovare l'indirizzo del sito di amministrazione nella sezione di output **Microsoft.Template** distribuzione.  

![Distribuzione Guida riepilogo](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Distribuzione di membro unione consortium

1. Scaricare il [modello di membro consortium da GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. Nel portale di amministrazione di Stack di Azure, selezionare **nuovo > distribuzione modello** per la distribuzione da un modello personalizzato.
3. Selezionare **modifica modelli** per modificare il nuovo modello personalizzato.
4. Nel riquadro di modifica a destra, copiare e incollare il modello di linea guida JSON è stato scaricato in precedenza.
5. Selezionare **Salva**.
6. Selezionare **modificare i parametri** e completare i parametri del modello per la distribuzione.

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Stringa utilizzata come base per la denominazione le risorse distribuite. | Caratteri alfanumerici con lunghezza da 1 a 6 | Eth
    AUTHTYPE | Metodo per l'autenticazione per la macchina virtuale. | Password o chiave pubblica SSH | Password
    ADMINUSERNAME | Nome utente dell'amministratore di ogni macchina virtuale distribuita | 1-64 caratteri | gethadmin
    ADMINPASSWORD (tipo di autenticazione = Password)| Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. La password deve contenere 3 dei requisiti seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale. <br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile modificarla dopo il provisioning.|12-72 caratteri|
    ADMINSSHKEY (tipo di autenticazione = parametri sshPublicKey) | Chiave Secure Shell usata per l'accesso remoto. | |
    CONSORTIUMMEMBERID | L'ID associato a ogni membro della rete consortium.   | Questo ID deve essere univoco nella rete. | 0
    NUMMININGNODES | Numero di nodi di data mining. | Compreso tra 2 e 15. | 2
    MNNODEVMSIZE | Dimensione di macchina virtuale dei nodi di data mining. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Prestazioni di archiviazione dei nodi di data mining. | | Standard_LRS
    NUMTXNODES | Numero di nodi di transazione. | Compreso tra 1 e 5. | 1
    TXNODEVMSIZE | Dimensione di macchina virtuale dei nodi della transazione. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Prestazioni di archiviazione dei nodi della transazione. | | Standard_LRS
    CONSORTIUMDATA | L'URL che punta ai dati di configurazione pertinente consortium forniti dalla distribuzione di un altro membro. Questo valore è reperibile nell'output di distribuzione della Guida. | |
    REMOTEMEMBERVNETADDRESSSPACE | L'indirizzo IP NVA della Guida. Questo valore è reperibile nell'output di distribuzione della Guida. | | 
    REMOTEMEMBERNVAPUBLICIP | L'indirizzo IP NVA della Guida. Questo valore è reperibile nell'output di distribuzione della Guida. | | 
    CONNECTIONSHAREDKEY | Un segreto prestabilito tra i membri della rete consortium che stabilisce una connessione. | |
    BASEURL | URL di base per il modello. | Usare il valore predefinito a meno che non si desidera personalizzare i modelli di distribuzione. | 

7. Selezionare **OK**.
8. In **distribuzione personalizzata**, specificare **sottoscrizione**, **gruppo di risorse**, e **percorso del gruppo di risorse**.

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    Sottoscrizione | La sottoscrizione sul quale eseguire la distribuzione della rete di consorzio | | Sottoscrizione di consumo
    Gruppo di risorse | Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio. | | MemberResources
    Località | La regione di Azure per gruppo di risorse. | | local

8. Selezionare **Create**.

La distribuzione può richiedere 20 minuti o più tempo.

Al termine della distribuzione, è possibile esaminare il riepilogo per la distribuzione **Microsoft.Template** nella sezione distribuzione del gruppo di risorse. Il riepilogo contiene valori di output che possono essere usati per connettere i membri consortium.

Per verificare la distribuzione del membro, visitare il sito di amministrazione del membro. È possibile trovare l'indirizzo del sito di amministrazione nella sezione di output della distribuzione Microsoft.Template.

![Distribuzione di membro riepilogo](media/azure-stack-ethereum/ethereum-node-status-2.png)

Come illustrato nell'immagine, lo stato di nodi del membro viene **non è in esecuzione**. Questo avviene perché non viene stabilita la connessione tra membro e il carattere di riempimento. La connessione tra membro e il carattere di riempimento è una connessione bidirezionale. Quando si distribuisce membro, modello crea automaticamente la connessione dal membro alla linea guida. Per creare la connessione da linea guida al membro andare al passaggio successivo.

### <a name="connect-member-and-leader"></a>Connettere membro e riempimento

Questo modello crea una connessione da linea guida per un membro remoto. 

1. Scaricare il [connettersi membro e il responsabile del modello da GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. Nel portale di amministrazione di Stack di Azure, selezionare **nuovo > distribuzione modello** per la distribuzione da un modello personalizzato.
3. Selezionare **modifica modelli** per modificare il nuovo modello personalizzato.
4. Nel riquadro di modifica a destra, copiare e incollare il modello di linea guida JSON è stato scaricato in precedenza.
    
    ![Modifica modello di connessione](media/azure-stack-ethereum/edit-connect-template.png)

5. Selezionare **Salva**.
6. Selezionare **modificare i parametri** e completare i parametri del modello per la distribuzione.
    
    ![Modifica i parametri del modello di connessione](media/azure-stack-ethereum/edit-connect-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefisso del nome della Guida. Questo valore è reperibile nell'output di distribuzione della Guida.  | Caratteri alfanumerici con lunghezza da 1 a 6 | |
    MEMBERROUTETABLENAME | Nome della tabella di routing del contatore. Questo valore è reperibile nell'output di distribuzione della Guida. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Addressspace del membro. Questo valore è reperibile nell'output di distribuzione del membro. | |
    CONNECTIONSHAREDKEY | Un segreto prestabilito tra i membri della rete consortium che stabilisce una connessione.  | |
    REMOTEMEMBERNVAPUBLICIP | L'indirizzo IP NVA del membro. Questo valore è reperibile nell'output di distribuzione del membro. | |
    MEMBERNVAPRIVATEIP | Indirizzo IP di NVA privato della Guida. Questo valore è reperibile nell'output di distribuzione della Guida. | |
    PERCORSO | Posizione dell'ambiente dello Stack di Azure. | | local
    BASEURL | URL di base per il modello. | Usare il valore predefinito a meno che non si desidera personalizzare i modelli di distribuzione. | 

7. Selezionare **OK**.
8. In **distribuzione personalizzata**, specificare **sottoscrizione**, **gruppo di risorse**, e **percorso del gruppo di risorse**.
    
    ![Connettere i parametri di distribuzione](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    Sottoscrizione | Sottoscrizione del contatore. | | Sottoscrizione di consumo
    Gruppo di risorse | Gruppo di risorse del contatore. | | EthereumResources
    Località | La regione di Azure per gruppo di risorse. | | local

8. Selezionare **Create**.

Al termine dell'installazione, sono necessari alcuni minuti per Guida e il membro avviare la comunicazione. Per verificare la distribuzione, aggiornare il sito di amministrazione del membro. Lo stato dei nodi del membro deve essere in esecuzione. 

![Verificare la distribuzione](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla Ethereum e Azure, vedere [Blockchain tecnologia e le applicazioni | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Per ulteriori informazioni sugli scenari blockchain in Azure, vedere [modello di soluzione proof-di-lavoro consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).