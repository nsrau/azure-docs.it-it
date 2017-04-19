---
title: 'Azure Active Directory Domain Services: aggiornare le impostazioni DNS per la rete virtuale di Azure | Microsoft Docs'
description: Introduzione a Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aggiornare le impostazioni DNS per la rete virtuale di Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Attività 4: Aggiornare le impostazioni DNS per la rete virtuale di Azure
Nelle attività di configurazione precedenti è stata completata l'abilitazione di Azure Active Directory Domain Services per la directory. L'attività successiva consiste nell'assicurarsi che i computer della rete virtuale possano connettersi a questi servizi e utilizzarli. In questo articolo si aggiorneranno le impostazioni del server DNS per la rete virtuale in modo che puntino ai due indirizzi IP in cui è disponibile Azure Active Directory Domain Services nella rete virtuale.

> [!NOTE]
> Dopo aver abilitato Azure Active Directory Domain Services per la directory, prendere nota degli indirizzi IP per Azure Active Directory Domain Services visualizzati nella scheda **Configura** della directory.
>
>

Per aggiornare l'impostazione del server DNS per la rete virtuale in cui è stato abilitato Azure Active Directory Domain Services, seguire questa procedura:

1. Passare al [portale di Azure classico](https://manage.windowsazure.com).
2. Nel riquadro sinistro selezionare **Reti**.  
    Verrà visualizzata la finestra **Reti**.

    ![Finestra delle reti virtuali](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. Nella scheda **Reti virtuali** selezionare la rete virtuale in cui è stato abilitato Azure Active Directory Domain Services per visualizzarne le proprietà.
4. Fare clic sulla scheda **Configura**.

    ![Finestra delle reti virtuali](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Nella sezione **Server DNS** immettere entrambi gli indirizzi IP visualizzati nella sezione **Servizi di dominio** della scheda **Configura** della directory.
6. Per salvare le impostazioni del server DNS per la rete virtuale, nel riquadro attività nella parte inferiore della finestra fare clic su **Salva**.

   ![Aggiornare le impostazioni del server DNS per la rete virtuale](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Dopo l'aggiornamento delle impostazioni del server DNS per la rete virtuale, l'aggiornamento della configurazione DNS nelle macchine virtuali della rete potrebbe richiedere tempo. Se una macchina virtuale non riesce a connettersi al dominio, è possibile scaricare la cache DNS (ipconfig /flushdns) nella macchina virtuale. Questo comando forza un aggiornamento delle impostazioni DNS nella macchina virtuale.
>
>

## <a name="next-steps"></a>Passaggi successivi
Attività 5: [Abilitare la sincronizzazione password con Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

