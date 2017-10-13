---
title: Distribuire SAP IDES EHP7 SP3 per SAP ERP 6.0 in Azure | Microsoft Docs
description: Distribuire SAP IDES EHP7 SP3 per SAP ERP 6.0 in Azure
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 91eed294077ff72d0760018b10c98f32db88f3be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Distribuire SAP IDES EHP7 SP3 per SAP ERP 6.0 in Azure
Questo articolo descrive come distribuire un sistema SAP IDES in esecuzione con SQL Server e il sistema operativo Windows in Azure tramite SAP Cloud Appliance Library (SAP CAL) 3.0. Gli screenshot illustrano il processo in dettaglio. Per distribuire una soluzione diversa, seguire la stessa procedura.

Per iniziare con SAP CAL, visitare il sito Web [SAP Cloud Appliance Library](https://cal.sap.com/). Esiste anche un blog di SAP sulla nuova [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
A partire dal 29 maggio 2017, per distribuire SAP CAL è possibile usare il modello di distribuzione Azure Resource Manager, oltre al modello di distribuzione classica, non preferito. È consigliabile usare il nuovo modello di distribuzione Resource Manager, ignorando il modello di distribuzione classica.

Se si è già creato un account di SAP CAL che usa il modello classico, *è necessario creare un altro account di SAP CAL*. Questo account deve eseguire la distribuzione in Azure esclusivamente tramite il modello Resource Manager.

Dopo l'accesso a SAP CAL, la prima pagina visualizzata è di solito la pagina **Solutions** (Soluzioni). Le soluzioni offerte con SAP CAL sono in costante aumento. Per individuare la soluzione voluta potrebbe quindi essere necessario scorrere la pagina per alcune righe. La soluzione SAP IDES basata su Windows evidenziata, disponibile esclusivamente in Azure, illustra il processo di distribuzione:

![Soluzioni SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Creare un account in SAP CAL
1. Per accedere a SAP CAL per la prima volta, usare un account SAP S-User o un altro account utente registrato con SAP. Definire quindi un account SAP CAL da usare per distribuire appliance in Azure con SAP CAL. Nella definizione dell'account è necessario:

    a. Selezionare il modello di distribuzione in Azure (Resource Manager o classica).

    b. Immettere la sottoscrizione di Azure. Un account SAP CAL può essere assegnato a una sola sottoscrizione. Se sono necessarie più sottoscrizioni, è necessario creare più account SAP CAL.
    
    c. Concedere a SAP CAL l'autorizzazione alla distribuzione all'interno della sottoscrizione di Azure.

    > [!NOTE]
    I passaggi successivi illustrano come creare un account SAP CAL per le distribuzioni Resource Manager. Se è già disponibile un account SAP CAL collegato al modello di distribuzione classica, per creare un nuovo account SAP CAL è *necessario* seguire questa procedura. Il nuovo account SAP CAL deve eseguire la distribuzione nel modello Resource Manager.

2. Per la creazione di un nuovo account di SAP CAL, nella pagina **Accounts** (Account) sono disponibili due opzioni per Azure: 

    a. **Microsoft Azure (classic)** (Microsoft Azure (classica)), il modello di distribuzione classica, non rappresenta più il modello di distribuzione preferito.

    b. **Microsoft Azure** corrisponde al nuovo modello di distribuzione Resource Manager.

    ![Account SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Per distribuire il modello Resource Manager, selezionare **Microsoft Azure**.

    ![Account SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. Immettere l'**ID sottoscrizione** di Azure, reperibile nel Portale di Azure. 

    ![ID sottoscrizione di SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. Per autorizzare SAP CAL alla distribuzione nella sottoscrizione di Azure definita, fare clic su **Authorize** (Autorizza). Nella scheda del browser viene visualizzata la pagina seguente:

    ![Accesso ai servizi cloud di Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Se sono elencati più utenti, scegliere l'account Microsoft collegato come coamministratore della sottoscrizione di Azure selezionata. Nella scheda del browser viene visualizzata la pagina seguente:

    ![Conferma dei servizi cloud di Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Fare clic **Accept**. Se l'autorizzazione ha esito positivo, viene visualizzata nuovamente la definizione dell'account SAP CAL. Dopo un breve periodo, viene visualizzato un messaggio di conferma del completamento del processo di autorizzazione.

7. Per assegnare a un utente l'account SAP CAL appena creato, immettere l'**ID utente** nella casella di testo a destra e fare clic su **Add** (Aggiungi). 

    ![Associazione tra account e utente](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. Per associare l'account all'utente usato per accedere a SAP CAL, fare clic su **Review** (Verifica). 

9. Per creare l'associazione tra l'utente e l'account SAP CAL appena creato, fare clic su **Create** (Crea).

    ![Associazione tra utente e account](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

È stato creato un account SAP CAL in grado di:

- Usare il modello di distribuzione Resource Manager.
- Distribuire i sistemi SAP nella sottoscrizione di Azure.

> [!NOTE]
Prima di distribuire la soluzione SAP IDES basata su Windows e SQL Server, potrebbe essere necessario registrarsi per una sottoscrizione di SAP CAL. In caso contrario, la soluzione potrebbe essere visualizzata come **bloccata** nella pagina Panoramica.

### <a name="deploy-a-solution"></a>Distribuire una soluzione
1. Dopo aver configurato un account di SAP CAL, selezionare la **soluzione SAP IDES in Windows e SQL Server**. Fare clic su **Create Instance** (Crea istanza) e confermare l'accettazione di termini e condizioni di utilizzo. 

2. Nella pagina **Basic Mode: Create Instance** (Modalità di base: crea istanza) è necessario:

    a. Immettere un **nome** di istanza.

    b. Selezionare un'**area** di Azure. Per visualizzare le diverse aree di Azure disponibili, potrebbe essere necessaria una sottoscrizione di SAP CAL.

    c.  Immettere la **password** master per la soluzione, come illustrato:

    ![Modalità SAP CAL di base: creare un'istanza](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Fare clic su **Crea**. Dopo alcuni minuti, a seconda della dimensione e della complessità della soluzione, di cui SAP CAL fornisce una stima, la soluzione viene visualizzata come attiva e pronta all'uso: 

    ![Istanze di SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Per individuare il gruppo di risorse e tutti i suoi oggetti creati con SAP CAL, passare al Portale di Azure. È possibile che le macchine virtuali vengano avviate con lo stesso nome di istanza assegnato in SAP CAL.

    ![Oggetti del gruppo di risorse](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. Nel portale di SAP CAL passare alle istanze distribuite e fare clic su **Connect** (Connetti). Verrà visualizzata la finestra popup seguente: 

    ![Finestra Connect to the Instance (Connettersi all'istanza)](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Prima che sia possibile usare una delle opzioni per la connessione ai sistemi distribuiti, è necessario fare clic su **Getting Started Guide** (Guida introduttiva). La documentazione cita gli utenti per ognuno dei metodi di connettività. Le password per questi utenti corrispondono alla password master definita all'inizio del processo di distribuzione. Nella documentazione sono elencati altri utenti funzionali con le relative password. È possibile usare questi utenti per accedere al sistema distribuito.

    ![Documentazione di benvenuto di SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

In poche ore un sistema SAP IDES integro verrà distribuito in Azure.

Se è stata acquistata una sottoscrizione SAP CAL, SAP offre il supporto completo alle distribuzioni in Azure tramite SAP CAL. La coda di supporto è BC-VCM-CAL.

