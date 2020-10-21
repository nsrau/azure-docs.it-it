---
title: Creare un'area di lavoro Log Analytics nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un'area di lavoro Log Analytics per abilitare soluzioni di gestione e la raccolta dei dati dagli ambienti cloud e locali nel portale di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 0cf7c89c23b3df51dce2710be50c3c62f8f1e3cf
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320090"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Log Analytics nel portale di Azure
Usare il menu **Aree di lavoro Log Analytics** per creare un'area di lavoro Log Analytics tramite il portale di Azure. Un'area di lavoro Log Analytics è un ambiente univoco per i dati di log di Monitoraggio di Azure. Ogni area di lavoro ha un proprio repository di dati e una propria configurazione, mentre le origini dati e le soluzioni sono configurate per l'archiviazione dei dati in un'area di lavoro specifica. È necessaria un'area di lavoro Log Analytics se si intende raccogliere dati dalle origini seguenti:

* Risorse di Azure nella sottoscrizione
* Computer locali monitorati tramite System Center Operations Manager
* Raccolte di dispositivi da Configuration Manager 
* Dati di diagnostica o di log dall'archiviazione di Azure

Per altre origini, ad esempio macchine virtuali di Azure e macchine virtuali di Windows o Linux presenti nell'ambiente, vedere gli argomenti seguenti:

*  [Raccogliere dati dalle macchine virtuali di Azure](./quick-collect-azurevm.md) 
*  [Raccogliere dati dal computer Linux ibrido](./quick-collect-linux-computer.md)
*  [Raccogliere dati dal computer Windows ibrido](quick-collect-windows-computer.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.

    ![Portale di Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Fare clic su **Aggiungi** e quindi selezionare le opzioni per gli elementi seguenti:

   * Specificare un nome per la nuova **area di lavoro Log Analytics**, ad esempio *DefaultLAWorkspace*. Questo nome deve essere univoco a livello globale in tutte le sottoscrizioni di Monitoraggio di Azure.
   * Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   * Per **Gruppo di risorse** scegliere di usare un gruppo di risorse esistente già configurato oppure crearne uno nuovo.  
   * Selezionare una **località** disponibile.  Per altre informazioni, vedere in quali [aree geografiche Log Analytics è disponibile](https://azure.microsoft.com/regions/services/) e cercare Monitoraggio di Azure dal campo **Ricerca di un prodotto**.  
   * Se si sta creando un'area di lavoro in una nuova sottoscrizione creata dopo il 2 aprile 2018, verrà automaticamente usato il piano di determinazione dei prezzi *Per GB* e non sarà disponibile l'opzione che consente di selezionare un piano tariffario.  Se si sta creando un'area di lavoro per una sottoscrizione esistente creata prima del 2 aprile o per una sottoscrizione collegata a un Contratto Enterprise (EA) esistente, selezionare il piano tariffario preferito.  Per altre informazioni sui piani specifici, vedere [Dettagli prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Creare il pannello della risorsa Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Dopo aver specificato le informazioni necessarie nel riquadro **area di lavoro Log Analytics**, fare clic su **OK**.  

Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu. 

## <a name="troubleshooting"></a>Risoluzione dei problemi
Quando si crea un'area di lavoro eliminata negli ultimi 14 giorni e in [stato di eliminazione temporanea](../platform/delete-workspace.md#soft-delete-behavior), l'operazione potrebbe avere risultati diversi a seconda della configurazione dell'area di lavoro:
1. Se si specificano lo stesso nome dell'area di lavoro, gruppo di risorse, sottoscrizione e area dell'area di lavoro eliminata, l'area di lavoro verrà ripristinata, inclusi i dati, la configurazione e gli agenti connessi.
2. Se si usa lo stesso nome dell'area di lavoro, ma un gruppo di risorse, una sottoscrizione o un'area diversa, si otterrà un errore *Questo nome dell'area di lavoro è già in uso. Provarne un altro*. Per eseguire l'override dell'eliminazione temporanea ed eliminare definitivamente l'area di lavoro e creare una nuova area di lavoro con lo stesso nome, attenersi alla procedura seguente per ripristinare prima l'area di lavoro ed eseguire l'eliminazione permanente:
   - [Recuperare](../platform/delete-workspace.md#recover-workspace) l'area di lavoro
   - [Eliminare definitivamente](../platform/delete-workspace.md#permanent-workspace-delete) l'area di lavoro
   - Creare una nuova area di lavoro usando il nome della stessa area di lavoro

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un'area di lavoro, è possibile configurare la raccolta di dati di telemetria di monitoraggio, eseguire ricerche nei log per analizzare i dati e aggiungere una soluzione di gestione per fornire informazioni analitiche dettagliate e dati aggiuntivi. 

* Vedere [monitorare l'integrità dell'area di lavoro log Analytics in monitoraggio di Azure](../platform/monitor-workspace.md) creare regole di avviso per monitorare l'integrità dell'area di lavoro. 
* Vedere [raccogliere i log e le metriche dei servizi di Azure da usare in log Analytics](../platform/resource-logs.md#send-to-log-analytics-workspace) per abilitare la raccolta di dati dalle risorse di azure con diagnostica di Azure o archiviazione di Azure.
