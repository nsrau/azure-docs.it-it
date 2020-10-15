---
title: Esercitazione per preparare il portale di Azure e l'ambiente del data center per la distribuzione di Azure Stack Edge Pro con GPU | Microsoft Docs
description: La prima esercitazione sulla distribuzione di Azure Stack Edge Pro con GPU include la preparazione del portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 1d207e7cc052af32917eb6c871f332136580e56c
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743267"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Esercitazione: Preparare la distribuzione di Azure Stack Edge Pro con GPU 

Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Stack Edge Pro con GPU. Questa esercitazione descrive come preparare il portale di Azure per la distribuzione di una risorsa Azure Stack Edge.

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

### <a name="get-started"></a>Introduzione

Per la distribuzione di Azure Stack Edge Pro, è prima di tutto necessario preparare l'ambiente. Quando l'ambiente è pronto, seguire la procedura prevista e, se necessario, i passaggi e le procedure facoltativi per la distribuzione completa del dispositivo. Le istruzioni dettagliate di distribuzione indicano quando è necessario eseguire ciascuno di questi passaggi obbligatori e facoltativi.

| Passaggio | Descrizione |
| --- | --- |
| **Preparazione** |Questi passaggi devono essere completati per preparare la successiva distribuzione. |
| **[Elenco di controllo configurazione della distribuzione](#deployment-configuration-checklist)** |Usare questo elenco di controllo per raccogliere e registrare informazioni prima e durante la distribuzione. |
| **[Prerequisiti di distribuzione](#prerequisites)** |Questi confermano che l'ambiente è pronto per la distribuzione. |
|  | |
|**Esercitazioni sulla distribuzione** |Queste esercitazioni sono necessarie per distribuire il dispositivo Azure Stack Edge Pro nell'ambiente di produzione. |
|**[1. Preparare il portale di Azure per Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)** |Creare e configurare la risorsa Azure Stack Edge prima di installare un dispositivo fisico Azure Stack Box Edge. |
|**[2. Installare Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)**|Disimballare, installare in rack e cablare il dispositivo fisico Azure Stack Edge Pro.  |
|**[3. Connettersi ad Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)** |Dopo aver installato il dispositivo, connettersi all'interfaccia utente Web locale del dispositivo.  |
|**[4. Configurare le impostazioni di rete per Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Configurare la rete, incluse la rete di calcolo e le impostazioni del proxy Web per il dispositivo.   |
|**[5. Configurare le impostazioni del dispositivo per Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Assegnare un nome di dispositivo e un dominio DNS, configurare il server di aggiornamento e l'ora del dispositivo. |
|**[6. Configurare le impostazioni di sicurezza per Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Configurare i certificati per il dispositivo. Usare i certificati generati dal dispositivo o caricare i certificati personali.   |
|**[7. Attivare Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md)** |Usare la chiave di attivazione dal servizio per attivare il dispositivo. Il dispositivo è pronto per la configurazione di condivisioni SMB o NFS o per la connessione tramite REST. |
|**[8. Configurare il ruolo di calcolo](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configurare il ruolo di calcolo nel dispositivo. Verrà creato anche un cluster Kubernetes. |
|**[9A. Trasferire dati con condivisioni di Edge](azure-stack-edge-j-series-deploy-add-shares.md)** |Aggiungere condivisioni e connettersi alle condivisioni da SMB o NFS. |
|**[9B. Trasferire dati con account di archiviazione di Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Aggiungere gli account di archiviazione e connettersi all'archiviazione BLOB tramite le API REST. |


È ora possibile iniziare a raccogliere informazioni relative alla configurazione del software per il dispositivo Azure Stack Edge Pro.

## <a name="deployment-configuration-checklist"></a>Elenco di controllo configurazione della distribuzione

Prima di distribuire il dispositivo, è necessario raccogliere informazioni per configurare il software nel dispositivo Azure Stack Edge Pro. La preparazione di alcune di queste informazioni in anticipo consente di semplificare il processo di distribuzione del dispositivo nell'ambiente. Usare l'[elenco di controllo per la configurazione della distribuzione di Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-checklist.md) per prendere nota dei dettagli di configurazione quando si distribuisce il dispositivo.


## <a name="prerequisites"></a>Prerequisiti

Di seguito sono indicati i prerequisiti di configurazione per la risorsa Azure Stack Edge, il dispositivo Azure Stack Edge Pro e la rete del data center.

### <a name="for-the-azure-stack-edge-resource"></a>Per la risorsa Azure Stack Edge

Prima di iniziare, verificare che:

- La sottoscrizione di Microsoft Azure sia abilitata per una risorsa Azure Stack Edge. Assicurarsi di aver usato una sottoscrizione supportata, ad esempio [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) o [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Le sottoscrizioni con pagamento in base al consumo non sono supportate. Per identificare il tipo di sottoscrizione di Azure disponibile, vedere [Che cos'è un'offerta di Azure?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer).
- Si abbia accesso di tipo Proprietario o Collaboratore a livello di gruppo di risorse per le risorse Azure Stack Edge Pro/Data Box Gateway, hub IoT e Archiviazione di Azure.

    - Per creare qualsiasi risorsa Azure Stack Edge/Data Box Gateway, è necessario disporre di autorizzazioni di Collaboratore o superiori con ambito a livello di gruppo di risorse. 
    - È anche necessario assicurarsi che i provider di risorse `Microsoft.DataBoxEdge` e `MicrosoftKeyVault` siano registrati. Per creare qualsiasi risorsa dell'hub IoT, è necessario che sia registrato il provider `Microsoft.Devices`. 
        - Per registrare un provider di risorse, nel portale di Azure passare a **Home > Sottoscrizioni > Sottoscrizione personale > Provider di risorse**. 
        - Cercare il provider di risorse specifico, ad esempio `Microsoft.DataBoxEdge`, e registrarlo. 
    - Per creare una risorsa account di archiviazione, è ugualmente necessario l'accesso di tipo Collaboratore o superiore con ambito a livello di gruppo di risorse. Archiviazione di Azure è un provider di risorse registrato per impostazione predefinita.
- Si ha accesso amministratore o utente all'API Graph di Azure Active Directory per generare operazioni relative a credenziali o chiavi di attivazione, ad esempio la creazione della condivisione che usa un account di archiviazione. Per altre informazioni, vedere [API Graph di Azure Active Directory](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).


### <a name="for-the-azure-stack-edge-pro-device"></a>Per il dispositivo Azure Stack Edge Pro

Prima di distribuire un dispositivo fisico, è necessario:

- Aver esaminato le informazioni sulla sicurezza incluse nel pacchetto di spedizione.
- Avere uno slot 1U disponibile in un rack standard da 19 pollici nel data center per montare in rack il dispositivo.
- Avere accesso a una superficie di lavoro orizzontale, stabile e piana su cui collocare il dispositivo in sicurezza.
- Che sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU rack con un gruppo di continuità (UPS) nell'ambiente in cui si intende configurare il dispositivo.
- Avere accesso a un dispositivo fisico.


### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

- La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Azure Stack Edge Pro. Per altre informazioni, vedere [Requisiti di sistema di Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

- Per le normali condizioni di funzionamento di Azure Stack Edge Pro, siano disponibili:

    - Larghezza di banda minima di download di 10 Mbps per assicurarsi che il dispositivo rimanga aggiornato.
    - Larghezza di banda minima di upload e download dedicata di 20 Mbps per trasferire i file.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Se si ha già una risorsa Azure Stack Edge per la gestione del dispositivo fisico, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Per creare una risorsa Azure Stack Edge, seguire questa procedura nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere al portale di Azure a questo indirizzo: [https://portal.azure.com](https://portal.azure.com).

2. Nel riquadro sinistro selezionare **Crea una risorsa**. Cercare e selezionare **Azure Stack Edge/Data Box Gateway**. Selezionare **Crea**. 

3. Selezionare la sottoscrizione che si vuole usare per il dispositivo Azure Stack Edge Pro. Selezionare il paese in cui si vuole spedire il dispositivo fisico. Selezionare **Mostra dispositivi**.

    ![Creare una risorsa 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Selezionare il tipo di dispositivo. In **Azure Stack Edge Pro** scegliere **Azure Stack Edge Pro con GPU** e quindi **Seleziona**. Se si verificano problemi o non è possibile selezionare il tipo di dispositivo, passare alla [risoluzione dei problemi relativi all'ordine](azure-stack-edge-troubleshoot-ordering.md).

    ![Creare una risorsa 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. In base alle esigenze aziendali, è possibile selezionare Azure Stack Edge Pro con 1 o 2 GPU (Graphical Processing Unit) da NVIDIA. 

    ![Creare una risorsa 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. Nella scheda **Informazioni di base** immettere o selezionare quanto segue in **Dettagli del progetto**.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |Subscription    |Questo campo viene popolato automaticamente in base alla selezione precedente. La sottoscrizione viene collegata all'account di fatturazione. |
    |Resource group  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/resource-group-overview.md).     |

7. Immettere o selezionare quanto segue in **Dettagli istanza**.

    |Impostazione  |Valore  |
    |---------|---------|
    |Nome   | Nome descrittivo per identificare la risorsa.<br>Il nome deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
    |Region     |Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se si usa Azure per enti pubblici, sono disponibili tutte le aree per enti pubblici riportate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|

    ![Creare una risorsa 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


8. Selezionare **Avanti: Indirizzo di spedizione**.

    - Se si ha già un dispositivo, selezionare la casella combinata **Ho un dispositivo Azure Stack Edge Pro**.

        ![Creare una risorsa 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Se si sta ordinando un nuovo dispositivo, immettere il nome del contatto, la società, l'indirizzo a cui spedire il dispositivo e le informazioni di contatto.

        ![Creare una risorsa 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. Selezionare **Avanti: Tag**. Facoltativamente, specificare i tag per classificare le risorse e consolidare la fatturazione. Selezionare **Avanti: Rivedi e crea**.

10. Nella scheda **Rivedi e crea** esaminare i **dettagli sui prezzi**, le **condizioni per l'utilizzo** e i dettagli della risorsa. Selezionare la casella combinata **Dichiaro di aver esaminato le informazioni fornite e di accettare le condizioni relative alla privacy**.

    ![Creare una risorsa 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    Viene anche visualizzata una notifica che indica che durante la creazione della risorsa verrà abilitata un'identità del servizio gestita che consente di eseguire l'autenticazione ai servizi cloud. Questa identità esiste fintanto che esiste la risorsa.

11. Selezionare **Create** (Crea).

La creazione della risorsa richiede alcuni minuti. Viene anche creata un'identità del servizio gestita che consente al dispositivo Azure Stack Edge di comunicare con il provider di risorse in Azure.

Al termine della creazione e della distribuzione della risorsa, si riceverà una notifica. Selezionare **Vai alla risorsa**.

![Andare alla risorsa Azure Stack Edge Pro](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Dopo che l'ordine è stato inviato, Microsoft lo verifica e invia quindi all'utente un messaggio di posta elettronica con i dettagli della spedizione.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

Se si verificano problemi durante il processo di ordine, vedere [Risolvere i problemi relativi all'ordine](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Azure Stack Edge è configurata e operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Azure Stack Edge Pro e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Selezionare la risorsa creata. Selezionare **Panoramica** e quindi **Configurazione dispositivo**.

    ![Selezionare Configurazione dispositivo](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)

2. Nel riquadro **Attiva** specificare un nome per l'istanza di Azure Key Vault o accettare il nome predefinito. La lunghezza del nome dell'insieme di credenziali delle chiavi deve essere compresa tra 3 e 24 caratteri. 

    Viene creato un insieme di credenziali delle chiavi per ogni risorsa Azure Stack Edge attivata con il dispositivo. L'insieme di credenziali delle chiavi consente di archiviare e accedere ai segreti, ad esempio la chiave di integrità del canale per il servizio viene archiviata nell'insieme di credenziali delle chiavi. 

    Dopo aver specificato un nome dell'insieme di credenziali delle chiavi, selezionare **Genera chiave** per creare una chiave di attivazione. 

    ![Ottenere una chiave di attivazione](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

    Attendere alcuni minuti mentre vengono creati l'insieme di credenziali delle chiavi e la chiave di attivazione. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.


> [!IMPORTANT]
> - La chiave di attivazione scade tre giorni dopo la generazione.
> - Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Stack Edge Pro, ad esempio:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Passare all'esercitazione successiva per informazioni su come installare Azure Stack Edge Pro.

> [!div class="nextstepaction"]
> [Installare Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-install.md)



