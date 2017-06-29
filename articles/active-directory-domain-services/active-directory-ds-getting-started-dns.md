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
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: dacd689576dce65bbf1a975409ea7d7f2c3ada90
ms.contentlocale: it-it
ms.lasthandoff: 06/14/2017


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
> Le macchine virtuali nella rete otterranno le nuove impostazioni DNS solo al riavvio. Se è necessario ottenere subito le impostazioni DNS aggiornate, eseguire il riavvio dal portale, da PowerShell o dall'interfaccia della riga di comando.
>
>

## <a name="next-steps"></a>Passaggi successivi
Attività 5: [Abilitare la sincronizzazione password con Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

