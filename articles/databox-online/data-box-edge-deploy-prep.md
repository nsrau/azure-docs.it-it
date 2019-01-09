---
title: Esercitazione sulla preparazione del portale di Azure per la distribuzione di Data Box Edge | Microsoft Docs
description: La prima esercitazione sulla distribuzione di Azure Data Box Edge include la preparazione del portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 25f68b011d1fcba450903e9a691b98dfe9e87281
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726120"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Esercitazione: Preparare la distribuzione di Azure Data Box Edge  


Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Data Box Edge. Questa esercitazione descrive come preparare il portale di Azure per la distribuzione di una risorsa Data Box Edge. 

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione.  

### <a name="get-started"></a>Attività iniziali

Per distribuire Data Box Edge, fare riferimento alle esercitazioni seguenti nella sequenza indicata.

| **#** | **In questo passaggio** | **Usare questi documenti** |
| --- | --- | --- | 
| 1. |**[Preparare il portale di Azure per Data Box Edge](data-box-edge-deploy-prep.md)** |Creare e configurare la risorsa di Data Box Edge prima di installare un dispositivo fisico Data Box Edge. |
| 2. |**[Installare Azure Data Box Edge](data-box-edge-deploy-install.md)**|Disimballare, installare in rack e cablare il dispositivo fisico Data Box Edge.  |
| 3. |**[Connettere, configurare e attivare Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Connettersi all'interfaccia utente Web locale, completare la configurazione del dispositivo e attivare il dispositivo. Il dispositivo è pronto per la configurazione di condivisioni SMB o NFS.  |
| 4. |**[Trasferire dati con Data Box Edge](data-box-edge-deploy-add-shares.md)** |Aggiungere condivisioni e connettersi alle condivisioni da SMB o NFS. |
| 5. |**[Trasformare dati con Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configurare i moduli di Edge nel dispositivo per trasformare i dati durante lo spostamento in Azure. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono indicati i prerequisiti di configurazione per la risorsa Data Box Edge, il dispositivo Data Box Edge e la rete del data center.

### <a name="for-the-data-box-edge-resource"></a>Per la risorsa Data Box Edge

Prima di iniziare, verificare che:

* La sottoscrizione di Microsoft Azure sia abilitata per la risorsa Data Box Edge.
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

### <a name="for-the-data-box-edge-device"></a>Per il dispositivo Data Box Edge

Prima di distribuire un dispositivo fisico, è necessario:
- Che sia disponibile uno slot 1U in un rack standard da 19" nel data center per montare in rack il dispositivo. 
- Avere accesso a una superficie di lavoro orizzontale, stabile e piana su cui collocare il dispositivo in sicurezza.
- Che sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU rack con un gruppo di continuità (UPS) nell'ambiente in cui si intende configurare il dispositivo.
- Avere accesso a un dispositivo fisico.


### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

* La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Data Box Edge. Per altre informazioni, vedere [Requisiti di sistema di Data Box Edge](data-box-gateway-system-requirements.md).

* Data Box Edge abbia una larghezza di banda Internet dedicata di almeno 20 Mbps sempre disponibile. La larghezza di banda non deve essere condivisa con altre applicazioni. Se si usa la limitazione della larghezza di banda della rete, per assicurarne il funzionamento è consigliabile usare una larghezza di banda Internet di almeno 32 Mbps.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Per creare una nuova risorsa Data Box Edge, eseguire la procedura seguente. 

Se si ha già una risorsa Data Box Edge per la gestione del dispositivo fisico, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Per creare la risorsa Data Box Edge, completare i passaggi seguenti nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere al portale di anteprima di Azure all'URL [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Selezionare la sottoscrizione che si vuole usare per l'anteprima di Data Box Edge. Selezionare l'area in cui si vuole distribuire la risorsa Data Box Edge. Nell'opzione **Data Box Edge** selezionare **Crea**.

    ![Cercare nel servizio Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Per la nuova risorsa immettere o selezionare le informazioni seguenti.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |Nome risorsa   | Nome descrittivo per identificare la risorsa.<br>Il nome della risorsa deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
    |Sottoscrizione    |La sottoscrizione viene collegata all'account di fatturazione. |
    |Gruppo di risorse  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Per altre informazioni, vedere [Gruppi di risorse di Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Località     |Per questa versione sono disponibili le località Stati Uniti orientali, Stati Uniti occidentali 2, Asia sud-orientale ed Europa occidentale. <br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|
    
    ![Creare una risorsa Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Selezionare **OK**.
 
La creazione della risorsa richiede alcuni minuti. Al termine della creazione della risorsa, si riceverà una notifica.


## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Data Box Edge è configurata e operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Data Box Edge e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Selezionare la risorsa creata e quindi selezionare **Panoramica**.

2. Selezionare **Genera chiave** per creare una chiave di attivazione. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.

    ![Ottenere una chiave di attivazione](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La chiave di attivazione scade tre giorni dopo la generazione. 
> - Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Edge, ad esempio:

> [!div class="checklist"]
> * Creazione di una nuova risorsa
> * Ottenimento della chiave di attivazione

Passare all'esercitazione successiva per informazioni su come installare Data Box Edge. 

> [!div class="nextstepaction"]
> [Installare Data Box Edge](./data-box-edge-deploy-install.md)



