---
title: Preparare i certificati di infrastruttura a chiave pubblica Azure Stack per la distribuzione di sistemi integrati di Azure Stack o la rotazione segreta | Microsoft Docs
description: Viene descritto come preparare i certificati di infrastruttura a chiave pubblica di Azure Stack per i sistemi integrati di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/02/2019
ms.openlocfilehash: 496a6c45c9f8930f77b3a3dab2f81db1b24c1801
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247205"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Preparare i certificati di infrastruttura a chiave pubblica di Azure Stack per l'uso nella distribuzione o la rotazione
I file di certificato [ottenuto dalla CA di scelta](azure-stack-get-pki-certs.md) devono essere importati ed esportati con proprietà corrispondenti requisiti dei certificati di Azure Stack.


## <a name="prepare-certificates-for-deployment"></a>Preparare i certificati per la distribuzione
Usare questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure Stack che saranno utilizzati per la distribuzione di un nuovo ambiente di Azure Stack o per la rotazione dei segreti in un ambiente Azure Stack: 

### <a name="import-the-certificate"></a>Importare il certificato

1.  Copiare le versioni certificato originale [ottenuto dalla CA di scelta](azure-stack-get-pki-certs.md) in una directory nell'host di distribuzione. 
  > [!WARNING]
  > Non copiare i file che sono già stati importati, esportati o modificati in alcun modo dai file forniti direttamente dalla CA.

1.  Pulsante destro del mouse sul certificato e selezionare **Installa certificato** oppure **Installa PFX** a seconda del modo in cui il certificato è stato recapitato da un'autorità di certificazione.

1. Nel **importazione guidata certificati**, selezionare **computer locale** come percorso di importazione. Selezionare **Avanti**. Nella schermata seguente fare clic su Avanti nuovamente.

    ![Percorso del computer locale importare](./media/prepare-pki-certs/1.png)

1.  Scegli **colloca tutti i certificati nel seguente archivio** e quindi selezionare **attendibilità per l'organizzazione** come posizione. Fare clic su **OK** per chiudere la finestra di dialogo Selezione archivio certificati e quindi **successivo**.

    ![Configurare l'archivio certificati](./media/prepare-pki-certs/3.png)

    a. Se si sta importando un file PFX, verrà visualizzato con una finestra di dialogo aggiuntiva. Nel **protezione con chiave privata** pagina, immettere la password per i file di certificato e quindi abilitare il **contrassegna questa chiave come esportabile. In questo modo è possibile eseguire il backup o trasferire le chiavi in un secondo momento** opzione. Selezionare **Avanti**.

    ![Contrassegna le chiavi come esportabili](./media/prepare-pki-certs/2.png)

1. Fare clic su Fine per completare l'importazione.

### <a name="export-the-certificate"></a>Esportare il certificato

Aprire la console di gestione certificati MMC e connettersi all'archivio certificati del computer locale.

1. Aprire Microsoft Management Console, in Windows 10 con il pulsante destro fare clic sul Menu Start, quindi fare clic su Esegui. Tipo di **mmc** fare clic su ok.

1. Fare clic sul File, Aggiungi/Rimuovi Snap-In, quindi selezionare i certificati, fare clic su Aggiungi.

    ![Aggiungere lo Snap-in certificati](./media/prepare-pki-certs/mmc-2.png)
 
1. Selezionare account Computer, fare clic su Avanti, quindi selezionare computer locale, quindi Fine. Fare clic su ok per chiudere la pagina Aggiungi/Rimuovi Snap-In.

    ![Aggiungere lo Snap-in certificati](./media/prepare-pki-certs/mmc-3.png)

1. Passare a certificati > attendibilità per l'organizzazione > percorso del certificato. Verificare che venga visualizzato il certificato sul lato destro.

1. Dalla console di gestione di attività sulla barra del certificato, selezionare **azioni** > **tutte le attività** > **Esporta**. Selezionare **Avanti**.

  > [!NOTE]
  > A seconda di quanti Azure Stack i certificati che disponibili, è potrebbe essere necessario completare questo processo più volte.

1. Selezionare **Sì, Esporta la chiave privata**, quindi fare clic su **successivo**.

1. Nella sezione formato File di esportazione:
    
    - Selezionare **se possibile, Includi tutti i certificati nel certificato**.  
    - Selezionare **Esporta tutte le proprietà estese**.  
    - Selezionare **Abilita privacy del certificato**.  
    - Fare clic su **Avanti**.  
    
    ![Esportazione guidata certificati con opzioni selezionate](./media/prepare-pki-certs\azure-stack-save-cert.png)

1. Selezionare **Password** e fornire una password per i certificati. Prendere nota della password perché è usato come parametro di distribuzione. Selezionare **Avanti**.

1. Scegliere un nome file e percorso del file pfx da esportare. Selezionare **Avanti**.

1. Selezionare **Fine**.

## <a name="next-steps"></a>Passaggi successivi

[Convalidare i certificati PKI](azure-stack-validate-pki-certs.md)
