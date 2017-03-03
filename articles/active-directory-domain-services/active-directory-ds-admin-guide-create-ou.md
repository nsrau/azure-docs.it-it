---
title: 'Azure Active Directory Domain Services: Guida all&quot;amministrazione | Documentazione Microsoft'
description: "Creare un&quot;unità organizzativa (OU) nei domini gestiti di Servizi di dominio Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 6e0da01df8ac5fd3cdb6b4e42dfbc08fab7d9615
ms.openlocfilehash: 5931d532a0790f1570d1d99687611231aafc7596
ms.lasthandoff: 02/08/2017


---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Creare un'unità organizzativa (OU) in un dominio gestito di Servizi di dominio Azure AD
I domini gestiti di Servizi di dominio Azure AD includono due contenitori predefiniti denominati rispettivamente "AADDC Computers" e "AADDC Users". Il contenitore "AADDC Computers" include oggetti computer per tutti i computer aggiunti al dominio gestito. Il contenitore "AADDC Users" include utenti e gruppi nel tenant Azure AD. In alcuni casi può essere necessario creare account del servizio nel dominio gestito per distribuire i carichi di lavoro. A questo scopo, è possibile creare un'unità organizzativa (OU) personalizzata nel dominio gestito e creare gli account del servizio nell'unità organizzativa. Questo articolo descrive come creare una OU nel dominio gestito.

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:

1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](active-directory-ds-getting-started.md).
4. Una macchina virtuale aggiunta al dominio da cui si amministrerà il dominio gestito di Servizi di dominio Azure AD. Se non è disponibile una macchina virtuale di questo tipo, seguire tutte le attività illustrate nell'articolo [Aggiungere una macchina virtuale Windows Server a un dominio gestito](active-directory-ds-admin-guide-join-windows-vm.md).
5. È necessario disporre delle credenziali di un **account utente appartenente al gruppo "AAD DC Administrators"** nella directory per poter creare un'unità organizzativa personalizzata per il dominio gestito.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installare gli strumenti di amministrazione di AD in una macchina virtuale aggiunta al dominio per l'amministrazione remota
I domini gestiti di Servizi di dominio Azure AD possono essere gestiti in remoto con i familiari strumenti di amministrazione di Active Directory, ad esempio il Centro di amministrazione di Active Directory o AD PowerShell. Gli amministratori tenant non hanno i privilegi necessari per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. Per amministrare il dominio gestito, installare la funzionalità Strumenti di amministrazione di AD in una macchina virtuale aggiunta al dominio gestito. Per istruzioni, vedere l'articolo [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md) .

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Creare un'unità organizzativa nel dominio gestito
Dopo aver installato gli strumenti di amministrazione di AD nella macchina virtuale aggiunta al dominio, sarà possibile usarli per creare un'unità organizzativa nel dominio gestito. Eseguire la procedura seguente:

> [!NOTE]
> Solo i membri del gruppo "AAD DC Administrators" hanno i privilegi necessari per creare un'unità organizzativa personalizzata. Assicurarsi di eseguire la procedura seguente come utente appartenente a questo gruppo.
> 
> 

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Gli strumenti di amministrazione di AD risulteranno installati nella macchina virtuale.
   
    ![Strumenti di amministrazione installati nel server](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Fare clic su **Centro di amministrazione di Active Directory**.
   
    ![Centro di amministrazione di Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Per visualizzare il dominio, fare clic sul nome di dominio nel riquadro a sinistra, ad esempio "contoso100.com".
   
    ![Centro di amministrazione di Active Directory - Visualizza dominio](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. Nel riquadro **Attività** sul lato destro fare clic su **Nuovo** sotto il nodo del nome di dominio. In questo esempio fare clic su **Nuovo** sotto il nodo "contoso100(local)" nel riquadro **Attività** sul lato destro.
   
    ![Centro di amministrazione di Active Directory - Nuova OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Verrà visualizzata l'opzione per creare un'unità organizzativa. Fare clic su **Unità organizzativa** per visualizzare la finestra di dialogo **Crea unità organizzativa**.
6. Nella finestra di dialogo **Crea unità organizzativa** specificare un **Nome** per la nuova unità organizzativa. Fornire una breve descrizione per la OU. È anche possibile impostare il campo **Gestita da** per la OU. Toccare **OK**per creare l'unità organizzativa personalizzata.
   
    ![Centro di amministrazione di Active Directory - finestra di dialogo Crea OU](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. La OU appena creato verrà visualizzata nel Centro di amministrazione di Active Directory.
   
    ![Centro di amministrazione di Active Directory - OU creata](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Autorizzazioni/sicurezza per le OU appena create
Per impostazione predefinita, all'utente, membro del gruppo "AAD DC Administrators", che ha creato l'unità organizzativa personalizzata verranno concessi privilegi amministrativi con controllo completo per l'unità organizzativa. L'utente può quindi procedere alla concessione di autorizzazioni ad altri utenti o al gruppo "AAD DC Administrators" secondo le esigenze. Come illustrato nello screenshot seguente, all'utente 'bob@domainservicespreview.onmicrosoft.com' che ha creato la nuova unità organizzativa "MyCustomOU" è concesso il controllo completo dell'unità organizzativa.

 ![Centro di amministrazione di Active Directory - Sicurezza della nuova OU](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Note sull'amministrazione delle OU personalizzate
Dopo avere creato un'unità organizzativa personalizzata, è possibile procedere alla creazione di utenti, gruppi, computer e account del servizio in questa unità organizzativa. Non è possibile spostare utenti o gruppi dall'unità organizzativa "AADDC Users" dell'utente alle unità organizzative personalizzate.

> [!WARNING]
> Account utente, gruppi, account del servizio e oggetti computer creati in unità organizzative personalizzate non sono disponibili nel tenant di Azure AD. In altre parole, questi oggetti non saranno visualizzati con l'API Graph di Azure AD o nell'interfaccia utente di Azure AD. Questi oggetti saranno disponibili solo nel dominio gestito di Servizi di dominio Azure AD.
> 
> 

## <a name="related-content"></a>Contenuti correlati
* [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Configurare criteri di gruppo in un dominio gestito](active-directory-ds-admin-guide-administer-group-policy.md)
* [Centro di amministrazione di Active Directory: Introduzione](https://technet.microsoft.com/library/dd560651.aspx)
* [Guida dettagliata agli account del servizio gestiti](https://technet.microsoft.com/library/dd548356.aspx)


