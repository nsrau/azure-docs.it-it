---
title: 'Azure Active Directory Domain Services: creare o selezionare una rete virtuale | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure classico
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 457519b00b65b0157effe2d4aba033a1c99852e8
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---
<a id="create-or-select-a-virtual-network-for-azure-active-directory-domain-services" class="xliff"></a>

# Creare o selezionare una rete virtuale per Azure Active Directory Domain Services
<a id="before-you-begin" class="xliff"></a>

## Prima di iniziare
Fare riferimento a [Considerazioni sulla rete per Azure Active Directory Domain Services](active-directory-ds-networking.md).

<a id="task-2-create-an-azure-virtual-network" class="xliff"></a>

## Attività 2: Creare una rete virtuale di Azure
L'attività di configurazione successiva consiste nel creare una rete virtuale di Azure e la relativa subnet. Abilitare Azure Active Directory Domain Services in questa subnet all'interno della rete virtuale. Se si dispone di una rete virtuale esistente che si preferisce usare è possibile ignorare questo passaggio.

> [!NOTE]
> Assicurarsi che la rete virtuale di Azure che si crea o si sceglie di usare con Azure Active Directory Domain Services appartenga a un'area di Azure supportata da Azure Active Directory Domain Services. Per conoscere le aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).
>
>Prendere nota del nome della rete virtuale per assicurarsi di selezionare la rete virtuale corretta al momento dell'abilitazione di Azure Active Directory Domain Services in un passaggio di configurazione successivo.


Per creare una rete virtuale di Azure in cui si desidera abilitare Azure Active Directory Domain Services, seguire queste istruzioni di configurazione:

1. Passare al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel riquadro sinistro selezionare **Reti**.

    ![Nodi di reti](./media/active-directory-domain-services-getting-started/networks-node.png)  
    Si apre la finestra **Reti virtuali**.
3. Fare clic su **Nuovo** nel riquadro attività nella parte inferiore della finestra.

    ![Finestra delle reti virtuali](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Fare clic su **Servizi di rete**, quindi selezionare **Rete virtuale**.

    ![Rete virtuale - creazione rapida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. Fare clic su **Creazione rapida** per creare una rete virtuale.

6. Specificare un **Nome** per la virtuale di rete e provare a eseguire le operazioni seguenti:
    * Si può scegliere di configurare i campi **Spazio indirizzo** o **Numero massimo VM** per la rete.
    * Per il momento è possibile confermare l'impostazione **Nessuno** per **Server DNS**. È possibile aggiornare l'impostazione dopo l'abilitazione di Azure Active Directory Domain Services.
7. Selezionare un'area di Azure supportata nell'elenco a discesa **Località**.  
    Per conoscere le aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).
8. Per creare la rete virtuale, fare clic sul pulsante **Crea rete virtuale**.

    ![Creare una rete virtuale per Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. Dopo aver creato la rete virtuale, selezionarle il nome delle rete virtuale e fare clic sulla scheda **Configura**.

    ![Creare una subnet](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Negli **spazi di indirizzi della rete virtuale**, fare clic su **Aggiungi subnet**, quindi specificare una subnet con il nome **AaddsSubnet**.

    ![Creare una subnet per Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. Fare clic su **Salva** per creare la subnet.


<a id="next-step" class="xliff"></a>

## Passaggio successivo
[Attività 3: Abilitare Azure Active Directory Domain Services](active-directory-ds-getting-started-enableaadds.md)

