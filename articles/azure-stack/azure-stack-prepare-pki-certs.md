---
title: Preparare i certificati di infrastruttura a chiave pubblica Azure dello Stack per la distribuzione di sistemi Azure Stack integrato | Documenti Microsoft
description: Viene descritto come preparare i certificati di infrastruttura a chiave pubblica di Azure dello Stack per i sistemi Azure Stack integrato.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203477"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Preparare i certificati di infrastruttura a chiave pubblica di Azure dello Stack per la distribuzione
I file di certificato [ottenuto dalla CA di scelta](azure-stack-get-pki-certs.md) deve importare ed esportare con proprietà corrispondenti requisiti dei certificati di Azure dello Stack.


## <a name="prepare-certificates-for-deployment"></a>Preparare i certificati per la distribuzione
Utilizzare questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack: 

### <a name="import-the-certificate"></a>Importare il certificato

1.  Copiare le versioni originali di certificato [ottenuto dalla CA di scelta](azure-stack-get-pki-certs.md) in una directory nell'host di distribuzione. 
  > [!WARNING]
  > Non copiare i file che sono già stati importati, esportati o modificati in alcun modo i file forniti direttamente dalla CA.

2.  Pulsante destro del mouse sul certificato e selezionare **Installa certificato** oppure **Installa PFX** a seconda del modo in cui è stato recapitato il certificato dalla CA.

3. Nel **importazione guidata certificati**selezionare **computer locale** come percorso di importazione. Selezionare **Avanti**. Nella schermata seguente fare clic su Avanti nuovamente.

    ![Percorso di importazione del computer locale](.\media\prepare-pki-certs\1.png)

4.  Scegliere **sul posto tutti certificati nel seguente archivio** e quindi selezionare **attendibilità per l'organizzazione** come percorso. Fare clic su **OK** per chiudere la finestra di dialogo Selezione archivio certificati e quindi **Avanti**.

    ![Configurare l'archivio certificati](.\media\prepare-pki-certs\3.png)

    a. Se si sta importando un file PFX verrà visualizzata una finestra di dialogo aggiuntiva. Nel **protezione della chiave privata** pagina, immettere la password per i file di certificato e quindi abilitare il **contrassegna questa chiave come esportabile. Ciò consente di eseguire il backup delle chiavi oppure trasferirle in un secondo momento** opzione. Selezionare **Avanti**.

    ![Contrassegna le chiavi come esportabili](.\media\prepare-pki-certs\2.png)

5. Fare clic su Fine per completare l'importazione.

### <a name="export-the-certificate"></a>Esportare il certificato

Aprire la console di gestione certificati MMC e connettersi all'archivio certificati del computer locale.

1. Aprire Microsoft Management Console, in Windows 10 fare clic sul Menu Start, quindi fare clic su Esegui. Tipo di **mmc** fare clic su ok.

2. Fare clic sul File, Aggiungi/Rimuovi Snap-In, quindi selezionare i certificati, fare clic su Aggiungi.

    ![Aggiungere lo Snap-in certificati](.\media\prepare-pki-certs\mmc-2.png)
 
3. Selezionare l'account Computer, fare clic su Avanti, quindi selezionare computer locale quindi Fine. Fare clic su ok per chiudere la pagina Aggiungi/Rimuovi Snap-In.

    ![Aggiungere lo Snap-in certificati](.\media\prepare-pki-certs\mmc-3.png)

4. Passare a certificati > attendibilità per l'organizzazione > percorso del certificato. Verificare che il certificato è visualizzato sulla destra.

5. La console di Gestione attività sulla barra del certificato, selezionare **azioni** > **tutte le attività** > **esportare**. Selezionare **Avanti**.

  > [!NOTE]
  > A seconda di quanti Stack Azure i certificati che è potrebbe essere necessario completare questo processo più volte.

4. Selezionare **Sì, Esporta la chiave privata**, quindi fare clic su **Avanti**.

5. Nella sezione formato File di esportazione, selezionare **Esporta tutte le proprietà estese** e quindi fare clic su **Avanti**.

6. Selezionare **Password** e fornire una password per i certificati. Prendere nota della password perché è usato come un parametro di distribuzione. Selezionare **Avanti**.

7. Scegliere un nome file e un percorso per il file pfx da esportare. Selezionare **Avanti**.

8. Selezionare **Fine**.

## <a name="next-steps"></a>Passaggi successivi
[La convalida dei certificati di infrastruttura a chiave pubblica](azure-stack-validate-pki-certs.md)