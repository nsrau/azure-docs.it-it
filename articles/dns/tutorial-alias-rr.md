---
title: 'Esercitazione: Creare un record alias servizio DNS di Azure per fare riferimento a un record di risorsa nella zona.'
description: Questa esercitazione illustra come configurare un record alias servizio DNS di Azure per fare riferimento a un record di risorsa nella zona.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 3b4ee688d6a5606ab6008b459fcf6331c24afaae
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091641"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Esercitazione: Creare un record alias per fare riferimento a un record di risorsa nella zona

I record alias possono fare riferimento ad altri set di record dello stesso tipo. È ad esempio possibile avere un set di record CNAME DNS come alias per un altro set di record CNAME dello stesso tipo. Questa funzionalità è utile se si intende definire alcuni set di record come alias e altri come non alias dal punto di vista del comportamento.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un record alias per un record di risorsa nella zona.
> * Testare il record alias.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di un nome di dominio che si possa ospitare in DNS di Azure per il test. È necessario disporre del controllo completo di questo dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.

Per istruzioni su come ospitare il dominio in DNS di Azure, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Creare un record alias

Creare un record alias che rimandi a un record di risorsa nella zona.

### <a name="create-the-target-resource-record"></a>Creare il record di risorsa di destinazione
1. Selezionare la zona DNS di Azure per aprirla.
2. Selezionare **Set di record**.
3. Nella casella di testo **Nome** immettere **server**.
4. Per il **Tipo**, selezionare **A**.
5. Nella casella di testo **INDIRIZZO IP** immettere **10.10.10.10**.
6. Selezionare **OK**.

### <a name="create-the-alias-record"></a>Creare il record alias
1. Selezionare la zona DNS di Azure per aprirla.
2. Selezionare **Set di record**.
3. Nella casella di testo **Nome** immettere **test**.
4. Per il **Tipo**, selezionare **A**.
5. Selezionare **Sì** nella casella di controllo **Set di record alias**. Selezionare quindi l'opzione **Set di record della zona**.
6. Per il **Set di record di zona**, selezionare il record **server**.
7. Selezionare **OK**.

## <a name="test-the-alias-record"></a>Testare il record alias

1. Avviare lo strumento nslookup preferito. Una possibilità consiste nel passare a [ https://network-tools.com/nslook ](https://network-tools.com/nslook).
2. Impostare il tipo di query per i record A e cercare **test.\<nome di dominio\>**. La risposta è **10.10.10.10**.
3. Nel portale di Azure, modificare il record A **server** in **10.11.11.11**.
4. Attendere qualche minuto e quindi usare nuovamente nslookup per il record **test**. La risposta è **10.11.11.11**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create per questa esercitazione non sono più necessarie, eliminare i record delle risorse **server** e **test** nella zona.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un record alias per fare riferimento a un record di risorsa nella zona. Per informazioni su DNS di Azure e le app Web, proseguire con l'esercitazione sulle app Web.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
