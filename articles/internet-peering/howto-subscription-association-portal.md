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

Prima di inviare una richiesta di peering, è necessario associare la sottoscrizione ASN ad Azure usando la procedura seguente.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Creare PeerAsn per associare l'ASN alla sottoscrizione di AzureCreate PeerAsn to associate your ASN with Azure Subscription

### <a name="sign-in-to-the-portal"></a>Accedere al portale
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Eseguire la registrazione per il provider di risorse di peeringRegister for peering resource provider
Eseguire la registrazione per il provider di risorse di peering nella sottoscrizione seguendo la procedura seguente. Se non si esegue questa operazione, le risorse di Azure necessarie per configurare il peering non sono accessibili.

1. Fare clic su **Abbonamenti** nell'angolo in alto a sinistra del portale. Se non lo vedi, clicca su **Altri servizi** e cercalo.

    > [!div class="mx-imgBorder"]
    > ![Abbonamenti aperti](./media/rp-subscriptions-open.png)

1. Fare clic sull'abbonamento che si vuole usare per il peering.

    > [!div class="mx-imgBorder"]
    > ![Abbonamento di lancio](./media/rp-subscriptions-launch.png)

1. Una volta aperta la sottoscrizione, a sinistra, fare clic su **Provider di risorse**. Quindi, nel riquadro destro, cercare il *peering* nella finestra di ricerca oppure utilizzare la barra di scorrimento per trovare **Microsoft.Peering** e osservare **lo stato**. Se lo stato è ***Registered***, ignorare i passaggi riportati di seguito e passare alla sezione **Create PeerAsn**. Se lo stato è ***NotRegistered***, selezionare **Microsoft.Peering** e fare clic su **Registra**.

    > [!div class="mx-imgBorder"]
    > ![Inizio registrazione](./media/rp-register-start.png)

1. Osservare che lo stato diventa ***Registro***.

    > [!div class="mx-imgBorder"]
    > ![Registrazione in corso](./media/rp-register-progress.png)

1. Attendere un min o giù di lì per completare la registrazione. Quindi, fare clic su **Aggiorna** e verificare che lo stato sia ***Registrato***.

    > [!div class="mx-imgBorder"]
    > ![Registrazione completata](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Creare PeerAsnCreate PeerAsn
È possibile creare una nuova risorsa PeerAsn per l'associazione di una sottoscrizione ASN (Autonomous System Number) ad Azure.You can create a new PeerAsn resource for associating an Autonomous System Number (ASN) with Azure subscription. È possibile associare più ASN a una sottoscrizione creando un **PeerAsn** per ogni numero ASN che è necessario associare.

1. Fare clic su **Crea una risorsa** > **Visualizza tutto**.

    > [!div class="mx-imgBorder"]
    > ![Cerca PeerAsn](./media/peerasn-seeall.png)

1. Cerca *PeerAsn* nella casella di ricerca e premi *Invio* sulla tastiera. Dai risultati, fare clic su risorsa **PeerAsn.**

    > [!div class="mx-imgBorder"]
    > ![Avviare PeerAsn](./media/peerasn-launch.png)

1. Una volta avviato **PeerAsn,** fare clic su **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Creare PeerAsnCreate PeerAsn](./media/peerasn-create.png)

1. Nella pagina **Associa un ASN peer,** nella scheda **Nozioni di base,** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Scheda Nozioni di base su PeerAsn](./media/peerasn-basics-tab.png)

    * **Il nome** corrisponde al nome della risorsa e può essere qualsiasi elemento scelto.  
    * Scegliere la **sottoscrizione** a cui associare l'ASN.
    * **Il nome peer** corrisponde al nome dell'azienda e deve essere il più vicino possibile al profilo PeeringDB. Si noti che il valore supporta solo i caratteri a-z, A- e lo spazio
    * Immettere l'ASN nel campo **ASN peer.**
    * Fare clic su **Crea nuovo** e immettere **EMAIL ADDRESS** e PHONE **NUMBER** per Network Operations Center (NOC)
1. Quindi, fare clic su **Revisione : creare** e osservare che il portale esegue la convalida di base delle informazioni immesse. Questo viene visualizzato in una barra multifunzione nella parte superiore, come *Esecuzione della convalida finale...*.

    > [!div class="mx-imgBorder"]
    > ![Scheda Revisione di PeerAsn](./media/peerasn-review-tab-validation.png)

1. Quando il messaggio nella barra multifunzione diventa *Convalida superata*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se la convalida non viene superata, fare clic su **Precedente** e ripetere i passaggi precedenti per modificare la richiesta e assicurarsi che i valori immessi non abbiano errori.

    > [!div class="mx-imgBorder"]
    > ![Scheda Revisione di PeerAsn](./media/peerasn-review-tab.png)

1. Dopo aver inviato la richiesta, attendere che completi la distribuzione. Se la distribuzione non riesce, [contattare Microsoft peering](mailto:peering@microsoft.com). Una distribuzione corretta verrà visualizzata come di seguito.

    > [!div class="mx-imgBorder"]
    > ![Successo di PeerAsn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Visualizzare lo stato di un PeerAsn
Una volta distribuita correttamente la risorsa PeerAsn, sarà necessario attendere che Microsoft approvi la richiesta di associazione. Potrebbero essere richieste fino a 12 ore per l'approvazione. Una volta approvato, riceverai una notifica all'indirizzo e-mail inserito nella sezione precedente.

> [!IMPORTANT]
> Attendi che ValidationState trasformi "Approved" prima di inviare una richiesta di peering. La approvazione potrebbe richiedere fino a 12 ore.

## <a name="modify-peerasn"></a>Modifica PeerAsn
La modifica di PeerAsn non è attualmente supportata. Se è necessario modificare, contattare [Microsoft peering](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Elimina PeerAsn
L'eliminazione di un PeerAsn non è attualmente supportata. Se è necessario eliminare PeerAsn, contattare [Microsoft peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto con il portale](howto-direct-portal.md)
* [Convertire un peering diretto legacy in una risorsa di Azure con il portale](howto-legacy-direct-portal.md)
* [Creare o modificare il peering di Exchange tramite il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)