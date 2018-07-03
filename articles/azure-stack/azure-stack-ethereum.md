---
title: Modello di soluzione di Azure Stack Ethereum blockchain
description: Usare i modelli di soluzione personalizzata per distribuire e configurare una rete di consorzio Ethereum blockchain in Azure Stack
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: fb870cbfbc233725752b3d97fc0ad048a7c14040
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341733"
---
# <a name="azure-stack-ethereum-blockchain-solution-templates"></a>Modelli di soluzioni di Azure Stack Ethereum blockchain

Il modello di soluzione Ethereum è progettato per renderlo più semplice e rapido distribuire e configurare una rete di consorzio multimembro Ethereum blockchain con una minima conoscenza di Azure ed Ethereum.

Con pochi gli input dell'utente e una distribuzione a singolo clic tramite il portale tenant di Azure Stack, ogni membro possa effettuare il provisioning il footprint di rete. Footprint di rete di ogni membro è costituito da un set di nodi con bilanciamento del carico delle transazioni con cui un'applicazione o un utente può interagire per inviare le transazioni, un set di nodi di data mining per registrare le transazioni e un'Appliance virtuale di rete (NVA). Un passaggio successivo di connessione connette le Appliance virtuali di rete per creare una rete completamente configurata multimembro blockchain.

## <a name="prerequisites"></a>Prerequisiti

Scaricare gli [da Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS versione 16.04.201802220
* Windows Server 2016 
* Script personalizzato per Linux 2.0 
* Estensione di script personalizzati 

Per altre informazioni sugli scenari di blockchain in Azure, vedere [modello di soluzione proof-of-work consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).

È necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="deployment-architecture"></a>Architettura di distribuzione

Questo modello di soluzione è possibile distribuire uno o più rete di consorzio Ethereum di membro. La rete virtuale è connessa in una topologia di catena con Appliance virtuale di rete e la connessione alle risorse. 

## <a name="deployment-use-cases"></a>Casi d'uso di distribuzione

Il modello può distribuire consortium Ethereum per leader e aggiunta a un membro di un'ampia gamma di modi, qui sono quelli testate:
- In uno Stack di Azure a più nodi, con Azure AD o AD FS, distribuire lead e membro utilizzando la stessa sottoscrizione o con diverse sottoscrizioni.
- In uno Stack di Azure a nodo singolo (con Azure AD) distribuite lead e membro utilizzando la stessa sottoscrizione.

### <a name="standalone-and-consortium-leader-deployment"></a>Distribuzione di leader autonomo e consortium

Il modello di leader consortium Configura footprint del primo membro della rete. 

1. Scaricare il [modello leader da GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. Nel portale di amministrazione di Azure Stack, selezionare **nuovo > distribuzione modello** per la distribuzione da un modello personalizzato.
3. Selezionare **modifica modello** per modificare il nuovo modello personalizzato.
4. Nel riquadro di modifica a destra, copiare e incollare il modello di leader JSON scaricati in precedenza.
    
    ![Modificare modello di leader](media/azure-stack-ethereum/edit-leader-template.png)

5. Selezionare **Salva**.
6. Selezionare **Upravit parametry** e completare i parametri del modello per la distribuzione.
    
    ![Modificare i parametri di modello leader](media/azure-stack-ethereum/edit-leader-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Stringa utilizzata come base per la denominazione le risorse distribuite. | Alfanumerico con lunghezza da 1 a 6 | Eth
    AUTHTYPE | Metodo per l'autenticazione per la macchina virtuale. | Password o chiave pubblica SSH | Password
    ADMINUSERNAME | Nome utente dell'amministratore di ogni macchina virtuale distribuita | 1-64 caratteri | gethadmin
    ADMINPASSWORD (tipo di autenticazione = Password)| Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. La password deve contenere 3 dei requisiti seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale. <br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile modificarla dopo il provisioning.|12-72 caratteri|
    ADMINSSHKEY (tipo di autenticazione sshPublicKey =) | Chiave Secure Shell usata per l'accesso remoto. | |
    GENESISBLOCK | Stringa JSON che rappresenta il blocco originale personalizzato. | |
    ETHEREUMACCOUNTPSSWD | La password amministratore usata per proteggere account Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | La passphrase utilizzata per generare la chiave privata associata all'account Ethereum. | |
    ETHEREUMNETWORKID | L'ID di rete di consorzio. | Usare qualsiasi valore compreso tra 5 e 999.999.999 | 72
    CONSORTIUMMEMBERID | L'ID associato a ogni membro della rete consortium.   | Questo ID deve essere univoco nella rete. | 0
    NUMMININGNODES | Numero di nodi di data mining. | Compreso tra 2 e 15. | 2
    MNNODEVMSIZE | Dimensioni di macchine Virtuali dei nodi di data mining. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Prestazioni di archiviazione dei nodi di data mining. | | Standard_LRS
    NUMTXNODES | Numero di nodi di transazione. | Tra 1 e 5. | 1
    TXNODEVMSIZE | Dimensioni di macchine Virtuali dei nodi delle transazioni. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Prestazioni dei nodi delle transazioni di archiviazione. | | Standard_LRS
    BASEURL | URL di base per ottenere i modelli a seconda dal. | Usare il valore predefinito a meno che non si desidera personalizzare i modelli di distribuzione. | 

7. Selezionare **OK**.
8. Nelle **distribuzione personalizzata**, specificare **sottoscrizione**, **gruppo di risorse**, e **località del gruppo di risorse**.
    
    ![Parametri di distribuzione di leader](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    Sottoscrizione | La sottoscrizione sul quale eseguire la distribuzione della rete di consorzio | | Sottoscrizione di consumo
    Gruppo di risorse | Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio. | | EthereumResources
    Località | La regione di Azure per gruppo di risorse. | | local

8. Selezionare **Create**.

La distribuzione può richiedere 20 minuti o più tempo.

Al termine della distribuzione, è possibile esaminare il riepilogo per la distribuzione **Microsoft. Modello** nella sezione distribuzione del gruppo di risorse. Il riepilogo contiene valori di output che possono essere utilizzati per associare i membri consortium.

Per verificare la distribuzione del leader, selezionare il sito di amministrazione di leader. È possibile trovare l'indirizzo del sito di amministrazione nella sezione di output **template** distribuzione.  

![Riepilogo della distribuzione leader](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Distribuzione di membro unione consortium

1. Scaricare il [modello membro consortium da GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. Nel portale di amministrazione di Azure Stack, selezionare **nuovo > distribuzione modello** per la distribuzione da un modello personalizzato.
3. Selezionare **modifica modello** per modificare il nuovo modello personalizzato.
4. Nel riquadro di modifica a destra, copiare e incollare il modello di leader JSON è stato scaricato in precedenza.
5. Selezionare **Salva**.
6. Selezionare **Upravit parametry** e completare i parametri del modello per la distribuzione.

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Stringa utilizzata come base per la denominazione le risorse distribuite. | Alfanumerico con lunghezza da 1 a 6 | Eth
    AUTHTYPE | Metodo per l'autenticazione per la macchina virtuale. | Password o chiave pubblica SSH | Password
    ADMINUSERNAME | Nome utente dell'amministratore di ogni macchina virtuale distribuita | 1-64 caratteri | gethadmin
    ADMINPASSWORD (tipo di autenticazione = Password)| Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. La password deve contenere 3 dei requisiti seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale. <br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile modificarla dopo il provisioning.|12-72 caratteri|
    ADMINSSHKEY (tipo di autenticazione sshPublicKey =) | Chiave Secure Shell usata per l'accesso remoto. | |
    CONSORTIUMMEMBERID | L'ID associato a ogni membro della rete consortium.   | Questo ID deve essere univoco nella rete. | 0
    NUMMININGNODES | Numero di nodi di data mining. | Compreso tra 2 e 15. | 2
    MNNODEVMSIZE | Dimensioni di macchine Virtuali dei nodi di data mining. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Prestazioni di archiviazione dei nodi di data mining. | | Standard_LRS
    NUMTXNODES | Numero di nodi di transazione. | Tra 1 e 5. | 1
    TXNODEVMSIZE | Dimensioni di macchine Virtuali dei nodi delle transazioni. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Prestazioni dei nodi delle transazioni di archiviazione. | | Standard_LRS
    CONSORTIUMDATA | URL che punta ai dati di configurazione rilevanti consortium forniti dalla distribuzione di un altro utente. Questo valore è reperibile nell'output di distribuzione del Leader. | |
    REMOTEMEMBERVNETADDRESSSPACE | L'indirizzo IP di NVA del leader. Questo valore è reperibile nell'output di distribuzione del leader. | | 
    REMOTEMEMBERNVAPUBLICIP | L'indirizzo IP di NVA del leader. Questo valore è reperibile nell'output di distribuzione del leader. | | 
    CONNECTIONSHAREDKEY | Un segreto prestabilito tra i membri della rete di consorzio che stabilisce una connessione. | |
    BASEURL | URL di base per il modello. | Usare il valore predefinito a meno che non si desidera personalizzare i modelli di distribuzione. | 

7. Selezionare **OK**.
8. Nelle **distribuzione personalizzata**, specificare **sottoscrizione**, **gruppo di risorse**, e **località del gruppo di risorse**.

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    Sottoscrizione | La sottoscrizione sul quale eseguire la distribuzione della rete di consorzio | | Sottoscrizione di consumo
    Gruppo di risorse | Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio. | | MemberResources
    Località | La regione di Azure per gruppo di risorse. | | local

8. Selezionare **Create**.

La distribuzione può richiedere 20 minuti o più tempo.

Al termine della distribuzione, è possibile esaminare il riepilogo per la distribuzione **template** nella sezione distribuzione del gruppo di risorse. Il riepilogo contiene valori di output che possono essere usati per connettersi membri consortium.

Per verificare la distribuzione del membro, selezionare il sito di amministrazione del membro. È possibile trovare l'indirizzo del sito di amministrazione nella sezione di output della distribuzione template.

![Riepilogo della distribuzione membro](media/azure-stack-ethereum/ethereum-node-status-2.png)

Come illustrato nell'immagine, lo stato di nodi del membro è **non è in esecuzione**. Questo avviene perché non viene stabilita la connessione tra il membro e leader. La connessione tra il membro e leader è una connessione bidirezionale. Quando si distribuisce membro, modello crea automaticamente la connessione dal membro per il leader. Per creare la connessione dal leader al membro, andare al passaggio successivo.

### <a name="connect-member-and-leader"></a>Connettersi al membro e il carattere di riempimento

Questo modello crea una connessione tra i leader per un membro remote. 

1. Scaricare il [connettersi membro e il carattere di riempimento modello da GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. Nel portale di amministrazione di Azure Stack, selezionare **nuovo > distribuzione modello** per la distribuzione da un modello personalizzato.
3. Selezionare **modifica modello** per modificare il nuovo modello personalizzato.
4. Nel riquadro di modifica a destra, copiare e incollare il modello di leader JSON è stato scaricato in precedenza.
    
    ![Modifica modello di connessione](media/azure-stack-ethereum/edit-connect-template.png)

5. Selezionare **Salva**.
6. Selezionare **Upravit parametry** e completare i parametri del modello per la distribuzione.
    
    ![Modifica i parametri del modello di connessione](media/azure-stack-ethereum/edit-connect-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefisso del nome del carattere di riempimento. Questo valore è reperibile nell'output di distribuzione del leader.  | Alfanumerico con lunghezza da 1 a 6 | |
    MEMBERROUTETABLENAME | Nome della tabella di route del contatore. Questo valore è reperibile nell'output di distribuzione del leader. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Addressspace del membro. Questo valore è reperibile nell'output di distribuzione del membro. | |
    CONNECTIONSHAREDKEY | Un segreto prestabilito tra i membri della rete di consorzio che stabilisce una connessione.  | |
    REMOTEMEMBERNVAPUBLICIP | L'indirizzo IP di NVA del membro. Questo valore è reperibile nell'output di distribuzione del membro. | |
    MEMBERNVAPRIVATEIP | Indirizzo NVA IP privato del leader. Questo valore è reperibile nell'output di distribuzione del leader. | |
    PERCORSO | Posizione dell'ambiente Azure Stack. | | local
    BASEURL | URL di base per il modello. | Usare il valore predefinito a meno che non si desidera personalizzare i modelli di distribuzione. | 

7. Selezionare **OK**.
8. Nelle **distribuzione personalizzata**, specificare **sottoscrizione**, **gruppo di risorse**, e **località del gruppo di risorse**.
    
    ![Connettere i parametri di distribuzione](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nome parametro | DESCRIZIONE | Valori consentiti | Valore di esempio
    ---------------|-------------|----------------|-------------
    Sottoscrizione | Sottoscrizione del contatore. | | Sottoscrizione di consumo
    Gruppo di risorse | Gruppo di risorse del contatore. | | EthereumResources
    Località | La regione di Azure per gruppo di risorse. | | local

8. Selezionare **Create**.

Dopo aver completata la distribuzione, sono necessari alcuni minuti per i membri e leader avviare la comunicazione. Per verificare la distribuzione, aggiornare il sito di amministrazione del membro. Lo stato dei nodi del membro deve essere in esecuzione. 

![Verificare la distribuzione](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Ethereum e Azure, vedere [applicazioni e la tecnologia Blockchain | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Per altre informazioni sugli scenari di blockchain in Azure, vedere [modello di soluzione proof-of-work consortium Ethereum](../blockchain-workbench/ethereum-deployment-guide.md).