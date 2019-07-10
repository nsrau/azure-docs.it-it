---
title: Esercitazione sulla preparazione del portale di Azure per la distribuzione di Data Box Gateway | Microsoft Docs
description: La prima esercitazione per la distribuzione di Azure Data Box Gateway include la preparazione del portale di Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 428b42e62f44d182de109740359544135561e54b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441500"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Esercitazione: Preparare la distribuzione di Azure Data Box Gateway


Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Data Box Gateway. Descrive come preparare il portale di Azure per la distribuzione della risorsa Data Box Gateway. 

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Scaricare l'immagine del dispositivo virtuale
> * Ottenere la chiave di attivazione

## <a name="get-started"></a>Attività iniziali

Per distribuire il Data Box Gateway, vedere le esercitazioni seguenti nella sequenza indicata.

| **#** | **In questo passaggio** | **Usare questi documenti** |
| --- | --- | --- | 
| 1. |**[Preparare il portale di Azure per Data Box Gateway](data-box-gateway-deploy-prep.md)** |Creare e configurare la risorsa Data Box Gateway prima di effettuare il provisioning di un dispositivo virtuale Data Box Gateway. |
| 2. |**[Effettuare il provisioning di Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Effettuare il provisioning di Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md)**|Per Hyper-V, effettuare il provisioning e connettersi a un dispositivo virtuale Data Box Gateway su un sistema host che esegue Hyper-V in Windows Server 2016 o Windows Server 2012 R2. <br><br><br> Per VMware, effettuare il provisioning e connettersi a un dispositivo virtuale Data Box Gateway in un sistema host che esegue VMware ESXi 6.0, 6.5 o 6.7.<br></br> |
| 3. |**[Connettere, configurare e attivare Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Connettersi all'interfaccia utente Web locale, completare la configurazione del dispositivo e attivare il dispositivo. È quindi possibile eseguire il provisioning delle condivisioni SMB.  |
| 4. |**[Trasferire i dati con Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Aggiungere condivisioni, connettersi alle condivisioni tramite SMB o NFS. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono elencati i prerequisiti di configurazione per la risorsa Data Box Gateway, il dispositivo Data Box Gateway e la rete del data center.

### <a name="for-the-data-box-gateway-resource"></a>Per la risorsa Data Box Gateway

Prima di iniziare, verificare che:

- La sottoscrizione di Microsoft Azure sia supportata per la risorsa Data Box Gateway. Le sottoscrizioni con pagamento in base al consumo siano ugualmente supportate.
- Si abbia accesso di tipo Proprietario o Collaboratore a livello di gruppo di risorse per le risorse Data Box Edge/Data Box Gateway, hub IoT e Archiviazione di Azure.

    - Per creare qualsiasi risorsa Data Box Edge/Data Box Gateway, è necessario disporre di autorizzazioni di Collaboratore o superiori con ambito a livello di gruppo di risorse. È anche necessario assicurarsi che il provider `Microsoft.DataBoxEdge` sia registrato. Per informazioni sulla registrazione, vedere [Registrare i provider di risorse](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Per creare una risorsa account di archiviazione, è ugualmente necessario l'accesso di tipo Collaboratore o superiore con ambito a livello di gruppo di risorse. Archiviazione di Azure è un provider di risorse registrato per impostazione predefinita.
- Si abbia accesso all'API Graph di Azure Active Directory Graph come utente o amministratore. Per altre informazioni, vedere [API Graph di Azure Active Directory](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

### <a name="for-the-data-box-gateway-device"></a>Per il dispositivo Data Box Gateway

Prima di distribuire un dispositivo virtuale, è necessario:

- Sia possibile accedere a un sistema host che esegue Hyper-V in Windows Server 2012 R2 o versioni successive o VMware (ESXi 6.0, 6.5 o 6.7) utilizzabile per il provisioning di un dispositivo.
- Il sistema host sia in grado di dedicare le risorse seguenti al provisioning del dispositivo virtuale Data Box:
  
  - Almeno 4 processori virtuali.
  - Almeno 8 GB di RAM.
  - Un'interfaccia di rete.
  - Un disco del sistema operativo da 250 GB.
  - Un disco virtuale da 2 TB per i dati di sistema.

### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

- La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Data Box Gateway. Per altre informazioni, vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).

- Per le normali condizioni di funzionamento di Data Box Gateway siano disponibili:

    - Larghezza di banda minima di download di 10 Mbps per assicurarsi che il dispositivo rimanga aggiornato.
    - Larghezza di banda minima di upload e download dedicata di 20 Mbps per trasferire i file.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Se si ha già una risorsa Data Box Gateway per la gestione dei dispositivi virtuali, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Per creare una risorsa Data Box Gateway, seguire questa procedura nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere a:

    - Portale di Azure all'URL [https://portal.azure.com](https://portal.azure.com).
    - Portale di Azure per enti pubblici all'URL [https://portal.azure.us](https://portal.azure.us). Per altri dettagli, vedere l'articolo su come [connettersi ad Azure per enti pubblici con il portale](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Nel riquadro sinistro selezionare **Crea una risorsa**. Cercare **Data Box Edge/Data Box Gateway**. Selezionare Data Box Edge/Data Box Gateway. Selezionare **Create** (Crea).
3. Selezionare la sottoscrizione da usare per il dispositivo Data Box Gateway. Selezionare l'area in cui si vuole distribuire la risorsa Data Box Gateway. Per questa versione sono disponibili le aree Stati Uniti orientali, Asia sud-orientale ed Europa occidentale. Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo. Nell'opzione **Data Box Gateway** selezionare **Crea**.

    ![Cercare nel servizio Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Nella scheda **Informazioni di base** immettere o selezionare quanto segue in **Dettagli del progetto**.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |Sottoscrizione    |Questo campo viene popolato automaticamente in base alla selezione precedente. La sottoscrizione viene collegata all'account di fatturazione. |
    |Gruppo di risorse  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/resource-group-overview.md).     |

5. Immettere o selezionare quanto segue in **Dettagli istanza**.

    |Impostazione  |Valore  |
    |---------|---------|
    |NOME   | Nome descrittivo per identificare la risorsa.<br>Il nome deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |   
    |Region     |Per questa versione, per la distribuzione della risorsa sono disponibili le aree Stati Uniti orientali, Asia sud-orientale ed Europa occidentale. Per Azure per enti pubblici sono disponibili tutte le aree per enti pubblici elencate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). <br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|
    
    ![Creazione della risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Selezionare **Rivedi e crea**.
 
7. Nella scheda **Rivedi e crea** esaminare i **dettagli sui prezzi**, le **condizioni per l'utilizzo** e i dettagli della risorsa. Selezionare **Create** (Crea).

    ![Esaminare i dettagli della risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

La creazione della risorsa richiede alcuni minuti. Al termine della creazione e della distribuzione della risorsa, si riceverà una notifica. Selezionare **Vai alla risorsa**.

![Esaminare i dettagli della risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Scaricare l'immagine del dispositivo virtuale

Una volta creata la risorsa Data Box Gateway, scaricare l'immagine del dispositivo virtuale appropriata per effettuare il provisioning di un dispositivo virtuale nel sistema host. Le immagini dei dispositivi virtuali sono specifiche di un sistema operativo.

> [!IMPORTANT]
> Il software in esecuzione nel Data Box Gateway può essere usato solo con la risorsa Data Box Gateway.

Per scaricare un'immagine di dispositivo virtuale, seguire questa procedura nel [portale di Azure](https://portal.azure.com/).

1. Nella risorsa creata selezionare **Panoramica**. Se è disponibile una risorsa Azure Data Box Gateway esistente, selezionare la risorsa e passare a **Panoramica**. Selezionare **Configurazione dispositivo**.

    ![Nuova risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Nel riquadro **Scarica immagine** selezionare l'immagine di dispositivo virtuale corrispondente al sistema operativo del server host usato per il provisioning della VM. I file di immagine sono di circa 5,6 GB.
   
   * [VHDX per Hyper-V in Windows Server 2012 R2 e versioni successive](https://aka.ms/dbe-vhdx-2012).
   * [VMDK per VMWare ESXi 6.0, 6.5 o 6.7](https://aka.ms/dbe-vmdk).

    ![Scaricare l'immagine del dispositivo virtuale Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Scaricare e decomprimere il file in un'unità locale, prendendo nota della posizione del file decompresso.


## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Data Box Gateway è operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Data Box Gateway e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Selezionare la risorsa creata e quindi selezionare **Panoramica**. In **Configurazione dispositivo** passare al riquadro **Configurare e attivare**.

    ![Riquadro Configurare e attivare](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selezionare **Genera chiave** per creare una chiave di attivazione. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.

    ![Ottenere una chiave di attivazione](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La chiave di attivazione scade tre giorni dopo la generazione.
> - Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Gateway, ad esempio:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Scaricare l'immagine del dispositivo virtuale
> * Ottenere la chiave di attivazione

Passare all'esercitazione successiva per informazioni su come effettuare il provisioning di una macchina virtuale per Data Box Gateway. A seconda del sistema operativo host, vedere le istruzioni dettagliate in:

> [!div class="nextstepaction"]
> [Effettuare il provisioning di Data Box Gateway in Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

Oppure

> [!div class="nextstepaction"]
> [Effettuare il provisioning di Data Box Gateway in VMware](./data-box-gateway-deploy-provision-vmware.md)


