---
title: Esercitazione sulla preparazione del portale di Azure per la distribuzione di Data Box Gateway | Microsoft Docs
description: La prima esercitazione per la distribuzione di Azure Data Box Gateway include la preparazione del portale di Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6db713984b62ce3db48b2e72a4b117696bdd6add
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452973"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>Esercitazione: Preparare la distribuzione di Azure Data Box Gateway (anteprima)


Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Data Box Gateway. Descrive come preparare il portale di Azure per la distribuzione della risorsa Data Box Gateway. 

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Scaricare l'immagine del dispositivo virtuale
> * Ottenere la chiave di attivazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


> [!IMPORTANT]
> - Data Box Gateway è in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 

### <a name="get-started"></a>Attività iniziali

Per distribuire il Data Box Gateway, vedere le esercitazioni seguenti nella sequenza indicata.

| **#** | **In questo passaggio** | **Usare questi documenti** |
| --- | --- | --- | 
| 1. |**[Preparare il portale di Azure per Data Box Gateway](data-box-gateway-deploy-prep.md)** |Creare e configurare la risorsa Data Box Gateway prima di effettuare il provisioning di un dispositivo virtuale Data Box Gateway. |
| 2. |**[Effettuare il provisioning di Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Effettuare il provisioning di Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md)**|Per Hyper-V, effettuare il provisioning e connettersi a un dispositivo virtuale Data Box Gateway su un sistema host che esegue Hyper-V in Windows Server 2016 o Windows Server 2012 R2. <br><br><br> Per VMware, effettuare il provisioning e connettersi a un dispositivo virtuale Data Box Gateway in un sistema host che esegue VMware ESXi 6.0 o 6.5.<br></br> |
| 3. |**[Connettere, configurare e attivare Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Connettersi all'interfaccia utente Web locale, completare la configurazione del dispositivo e attivare il dispositivo. È quindi possibile eseguire il provisioning delle condivisioni SMB.  |
| 4. |**[Trasferire i dati con Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Aggiungere condivisioni, connettersi alle condivisioni tramite SMB o NFS. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono elencati i prerequisiti di configurazione per la risorsa Data Box Gateway, il dispositivo Data Box Gateway e la rete del data center.

### <a name="for-the-data-box-gateway-resource"></a>Per la risorsa Data Box Gateway

Prima di iniziare, verificare che:

* La sottoscrizione di Microsoft Azure sia abilitata per la risorsa Data Box Gateway.
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

### <a name="for-the-data-box-gateway-device"></a>Per il dispositivo Data Box Gateway

Prima di distribuire un dispositivo virtuale, è necessario:

* Si abbia accesso a un sistema host che esegue Hyper-V in Windows Server 2012 R2 o versioni successive o VMware (ESXi 6.0 o 6.5) da poter usare per il provisioning di un dispositivo.
* Il sistema host sia in grado di dedicare le risorse seguenti al provisioning del dispositivo virtuale Data Box:
  
  * Un minimo di 4 memorie centrali.
  * Almeno 8 GB di RAM. 
  * Un'interfaccia di rete.
  * Un disco del sistema operativo da 250 GB.
  * Un disco virtuale da 2 TB per i dati di sistema.

### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

* La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Data Box Gateway. Per altre informazioni, vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).

* Data Box Gateway deve disporre di una larghezza di banda Internet dedicata a 20 Mbps (o superiore) sempre disponibile. La larghezza di banda non deve essere condivisa con altre applicazioni. Se si usa la limitazione della larghezza di banda della rete, per assicurarne il funzionamento è consigliabile usare una larghezza di banda Internet ad almeno 32 Mbps.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Per creare una nuova risorsa Data Box Gateway, eseguire la procedura seguente. 

Se si ha già una risorsa Data Box Gateway per la gestione dei dispositivi virtuali, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Eseguire la procedura seguente nel portale di Azure per creare una risorsa Data Box.
1. Usare le credenziali di Microsoft Azure per accedere al portale di anteprima di Azure all'URL [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Selezionare la sottoscrizione che si vuole usare per l'anteprima di Data Box Edge. Selezionare l'area in cui si vuole distribuire la risorsa Data Box Edge. Nell'opzione **Data Box Gateway** fare clic su **Crea**.

    ![Cercare nel servizio Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. Per la nuova risorsa immettere o selezionare le informazioni seguenti.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |Nome risorsa   | Nome descrittivo per identificare la risorsa.<br>Il nome deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
    |Sottoscrizione    |La sottoscrizione viene collegata all'account di fatturazione. |
    |Gruppo di risorse  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Località     |Per questa versione sono disponibili le località Stati Uniti orientali, Stati Uniti occidentali 2, Asia sud-orientale ed Europa occidentale. <br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|
    
    ![Creazione della risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Fare clic su **OK**.
 
La creazione della risorsa richiede alcuni minuti. Al termine della creazione, si riceverà la relativa notifica.


## <a name="download-the-virtual-device-image"></a>Scaricare l'immagine del dispositivo virtuale

Una volta creata la risorsa Data Box Gateway, scaricare l'immagine del dispositivo virtuale appropriata per effettuare il provisioning di un dispositivo virtuale nel sistema host. Le immagini dei dispositivi virtuali sono specifiche del sistema operativo e si possono scaricare dal pannello **Avvio rapido** della risorsa nel portale di Azure.

> [!IMPORTANT]
> Il software in esecuzione nel Data Box Gateway può essere usato solo con la risorsa Data Box Gateway.


Eseguire la procedura seguente nel [portale di Azure](https://portal.azure.com/).

1. Fare clic sulla risorsa creata e quindi su **Panoramica**. Se si dispone già di una risorsa Azure Data Box Gateway, fare clic sulla risorsa e passare a **Panoramica**.

    ![Nuova risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. In Avvio rapido nel riquadro destro fare clic sul collegamento corrispondente all'immagine che si vuole scaricare. I file di immagine sono pari a circa 4,8 GB.
   
   * [VHDX per Hyper-V in Windows Server 2012 R2 e versioni successive](https://aka.ms/dbe-vhdx-2012).
   * [VMDK per VMWare ESXi 6.0 o 6.5](https://aka.ms/dbe-vmdk).

5. Scaricare e decomprimere il file in un'unità locale, prendendo nota della posizione del file decompresso.


## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Ora che la risorsa Data Box Gateway è configurata e funzionante, occorre ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Data Box Gateway e connetterlo alla risorsa.

La chiave di attivazione viene usata per registrare tutti i dispositivi Data Box Gateway che devono essere attivati con la risorsa Data Box Gateway. È possibile ottenere questa chiave ora nel portale di Azure.

1. Fare clic sulla risorsa creata e quindi su **Panoramica**.

    ![Nuova risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Fare clic su **Genera chiave** per creare una chiave di attivazione. Fare clic sull'icona di copia per copiare la chiave e salvarla per un utilizzo successivo.

    ![Ottenere una chiave di attivazione](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La chiave di attivazione scade 3 giorni dopo la generazione. 
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


