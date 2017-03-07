---
title: 'Azure Active Directory Domain Services: amministrare Criteri di gruppo in domini gestiti | Microsoft Docs'
description: Amministrare Criteri di gruppo in domini gestiti di Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 76987a6e91ae688b3856567073a7d27472e5ba09
ms.openlocfilehash: 9245eb870f592ee0a1f1d6956ce3d573f4902485
ms.lasthandoff: 01/28/2017


---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Amministrare Criteri di gruppo in un dominio gestito di Azure AD Domain Services
Azure Active Directory Domain Services include oggetti Criteri di gruppo (GPO) predefiniti per i contenitori "AADDC Users" e "AADDC Computers". È possibile personalizzare questi oggetti Criteri di gruppo predefiniti per configurare Criteri di gruppo nel dominio gestito. Inoltre, i membri del gruppo "AAD DC Administrators" possono creare le proprie unità organizzative nel dominio gestito. Possono inoltre creare oggetti Criteri di gruppo personalizzati e collegarli a tali unità organizzative. Agli utenti che appartengono al gruppo "AAD DC Administrators" vengono concessi privilegi di amministrazione di Criteri di gruppo nel dominio gestito.

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:

1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](active-directory-ds-getting-started.md).
4. Una **macchina virtuale aggiunta al dominio** da cui si amministrerà il dominio gestito di Servizi di dominio Azure AD. Se non è disponibile una macchina virtuale di questo tipo, seguire tutte le attività illustrate nell'articolo [Aggiungere una macchina virtuale Windows Server a un dominio gestito](active-directory-ds-admin-guide-join-windows-vm.md).
5. È necessario disporre delle credenziali di un **account utente appartenente al gruppo "AAD DC Administrators"** nella directory per poter amministrare i criteri di gruppo per il dominio gestito.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Attività 1: Eseguire il provisioning di una macchina virtuale aggiunta a un dominio per amministrare in remoto i criteri di gruppo per il dominio gestito
I domini gestiti di Servizi di dominio Azure AD possono essere gestiti in remoto con i familiari strumenti di amministrazione di Active Directory, ad esempio il Centro di amministrazione di Active Directory o AD PowerShell. Analogamente, i criteri di gruppo per il dominio gestito possono essere amministrati in remoto usando gli strumenti di amministrazione dei criteri di gruppo.

Gli amministratori nella directory di Azure AD non hanno i privilegi necessari per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. I membri del gruppo "AAD DC Administrators" possono amministrare da remoto i criteri di gruppo per i domini gestiti. Possono utilizzare gli strumenti dei criteri di gruppo su un computer Windows Server/client aggiunto al dominio gestito. La funzionalità Strumenti per criteri di gruppo può essere installata come parte della funzionalità facoltativa di gestione dei criteri di gruppo in Windows Server e nei computer client aggiunti al dominio gestito.

La prima attività consiste nel provisioning di una macchina virtuale Windows Server aggiunta al dominio gestito. Per istruzioni, vedere l'articolo [Aggiungere una macchina virtuale Windows Server a un dominio gestito](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Attività 2: Installare gli strumenti per i criteri di gruppo nella macchina virtuale
Eseguire questa procedura per installare gli strumenti di amministrazione dei criteri di gruppo nella macchina virtuale aggiunta a un dominio.

1. Passare al nodo **Macchine virtuali** nel portale di Azure classico. Selezionare la macchina virtuale creata nell'attività 1 e fare clic su **Connetti** sulla barra dei comandi nella parte inferiore della finestra.

    ![Connettersi alla macchina virtuale Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Il portale classico richiederà di aprire o salvare un file con estensione rdp, usato per connettersi alla macchina virtuale. Al termine del download fare clic sul file.
3. Al prompt di accesso usare le credenziali di un utente appartenente al gruppo "AAD DC Administrators". In questo caso, ad esempio, si usa 'bob@domainservicespreview.onmicrosoft.com'.
4. Dalla schermata Start aprire **Server Manager**. Fare clic su **Aggiungi ruoli e funzionalità** nel riquadro centrale della finestra di Server Manager.

    ![Avviare Server Manager nella macchina virtuale](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Nella pagina **Prima di iniziare** dell'**aggiunta guidata ruoli e funzionalità** fare clic su **Avanti**.

    ![Pagina Prima di iniziare](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Nella pagina **Tipo di installazione** lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e fare clic su **Avanti**.

    ![Pagina Tipo di installazione](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Nella pagina **Selezione server** selezionare la macchina virtuale corrente dal pool di server e fare clic su **Avanti**.

    ![Pagina Selezione dei server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Nella pagina **Ruoli del server** fare clic su **Avanti**. Questa pagina verrà ignorata perché non si stanno installando ruoli nel server.
9. Nella pagina **Funzionalità**, selezionare **Gestione Criteri di gruppo**.

    ![Pagina Funzionalità](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Nella pagina **Conferma** fare clic su **Installa** per installare la funzionalità Gestione Criteri di gruppo nella macchina virtuale. Dopo aver completato correttamente l'installazione della funzionalità, fare clic su **Chiudi** per uscire dall'**aggiunta guidata ruoli e funzionalità**.

    ![Pagina di conferma](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Attività 3: Avviare la console di gestione di Criteri di gruppo per amministrare Criteri di gruppo
È possibile utilizzare la console di gestione di Criteri di gruppo nella macchina virtuale aggiunta al dominio per amministrare Criteri di gruppo nel dominio gestito.

> [!NOTE]
> È necessario essere un membro del gruppo "AAD DC Administrators" per poter amministrare Criteri di gruppo nel dominio gestito.
>
>

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Verrà visualizzata la console **Gestione Criteri di gruppo** installata nella macchina virtuale.

    ![Avviare Gestione Criteri di gruppo](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Fare clic su **Gestione Criteri di gruppo** per avviare la relativa console.

    ![Console Criteri di gruppo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Attività 4: personalizzare gli Oggetti Criteri di gruppo predefiniti
Sono presenti due oggetti Criteri di gruppo predefiniti nel dominio gestito, uno per il contenitore "AADDC Computers" e uno per il contenitore "AADDC Users". È possibile personalizzare questi oggetti Criteri di gruppo per configurare i criteri di gruppo nel dominio gestito.

1. Nella console **Gestione Criteri di gruppo** fare clic per espandere i nodi **Foresta: contoso100.com** e **Domini** per visualizzare i criteri di gruppo del dominio gestito.

    ![Oggetti Criteri di gruppo predefiniti](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. È possibile personalizzare questi oggetti Criteri di gruppo predefiniti per configurare criteri di gruppo nel dominio gestito. Fare clic con il pulsante destro del mouse sull'oggetto Criteri di gruppo e quindi su **Modifica...** per personalizzare l'oggetto predefinito. L'Editor di configurazione degli oggetti Criteri di gruppo consente di personalizzare tali oggetti.

    ![Modificare gli oggetti Criteri di gruppo predefiniti](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. È ora possibile utilizzare la console dell'**Editor di Gestione Criteri di gruppo** per modificare gli oggetti Criteri di gruppo predefiniti. Ad esempio, nella schermata seguente viene illustrato come personalizzare l'oggetto Criteri di gruppo predefinito "AADDC Computers".

    ![Personalizzare un oggetto Criteri di gruppo predefinito](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Attività 5: creare un Oggetto Criteri di gruppo (GPO) personalizzato
È possibile creare o importare i propri oggetti criteri di gruppo personalizzati. È anche possibile collegare gli Oggetto Criteri di gruppo personalizzati a un'unità organizzativa creata nel dominio gestito. Per ulteriori informazioni sulla creazione di unità organizzative personalizzate, consultare l'articolo su come [creare un'Unità organizzativa in un dominio gestito](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> È necessario essere un membro del gruppo "AAD DC Administrators" per poter amministrare Criteri di gruppo nel dominio gestito.
>
>

1. Nella console **Gestione Criteri di gruppo** fare clic per selezionare l'unità organizzativa (OU) personalizzata. Fare clic con il tasto destro del mouse sull'unità organizzativa e quindi su **Crea un oggetto Criteri di gruppo in questo dominio e crea qui un collegamento...**.

    ![Creare un Oggetto Criteri di gruppo personalizzato](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Specificare il nome del nuovo Oggetto Criteri di gruppo e fare clic su **OK**.

    ![Specificare il nome dell'Oggetto Criteri di gruppo](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Viene creato un nuovo Oggetto Criteri di gruppo e collegato all'Unità organizzativa personalizzata. Fare clic con il tasto destro del mouse sull'Oggetto Criteri di gruppo e quindi su **Modifica...** nel menu.

    ![Oggetto Criteri di gruppo appena creato](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. È possibile personalizzare l'Oggetto Criteri di gruppo appena creato utilizzando l'**Editor Gestione Criteri di gruppo**.

    ![Personalizzare il nuovo Oggetto Criteri di gruppo](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Ulteriori informazioni sull'uso della [console di Gestione Criteri di gruppo](https://technet.microsoft.com/library/cc753298.aspx) sono disponibili su TechNet.

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Console Gestione Criteri di gruppo](https://technet.microsoft.com/library/cc753298.aspx)

