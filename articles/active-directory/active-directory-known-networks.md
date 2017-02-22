---
title: Reti note nel portale di Azure classico | Documentazione Microsoft
description: "Configurando le reti note si evita che indirizzi IP di proprietà dell&quot;organizzazione vengano inclusi nei report Accessi da più aree geografiche e Accessi da indirizzi IP con attività sospetta."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c8f41a5f9dcb6a069850c59e18dfdcfa92daa333
ms.openlocfilehash: f14ced0c578fc54a48dd027aedfc24ad3478aef4


---
# <a name="known-networks"></a>Reti note

> [!div class="op_single_selector"]
> * [Portale di Azure classico](active-directory-known-networks.md)
> * [Portale di Azure](active-directory-known-networks-azure-portal.md)
> 
> 


È possibile usare i report di utilizzo e accesso di Azure Active Directory per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione. Con queste informazioni un amministratore di directory può stabilire meglio dove potrebbero esserci possibili rischi per la sicurezza in modo da poterne pianificare adeguatamente la riduzione.

È possibile che i report "*Accessi da più aree geografiche*" e "*Accessi da indirizzi IP con attività sospetta*" indichino erroneamente indirizzi IP che sono effettivamente di proprietà dell'organizzazione. 

Questo può avvenire, ad esempio, nei casi seguenti: 

* L'accesso di un utente dell'ufficio di Boston in modalità remota al data center di San Francisco attiva il report "Accessi da più aree geografiche" 
* Un utente dell'organizzazione tenta di accedere più volte con una password non corretta, attivando il report "Accessi da indirizzi IP con attività sospetta" 

Per impedire che casi come questi generino report sulla sicurezza fuorvianti, è necessario aggiungere gli intervalli di indirizzi IP noti all'elenco di indirizzi IP pubblici dell'organizzazione.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Per aggiungere gli intervalli di indirizzi IP pubblici dell'organizzazione, seguire questa procedura:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).

2. Nel riquadro di sinistra fare clic su **Active Directory**. 

    ![Reti note](./media/active-directory-known-networks/known-netwoks-01.png)

3. Selezionare la propria directory nella scheda **Directory** .

4. Nel menu in alto fare clic su **Configura**. 

    ![Reti note](./media/active-directory-known-networks/known-netwoks-02.png)

5. Nella scheda Configura passare agli **intervalli di indirizzi IP pubblici dell'organizzazione** 

    ![Reti note](./media/active-directory-known-networks/known-netwoks-03.png)

6. Fare clic su **Aggiungi intervalli di indirizzi IP noti**.

7. Aggiungere gli intervalli di indirizzi nella finestra di dialogo visualizzata e quindi fare clic sul segno di spunta al termine dell'operazione. 

    ![Reti note](./media/active-directory-known-networks/known-netwoks-04.png)

**Risorse aggiuntive:**

* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)
* [Accessi da indirizzi IP con attività sospetta](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Accessi da più aree geografiche](active-directory-reporting-sign-ins-from-multiple-geographies.md)




<!--HONumber=Jan17_HO3-->


