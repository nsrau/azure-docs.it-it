---
title: 'Azure AD Domain Services: creare o selezionare una rete virtuale | Documentazione Microsoft'
description: Introduzione a Servizi di dominio Azure Active Directory
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
ms.date: 10/03/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9e933774e3b618b1584b4f24a0491eda49e42077


---
# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Creare o selezionare una rete virtuale per Servizi di dominio Azure AD
## <a name="guidelines-to-select-an-azure-virtual-network"></a>Linee guida per la selezione di una rete virtuale di Azure
> [!NOTE]
> **Prima di iniziare**, vedere [Networking considerations for Azure AD Domain Services](active-directory-ds-networking.md)(Considerazioni sulla rete per Servizi di dominio Azure AD).
> 
> 

## <a name="task-2-create-an-azure-virtual-network"></a>Attività 2: Creare una rete virtuale di Azure
L'attività di configurazione successiva consiste nel creare una rete virtuale di Azure e la relativa subnet. Abilitare Azure AD Domain Services in questa subnet della rete virtuale. Se si dispone già di una rete virtuale esistente, che si preferisce usare, è possibile ignorare questo passaggio.

> [!NOTE]
> Assicurarsi che la rete virtuale di Azure che si crea o si sceglie di usare con Servizi di dominio Azure AD appartenga a un'area di Azure supportata da Servizi di dominio Azure AD. Per informazioni sulle aree di Azure in cui è disponibile Servizi di dominio Azure AD, vedere la pagina relativa ai [servizi di Azure per area](https://azure.microsoft.com/regions/#services/) .
> 
> 

Prendere nota del nome della rete virtuale per selezionare la rete virtuale corretta al momento dell'abilitazione di Servizi di dominio Azure AD in un passaggio di configurazione successivo.

Eseguire i passaggi di configurazione seguenti per creare una rete virtuale di Azure in cui si vuole abilitare Servizi di dominio Azure AD.

1. Passare al **portale di Azure classico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selezionare il nodo **Reti** nel riquadro sinistro.
   
    ![Reti virtuali](./media/active-directory-domain-services-getting-started/networks-node.png)
3. Fare clic su **NUOVO** nel riquadro attività nella parte inferiore della pagina.
   
    ![Nodo Reti virtuali](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Nel nodo **Servizi di rete** selezionare **Rete virtuale**.
5. Fare clic su **Creazione rapida** per creare una rete virtuale.
   
    ![Rete virtuale - creazione rapida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
6. Specificare un **Nome** per la rete virtuale. Si può anche scegliere di configurare i campi **Spazio di indirizzi** o **Numero massimo VM** per la rete. Per il momento è possibile confermare l'impostazione Per il momento è possibile confermare l'impostazione "Nessuno" per **Server DNS**. È possibile aggiornare l'impostazione Server DNS dopo l'abilitazione di Azure AD Domain Services.
7. Assicurarsi di selezionare un'area di Azure supportata nell'elenco a discesa **Percorso** . Per informazioni sulle aree di Azure in cui è disponibile Servizi di dominio Azure AD, vedere la pagina relativa ai [servizi di Azure per area](https://azure.microsoft.com/regions/#services/) .
8. Per creare la rete virtuale, fare clic sul pulsante **Crea rete virtuale** .
   
    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. Dopo aver creato la rete virtuale, selezionarla e fare clic sulla scheda **CONFIGURA**.
   
    ![Creare una subnet](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Passare alla sezione **Spazi di indirizzi della rete virtuale**. Fare clic su **Aggiungi subnet** e specificare una subnet denominata **AaddsSubnet**. Fare clic su **Salva** per creare la subnet.
    
    ![Creare una subnet per Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Attività 3: Abilitare Servizi di dominio Azure AD
L'attività di configurazione successiva consiste nell' [abilitare Servizi di dominio Azure AD](active-directory-ds-getting-started-enableaadds.md).




<!--HONumber=Dec16_HO2-->


