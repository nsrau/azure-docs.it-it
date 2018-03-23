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
ms.openlocfilehash: dadb443f8b7739e3a18c0d3beb558d8c42e9d19c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Preparare i certificati di infrastruttura a chiave pubblica di Azure dello Stack per la distribuzione
I file di certificato [ottenuto dalla CA di scelta](azure-stack-get-pki-certs.md) deve importare ed esportare con proprietà corrispondenti requisiti dei certificati di Azure dello Stack.


## <a name="prepare-certificates-for-deployment"></a>Preparare i certificati per la distribuzione
Utilizzare questi passaggi per preparare e convalidare i certificati di infrastruttura a chiave pubblica di Azure dello Stack: 

1.  Copiare le versioni originali di certificato [ottenuto dalla CA di scelta](azure-stack-get-pki-certs.md) in una directory nell'host di distribuzione. 
  > [!WARNING]
  > Non copiare i file che sono già stati importati, esportati o modificati in alcun modo i file forniti direttamente dalla CA.

2.  Importare i certificati nell'archivio certificati del computer locale:

    a.  Fare doppio clic sul certificato e selezionare **Installa PFX**.

    b.  Nel **importazione guidata certificati**selezionare **computer locale** come percorso di importazione. Selezionare **Avanti**.

    ![Percorso di importazione del computer locale](.\media\prepare-pki-certs\1.png)

    c.  Selezionare **Avanti** sul **Scegli file da importare** pagina.

    d.  Nel **protezione della chiave privata** pagina, immettere la password per i file di certificato e quindi abilitare il **contrassegna questa chiave come esportabile. Ciò consente di eseguire il backup delle chiavi oppure trasferirle in un secondo momento** opzione. Selezionare **Avanti**.

    ![Contrassegna le chiavi come esportabili](.\media\prepare-pki-certs\2.png)

    e.  Scegliere **sul posto tutti certificati nel seguente archivio** e quindi selezionare **attendibilità per l'organizzazione** come percorso. Fare clic su **OK** per chiudere la finestra di dialogo Selezione archivio certificati e quindi **Avanti**.

    ![Configurare l'archivio certificati](.\media\prepare-pki-certs\3.png)

  f.    Fare clic su **fine** per completare l'importazione guidata certificati.

  g.    Ripetere il processo per tutti i certificati che si specifica per la distribuzione.

3. Esportare il certificato in formato di file PFX con requisiti di Azure dello Stack:

  a.    Aprire la console di gestione certificati MMC e connettersi all'archivio certificati del computer locale.

  b.    Passare al **attendibilità per l'organizzazione** directory.

  c.    Selezionare uno dei certificati che è stato importato nel precedente passaggio 2.

  d.    La console di Gestione attività sulla barra del certificato, selezionare **azioni** > **tutte le attività** > **esportare**.

  e.    Selezionare **Avanti**.

  f.    Selezionare **Sì, Esporta la chiave privata**, quindi fare clic su **Avanti**.

  g.    Nella sezione formato File di esportazione, selezionare **Esporta tutte le proprietà estese** e quindi fare clic su **Avanti**.

  h.    Selezionare **Password** e fornire una password per i certificati. Prendere nota della password perché è usato come un parametro di distribuzione. Selezionare **Avanti**.

  i.    Scegliere un nome file e un percorso per il file pfx da esportare. Selezionare **Avanti**.

  j.    Selezionare **Fine**.

  k.    Ripetere questo processo per tutti i certificati importati per la distribuzione nel precedente passaggio 2.

## <a name="next-steps"></a>Passaggi successivi
[La convalida dei certificati di infrastruttura a chiave pubblica](validate-pki-certs.md)