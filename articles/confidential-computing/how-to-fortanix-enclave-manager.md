---
title: "Procedura: eseguire un'applicazione con Fortanix enclave Manager"
description: Informazioni su come usare Fortanix enclave Manager per convertire le immagini in contenitori
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 8996673cf8f01334e3dcd35aab8544643bd37360
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88802181"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>Procedura: eseguire un'applicazione con Fortanix enclave Manager 

Avviare l'esecuzione dell'applicazione in Azure Confidential computing con [Fortanix enclave Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) e [Fortanix node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) da [Fortanix](https://www.fortanix.com/).


Fortanix è un fornitore di software di terze parti con prodotti e servizi basati sull'infrastruttura di Azure. Sono disponibili altri provider di terze parti che offrono servizi di elaborazione riservati simili in Azure, ad esempio [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) e [Scone](https://sconedocs.github.io).  

> [!Note] 
 > I PRODOTTI A CUI VIENE FATTO RIFERIMENTO IN QUESTO DOCUMENTO NON SONO SOTTO IL CONTROLLO DI MICROSOFT. MICROSOFT FORNISCE QUESTE INFORMAZIONI SOLO PER PRATICITÀ E IL RIFERIMENTO A QUESTI PRODOTTI NON MICROSOFT NON IMPLICA L'APPROVAZIONE DA PARTE DI MICROSOFT.



Questa esercitazione illustra come convertire l'immagine dell'applicazione in un'immagine riservata protetta da calcolo. Questo ambiente USA il software [Fortanix](https://www.fortanix.com/) , basato sulle macchine virtuali abilitate per Intel SGX serie DCsv2 di Azure. Questa soluzione orchestra i criteri di sicurezza critici, ad esempio la verifica dell'identità e il controllo dell'accesso ai dati.

 Per il supporto specifico di Fortanix, partecipare alla [community di Fortanix Slack](https://fortanix.com/community/) e usare il canale #enclavemanager.


## <a name="prerequisites"></a>Prerequisiti

1. Se non si dispone di un account Fortanix enclave Manager, [iscriversi](https://em.fortanix.com/auth/sign-up) prima di iniziare.
1. Un registro [Docker](https://docs.docker.com/) privato per eseguire il push delle immagini dell'applicazione convertite.
1. Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

> [!NOTE]
> Gli account di valutazione gratuiti non hanno accesso alle macchine virtuali usate in questa esercitazione. Eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

## <a name="add-an-application-to-fortanix-enclave-manager"></a>Aggiungere un'applicazione a Fortanix enclave Manager
1. Accedi a [FORTANIX em](https://fortanix.com)
1. Passare alla pagina **account** e selezionare **Aggiungi account** per creare un nuovo account. 
    
[!div class="mx-imgBorder"]
![Creare un account](media/how-to-fortanix-enclave-manager/create-account.png)

1. Dopo aver creato l'account, fare clic su **Seleziona** per selezionare l'account appena creato. A questo punto è possibile avviare la registrazione dei nodi di calcolo e la creazione di applicazioni. 
1. Selezionare il pulsante **+ applicazione** per aggiungere un'applicazione. In questo esempio verrà aggiunta un'applicazione del sistema operativo del server Flask. 

1. Selezionare il pulsante **Aggiungi** per l'applicazione del sistema operativo enclave. 

    ![Aggiunta di un'applicazione](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > Questa esercitazione illustra come aggiungere solo le applicazioni del sistema operativo enclave. Per altre informazioni, [vedere](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager) l'introduzione di applicazioni anticorrosione EDP a Fortanix enclave Manager. 

6. In questa esercitazione si userà il registro Docker di Fortanix per l'applicazione di esempio. Inserire i dettagli dalle informazioni riportate di seguito. Usare il registro Docker privato per salvare l'immagine di output. 
 
    - **Nome applicazione**: server applicazioni Python
    - **Descrizione**: server Python Flask
    - **Nome dell'immagine di input**: fortanix/Python-Flask
    - **Nome dell'immagine di output**: fortanx-private/Python-Flask-SGX
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Dimensioni memoria**: 1 GB
    - **Conteggio thread**: 128

    *Facoltativo*: eseguire l'applicazione.
    - **Hub Docker**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **App**: fortanix/Python-Flask

        Eseguire il comando seguente:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Aggiungere un certificato. Immettere le informazioni usando i dettagli seguenti e quindi selezionare **Avanti**:
    - **Dominio**: MyApp. Domain. Dom
    - **Tipo**: certificato emesso da enclave Manager 
    - **Percorso della chiave**:/appkey.pem
    - **Tipo di chiave**: RSA
    - **Percorso certificato**:/appcert.pem
    - **Dimensioni chiave RSA**: 2048 bit
    

## <a name="create-an-image"></a>Creare un'immagine
Un'immagine Fortanix EM è una versione software o una versione di un'applicazione. Ogni immagine è associata a un hash dell'Enclave (MRENCLAVE). 
1. Nella pagina **Aggiungi immagine** immettere le **credenziali del registro di sistema** per il **nome dell'immagine di output**. Queste credenziali vengono usate per accedere al registro Docker privato in cui verrà eseguito il push dell'immagine. 

    ![Crea immagine](media/how-to-fortanix-enclave-manager/create-image.png)
1. Specificare il tag Image e selezionare **Crea**.

    ![Aggiungi tag](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Elenco Consenti dominio e immagine 
Un'applicazione il cui dominio viene aggiunto all'elenco Consenti riceve un certificato TLS da Fortanix enclave Manager. Analogamente, quando un'applicazione viene eseguita dall'immagine convertita, tenterà di contattare Fortanix enclave Manager. L'applicazione richiederà quindi un certificato TLS. 

Passare alla scheda **attività** a sinistra e approvare le richieste in sospeso per consentire il dominio e l'immagine. 

## <a name="enroll-compute-node-agent-in-azure"></a>Registrare l'agente del nodo di calcolo in Azure

### <a name="generate-and-copy-join-token"></a>Genera e copia il token di join
In Fortanix enclave Manager viene creato un token. Questo token consente a un nodo di calcolo in Azure di autenticarsi. È necessario assegnare questo token alla macchina virtuale di Azure.
1. Nella console di gestione di selezionare il pulsante **+ registra nodo** . 
1. Selezionare **genera token** per generare il token di join. Copiare il token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Registrare i nodi nell'agente del nodo Fortanix in Azure Marketplace

La creazione di un agente del nodo Fortanix distribuirà una macchina virtuale, un'interfaccia di rete, una rete virtuale, un gruppo di sicurezza di rete e un indirizzo IP pubblico nel gruppo di risorse di Azure. La tua sottoscrizione di Azure verrà fatturata ogni ora per la macchina virtuale. Prima di creare un agente del nodo Fortanix, vedere la [pagina dei prezzi della macchina virtuale](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) di Azure per la serie DCsv2. Eliminare le risorse di Azure quando non sono in uso. 

1. Passare ad [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) e accedere con le credenziali di Azure.
1. Nella barra di ricerca digitare **Fortanix Confidential computing node Agent**. Selezionare l'app visualizzata nella casella di ricerca denominata **Fortanix Confidential computing node Agent** per passare all'Home page dell'offerta. 
     ![Cerca nel Marketplace](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Selezionare **Get it Now (Ottieni ora**), inserire le informazioni, se necessario, e selezionare **continue (continua**). Si verrà reindirizzati al portale di Azure. 
1. Selezionare **Crea** per accedere alla pagina di distribuzione di Fortanix Confidential computing node Agent.
1. In questa pagina si immetteranno le informazioni per distribuire una macchina virtuale. In particolare, questa VM è una macchina virtuale abilitata per DCsv2 Intel SGX di Azure con il software dell'agente del nodo Fortanix installato. L'agente node consentirà all'immagine convertita di essere eseguita in modo sicuro sui nodi Intel SGX in Azure.  Selezionare la **sottoscrizione** e il **gruppo di risorse** in cui si vuole distribuire la macchina virtuale e le risorse associate. 
 
    > [!NOTE]
    > Esistono vincoli per la distribuzione di macchine virtuali serie DCsv2 in Azure. Potrebbe essere necessario richiedere una quota per i core aggiuntivi. Per altre informazioni, vedere [soluzioni di elaborazione riservate nelle VM di Azure](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solution) . 

1. Selezionare un'area disponibile.
1. Immettere un nome per la macchina virtuale nel campo **nome nodo** . 
1. Immettere e nome utente e password (o chiave SSH) per l'autenticazione nella macchina virtuale.
1. Lasciare la dimensione predefinita del disco del sistema operativo come 200 e selezionare le dimensioni della macchina virtuale (Standard_DC4s_v2 sufficiente per questa esercitazione)
1. Incollare il token generato in precedenza nel campo **token join** .

     ![distribuire risorse](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. Selezionare **Rivedi e crea**. Verificare che la convalida venga superata, quindi selezionare **Crea**. Dopo la distribuzione di tutte le risorse, il nodo di calcolo è ora registrato in gestione enclave. 

## <a name="run-the-application-image-on-the-compute-node"></a>Eseguire l'immagine dell'applicazione nel nodo di calcolo
Eseguire l'applicazione eseguendo il comando seguente. Assicurarsi di modificare l'IP del nodo, la porta e il nome dell'immagine convertita come input per l'applicazione specifica. 
 
In questa esercitazione, il comando da eseguire è:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

dove 
- *52.152.206.164* è l'indirizzo IP dell'host dell'agente del nodo
- *9092* è la porta su cui è in ascolto node Agent
- *fortanix-private/Python-Flask-SGX* è l'app convertita che si trova nella scheda immagini nella colonna **nome immagine** della tabella **Immagini** del portale di gestione dell'enclave di fortanix. 
    
## <a name="verify-and-monitor-the-running-application"></a>Verificare e monitorare l'applicazione in esecuzione
1. Tornare a [Fortanix enclave Manager](https://em.fortanix.com/console)
1. Assicurarsi di lavorare all'interno dell' **account** in cui è stato registrato il nodo
1. Passare alla **console di gestione** selezionando l'icona superiore nel riquadro di spostamento a sinistra. 
1. Selezionare la scheda **applicazione**
1. Verificare che sia presente un'applicazione in esecuzione con un nodo di calcolo associato


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile eliminare il gruppo di risorse, la macchina virtuale e le risorse associate. Eliminando il gruppo di risorse, verrà annullata la registrazione dei nodi associati all'immagine convertita. 

Selezionare il gruppo di risorse per la macchina virtuale, quindi fare clic su **Elimina**. Confermare il nome del gruppo di risorse per completare l'eliminazione delle risorse. \

Per eliminare l'account di Fortanix enclave Manager creato, passare alla [pagina accounts](https://em.fortanix.com/accounts) (gestione enclave). Passare il puntatore del mouse sull'account che si desidera eliminare. Selezionare i punti neri verticali nell'angolo superiore destro e selezionare **Elimina account**.

  ![eliminare](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva sono stati usati gli strumenti Fortanix per convertire l'immagine dell'applicazione per l'esecuzione in una macchina virtuale di elaborazione riservata. Per altre informazioni sulle macchine virtuali di confidential computing in Azure, vedere [Soluzioni nelle macchine virtuali](virtual-machine-solutions.md). 

Per altre informazioni sulle offerte di informatica riservate di Azure, vedere [Panoramica di Azure Confidential computing](overview.md)

