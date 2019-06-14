---
title: 'Azure Active Directory Domain Services: Introduzione | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 637ad62744affa37630df9c841f3c7529674e788
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246330"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure
Questo articolo illustra come abilitare Azure Active Directory Domain Services (Azure AD DS) tramite il portale di Azure.


## <a name="before-you-begin"></a>Prima di iniziare
Per completare le attività descritte in questo articolo, sono necessari gli elementi seguenti:

* Una **sottoscrizione di Azure**valida.
* Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
* La **sottoscrizione di Azure deve essere associata alla directory di Azure AD**.
* Per abilitare Azure AD Domain Services, sono necessari privilegi di **amministratore globale** per la directory di Azure AD.


## <a name="enable-azure-ad-domain-services"></a>Abilitare Azure Active Directory Domain Services

Per avviare la procedura guidata **Abilita Azure AD Domain Services**, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro fare clic su **Crea una risorsa**.
3. Nella pagina **Nuovo** digitare **Domain Services** nella barra di ricerca.

    ![Ricerca di Domain Services](./media/getting-started/search-domain-services.png)

4. Fare clic per selezionare **Azure AD Domain Services** dall'elenco dei suggerimenti di ricerca. Nella pagina **Azure AD Domain Services** fare clic sul pulsante **Crea**.

    ![Vista dei servizi di dominio](./media/getting-started/domain-services-blade.png)

5. Viene avviata la procedura guidata **Abilita Azure AD Domain Services**.


## <a name="task-1-configure-basic-settings"></a>Attività 1: Configurare le impostazioni di base
Nella pagina **Informazioni di base** della procedura guidata specificare il nome di dominio DNS relativo al dominio gestito. È possibile anche scegliere il gruppo di risorse e la località di Azure in cui deve essere distribuito il dominio gestito.

![Configurare le informazioni di base](./media/getting-started/domain-services-blade-basics.png)

1. Scegliere il **Nome dominio DNS** per il dominio gestito.

   > [!NOTE]
   > **Linee guida per la selezione di un nome di dominio DNS**
   > * **Nome di dominio predefinito:** per impostazione predefinita, la procedura guidata specifica il nome di dominio predefinito della directory con suffisso **.onmicrosoft.com**. Se si sceglie di abilitare l'accesso LDAP sicuro al dominio gestito su Internet, si verificheranno problemi nella creazione di un record DNS pubblico o nell'ottenimento di un certificato LDAP sicuro da un'autorità di certificazione pubblica per questo nome di dominio. Microsoft è proprietaria del dominio *.onmicrosoft.com* e l'autorità di certificazione non emette certificati a garanzia di questo dominio.
   > * **Nomi di dominio personalizzati:** È anche possibile immettere un nome di dominio personalizzato. In questo esempio, il nome di dominio personalizzato è *contoso100.com*.
   > * **Suffissi di dominio non instradabili:** in genere è consigliabile evitare suffissi di dominio non instradabili. È ad esempio preferibile evitare di creare un dominio con il nome di dominio DNS 'contoso.local'. Il suffisso DNS '.local' non è instradabile e può causare problemi con la risoluzione DNS.
   > * **Limitazioni dei prefissi di dominio:** Il prefisso del nome del dominio specificato (ad esempio, *contoso100* nel nome di dominio *contoso100.com*) può contenere massimo 15 caratteri. Non è possibile creare un dominio gestito con un prefisso più lungo di 15 caratteri.
   > * **Conflitti nei nomi di rete:** Assicurarsi che il nome di dominio DNS scelto per il dominio gestito non esista già nella rete virtuale. In particolare, verificare se:
   >     * È già presente un dominio di Active Directory con lo stesso nome di dominio DNS nella rete virtuale.
   >     * La rete virtuale in cui si intende abilitare il dominio gestito ha una connessione VPN alla rete locale. In questo caso, verificare che non sia presente un dominio con lo stesso nome di dominio DNS nella rete locale.
   >     * Esiste un servizio cloud con lo stesso nome della rete virtuale.

2. Selezionare la **Sottoscrizione** di Azure in cui si vuole creare il dominio gestito.

3. Selezionare il **Gruppo di risorse** a cui deve appartenere il dominio gestito. Per selezionare il gruppo di risorse, scegliere tra le opzioni **Crea nuovo** e **Usa esistente**.

4. Scegliere la **Località** di Azure in cui deve essere creato il dominio gestito. Nella pagina **Rete** della procedura guidata vengono visualizzate solo le reti virtuali appartenenti alla località selezionata.

5. Fare clic su **OK** per passare alla pagina **Rete** della procedura guidata.


## <a name="next-step"></a>Passaggio successivo
[Attività 2: Configurare le impostazioni di rete](active-directory-ds-getting-started-network.md)
