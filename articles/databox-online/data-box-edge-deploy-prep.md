---
title: Esercitazione sulla preparazione del portale di Azure per la distribuzione di Data Box Edge | Microsoft Docs
description: La prima esercitazione per la distribuzione di Azure Data Box Edge include la preparazione del portale di Azure.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 79061caac3d598df79f383b9b13458ab9537cd81
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832800"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge-preview"></a>Esercitazione: Preparare la distribuzione di Azure Data Box Edge (anteprima)


Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Data Box Edge. Descrive come preparare il portale di Azure per la distribuzione della risorsa Data Box Edge. 

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 

### <a name="get-started"></a>Attività iniziali

Per distribuire il Data Box Edge, vedere le esercitazioni seguenti nella sequenza indicata.

| **#** | **In questo passaggio** | **Usare questi documenti** |
| --- | --- | --- | 
| 1. |**[Preparare il portale di Azure per Data Box Edge](data-box-edge-deploy-prep.md)** |Creare e configurare la risorsa di Data Box Edge prima di installare un dispositivo fisico Data Box Edge. |
| 2. |**[Installare Azure Data Box Edge](data-box-edge-deploy-install.md)**|Disimballare, installare in rack e cablare il dispositivo fisico Data Box Edge.  |
| 3. |**[Connettere, configurare e attivare Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Connettersi all'interfaccia utente Web locale, completare la configurazione del dispositivo e attivare il dispositivo. Il dispositivo è pronto per la configurazione di condivisioni SMB o NFS.  |
| 4. |**[Trasferire dati con Data Box Edge](data-box-edge-deploy-add-shares.md)** |Aggiungere condivisioni e connettersi alle condivisioni da SMB o NFS. |
| 5. |**[Trasformare dati con Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configurare i moduli di Edge nel dispositivo per trasformare i dati durante lo spostamento in Azure. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono elencati i prerequisiti di configurazione per la risorsa Data Box Edge, il dispositivo Data Box Edge e la rete del data center.

### <a name="for-the-data-box-edge-resource"></a>Per la risorsa Data Box Edge

Prima di iniziare, verificare che:

* La sottoscrizione di Microsoft Azure sia abilitata per la risorsa Data Box Edge.
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

### <a name="for-the-data-box-edge-device"></a>Per il dispositivo Data Box Edge

Prima di distribuire un dispositivo fisico, è necessario:

- Che sia disponibile 1 slot a U in un rack standard da 19 pollici nel data center per montare in rack il dispositivo. 
- Assicurarsi di avere accesso a una superficie di lavoro orizzontale, stabile e piana su cui collocare il dispositivo in sicurezza.
- Verificare che nell'ambiente in cui si intende effettuare l'installazione del dispositivo sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU (Power Distribution Unit) rack con un gruppo di continuità.
- Avere accesso a un dispositivo fisico.


### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

* La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Data Box Edge. Per altre informazioni, vedere i [requisiti di sistema di Data Box Edge](data-box-gateway-system-requirements.md).

* Data Box Edge deve avere una larghezza di banda Internet dedicata a 20 Mbps (o superiore) sempre disponibile. La larghezza di banda non deve essere condivisa con altre applicazioni. Se si usa la limitazione della larghezza di banda della rete, per assicurarne il funzionamento è consigliabile usare una larghezza di banda Internet ad almeno 32 Mbps.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Per creare una nuova risorsa Data Box Edge, eseguire la procedura seguente. 

Se si ha già una risorsa Data Box Edge per la gestione del dispositivo fisico, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Eseguire la procedura seguente nel portale di Azure per creare una risorsa Data Box.

1. Usare le credenziali di Microsoft Azure per accedere al portale di anteprima di Azure all'URL [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Selezionare la sottoscrizione che si vuole usare per l'anteprima di Data Box Edge. Selezionare l'area in cui si vuole distribuire la risorsa Data Box Edge. Nell'opzione **Data Box Edge** fare clic su **Crea**.

    ![Cercare nel servizio Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Per la nuova risorsa immettere o selezionare le informazioni seguenti.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |Nome risorsa   | Nome descrittivo per identificare la risorsa.<br>Il nome della risorsa deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
    |Sottoscrizione    |La sottoscrizione viene collegata all'account di fatturazione. |
    |Gruppo di risorse  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Località     |Per questa versione sono disponibili le località Stati Uniti orientali, Stati Uniti occidentali 2, Asia sud-orientale ed Europa occidentale. <br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|
    
    ![Crea risorsa Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Fare clic su **OK**.
 
La creazione della risorsa richiede alcuni minuti. Al termine della creazione, si riceverà la relativa notifica.


## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Ora che la risorsa Data Box Edge è configurata e funzionante, occorre ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Data Box Edge e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Fare clic sulla risorsa creata e quindi su **Panoramica**.

2. Fare clic su **Genera chiave** per creare una chiave di attivazione. Fare clic sull'icona di copia per copiare la chiave e salvarla per un utilizzo successivo.

    ![Ottenere una chiave di attivazione](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La chiave di attivazione scade 3 giorni dopo la generazione. 
> - Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Edge, ad esempio:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Passare all'esercitazione successiva per informazioni su come installare il Data Box Edge. 

> [!div class="nextstepaction"]
> [Installare un Data Box Edge](./data-box-edge-deploy-install.md)



