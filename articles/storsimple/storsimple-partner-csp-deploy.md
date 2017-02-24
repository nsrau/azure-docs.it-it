---
title: Panoramica di Microsoft Azure StorSimple e del Cloud Solutions Provider Program | Microsoft Docs
description: Panoramica di StorSimple e del programma CSP per i partner StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 0b28c5e93dc8f53597b1d2fa6f4ae154acf16068
ms.openlocfilehash: c8cb51093142146fc7d43b51a62d949f6cc38988

---

# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Distribuire un array virtuale StorSimple per il Cloud Solution Provider Program

## <a name="overview"></a>Panoramica

I partner Cloud Solution Provider (CSP) possono distribuire un array virtuale StorSimple per i propri clienti. Un partner CSP può creare un Servizio Gestione dispositivi StorSimple. Questo servizio può quindi essere usato per distribuire e gestire l'array virtuale StorSimple e relativi volumi, backup e condivisioni.

In questo articolo viene descritto come un partner CSP può aggiungere un cliente o una nuova sottoscrizione a un cliente esistente e quindi creare un servizio per distribuire un array virtuale StorSimple in CSP.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

- Essere iscritti al programma CSP.
- Disporre di credenziali di accesso valide per il [Centro per i partner](http://partnercenter.microsoft.com/). Le credenziali consentono di accedere al portale per i partner per aggiungere nuovi clienti, cercare i clienti o passare a un account cliente dal dashboard dei partner. Il CSP può fungere da amministratore di StorSimple per conto del cliente nel portale di Azure.
                             
## <a name="add-a-customer"></a>Aggiungere un cliente

Se si aggiunge un cliente, viene automaticamente creata una sottoscrizione. Per aggiungere un cliente e creare automaticamente una sottoscrizione, eseguire i passaggi seguenti nel portale per i partner.

1. Andare al [Centro per i partner](http://partnercenter.microsoft.com/) e accedere usando le credenziali di CSP. Fare clic su **Dashboard**.

     ![Dashboard nel Centro per i partner](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Nel riquadro a sinistra, fare clic su **Clienti**. Nel riquadro a destra, fare clic su **Aggiungi clienti**. Immettere i dettagli del cliente. Fare clic su **Successivo: Sottoscrizioni** per creare una sottoscrizione del cliente.

    ![Aggiungere un cliente](./media/storsimple-partner-csp-deploy/image2.png)

3.  Selezionare l'offerta **Microsoft Azure**. Scorrere fino alla parte inferiore della pagina e fare clic su **Verifica**.

    ![Verificare le informazioni sulla sottoscrizione](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Verificare le informazioni e quindi fare clic su **Invia**.

    ![Inviare la sottoscrizione](./media/storsimple-partner-csp-deploy/image4.png)

5. Salvare le informazioni di conferma per riferimento futuro.

    ![Salvare la conferma](./media/storsimple-partner-csp-deploy/image5.png)

6. Individuare o passare al cliente appena aggiunto. Fare clic su **Nome società** per visualizzare tutti i dettagli.

    ![Cercare il cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. Nel riquadro a sinistra, selezionare **Gestione servizi**. Nel riquadro di destra, in **Amministra servizi**, fare clic su **Portale di gestione di Microsoft Azure** per accedere come amministratore di Azure per il cliente.

    ![Accedere al portale di Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Per creare una Gestione dispositivi StorSimple, fare clic su **+Nuovo** e cercare o passare a **Serie di dispositivi virtuali StorSimple**. Per altre informazioni, vedere [Distribuire un servizio di Gestione dispositivi StorSimple](storsimple-virtual-array-manage-service.md).

    ![Creare un servizio Gestione dispositivi StorSimple](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Aggiungere una sottoscrizione

In alcuni casi, si potrebbe disporre di un cliente esistente e potrebbe essere necessario aggiungere una sottoscrizione. Per aggiungere una sottoscrizione a un cliente esistente, eseguire i passaggi seguenti nel portale per i partner.

1. Andare al [Centro per i partner](http://partnercenter.microsoft.com/) e accedere usando le credenziali di CSP. Fare clic su **Dashboard**.

     ![Dashboard nel Centro per i partner](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Nel riquadro a sinistra, fare clic su **Clienti**. Trovare o passare al cliente a cui si vuole aggiungere una sottoscrizione. Fare clic sull'![icona del segno di spunta Espandi](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) per espandere la riga relativa al nome della società del cliente. Nei dettagli, fare clic su **Aggiungi sottoscrizioni**.

    ![Clienti](./media/storsimple-partner-csp-deploy/image10.png)

3. Controllare **Microsoft Azure** per le **Migliori offerte** nella sottoscrizione e fare clic su **Invia**. Verrà creata una nuova sottoscrizione.

    ![Aggiungere una nuova sottoscrizione](./media/storsimple-partner-csp-deploy/image11.png)

6. Dopo aver creata una nuova sottoscrizione, fare clic su **<-- Clienti** nel riquadro a sinistra per tornare alla pagina **Clienti**. Cercare il cliente per il quale è stato appena creata una sottoscrizione. Fare clic su **Nome società** per visualizzare tutti i dettagli.

    ![Cercare il cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. Nel riquadro a sinistra, selezionare **Gestione servizi**. Nel riquadro di destra, in **Amministra servizi**, fare clic su **Portale di gestione di Microsoft Azure** per accedere come amministratore di Azure per il cliente.

    ![Accedere al portale di Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Per creare una Gestione dispositivi StorSimple, fare clic su **+Nuovo** e cercare o passare a **Serie di dispositivi virtuali StorSimple**. Per altre informazioni, vedere [Distribuire un servizio di Gestione dispositivi StorSimple](storsimple-virtual-array-manage-service.md).

    ![Creare un servizio Gestione dispositivi StorSimple](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre domande su StorSimple per CSP, passare a [StorSimple per CSP: domande frequenti](storsimple-partner-csp-faq.md).
- Se si è pronti a distribuire StorSimple, andare a [Distribuire StorSimple per CSP](storsimple-partner-csp-deploy.md).



<!--HONumber=Feb17_HO2-->


