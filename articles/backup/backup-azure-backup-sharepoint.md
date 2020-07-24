---
title: Eseguire il backup di una farm di SharePoint in Azure con DPM
description: Questo articolo offre una panoramica sulla protezione del server DPM/Backup di Azure di una farm di SharePoint in Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: b326ce15cd605f4f768a03b95337bbc5bd6aaabc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032885"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Eseguire il backup di una farm di SharePoint in Azure con DPM

Il backup di una farm di SharePoint in Azure si esegue tramite System Center Data Protection Manager (DPM) in modo analogo al backup delle altre origini dati. Backup di Azure offre flessibilità nella pianificazione di backup per creare punti di backup quotidiani, settimanali, mensili o annuali e offre diverse opzioni in termini di criteri di conservazione per i vari intervalli di backup. DPM offre la possibilità di archiviare copie dei dischi locali per obiettivi di tempi di ripristino (RTO) rapidi e di archiviare copie in Azure per una conservazione economicamente conveniente e a lungo termine.

Il backup di SharePoint in Azure con DPM è un processo molto simile al backup locale di SharePoint in DPM. In questo articolo vengono indicate alcune considerazioni specifiche per Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versioni supportate di SharePoint e relativi scenari di protezione

Per un elenco delle versioni di SharePoint supportate e delle versioni di DPM necessarie per eseguirne il backup, vedere [What can DPM back up?](/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup) (Elementi dei quali DPM può eseguire il backup)

## <a name="before-you-start"></a>Prima di iniziare

È necessario verificare alcuni aspetti prima di eseguire il backup di una farm di SharePoint in Azure.

### <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi che tutti i [prerequisiti per l'uso di Backup di Microsoft Azure](backup-azure-dpm-introduction.md#prerequisites-and-limitations) per la protezione dei carichi di lavoro siano stati soddisfatti. Alcune attività per i prerequisiti includono: creare insiemi di credenziali di backup, scaricare credenziali di insiemi di credenziali, installare l'agente di Backup di Azure e registrare il server di Backup di Azure o DPM con l'insieme di credenziali.

Ulteriori prerequisiti e limitazioni sono disponibili nell'articolo [backup di SharePoint con DPM](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations) .

## <a name="configure-backup"></a>Configurare il backup

Per eseguire il backup della farm di SharePoint configurare la protezione per SharePoint tramite ConfigureSharePoint.exe e creare un gruppo protezione dati in DPM. Per istruzioni, vedere [configurare il backup](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) nella documentazione di DPM.

## <a name="monitoring"></a>Monitoraggio

Per monitorare il processo di backup, seguire le istruzioni in [monitoraggio del backup di DPM](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring) .

## <a name="restore-sharepoint-data"></a>Ripristinare i dati di SharePoint

Per informazioni su come ripristinare un elemento di SharePoint da un disco con DPM, vedere [ripristinare i dati di SharePoint](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Ripristinare un database di SharePoint da Azure tramite DPM

1. Per ripristinare un database del contenuto di SharePoint, scorrere i vari punti di ripristino (come illustrato in precedenza) e selezionare il punto di ripristino che si vuole ripristinare.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Fare doppio clic sul punto di ripristino di SharePoint per visualizzare le informazioni del catalogo di SharePoint disponibili.

   > [!NOTE]
   > Poiché la farm di SharePoint è protetta per la conservazione a lungo termine in Azure, non è disponibile nessuna informazione sul catalogo (metadati) nel server DPM. Di conseguenza, ogni volta che si vuole ripristinare un database del contenuto di SharePoint temporizzato, si deve ricatalogare la farm di SharePoint.
   >
   >
3. Fare clic su **Ricatalogazione**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Viene visualizzata la finestra di stato di **Ricatalogazione cloud** .

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Dopo aver completato la catalogazione, viene visualizzato lo stato *Operazione completata*. Fare clic su **Close**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Fare clic sull'oggetto di SharePoint visualizzato nella scheda **Ripristino** di DPM per ottenere la struttura del database del contenuto. Fare clic con il pulsante destro del mouse sull'elemento e scegliere **Ripristina**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. A questo punto seguire i passaggi di ripristino illustrati precedentemente in questo articolo per ripristinare il database del contenuto di SharePoint dal disco.

## <a name="switching-the-front-end-web-server"></a>Cambio del server Web front-end

Se si dispone di più di un server Web front-end e si desidera cambiare il server utilizzato da DPM per proteggere la farm, seguire le istruzioni riportate in [commutazione del server Web front-end](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti su server di Backup di Azure e DPM](backup-azure-dpm-azure-server-faq.md)
* [Risolvere i problemi relativi a System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
