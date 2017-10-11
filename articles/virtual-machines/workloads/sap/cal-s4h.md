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
ms.openlocfilehash: 4788fa14a6c49d39b5a3096a69b6738f4a5d8cca
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Distribuire SAP S/4HANA o BW/4HANA in Azure
Questo articolo descrive come distribuire S/4HANA in Azure tramite SAP Cloud Appliance Library (SAP CAL) 3.0. Per distribuire altre soluzioni basate su SAP HANA, ad esempio BW/4HANA, seguire la stessa procedura.

> [!NOTE]
Per altre informazioni su SAP CAL, visitare il sito Web [SAP Cloud Appliance Library](https://cal.sap.com/). Esiste anche un blog di SAP su [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
A partire dal 29 maggio 2017, per distribuire SAP CAL è possibile usare il modello di distribuzione Azure Resource Manager, oltre al modello di distribuzione classica, non preferito. È consigliabile usare il nuovo modello di distribuzione Resource Manager, ignorando il modello di distribuzione classica.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Procedura dettagliata per distribuire la soluzione

La sequenza di screenshot che segue illustra come distribuire S/4HANA in Azure tramite SAP CAL. Per altre soluzioni, ad esempio BW/4HANA, il processo è lo stesso.

La pagina **Solutions** (Soluzioni) illustra alcune delle soluzioni SAP CAL basate su HANA disponibili in Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated Appliance** è nella riga centrale:

![Soluzioni SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Creare un account in SAP CAL
1. Per accedere a SAP CAL per la prima volta, usare un account SAP S-User o un altro account utente registrato con SAP. Definire quindi un account SAP CAL da usare per distribuire appliance in Azure con SAP CAL. Nella definizione dell'account è necessario:

    a. Selezionare il modello di distribuzione in Azure (Resource Manager o classica).

    b. Immettere la sottoscrizione di Azure. Un account SAP CAL può essere assegnato a una sola sottoscrizione. Se sono necessarie più sottoscrizioni, è necessario creare più account SAP CAL.

    c. Concedere a SAP CAL l'autorizzazione alla distribuzione all'interno della sottoscrizione di Azure.

    > [!NOTE]
    I passaggi successivi illustrano come creare un account SAP CAL per le distribuzioni Resource Manager. Se è già disponibile un account SAP CAL collegato al modello di distribuzione classica, per creare un nuovo account SAP CAL è *necessario* seguire questa procedura. Il nuovo account SAP CAL deve eseguire la distribuzione nel modello Resource Manager.

2. Creare un nuovo account SAP CAL. La pagina **Accounts** (Account) presenta tre scelte per Azure: 

    a. **Microsoft Azure (classic)** (Microsoft Azure (classica)), il modello di distribuzione classica, non rappresenta più il modello di distribuzione preferito.

    b. **Microsoft Azure** corrisponde al nuovo modello di distribuzione Resource Manager.

    c. **Windows Azure operated by 21Vianet** (Windows Azure gestito da 21Vianet) è un'opzione disponibile in Cina che usa il modello di distribuzione classica.

    Per distribuire il modello Resource Manager, selezionare **Microsoft Azure**.

    ![Dettagli dell'account SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

3. Immettere l'**ID sottoscrizione** di Azure, reperibile nel Portale di Azure.

   ![Account SAP CAL](./media/cal-s4h/s4h-pic3c.png)

4. Per autorizzare SAP CAL alla distribuzione nella sottoscrizione di Azure definita, fare clic su **Authorize** (Autorizza). Nella scheda del browser viene visualizzata la pagina seguente:

   ![Accesso ai servizi cloud di Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

5. Se sono elencati più utenti, scegliere l'account Microsoft collegato come coamministratore della sottoscrizione di Azure selezionata. Nella scheda del browser viene visualizzata la pagina seguente:

   ![Conferma dei servizi cloud di Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

6. Fare clic **Accept**. Se l'autorizzazione ha esito positivo, viene visualizzata nuovamente la definizione dell'account SAP CAL. Dopo un breve periodo, viene visualizzato un messaggio di conferma del completamento del processo di autorizzazione.

7. Per assegnare a un utente l'account SAP CAL appena creato, immettere l'**ID utente** nella casella di testo a destra e fare clic su **Add** (Aggiungi).

   ![Associazione tra account e utente](./media/cal-s4h/s4h-pic8a.png)

8. Per associare l'account all'utente usato per accedere a SAP CAL, fare clic su **Review** (Verifica). 
 
9. Per creare l'associazione tra l'utente e l'account SAP CAL appena creato, fare clic su **Create** (Crea).

   ![Associazione tra utente e account SAP CAL](./media/cal-s4h/s4h-pic9b.png)

È stato creato un account SAP CAL in grado di:

- Usare il modello di distribuzione Resource Manager.
- Distribuire i sistemi SAP nella sottoscrizione di Azure.

È ora possibile iniziare a distribuire S/4HANA nella sottoscrizione utente in Azure.

> [!NOTE]
Prima di continuare, determinare se sono disponibili quote core di Azure per macchine virtuali di serie H di Azure. Attualmente, per distribuire alcune soluzioni basate su SAP HANA, SAP CAL usa macchine virtuali di serie H di Azure. È possibile tuttavia che la sottoscrizione di Azure in uso non abbia quote core per la serie H. In questo caso, è necessario contattare il supporto tecnico di Azure per ottenere una quota di almeno 16 serie H.

> [!NOTE]
Quando si distribuisce una soluzione in Azure con SAP CAL, può accadere che sia possibile scegliere una sola area di Azure. Per eseguire la distribuzione in aree di Azure diverse da quella suggerita da SAP CAL, è necessario acquistare una sottoscrizione CAL da SAP. Potrebbe anche essere necessario aprire un messaggio con SAP per farsi abilitare l'account CAL per il recapito in aree di Azure diverse da quelle suggerite inizialmente.

### <a name="deploy-a-solution"></a>Distribuire una soluzione

Verrà ora distribuita una soluzione dalla pagina **Solutions** (Soluzioni) di SAP CAL. Con SAP CAL sono disponibili due sequenze di distribuzione:

- Una sequenza di base, che usa una sola pagina per definire il sistema da distribuire
- Una sequenza avanzata, che offre opzioni specifiche per le dimensioni delle macchine virtuali 

In questo articolo verrà illustrato il percorso di distribuzione di base.

1. Nella pagina **Account Details** (Dettagli account) è necessario:

    a. Selezionare un account SAP CAL. Usare un account associato alla distribuzione con il modello di distribuzione Resource Manager.

    b. Immettere un **nome** di istanza.

    c. Selezionare un'**area** di Azure. SAP CAL suggerisce un'area. Se è necessaria un'altra area di Azure e non si ha una sottoscrizione SAP CAL, è necessario ordinare una sottoscrizione CAL a SAP.

    d. Immettere una **password** master di otto o nove caratteri per la soluzione. La password viene usata per gli amministratori dei diversi componenti.

   ![Modalità SAP CAL di base: creare un'istanza](./media/cal-s4h/s4h-pic10a.png)

2. Fare clic su **Create** (Crea). Nella finestra di messaggio visualizzata fare clic su **OK**.

   ![Dimensioni delle macchine virtuali supportate da SAP CAL](./media/cal-s4h/s4h-pic10b.png)

3. Nella finestra di dialogo **Private Key** (Chiave privata) fare clic su **Store** (Archivia) per archiviare la chiave privata in SAP CAL. Per usare la password di protezione per la chiave privata, fare clic su **Download** (Scarica). 

   ![Chiave privata SAP CAL](./media/cal-s4h/s4h-pic10c.png)

4. Leggere il messaggio in **Warning** (Avviso) e fare clic su **OK**.

   ![Avviso di SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Verrà ora eseguita la distribuzione. Dopo alcuni minuti, a seconda della dimensione e della complessità della soluzione, di cui SAP CAL fornisce una stima, la soluzione viene visualizzata come attiva e pronta all'uso.

5. Per individuare le macchine virtuali raccolte con le altre risorse associate in un unico gruppo di risorse, passare al Portale di Azure: 

   ![Oggetti SAP CAL distribuiti nel nuovo portale](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. Nel portale SAP CAL lo stato è visualizzato come **Active** (Attivo). Per connettersi alla soluzione, fare clic su **Connect** (Connetti). All'interno di questa soluzione sono distribuite opzioni diverse per la connessione a componenti diversi.

   ![Istanze di SAP CAL](./media/cal-s4h/active_solution.png)

7. Prima che sia possibile usare una delle opzioni per la connessione ai sistemi distribuiti, è necessario fare clic su **Getting Started Guide** (Guida introduttiva). 

   ![Finestra Connect to the Instance (Connettersi all'istanza)](./media/cal-s4h/connect_to_solution.png)

    La documentazione cita gli utenti per ognuno dei metodi di connettività. Le password per questi utenti corrispondono alla password master definita all'inizio del processo di distribuzione. Nella documentazione sono elencati altri utenti funzionali con le relative password. È possibile usare questi utenti per accedere al sistema distribuito. 

    Se ad esempio si usa la GUI SAP preinstallata nel computer Desktop remoto Windows, il sistema S/4 può avere un aspetto simile al seguente:

   ![SM50 nella GUI SAP preinstallata](./media/cal-s4h/gui_sm50.png)

    Se invece si usa DBA Cockpit, l'istanza può avere un aspetto simile al seguente:

   ![SM50 nella GUI SAP DBA Cockpit](./media/cal-s4h/dbacockpit.png)

In poche ore un'appliance SAP S/4 integra verrà distribuita in Azure.

Se è stata acquistata una sottoscrizione SAP CAL, SAP offre il supporto completo alle distribuzioni in Azure tramite SAP CAL. La coda di supporto è BC-VCM-CAL.







