---
title: Applicare gli aggiornamenti nello Stack di Azure | Documenti Microsoft
description: Informazioni su come importare e installare pacchetti di aggiornamento di Microsoft per un sistema integrato dello Stack di Azure.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: b00bd606faaffaad30ff6cea3bcf47dc85282f69
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="apply-updates-in-azure-stack"></a>Applicare gli aggiornamenti nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

Come operatore di Stack di Azure, è possibile applicare riquadro pacchetti per lo Stack di Azure tramite l'aggiornamento nel portale di amministrazione di Microsoft update. È necessario scaricare il pacchetto di aggiornamento di Microsoft, importare i file del pacchetto allo Stack di Azure e quindi installare il pacchetto di aggiornamento. 

## <a name="download-the-update-package"></a>Scaricare il pacchetto di aggiornamento

Quando un pacchetto di aggiornamento di Microsoft per lo Stack di Azure è disponibile, scaricare il pacchetto in un percorso raggiungibile dallo Stack di Azure ed esaminare il contenuto del pacchetto. Un pacchetto di aggiornamento prevede in genere i file seguenti:

- Un autoestraente *PackageName*file .exe. Questo file contiene il payload per l'aggiornamento, ad esempio l'aggiornamento cumulativo più recente per Windows Server.   
- Corrispondente *PackageName*file con estensione bin. Questi file forniscono la compressione per il payload di cui è associato il *PackageName*file .exe. 
- Un file Metadata.xml. Questo file contiene informazioni essenziali sull'aggiornamento, ad esempio il server di pubblicazione, nome, prerequisito, dimensioni e percorso URL del supporto tecnico.

## <a name="import-and-install-updates"></a>Importare e installare gli aggiornamenti

La procedura seguente viene illustrato come importare e installare i pacchetti di aggiornamento nel portale di amministrazione.

> [!IMPORTANT]
> È consigliabile inviare una notifica agli utenti di tutte le operazioni di manutenzione e pianificare le operazioni di manutenzione durante le ore non lavorative quanto possibile. Sia i carichi di lavoro di utente e le operazioni del portale, possono influire sulle operazioni di manutenzione.

1. Nel portale di amministrazione, selezionare **più servizi**. Quindi, sotto il **dati e archiviazione** categoria, seleziona **gli account di archiviazione**. (O, nella casella del filtro, iniziare a digitare **gli account di archiviazione**e selezionarlo.)

    ![Viene illustrato dove trovare gli account di archiviazione nel portale](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Nella casella del filtro, digitare **aggiornare**e selezionare il **updateadminaccount** account di archiviazione.

    ![Viene illustrato come eseguire la ricerca di updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. Account di archiviazione dei dettagli, in **servizi**selezionare **BLOB**.
 
    ![Viene illustrato come ottenere ai BLOB dell'account di archiviazione](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. In **servizio Blob**selezionare **+ contenitore** per creare un contenitore. Immettere un nome (ad esempio *aggiornamento 1709*), quindi selezionare **OK**.
 
     ![Viene illustrato come aggiungere un contenitore nell'account di archiviazione](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Dopo la creazione del contenitore, fare clic sul nome di contenitore e quindi fare clic su **caricare** per caricare i file di pacchetto per il contenitore.
 
    ![Viene illustrato come caricare i file di pacchetto](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. In **caricamento blob**, fare clic sull'icona della cartella, passare al file .exe del pacchetto di aggiornamento e quindi fare clic su **aprire** nella finestra di Esplora file.
  
7. In **caricamento blob**, fare clic su **caricare**. 
 
    ![Viene illustrato come caricare ogni file di pacchetto](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Ripetere i passaggi 6 e 7 per la *PackageName*con estensione bin e i file Metadata.xml. 
9. Al termine, è possibile esaminare le notifiche (icona campanello in alto a destra del portale). Le notifiche dovrebbero indicare che il caricamento è stata completata. 
10. Passare al riquadro nel dashboard di aggiornamento. Il riquadro deve indicare che è disponibile un aggiornamento. Fare clic sul riquadro per esaminare il pacchetto di aggiornamento appena aggiunto.
11. Per installare l'aggiornamento, selezionare il pacchetto che è contrassegnato come **pronto** e fare doppio clic sul pacchetto e selezionare **Aggiorna**, oppure fare clic su di **Aggiorna** azione nella parte superiore .
12. Quando si sceglie il pacchetto di aggiornamento di installazione, è possibile visualizzare lo stato di **dettagli di esecuzione di aggiornamento** area. Da qui, è anche possibile fare clic su **al Download completo** per scaricare i file di log.
13. Al termine dell'aggiornamento, il riquadro di aggiornamento Mostra la versione aggiornata dello Stack di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md)
- [Azure Stack di manutenzione dei criteri](azure-stack-servicing-policy.md)
