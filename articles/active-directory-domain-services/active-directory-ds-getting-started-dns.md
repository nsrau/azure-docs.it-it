---
title: 'Azure AD Domain Services: aggiornare le impostazioni DNS per la rete virtuale di Azure | Documentazione Microsoft'
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
ms.date: 09/21/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 81c0564390c57c7ee001ce5a574ab670faf898b4


---
# <a name="azure-ad-domain-services-update-dns-settings-for-the-azure-virtual-network"></a>Azure Active Directory Domain Services: Aggiornare le impostazioni DNS per la rete virtuale di Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Attività 4: Aggiornare le impostazioni DNS per la rete virtuale di Azure
Nelle attività di configurazione precedenti è stata completata l'abilitazione di Azure Active Directory Domain Services per la directory. L'attività successiva consiste nell'assicurarsi che i computer della rete virtuale possano connettersi a questi servizi e utilizzarli. Aggiornare le impostazioni del server DNS per la rete virtuale in modo che punti ai due indirizzi IP in cui è disponibile Azure Active Directory Domain Services nella rete virtuale.

> [!NOTE]
> Prendere nota degli indirizzi IP per Servizi di dominio Azure AD visualizzati nella scheda **Configura** della directory, dopo avere abilitato Servizi di dominio Azure AD per la directory.
> 
> 

Eseguire i passaggi di configurazione seguenti per aggiornare l'impostazione del server DNS per la rete virtuale in cui è stato abilitato Azure Active Directory Domain Services.

1. Passare al **portale di Azure classico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selezionare il nodo **Reti** nel riquadro sinistro.
   
    ![Nodo Reti virtuali](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. Nella scheda **Reti virtuali** selezionare la rete virtuale in cui è abilitato Servizi di dominio Azure AD per visualizzarne le proprietà.
4. Fare clic sulla scheda **Configure** .
   
    ![Nodo Reti virtuali](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Nella sezione **Server DNS** immettere gli indirizzi IP di Servizi di dominio Azure AD.
6. Assicurarsi di immettere entrambi gli indirizzi IP visualizzati nella sezione **Servizi di dominio** della scheda **Configura** della directory.
7. Per salvare le impostazioni del server DNS per la rete virtuale, fare clic su **Salva** nel riquadro attività nella parte inferiore della pagina.
   
   ![Aggiornare le impostazioni del server DNS per la rete virtuale.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Dopo l'aggiornamento delle impostazioni del server DNS per la rete virtuale, l'aggiornamento della configurazione DNS per le reti virtuali nella rete potrebbe richiedere del tempo. Se una macchina virtuale non riesce a connettersi al dominio, è possibile scaricare la cache DNS, ad esempio con ipconfig /flushdns, nella macchina virtuale. Questo comando forza un aggiornamento delle impostazioni DNS nella macchina virtuale.
> 
> 

## <a name="task-5-enable-password-synchronization-to-azure-ad-domain-services"></a>Attività 5: Abilitare la sincronizzazione password in Servizi di dominio Azure AD.
L'attività di configurazione successiva consiste nell' [abilitare la sincronizzazione password in Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md).




<!--HONumber=Nov16_HO2-->


