---
title: Creare report snapshot di Cloud App Discovery in Azure Active Directory | Microsoft Docs
description: Fornisce informazioni sull'individuazione e la gestione delle applicazioni con Cloud App Discovery, quali sono i vantaggi e il relativo funzionamento.
services: active-directory
keywords: cloud app discovery, gestione delle applicazioni
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: 69a633b39e68596c536700dbb2c7c8d35f3b44bb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Creare report snapshot di Cloud App Discovery

Prima di configurare l'agente di raccolta log automatico, caricare manualmente un log e lasciare che Cloud App Security lo analizzi. Se in mancanza di un log si vuole esaminare un log di esempio, usare la procedura seguente per scaricare un file di log di esempio e scoprire che aspetto avrà il log.

## <a name="to-create-a-snapshot-report"></a>Per creare un report snapshot

1. Raccogliere i file di log dal firewall e dal server proxy tramite cui gli utenti dell'organizzazione accedono a Internet. Raccogliere i log durante i periodi di traffico più intenso, che rappresentano l'attività degli utenti nell'organizzazione.
2. Sulla [barra dei menu di Cloud App Security](https://portal.cloudappsecurity.com) selezionare **Individua** e quindi **Crea il report snapshot**.
  
  ![Creare un nuovo report snapshot](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Impostare **Nome report** e **Descrizione**.
    
  ![Nuovo report snapshot](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. In **Origine dati** selezionare l'origine dati da cui caricare i file di log.
5. Verificare il formato di log per assicurarsi che sia corretto in base all'esempio che è possibile scaricare. Fare clic su **Visualizzare e verificare** e quindi fare clic su **Scarica il log di esempio**. Confrontare quindi il log con l'esempio fornito per assicurarsi che sia compatibile.
  
  ![Verificare il formato del log](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > Il formato di esempio FTP è supportato negli snapshot e nel caricamento automatico, mentre syslog è supportato solo nel caricamento automatico. Quando si scarica un log di esempio, viene scaricato un log FTP.
6. **Scegliere i log sul traffico** da caricare. È possibile caricare fino a 20 file contemporaneamente. I file compressi e ZIP sono supportati.
  
7. Fare clic su **Crea**. Al termine del caricamento, l'analisi dei log può richiedere tempo. In questo caso, Cloud App Discovery invia una notifica di posta elettronica quando i log sono pronti.

8. Selezionare **Gestisci i report snapshot** e quindi selezionare il report snapshot.
  
  ![Gestione dei report snapshot](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'uso di Cloud App Discovery in Azure AD](cloudappdiscovery-get-started.md)
* [Configurare il caricamento automatico dei log per i report continui](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Usare un parser di log personalizzato](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
