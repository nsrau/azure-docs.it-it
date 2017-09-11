---
title: 'Azure Active Directory Domain Services: Introduzione | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure (anteprima)
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
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: d2d570bc2bab3bfd28282cd95b0a1be65e96743d
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure (Anteprima)
Questo articolo illustra come abilitare Azure Active Directory Domain Services (Azure AD DS) tramite il portale di Azure.

Per avviare la procedura guidata **Abilita Azure AD Domain Services**, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro fare clic su **Nuovo**.
3. Nel pannello **Nuovo** digitare **Domain Services** nella barra di ricerca.

    ![Ricerca di Domain Services](./media/getting-started/search-domain-services.png)

4. Fare clic per selezionare **Azure AD Domain Services** dall'elenco dei suggerimenti di ricerca. Nel pannello **Azure AD Domain Services** fare clic sul pulsante **Crea**.

    ![Pannello Domain Services](./media/getting-started/domain-services-blade.png)

5. Viene avviata la procedura guidata **Abilita Azure AD Domain Services**.


## <a name="task-1-configure-basic-settings"></a>Attività 1: Configurare le impostazioni di base
Nella pagina **Informazioni di base** della procedura guidata è possibile specificare il nome di dominio DNS relativo al dominio gestito. È possibile anche scegliere il gruppo di risorse e la località di Azure in cui deve essere distribuito il dominio gestito.

![Configurare le informazioni di base](./media/getting-started/domain-services-blade-basics.png)

1. Scegliere il **Nome dominio DNS** per il dominio gestito.

   * Il nome di dominio predefinito della directory (con suffisso **.onmicrosoft.com**) viene specificato per impostazione predefinita.

   * È anche possibile immettere un nome di dominio personalizzato. In questo esempio, il nome di dominio personalizzato è *contoso100.com*.

     > [!WARNING]
     > Il prefisso del nome del dominio specificato (ad esempio, *contoso100* nel nome di dominio *contoso100.com*) può contenere massimo 15 caratteri. Non è possibile creare un dominio gestito con un prefisso più lungo di 15 caratteri.
     >
     >

2. Assicurarsi che il nome di dominio DNS scelto per il dominio gestito non esista già nella rete virtuale. In particolare, verificare se:

   * È già presente un dominio con lo stesso nome di dominio DNS nella rete virtuale.

   * La rete virtuale in cui si intende abilitare il dominio gestito ha una connessione VPN alla rete locale. In questo caso, verificare che non sia presente un dominio con lo stesso nome di dominio DNS nella rete locale.

   * Esiste un servizio cloud con lo stesso nome della rete virtuale.

3. Scegliere il **tipo di rete virtuale**. Per impostazione predefinita, viene selezionato il tipo di rete virtuale **Resource Manager**. È consigliabile usare questo tipo di rete virtuale per tutti i nuovi domini gestiti creati.

4. Selezionare la **Sottoscrizione** di Azure in cui si vuole creare il dominio gestito.

5. Selezionare il **Gruppo di risorse** a cui deve appartenere il dominio gestito. Quando si seleziona il gruppo di risorse, è possibile scegliere tra le opzioni **Crea nuovo** e **Usa esistente**.

6. Scegliere la **Località** di Azure in cui deve essere creato il dominio gestito. Nella pagina **Rete** della procedura guidata vengono visualizzate solo le reti virtuali appartenenti alla località selezionata.

7. Al termine, fare clic su **OK** per passare alla pagina **Rete** della procedura guidata.


## <a name="next-step"></a>Passaggio successivo
[Attività 2: Configurare le impostazioni di rete](active-directory-ds-getting-started-network.md)

