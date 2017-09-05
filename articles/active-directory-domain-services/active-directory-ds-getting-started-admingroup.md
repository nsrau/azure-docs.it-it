---
title: 'Azure Active Directory Domain Services: Introduzione | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure (Anteprima)
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
ms.date: 07/15/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f87bcf33d3b1eb21c7d84814e4c4086f664e293d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure (Anteprima)


## <a name="task-3-configure-administrative-group"></a>Attività 3: Configurare un gruppo amministrativo
In questa attività di configurazione si crea un gruppo amministrativo nella directory di Azure AD. Questo gruppo amministrativo speciale è chiamato *AAD DC Administrators*. Ai membri di questo gruppo vengono concesse autorizzazioni amministrative per i computer aggiunti al dominio gestito. Nei computer appartenenti a un dominio viene aggiunto al gruppo degli amministratori. Inoltre, i membri di questo gruppo possono usare Desktop remoto per connettersi ai computer del dominio da remoto.

> [!NOTE]
> Non sarà possibile esercitare i privilegi di amministratore di dominio o amministratore dell'organizzazione all'interno del dominio gestito creato con Azure Active Directory Domain Services. Nei domini gestiti questi privilegi sono riservati dal servizio e non vengono resi disponibili agli utenti all'interno del tenant. Per poter eseguire alcune operazioni con privilegi, sarà tuttavia possibile usare il gruppo amministrativo speciale creato in questa attività di configurazione. Queste operazioni prevedono l'aggiunta di computer al dominio, l'appartenenza al gruppo di amministrazione su computer aggiunti al dominio e la configurazione di Criteri di gruppo.
>

La procedura guidata crea automaticamente il gruppo amministrativo nella directory di Azure AD. Il gruppo viene chiamato "Amministratori di AAD DC". Se si dispone di un gruppo esistente con questo nome nella directory di Azure AD, la procedura guidata seleziona questo gruppo. È possibile configurare l'appartenenza al gruppo usando la pagina **Gruppo Amministratori** della procedura guidata.

1. Per configurare l'appartenenza al gruppo, fare clic su **Amministratori di AAD DC**.

    ![Configurare l'appartenenza al gruppo](./media/getting-started/domain-services-blade-admingroup.png)

2. Fare clic su **Aggiungi membri** per aggiungere gli utenti dalla directory di Azure AD al gruppo di amministratori.

3. Al termine, fare clic su **OK** per passare alla pagina **Riepilogo** della procedura guidata.

4. Nella pagina **Riepilogo** della procedura guidata controllare le impostazioni di configurazione per il dominio gestito. Se necessario, è possibile tornare a qualsiasi passaggio precedente della procedura guidata per eseguire le modifiche desiderate. Al termine, fare clic su **OK** per creare il nuovo dominio gestito.

    ![Riepilogo](./media/getting-started/domain-services-blade-summary.png)

5. Viene visualizzata una notifica che mostra lo stato di avanzamento della distribuzione di Azure AD Domain Services. Fare clic sulla notifica per visualizzare lo stato di avanzamento dettagliato del processo di distribuzione.

    ![Notifica - Distribuzione in corso](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="provision-your-managed-domain"></a>Effettuare il provisioning del dominio gestito
Il processo di provisioning del dominio gestito può richiedere fino a un'ora.

1. Mentre è in corso la distribuzione, è possibile cercare "domain services" nella casella di ricerca **Cerca risorse**. Selezionare **Azure AD Domain Services** dai risultati della ricerca. Nel pannello **Azure AD Domain Services** viene elencato il dominio gestito di cui viene effettuato il provisioning.

    ![Trovare il dominio gestito di cui viene effettuato il provisioning](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Fare clic sul nome del dominio gestito (ad esempio, "contoso100.com") per visualizzare altre informazioni sul dominio.

    ![Domain Services - Stato del provisioning](./media/getting-started/domain-services-provisioning-state.png)

3. La scheda **Panoramica** indica che il dominio è attualmente sottoposto a provisioning. Non è possibile configurare il dominio gestito fino a quando non ne è stato completato il provisioning. Per completare il provisioning del dominio gestito può essere necessaria fino a un'ora.

    ![Domain Services - Scheda Panoramica durante lo stato di provisioning ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Al termine del provisioning del dominio gestito, nella scheda **Panoramica** il dominio gestito è impostato su **In esecuzione**.

    ![Domain Services - Scheda Panoramica al termine del provisioning](./media/getting-started/domain-services-provisioned.png)

5. Nella scheda **Proprietà** vengono visualizzati i due indirizzi IP in cui sono disponibili i controller di dominio per la rete virtuale.

    ![Domain Services - Scheda Proprietà al termine del provisioning](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="next-step"></a>Passaggio successivo
[Attività 4: Aggiornare le impostazioni DNS per la rete virtuale di Azure](active-directory-ds-getting-started-dns.md)

