---
title: Connessione peering per partner Exchange con routing server tramite il portale
titleSuffix: Azure
description: Creare o modificare un peering di Exchange con routing server usando il portale di Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1eaf7413b01bceacbcbf3640bfe654fdad026672
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700669"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Creare o modificare un peering di Exchange con il server di route in portale di Azure

Questo articolo descrive come creare un peering di Microsoft Exchange con un server di route usando il portale di Azure. Questo articolo descrive come controllare lo stato della risorsa, aggiornarla o eliminarla ed effettuarne il deprovisioning.


## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md).
* Se esistono già peering di Exchange con Microsoft che non sono convertiti in risorse di Azure, vedere [Convertire un peering di Exchange legacy in risorsa di Azure tramite il portale](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Creare un peering di Exchange ed effettuarne il provisioning

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Accedere al portale e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>Creare un peering di Exchange con il server di route


I provider di Internet Exchange possono creare una richiesta di peering di Exchange tramite la [creazione di un peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Nella scheda **Generale** della pagina **Create a Peering** (Crea peering) compilare le caselle come illustrato nell'immagine seguente:

    > [!div class="mx-imgBorder"] 
    > ![Registrare un servizio di peering](./media/setup-basics-tab.png)

* Selezionare la sottoscrizione di Azure.

* Per Gruppo di risorse, è possibile sceglierne uno esistente nell'elenco a discesa o crearne uno nuovo selezionando Crea nuovo. Per questo esempio, creare un nuovo gruppo di risorse.

* Il nome corrisponde a quello della risorsa e può essere qualsiasi nome desiderato.

* Se si sceglie un gruppo di risorse esistente, l'area viene selezionata automaticamente. Se si sceglie di creare un nuovo gruppo di risorse, è necessario scegliere anche l'area di Azure in cui deve trovarsi la risorsa.

    >[!NOTE]
    >L'area in cui si trova un gruppo di risorse è indipendente dalla località in cui si vuole creare il peering con Microsoft. Tuttavia, è consigliabile organizzare le risorse di peering all'interno di gruppi di risorse che si trovano nelle aree di Azure più vicine. Ad esempio, per peering in Ashburn è possibile creare un gruppo di risorse nell'area Stati Uniti orientali o Stati Uniti orientali 2.

* Selezionare l'ASN nella casella **ASN peer**.

    >[!IMPORTANT] 
    >È possibile scegliere un ASN solo con stato di convalida approvato prima di inviare una richiesta di peering. Se la richiesta dell'ASN del peer è appena stata inviata, attendere 12 ore che venga approvata l'associazione dell'ASN. Se la convalida dell'ASN selezionato è in sospeso, verrà visualizzato un messaggio di errore. Se l'ASN che è necessario scegliere non è visualizzato, verificare di aver selezionato la sottoscrizione corretta. Se sì, controllare se è già stata creata una richiesta di ASN del peer facendo riferimento a **[Associare l'ASN del peer alla sottoscrizione di Azure](https://go.microsoft.com/fwlink/?linkid=2129592)** .

* Selezionare **Avanti: Configurazione** per continuare.

#### <a name="configure-connections-and-submit"></a>Configurare le connessioni e inviare

1. Nella scheda configurazione della pagina Crea un peering compilare le caselle come illustrato di seguito:

    > [!div class="mx-imgBorder"]
    > ![Configurare il server di route](./media/setup-exchange-conf-tab-routeserver.png)
 
    * Per tipo di peering selezionare **diretta**
    * Per Microsoft Network selezionare **AS8075 con Exchange route server**. 
    * Selezionare SKU come **Basic Free**. Non selezionare Premium gratuito perché è riservato per le applicazioni speciali.
    * Selezionare la **località** in cui si desidera configurare il peering.

1. In **connessioni peering**selezionare **Crea nuovo**

1.  In **connessione peering diretto**compilare i dettagli della sessione BGP seguenti:

    > [!div class="mx-imgBorder"]
    > ![Configurare il server di route di peering diretto](./media/setup-exchange-conf-tab-direct-route.png)


     * Funzionalità di peering selezionare la posizione fisica appropriata per il peering
     * Provider di indirizzi di sessione, selezionare peer
     * Il prefisso IPv4 della sessione verrà fornito dal peer del provider di Exchange
     * Indirizzo IPv4 della sessione peer verrà selezionato dal peer di Exchange per il server di route dall'intervallo di prefisso IP.
     * Indirizzo IPv4 di Microsoft Session, sarà l'IP del router allocato dall'intervallo di prefisso IP.
     * La sessione IPv6 è facoltativa al momento.
     * Il prefisso IPv4 massimo annunciato può essere fino a 20000. 
     * L'uso di per il servizio di peering è disabilitato per impostazione predefinita. Può essere abilitato una volta che il provider di Exchange ha firmato un contratto di servizio di peering con Microsoft.

1. Al termine, fare clic su **Salva**. 

1. In crea un peering viene visualizzata la convalida. Una volta superata la convalida, fare clic su **Crea**

    > [!div class="mx-imgBorder"]
    > ![Convalida delle impostazioni](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >Per i normali provider di servizi Internet (ISP) che sono partner del servizio peering Microsoft, è richiesta la registrazione dei prefissi IP del cliente. Tuttavia, nel caso di partner Exchange con un server di route, è necessario registrare i ASN dei clienti e non i prefissi. La stessa chiave ASN sarà valida per la registrazione del prefisso del cliente.

1. Selezionare **registrato ASN** nella sezione Impostazioni.

    > [!div class="mx-imgBorder"]
    > ![Configurare il server di route di peering diretto](./media/setup-exchange-registered-asn.png)

1. Selezionare **Aggiungi ASN registrato** per creare un nuovo ASN del cliente nella sottoscrizione di Exchange.

    > [!div class="mx-imgBorder"]
    > ![Configurare il server di route di peering diretto](./media/setup-exchange-register-new-asn.png)

1. In registra un ASN selezionare un nome, popolare il valore ASN del cliente e fare clic su Salva.

1. In ASN registrato, a ogni ASN verrà assegnata una chiave di prefisso associata. Come provider di Exchange, sarà necessario fornire la chiave di prefisso al cliente per poter registrare il servizio di peering nella sottoscrizione.

    > [!div class="mx-imgBorder"]
    > ![Configurare il server di route di peering diretto](./media/setup-exchange-register-asn-prefixkey.png)




### <a name="verify-an-exchange-peering"></a><a name=get></a>Verificare un peering di Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificare un peering di Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Effettuare il deprovisioning di un peering di Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto con il portale](howto-direct-portal.md)
* [Convertire un peering diretto legacy in risorsa di Azure con il portale](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).
