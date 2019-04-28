---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 05/09/2018
ms.date: 06/04/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9c4910d9ec1fdf651b1bc29955a4e1de2a775f7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576154"
---
1. Fare clic sul pulsante **Connetti** nella pagina delle proprietà della macchina virtuale. 
2. Nella pagina **Connetti a macchina virtuale** selezionare le opzioni appropriate e fare clic su **Scarica file RDP**.
2. Aprire il file RDP scaricato e fare clic su **Connetti** quando richiesto. 
2. Verrà visualizzato un avviso che indica che l'autore del file con estensione `.rdp` è sconosciuto. Si tratta di una situazione normale. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

    ![Screenshot di un avviso relativo a un autore sconosciuto.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare le credenziali di un account della macchina virtuale e quindi fare clic su **OK**.

     **Account locale** : corrisponde in genere al nome utente e alla password dell'account locale specificati al momento della creazione della macchina virtuale. In questo caso, il dominio è il nome della macchina virtuale e viene immesso come *nomevm*&#92;*nomeutente*.  

    **VM aggiunta a un dominio**: se la VM appartiene a un dominio, immettere il nome utente con formato *Dominio*&#92;*Nomeutente*. L'account deve anche appartenere al gruppo degli amministratori o deve possedere privilegi di accesso remoto alla VM.

    **Controller di dominio** : se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.
4. Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.

   ![Screenshot che visualizza un messaggio sulla verifica dell'identità della VM.](./media/virtual-machines-log-on-win-server/cert-warning.png)
