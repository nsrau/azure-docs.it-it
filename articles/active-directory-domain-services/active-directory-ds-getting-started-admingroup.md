---
title: 'Azure Active Directory Domain Services: Introduzione | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ergreenl
ms.openlocfilehash: cac1814057b5adbb75d9484b332bd3f8aaa31a18
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446859"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure


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

## <a name="configure-synchronization"></a>Configurare la sincronizzazione

Azure AD Domain Services consente di scegliere tra la sincronizzazione completa di tutti gli utenti e i gruppi disponibili in Azure AD o la sincronizzazione con ambito per sincronizzare solo gruppi specifici. Se si sceglie la sincronizzazione completa, **non** sarà possibile scegliere la sincronizzazione con ambito in un secondo momento. Per altre informazioni sulla sincronizzazione con ambito, vedere l'[articolo sulla sincronizzazione con ambito in Azure AD Domain Services](active-directory-ds-scoped-synchronization.md).

### <a name="full-synchronization"></a>Sincronizzazione completa

1. Per scegliere la sincronizzazione completa, è sufficiente fare clic su "OK" nella parte inferiore della schermata, trattandosi dell'opzione predefinita.
    ![Sincronizzazione completa](./media\active-directory-domain-services-admin-guide\create-sync-all.PNG)

### <a name="scoped-synchronization"></a>Sincronizzazione con ambito

1. Fare clic sul pulsante di sincronizzazione per attivare/disattivare la modalità "Con ambito". Verrà visualizzata la pagina Selezione gruppi. Da questa pagina è possibile visualizzare i gruppi già selezionati per la sincronizzazione con il dominio gestito.
    ![Sincronizzazione con ambito](media\active-directory-domain-services-admin-guide\create-sync-scoped.PNG)
2. Fare clic su **Selezione gruppi** nella barra di spostamento in alto. Sul lato della schermata verrà visualizzata una finestra per la selezione dei gruppi che è possibile usare per selezionare tutti i gruppi aggiuntivi da sincronizzare con Azure AD Domain Services. Al termine, fare clic su **Seleziona** per chiudere la finestra per la selezione dei gruppi e aggiungerli all'elenco selezionato.
    ![Selezione dei gruppi per la sincronizzazione con ambito](media\active-directory-domain-services-admin-guide\create-sync-scoped-groupselect.PNG)
3. Fare clic su **OK** per passare alla pagina di riepilogo.

## <a name="deploy-your-managed-domain"></a>Distribuire il dominio gestito

1. Nella pagina **Riepilogo** della procedura guidata controllare le impostazioni di configurazione per il dominio gestito. Se necessario, è possibile tornare a qualsiasi passaggio precedente della procedura guidata per eseguire le modifiche desiderate. Al termine, fare clic su **OK** per creare il nuovo dominio gestito.

    ![Summary](./media/getting-started/domain-services-blade-summary.png)

2. Viene visualizzata una notifica che mostra lo stato di avanzamento della distribuzione di Azure AD Domain Services. Fare clic sulla notifica per visualizzare lo stato di avanzamento dettagliato del processo di distribuzione.

    ![Notifica - Distribuzione in corso](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="check-the-deployment-status-of-your-managed-domain"></a>Verificare lo stato della distribuzione del dominio gestito
Il processo di provisioning del dominio gestito può richiedere fino a un'ora.

1. Mentre è in corso la distribuzione, è possibile cercare "domain services" nella casella di ricerca **Cerca risorse**. Selezionare **Azure AD Domain Services** dai risultati della ricerca. Nel pannello **Azure AD Domain Services** viene elencato il dominio gestito di cui viene effettuato il provisioning.

    ![Trovare il dominio gestito di cui viene effettuato il provisioning](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Fare clic sul nome del dominio gestito (ad esempio, 'contoso100.com') per visualizzare altre informazioni sul dominio gestito.

    ![Domain Services - Stato del provisioning](./media/getting-started/domain-services-provisioning-state.png)

3. La scheda **Panoramica** indica che il dominio gestito è attualmente sottoposto a provisioning. Non è possibile configurare il dominio gestito fino a quando non ne è stato completato il provisioning. Per completare il provisioning del dominio gestito può essere necessaria fino a un'ora.

    ![Domain Services - Scheda Panoramica durante lo stato di provisioning ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Al termine del provisioning del dominio gestito, nella scheda **Panoramica** il dominio gestito è impostato su **In esecuzione**.

    ![Domain Services - Scheda Panoramica al termine del provisioning](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Durante il processo di provisioning, Azure AD Domain Services crea le applicazioni aziendali "Domain Controller Services" e "AzureActiveDirectoryDomainControllerServices" all'interno della directory dell'organizzazione. Queste applicazioni aziendali sono necessarie per gestire il dominio gestito ed è fondamentale che non vengono eliminate in alcuna circostanza.
    >

5. Nella scheda **Proprietà** vengono visualizzati i due indirizzi IP in cui sono disponibili i controller di dominio per la rete virtuale.

    ![Domain Services - Scheda Proprietà al termine del provisioning](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>Richiesta di assistenza
Eseguire il provisioning di entrambi i controller di dominio del dominio gestito potrebbe richiedere un'ora o due. Se la distribuzione non è riuscita o è bloccata nello stato 'In sospeso' per più di un paio d'ore, [contattare il team del prodotto per assistenza](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Passaggio successivo
[Attività 4: Aggiornare le impostazioni DNS per la rete virtuale di Azure](active-directory-ds-getting-started-dns.md)
