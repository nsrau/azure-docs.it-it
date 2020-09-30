---
title: Associare l'ASN peer alla sottoscrizione di Azure con il portale
titleSuffix: Azure
description: Associare l'ASN peer alla sottoscrizione di Azure con il portale
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540269"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associare l'ASN peer alla sottoscrizione di Azure con il portale

In qualità di provider di servizi Internet o provider di Internet Exchange, prima di inviare una richiesta di peering, è necessario associare l'ASN a una sottoscrizione di Azure seguendo questa procedura.

Se si preferisce, è possibile completare questa guida usando [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Creare un oggetto PeerAsn per associare l'ASN alla sottoscrizione di Azure

### <a name="sign-in-to-the-portal"></a>Accedere al portale
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Eseguire la registrazione per il provider di risorse di peering
Eseguire la registrazione per il provider di risorse di peering nella sottoscrizione attenendosi alla procedura descritta di seguito. Se non si esegue questa operazione, le risorse di Azure necessarie per configurare il peering non sono accessibili.

1. Fare clic su **Sottoscrizioni** nell'angolo superiore sinistro del portale. Se il comando non è visualizzato, fare clic su **Altri servizi** e cercarlo.

    > [!div class="mx-imgBorder"]
    > ![Aprire le sottoscrizioni](./media/rp-subscriptions-open.png)

1. Fare clic sulla sottoscrizione da usare per il peering.

    > [!div class="mx-imgBorder"]
    > ![Avviare la sottoscrizione](./media/rp-subscriptions-launch.png)

1. Una volta aperta la sottoscrizione, a sinistra fare clic su **Provider di risorse**. Nel riquadro di destra cercare quindi *peering* nella finestra di ricerca o usare la barra di scorrimento per trovare **Microsoft.Peering** ed esaminare il valore di **Stato**. Se lo stato è ***Registrato***, ignorare la procedura seguente e passare alla sezione **Creare un oggetto PeerAsn**. Se lo stato è ***Non registrato***, selezionare **Microsoft.Peering** e fare clic su **Registra**.

    > [!div class="mx-imgBorder"]
    > ![Avvio della registrazione](./media/rp-register-start.png)

1. Osservare che lo stato cambia in ***Registrazione***.

    > [!div class="mx-imgBorder"]
    > ![Registrazione in corso](./media/rp-register-progress.png)

1. Attendere circa un minuto per il completamento della registrazione. Fare quindi clic su **Aggiorna** e verificare che lo stato sia ***Registrato***.

    > [!div class="mx-imgBorder"]
    > ![Registrazione completata](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Creare un oggetto PeerAsn
In qualità di provider di servizi Internet o provider di Internet Exchange, è possibile creare una nuova risorsa Peerasn sugli per l'associazione di un numero di sistema autonomo (ASN) con la sottoscrizione di Azure nella [pagina associare un ASN peer](https://go.microsoft.com/fwlink/?linkid=2129592) . È possibile associare più ASN a una sottoscrizione creando un **peerasn sugli** per ogni ASN che è necessario associare.

1. Nella pagina **Associate a Peer ASN** (Associa un ASN peer), nella scheda **Informazioni di base** compilare i campi come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Scheda Informazioni di base per l'oggetto PeerAsn](./media/peerasn-basics-tab.png)

    * **Nome** corrisponde al nome della risorsa e può essere qualsiasi valore desiderato.  
    * **Sottoscrizione** indica la sottoscrizione a cui associare l'ASN.
    * **Nome del peer** corrisponde al nome dell'azienda e deve essere il più simile possibile al profilo PeeringDB. Si noti che il valore supporta solo i caratteri a-z, A-Z e lo spazio
    * Immettere l'ASN nel campo **ASN peer**.
    * Fare clic su **Crea nuovo** e immettere **INDIRIZZO DI POSTA ELETTRONICA** e **NUMERO DI TELEFONO** per il Network Operations Center (NOC)
1. Fare quindi clic su **Rivedi e crea** e osservare che il portale esegue una convalida di base delle informazioni immesse. L'operazione viene visualizzata su una barra multifunzione nella parte superiore, con l'indicazione *Esecuzione della convalida finale*.

    > [!div class="mx-imgBorder"]
    > ![Screenshot mostra la scheda associa un peer A S N nozioni di base.](./media/peerasn-review-tab-validation.png)

1. Una volta che il messaggio sulla barra multifunzione indica *Convalida superata*, verificare le informazioni e inviare la richiesta facendo clic su **Crea**. Se la convalida non viene superata, fare clic su **Indietro** e ripetere i passaggi precedenti per modificare la richiesta e verificare che i valori immessi non contengano errori.

    > [!div class="mx-imgBorder"]
    > ![Screenshot mostra la scheda associa un peer A S N nozioni di base con convalida superata.](./media/peerasn-review-tab.png)

1. Dopo avere inviato la richiesta, attendere il completamento della distribuzione. Se la distribuzione ha esito negativo, contattare il team [Peer Microsoft](mailto:peering@microsoft.com). In caso di esito positivo, la distribuzione verrà visualizzata come illustrato di seguito.

    > [!div class="mx-imgBorder"]
    > ![Esito positivo di PeerAsn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Visualizzare lo stato di un oggetto PeerAsn
Una volta eseguita la distribuzione della risorsa PeerAsn, sarà necessario attendere l'approvazione della richiesta di associazione da parte di Microsoft. L'approvazione può richiedere fino a 12 ore. Una volta ottenuta l'approvazione, si riceverà una notifica all'indirizzo di posta elettronica immesso nella sezione precedente.

> [!IMPORTANT]
> Attendere che lo stato di convalida sia "Approvato" prima di inviare una richiesta di peering. L'approvazione può richiedere fino a 12 ore.

## <a name="modify-peerasn"></a>Modificare l'oggetto PeerAsn
La modifica di un oggetto PeerAsn non è attualmente supportata. Se è necessario apportare una modifica, contattare il team [Peer Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Eliminare un oggetto PeerAsn
L'eliminazione di un oggetto PeerAsn non è attualmente supportata. Se è necessario eliminare un oggetto PeerAsn, contattare il team [Peer Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto con il portale](howto-direct-portal.md)
* [Convertire un peering diretto legacy in una risorsa di Azure con il portale](howto-legacy-direct-portal.md)
* [Creare o modificare un peering di Exchange con il portale](howto-exchange-portal.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure con il portale](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md)
