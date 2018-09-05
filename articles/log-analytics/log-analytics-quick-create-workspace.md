---
title: Creare un'area di lavoro di Log Analytics nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un'area di lavoro di Log Analytics per abilitare soluzioni di gestione e la raccolta dei dati dagli ambienti cloud e locali nel portale di Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptal
ms.date: 08/23/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: bdf1d1a62bd8e2e1d0a0a8ad30f2d4c4833be0e4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125094"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Creare un'area di lavoro di Log Analytics nel portale di Azure
Nel portale di Azure è possibile configurare un'area di lavoro di Log Analytics, ovvero un ambiente di Log Analytics univoco che contiene un archivio dati, origini dati e soluzioni.  I passaggi descritti in questo articolo sono obbligatori se si intende raccogliere i dati dalle origini seguenti:

* Risorse di Azure nella sottoscrizione
* Computer locali monitorati tramite System Center Operations Manager
* Raccolte di dispositivi da System Center Configuration Manager 
* Dati di diagnostica o dei log dall'archiviazione di Azure

Per altre origini, ad esempio macchine virtuali di Azure e macchine virtuali di Windows o Linux presenti nell'ambiente, vedere gli argomenti seguenti:

*  [Raccogliere dati dalle macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md) 
*  [Raccogliere dati dal computer Linux ibrido](log-analytics-quick-collect-linux-computer.md)
*  [Raccogliere dati dal computer Windows ibrido](log-analytics-quick-collect-windows-computer.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.

    ![Portale di Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)
  
2. Fare clic su **Crea** e quindi selezionare le opzioni per gli elementi seguenti:

  * Specificare un nome per la nuova **area di lavoro di OMS**, ad esempio *DefaultLAWorkspace*. 
  * Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
  * Per **Gruppo di risorse** scegliere di usare un gruppo di risorse esistente già configurato oppure crearne uno nuovo.  
  * Selezionare una **località** disponibile.  Per altre informazioni, vedere le [are in cui è disponibile Log Analytics](https://azure.microsoft.com/regions/services/).
  * Se si sta creando un'area di lavoro in una nuova sottoscrizione creata dopo il 2 aprile 2018, verrà automaticamente usato il piano di determinazione dei prezzi *Per GB* e non sarà disponibile l'opzione che consente di selezionare un piano tariffario.  Se si sta creando un'area di lavoro per una sottoscrizione esistente creata prima del 2 aprile o per una sottoscrizione collegata a un Contratto Enterprise (EA) esistente, selezionare il piano tariffario preferito.  Per altre informazioni sui piani specifici, vedere [Dettagli prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)  

3. Dopo aver specificato le informazioni necessarie nel riquadro **Area di lavoro di OMS**, fare clic su **OK**.  

Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu. 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un'area di lavoro, è possibile configurare la raccolta di dati di telemetria di monitoraggio, eseguire ricerche nei log per analizzare i dati e aggiungere una soluzione di gestione per fornire informazioni analitiche dettagliate e dati aggiuntivi. 

* Per abilitare la raccolta di dati dalle risorse di Azure con Diagnostica di Azure o l'archiviazione di Azure, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](log-analytics-azure-storage.md).  
* [Aggiungere System Center Operations Manager come origine dati](log-analytics-om-agents.md) per raccogliere i dati da agenti di creazione report per il gruppo di gestione Operations Manager e archiviarli nell'area di lavoro di Log Analytics. 
* Connettere [Configuration Manager](log-analytics-sccm.md) per importare computer che sono membri di raccolte nella gerarchia.  
* Esaminare le [soluzioni di gestione](log-analytics-add-solutions.md) disponibili e verificare come aggiungere o rimuovere una soluzione dall'area di lavoro.
