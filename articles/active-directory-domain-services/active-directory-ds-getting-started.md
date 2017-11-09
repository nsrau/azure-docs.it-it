---
title: 'Azure Active Directory Domain Services: Introduzione | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 7d80049d4b6f7d57924522e3f273c42f4c887fee
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure
Questo articolo illustra come abilitare Azure Active Directory Domain Services (Azure AD DS) tramite il portale di Azure.

Per avviare la procedura guidata **Abilita Azure AD Domain Services**, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro fare clic su **Nuovo**.
3. Nella pagina **Nuovo** digitare **Domain Services** nella barra di ricerca.

    ![Ricerca di Domain Services](./media/getting-started/search-domain-services.png)

4. Fare clic per selezionare **Azure AD Domain Services** dall'elenco dei suggerimenti di ricerca. Nella pagina **Azure AD Domain Services** fare clic sul pulsante **Crea**.

    ![Vista dei servizi di dominio](./media/getting-started/domain-services-blade.png)

5. Viene avviata la procedura guidata **Abilita Azure AD Domain Services**.


## <a name="task-1-configure-basic-settings"></a>Attività 1: Configurare le impostazioni di base
Nella pagina **Informazioni di base** della procedura guidata è possibile specificare il nome di dominio DNS relativo al dominio gestito. È possibile anche scegliere il gruppo di risorse e la località di Azure in cui deve essere distribuito il dominio gestito.

![Configurare le informazioni di base](./media/getting-started/domain-services-blade-basics.png)

1. Scegliere il **Nome dominio DNS** per il dominio gestito.

   > [!NOTE]
   > **Linee guida per la selezione di un nome di dominio DNS**
   > * **Nome di dominio predefinito**: per impostazione predefinita, la procedura guidata specifica il nome di dominio predefinito della directory con suffisso **.onmicrosoft.com**. Se si sceglie di abilitare l'accesso LDAP sicuro al dominio gestito su Internet, si verificheranno problemi nella creazione di un record DNS pubblico o nell'ottenimento di un certificato LDAP sicuro da un'autorità di certificazione pubblica per questo nome di dominio. Microsoft è proprietaria del dominio *.onmicrosoft.com* e l'autorità di certificazione non emette certificati a garanzia di questo dominio.
   * **Nomi di dominio personalizzato**: è anche possibile immettere un nome di dominio personalizzato. In questo esempio, il nome di dominio personalizzato è *contoso100.com*.
   * **Suffissi di dominio non instradabili**: in genere è consigliabile evitare suffissi di dominio non instradabili. È ad esempio preferibile evitare di creare un dominio con il nome di dominio DNS 'contoso.local'. Il suffisso DNS '.local' non è instradabile e può causare problemi con la risoluzione DNS.
   * **Limitazioni dei prefissi di dominio**: il prefisso del nome di dominio specificato, ad esempio *contoso100* nel nome di dominio *contoso100.com*, può contenere al massimo 15 caratteri. Non è possibile creare un dominio gestito con un prefisso più lungo di 15 caratteri.
   * **Conflitti dei nomi di rete**: assicurarsi che il nome di dominio DNS scelto per il dominio gestito non esista già nella rete virtuale. In particolare, verificare se:
       * È già presente un dominio di Active Directory con lo stesso nome di dominio DNS nella rete virtuale.
       * La rete virtuale in cui si intende abilitare il dominio gestito ha una connessione VPN alla rete locale. In questo caso, verificare che non sia presente un dominio con lo stesso nome di dominio DNS nella rete locale.
       * Esiste un servizio cloud con lo stesso nome della rete virtuale.
    >

2. Selezionare la **Sottoscrizione** di Azure in cui si vuole creare il dominio gestito.

3. Selezionare il **Gruppo di risorse** a cui deve appartenere il dominio gestito. Quando si seleziona il gruppo di risorse, è possibile scegliere tra le opzioni **Crea nuovo** e **Usa esistente**.

4. Scegliere la **Località** di Azure in cui deve essere creato il dominio gestito. Nella pagina **Rete** della procedura guidata vengono visualizzate solo le reti virtuali appartenenti alla località selezionata.

5. Al termine, fare clic su **OK** per passare alla pagina **Rete** della procedura guidata.


## <a name="next-step"></a>Passaggio successivo
[Attività 2: Configurare le impostazioni di rete](active-directory-ds-getting-started-network.md)
