---
title: Associare l'ASN peer alla sottoscrizione di Azure con il portale
titleSuffix: Azure
description: Associare l'ASN peer alla sottoscrizione di Azure con il portale
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912192"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associare l'ASN peer alla sottoscrizione di Azure con il portale

Prima di inviare una richiesta di peering, è necessario associare l'ASN alla sottoscrizione di Azure seguendo questa procedura.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Creare Peerasn sugli per associare l'ASN alla sottoscrizione di Azure

### <a name="sign-in-to-the-portal"></a>Accedere al portale
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registra per provider di risorse di peering
Eseguire la registrazione per il provider di risorse di peering nella sottoscrizione attenendosi alla procedura descritta di seguito. Se non si esegue questa operazione, le risorse di Azure necessarie per configurare il peering non sono accessibili.

1. Fare clic su **sottoscrizioni** nell'angolo superiore sinistro del portale. Se non viene visualizzato, fare clic su **altri servizi** e cercarlo.

    > [!div class="mx-imgBorder"]
    > ![Apri sottoscrizioni](./media/rp-subscriptions-open.png)

1. Fare clic sulla sottoscrizione che si vuole usare per il peering.

    > [!div class="mx-imgBorder"]
    > ![Avvia sottoscrizione](./media/rp-subscriptions-launch.png)

1. Quando si apre la sottoscrizione, a sinistra fare clic su **provider di risorse**. Quindi, nel riquadro di destra, cercare il *peering* nella finestra di ricerca oppure utilizzare la barra di scorrimento per trovare **Microsoft. peering** ed esaminare lo **stato**. Se lo stato è ***registrato***, ignorare i passaggi seguenti e passare alla sezione **creare peerasn sugli**. Se lo stato è ***NotRegistered***, selezionare **Microsoft. peering** e fare clic su **Register (registra**).

    > [!div class="mx-imgBorder"]
    > ![Inizio registrazione](./media/rp-register-start.png)

1. Osservare che lo stato diventa ***registrazione***.

    > [!div class="mx-imgBorder"]
    > ![Registrazione in corso](./media/rp-register-progress.png)

1. Attendere almeno un minuto per completare la registrazione. Quindi, fare clic su **Aggiorna** e verificare che lo stato sia ***registrato***.

    > [!div class="mx-imgBorder"]
    > ![Registrazione completata](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Crea Peerasn sugli
È possibile creare una nuova risorsa Peerasn sugli per associare un numero di sistema autonomo (ASN) a una sottoscrizione di Azure. È possibile associare più ASN a una sottoscrizione creando un **peerasn sugli** per ogni ASN che è necessario associare.

1. Fare clic su **Crea una risorsa** > **Vedi tutto**.

    > [!div class="mx-imgBorder"]
    > ![Cerca Peerasn sugli](./media/peerasn-seeall.png)

1. Cercare *peerasn sugli* nella casella di ricerca e premere *invio* sulla tastiera. Nei risultati fare clic su risorsa **peerasn sugli** .

    > [!div class="mx-imgBorder"]
    > ![Avviare Peerasn sugli](./media/peerasn-launch.png)

1. Dopo l'avvio di **peerasn sugli** , fare clic su **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Crea Peerasn sugli](./media/peerasn-create.png)

1. Nella pagina **associa un ASN peer** , nella scheda **nozioni di base** , compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Scheda nozioni di base di Peerasn sugli](./media/peerasn-basics-tab.png)

    * Il **nome** corrisponde al nome della risorsa e può essere qualsiasi elemento selezionato.  
    * Scegliere la **sottoscrizione** a cui è necessario associare ASN.
    * Il **nome peer** corrisponde al nome della società e deve essere il più vicino possibile al profilo PeeringDB. Si noti che il valore supporta solo i caratteri a-z, A-Z e lo spazio
    * Immettere il numero ASN nel campo **peer ASN** .
    * Fare clic su **Crea nuovo** e immettere l' **indirizzo di posta elettronica** e il **numero di telefono** per Network Operations Center (NOC)
1. Quindi, fare clic su **Verifica + crea** e osservare che il portale esegue la convalida di base delle informazioni immesse. Viene visualizzato in una barra multifunzione in alto, come *esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Revisione Peerasn sugli](./media/peerasn-review-tab-validation.png)

1. Quando il messaggio nella barra multifunzione *passa alla convalida superata*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se la convalida non viene superata, fare clic su **indietro** e ripetere i passaggi precedenti per modificare la richiesta e assicurarsi che i valori immessi non abbiano errori.

    > [!div class="mx-imgBorder"]
    > ![Scheda Revisione Peerasn sugli](./media/peerasn-review-tab.png)

1. Dopo aver inviato la richiesta, attenderne il completamento della distribuzione. Se la distribuzione non riesce, contattare il [peering Microsoft](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come indicato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Peerasn sugli riuscito](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Visualizzare lo stato di un Peerasn sugli
Una volta distribuita correttamente la risorsa Peerasn sugli, sarà necessario attendere che Microsoft approvi la richiesta di associazione. L'approvazione può richiedere fino a 12 ore. Una volta approvata, si riceverà una notifica per l'indirizzo di posta elettronica immesso nella sezione precedente.

> [!IMPORTANT]
> Attendere che il ValidationState "Approved" prima di inviare una richiesta di peering. L'approvazione può richiedere fino a 12 ore.

## <a name="modify-peerasn"></a>Modificare Peerasn sugli
La modifica di Peerasn sugli non è attualmente supportata. Se è necessario modificare, contattare il [peering Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Elimina Peerasn sugli
L'eliminazione di un Peerasn sugli non è attualmente supportata. Se è necessario eliminare Peerasn sugli, contattare il [peering Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto con il portale](howto-direct-portal.md)
* [Convertire un peering diretto legacy in una risorsa di Azure con il portale](howto-legacy-direct-portal.md)
* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [domande frequenti sul peering Internet](faqs.md)