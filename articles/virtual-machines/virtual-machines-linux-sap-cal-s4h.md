---
title: Distribuire SAP S/4 HANA o BW/4 HANA in una macchina virtuale di Azure | Microsoft Docs
description: Distribuire SAP S/4HANA o BW/4HANA in una macchina virtuale di Azure
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 6f345bb816a3fd6f6fb8672b9a43a0d075bd94eb
ms.openlocfilehash: 939c051cad98590acffb6e550ca45bf5fec90d7e
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>Distribuire SAP S/4HANA o BW/4HANA in Microsoft Azure
In questo articolo viene descritto come distribuire S/4HANA in Microsoft Azure tramite SAP Cloud Appliance Library (SAP CAL) 3.0. Sul piano procedurale, per le altre soluzioni basate su SAP HANA, come BW/4HANA, la distribuzione funziona nello stesso modo. Semplicemente, si sceglie una soluzione diversa.

> [!NOTE]
Per altre informazioni su SAP Cloud Appliance Library, vedere l'[home page del relativo sito](https://cal.sap.com/). Esiste anche un blog di SAP su [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

## <a name="step-by-step-process-to-deploy-the-solution"></a>Procedura dettagliata per distribuire la soluzione

Gli screenshot seguenti illustrano come distribuire S/4HANA in Azure. Per altre soluzioni BW/4HANA, la procedura è sempre la stessa.

Il primo screenshot mostra tutte le soluzioni SAP CAL basate su HANA disponibili in Azure. Si noti **SAP S/4HANA on-premises edition** nella parte inferiore.

![Screenshot della finestra Solutions di SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

Innanzitutto, creare un nuovo account SAP CAL. In **Accounts** (Account) sono presenti due scelte per Azure: Microsoft Azure e un'opzione di Azure gestita da 21Vianet. Per questo esempio, scegliere **Microsoft Azure**.

![Screenshot della finestra Accounts (Account) di SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Quindi, immettere l'ID sottoscrizione di Azure, reperibile nel Portale di Azure. In seguito, scaricare un certificato di gestione di Azure.

![Screenshot della finestra Accounts (Account) di SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
Per trovare l'ID sottoscrizione di Azure, usare il portale di Azure classico, non la versione più recente. Questo perché SAP CAL non è ancora adattato a questo nuovo modello e richiede il portale classico per poter usare i certificati di gestione.

Lo screenshot seguente mostra il portale classico. In **IMPOSTAZIONI**, selezionare la scheda **SOTTOSCRIZIONI** per trovare l'ID sottoscrizione da inserire nella finestra Accounts (Account) di SAP CAL.

![Screenshot del portale di Azure classico](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

In **IMPOSTAZIONI**, passare alla scheda **CERTIFICATI DI GESTIONE**. Caricando un certificato di gestione, SAP CAL riceve le autorizzazioni necessarie per creare macchine virtuali in una sottoscrizione del cliente. Si carica il certificato di gestione precedentemente scaricato da SAP CAL.

![Screenshot del portale di Azure classico, scheda Certificati di gestione](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Viene visualizzata una finestra di dialogo in cui selezionare il file di certificato scaricato.

![Screenshot della finestra di dialogo Carica certificato di gestione](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Una volta caricato il certificato, in SAP CAL è possibile testare la connessione tra SAP CAL e la sottoscrizione Azure. Un piccolo messaggio popup indicherà se la connessione è valida.

![Screenshot della finestra Accounts (Account) di SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Successivamente, selezionare una soluzione da distribuire e creare un'istanza.
Immettere un nome da assegnare all'istanza, scegliere un'area di Azure e definire la password master della soluzione.

![Screenshot della finestra Solutions (Soluzioni) di SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Dopo alcuni minuti, a seconda della dimensione e della complessità (stimate da SAP CAL), la soluzione viene visualizzata come attiva e pronta all'uso.

![Screenshot della finestra Instances (Istanze) di SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

È possibile visualizzare alcuni dettagli della soluzione, ad esempio la tipologia di macchine virtuali distribuite. In questo caso sono state create tre macchine virtuali di Azure diverse per dimensioni e scopo.

![Screenshot della finestra Instances (Istanze) di SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Nel portale di Azure classico è possibile che le macchine virtuali vengano avviate con lo stesso nome di istanza assegnato in SAP CAL.

![Screenshot che mostra tre macchine virtuali nel portale di Azure classico](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

A questo punto è possibile connettersi alla soluzione tramite l'apposito pulsante disponibile nel portale di SAP CAL. Nella finestra di dialogo è presente il collegamento a un manuale dell'utente che descrive tutte le credenziali predefinite per l'utilizzo della soluzione.

![Screenshot della finestra di dialogo Connect to the Instance (Connettersi all'istanza)](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Un'altra opzione consiste nell'accedere alla macchina virtuale client di Windows e avviare, ad esempio, la GUI SAP preconfigurata.

![Screenshot della GUI SAP preconfigurata](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

