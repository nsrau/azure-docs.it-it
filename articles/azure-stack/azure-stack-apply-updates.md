---
title: Applicare gli aggiornamenti in Azure Stack | Microsoft Docs
description: Informazioni su come importare e installare i pacchetti di aggiornamento di Microsoft per un sistema integrato Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 585fc4f1bbddb08d881414b581120b7bc14232ab
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729935"
---
# <a name="apply-updates-in-azure-stack"></a>Applicare gli aggiornamenti in Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

È possibile usare la **aggiornare** riquadro nel portale di amministrazione per applicare i pacchetti di aggiornamento di Microsoft o un OEM per Azure Stack. È necessario scaricare il pacchetto di aggiornamento, importare i file del pacchetto in Azure Stack e quindi installare il pacchetto di aggiornamento.

## <a name="download-the-update-package"></a>Scaricare il pacchetto di aggiornamento

Quando un pacchetto di aggiornamento Microsoft o un OEM per Azure Stack è disponibile, scaricare il pacchetto in una posizione raggiungibile da Azure Stack e rivedere il contenuto del pacchetto. Un pacchetto di aggiornamento prevede in genere i file seguenti:

- Un autoestraente `<PackageName>.exe` file. Questo file contiene il payload per l'aggiornamento, ad esempio l'aggiornamento cumulativo più recente per Windows Server.

- Corrispondente `<PackageName>.bin` file. Questi file forniscono la compressione per il payload che è associato il *PackageName*.exe file.

- Oggetto `Metadata.xml` file. Questo file contiene le informazioni essenziali sull'aggiornamento, ad esempio il server di pubblicazione, nome, prerequisito, dimensioni e URL del percorso.

> [!IMPORTANT]  
> Dopo aver applicato il pacchetto di aggiornamento di Azure Stack 1901, il formato di pacchetto per Azure Stack update pacakges passerà da .exe, .bin(s) e il formato con estensione XML da un .zip(s) e formato. Xml. Operatori di Azure Stack che si sono connessi gli indicatori non saranno interessati. Operatori di Azure Stack disconnesse semplicemente importerà i file con estensione XML e ZIP usando lo stesso processo descritto di seguito.

## <a name="import-and-install-updates"></a>Importare e installare gli aggiornamenti

La procedura seguente viene illustrato come importare e installare i pacchetti di aggiornamento nel portale di amministrazione.

> [!IMPORTANT]  
> È consigliabile inviare notifiche agli utenti di tutte le operazioni di manutenzione e pianificare finestre di manutenzione normale durante non lavorative quanto più possibile. I carichi di lavoro utente e operazioni nel portale, possono influire sulle operazioni di manutenzione.

1. Nel portale di amministrazione, selezionare **tutti i servizi**. Quindi, sotto il **DATA + STORAGE** categoria, seleziona **account di archiviazione**. (O, nella casella del filtro, iniziare a digitare **gli account di archiviazione**e selezionarlo.)

    ![Viene illustrato dove trovare gli account di archiviazione nel portale](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Nella casella del filtro, digitare **aggiornare**e selezionare il **updateadminaccount** account di archiviazione.

3. Nella risorsa di archiviazione dell'account informazioni dettagliate, sotto **Services**, selezionare **BLOB**.
 
    ![Viene illustrato come ottenere per i BLOB dell'account di archiviazione](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. Sotto **servizio Blob**, selezionare **+ contenitore** per creare un contenitore. Immettere un nome (ad esempio *Update-1811*), quindi selezionare **OK**.
 
     ![Viene illustrato come aggiungere un contenitore nell'account di archiviazione](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Dopo aver creato il contenitore, fare clic sul nome del contenitore e quindi fare clic su **caricare** per caricare i file del pacchetto nel contenitore.
 
    ![Illustra come caricare i file del pacchetto](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Sotto **carica blob**, fare clic sull'icona della cartella, passare al file .exe del pacchetto di aggiornamento e quindi fare clic su **Open** nella finestra Esplora file.
  
7. Sotto **carica blob**, fare clic su **caricare**.
  
    ![Viene illustrato come caricare ogni file di pacchetto](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Ripetere i passaggi 6 e 7 per il *PackageName*con estensione bin e i file Metadata. Xml. Non importare il file Notice. txt supplementare se inclusi.
9. Al termine, è possibile esaminare le notifiche (icona a forma di campanello nell'angolo in alto a destra del portale). Le notifiche dovrebbero indicare che il caricamento è stata completata.
10. Ritornare alla ggiorna riquadro nel dashboard. Il riquadro dovrebbe indicare che è disponibile un aggiornamento. Fare clic sul riquadro per esaminare il pacchetto di aggiornamento appena aggiunto.
11. Per installare l'aggiornamento, selezionare il pacchetto che è contrassegnato come **pronti** e fare doppio clic su pacchetto e selezionare **aggiornale**, oppure fare clic sui **Aggiorna adesso** azione nella parte superiore .
12. Quando si fa clic sul pacchetto di aggiornamento di installazione, è possibile visualizzare lo stato nella **dettagli dell'esecuzione aggiornamento** area. A questo punto, è anche possibile fare clic su **Scarica i log completi** per scaricare i file di log.
13. Al termine dell'aggiornamento, il riquadro di aggiornamento Visualizza la versione aggiornata di Azure Stack.

È possibile eliminare manualmente gli aggiornamenti dall'account di archiviazione dopo che sono stati installati in Azure Stack. Azure Stack periodicamente verifica la presenza di pacchetti di aggiornamento meno recenti e li rimuove da un archivio. Azure Stack può richiedere due settimane per rimuovere i pacchetti meno recenti.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della gestione degli aggiornamenti in Azure Stack](azure-stack-updates.md)
- [Azure Stack di manutenzione dei criteri](azure-stack-servicing-policy.md)
