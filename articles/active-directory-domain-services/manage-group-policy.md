---
title: Creare e gestire criteri di gruppo in Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come modificare gli oggetti Criteri di gruppo predefiniti e creare criteri personalizzati in un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to edit the built-in group policy objects (GPOs) and create your own custom policies in an Azure Active Directory Domain Services managed domain.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 742d716ecdfff6ab67dedc281aa6134020f57add
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655045"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Amministrare Criteri di gruppo in un dominio gestito di Servizi di dominio Azure ADAdminister Group Policy in an Azure AD Domain Services managed domain

Le impostazioni per gli oggetti utente e computer in Servizi di dominio Azure Active Directory vengono spesso gestite tramite oggetti Criteri di gruppo. Servizi di dominio Active Directory di Azure include oggetti Criteri di gruppo incorporati per i contenitori *Utenti AADDC* e *Computer AADDC.* È possibile personalizzare questi oggetti Criteri di gruppo incorporati per configurare Criteri di gruppo in base alle esigenze dell'ambiente. I membri del gruppo *Amministratori controller di* dominio di Azure AD dispongono dei privilegi di amministrazione di Criteri di gruppo nel dominio di Azure AD DS e possono anche creare oggetti Criteri di gruppo personalizzati e unità organizzative. Ulteriori informazioni su cos'è Criteri di gruppo e come funziona, vedere [Panoramica di Criteri di gruppo][group-policy-overview].

In a hybrid environment, group policies configured in an on-premises AD DS environment aren't synchronized to Azure AD DS. Per definire le impostazioni di configurazione per utenti o computer in Servizi di dominio Active Directory di Azure, modificare uno degli oggetti Criteri di gruppo predefiniti o creare un oggetto Criteri di gruppo personalizzato.

In questo articolo viene illustrato come installare gli strumenti Gestione Criteri di gruppo, quindi modificare gli oggetti Criteri di gruppo incorporati e creare oggetti Criteri di gruppo personalizzati.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure.A Windows Server management VM that is joined to the Azure AD DS managed domain.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito.][create-join-windows-vm]
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

> [!NOTE]
> È possibile utilizzare Modelli amministrativi di Criteri di gruppo copiando i nuovi modelli nella workstation di gestione. Copiare i file `%SYSTEMROOT%\PolicyDefinitions` *ADMX* in e copiare i `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`file `Language-CountryRegion` *ADML* specifici delle impostazioni locali in , dove corrispondano alla lingua e all'area geografica dei file *ADML.*
>
> Ad esempio, copiare la versione inglese, negli Stati `\en-us` Uniti dei file *ADML* nella cartella.
>
> In alternativa, è possibile archiviare centralmente il modello amministrativo di Criteri di gruppo nei controller di dominio che fanno parte del dominio gestito di Servizi di dominio Active Directory di Azure.Alternatively, you can centrally store your Group Policy Administrative Template on the domain controllers that are part of the Azure AD DS managed domain. Per ulteriori informazioni, vedere [Come creare e gestire l'archivio centrale per](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)i modelli amministrativi di Criteri di gruppo in Windows .

## <a name="install-group-policy-management-tools"></a>Installare gli strumenti di Gestione Criteri di gruppo

Per creare e configurare oggetti Criteri di gruppo , è necessario installare gli strumenti di gestione di criteri di gruppo. Questi strumenti possono essere installati come funzionalità in Windows Server.These tools can be installed as a feature in Windows Server. Per ulteriori informazioni su come installare gli strumenti di amministrazione in un client Windows, vedere installare Strumenti di [amministrazione remota del server (RSAT)][install-rsat].

1. Accedere alla macchina virtuale di gestione. Per la procedura di connessione tramite il portale di Azure, vedere [Connettersi a una macchina virtuale][connect-windows-server-vm]di Windows Server.For steps on how to connect using the Azure portal, see Connect to a Windows Server VM .
1. Quando si accede alla macchina virtuale, per impostazione predefinita dovrebbe essere visualizzata la finestra di **Server Manager**. In caso contrario, scegliere **Server Manager** dal menu **Start**.
1. Fare clic su *Aggiungi ruoli e funzionalità* nel riquadro **Dashboard** della finestra **Server Manager**.
1. Nella pagina **Prima di iniziare** dell'*aggiunta guidata ruoli e funzionalità* selezionare **Avanti**.
1. Per *Tipo di installazione* lasciare selezionata l'opzione **Installazione basata su ruoli o basata su funzionalità** e selezionare **Avanti**.
1. Nella pagina **Selezione server** scegliere la macchina virtuale corrente dal pool di server, ad esempio *myvm.aaddscontoso.com*, quindi selezionare **Avanti**.
1. Nella pagina **Ruoli server** fare clic su **Avanti**.
1. Nella pagina **Funzionalità**, selezionare **Gestione Criteri di gruppo**.

    ![Installare Gestione Criteri di gruppo dalla pagina Funzionalità](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Nella pagina **Conferma** selezionare **Installa**. L'installazione degli strumenti Gestione Criteri di gruppo potrebbe richiedere uno o due minuti.
1. Dopo aver completato correttamente l'installazione della funzionalità, selezionare **Chiudi** per uscire dall'**Aggiunta guidata ruoli e funzionalità**.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Aprire la Console Gestione Criteri di gruppo e modificare un oggetto

Gli oggetti Criteri di gruppo predefiniti sono disponibili per utenti e computer in un dominio gestito di Servizi di dominio Active Directory di Azure.Default group policy objects (GPOs) exist for users and computers in an Azure AD DS managed domain. Con la funzionalità Gestione Criteri di gruppo installata nella sezione precedente, è possibile visualizzare e modificare un oggetto Criteri di gruppo esistente. Nella sezione successiva verrà creato un oggetto Criteri di gruppo personalizzato.

> [!NOTE]
> Per amministrare i criteri di gruppo in un dominio gestito di Servizi di dominio Active Directory di Azure, è necessario accedere a un account utente membro del gruppo *AAD DC Administrators.*

1. Nella schermata Start selezionare **Strumenti di amministrazione.** Viene visualizzato un elenco degli strumenti di gestione disponibili, incluso **Gestione Criteri di gruppo** installato nella sezione precedente.
1. Per aprire Console Gestione Criteri di gruppo, scegliere **Gestione Criteri di gruppo.**

    ![La Console Gestione Criteri di gruppo viene aperta per la modifica degli oggetti Criteri di gruppo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

In un dominio gestito di Servizi di dominio Active Directory di Azure sono disponibili due oggetti Criteri di gruppo predefiniti: uno per il contenitore *Computer AADDC* e uno per il contenitore *Utenti AADDC.* È possibile personalizzare questi oggetti Criteri di gruppo per configurare i criteri di gruppo in base alle esigenze all'interno del dominio gestito di Servizi di dominio Active Directory di Azure.You can customize these GPOs to configure group policy as needed within your Azure AD DS managed domain.

1. Nella console **Gestione Criteri di gruppo** espandere il nodo **Foresta: aaddscontoso.com.** Espandere quindi i nodi Domini.Next, expand the **Domains** nodes.

    Esistono due contenitori predefiniti per i *computer AADDC* e *gli utenti AADDC*. A ognuno di questi contenitori è applicato un oggetto Criteri di gruppo predefinito.

    ![Oggetti Criteri di gruppo incorporati applicati ai contenitori predefiniti 'Computer AADDC' e 'Utenti AADDC'](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Questi oggetti Criteri di gruppo predefiniti possono essere personalizzati per configurare criteri di gruppo specifici nel dominio gestito di Servizi di dominio Active Directory di Azure.These built-in GPOs can be customized to configure specific group policies on your Azure AD DS managed domain. Fare clic con il pulsante destro del mouse su uno degli oggetti Criteri di gruppo, ad esempio *L'oggetto Criteri*di gruppo Computer AADDC , quindi scegliere **Modifica...**.

    ![Scegliere l'opzione 'Modifica' uno degli oggetti Criteri di gruppo incorporati](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Viene aperto lo strumento Editor Gestione Criteri di gruppo che consente di personalizzare l'oggetto Criteri di gruppo, ad esempio *Criteri account:*

    ![Personalizzare l'oggetto Criteri di gruppo per configurare le impostazioni in base alle esigenze](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Al termine, scegliere **File > Salva** per salvare il criterio. Computer aggiornano Criteri di gruppo per impostazione predefinita ogni 90 minuti e applicano le modifiche apportate.

## <a name="create-a-custom-group-policy-object"></a>Creare un oggetto Criteri di gruppo personalizzatoCreate a custom Group Policy Object

Per raggruppare impostazioni di criteri simili, spesso si creano oggetti Criteri di gruppo aggiuntivi anziché applicare tutte le impostazioni necessarie nel singolo oggetto Criteri di gruppo predefinito. Con Servizi di dominio Active Directory è possibile creare o importare oggetti Criteri di gruppo personalizzati e collegarli a un'unità organizzativa personalizzata. Se è necessario creare prima un'unità organizzativa personalizzata, vedere [Creare un'unità organizzativa personalizzata in un dominio gestito di Azure AD DS.](create-ou.md)

1. Nella console **Gestione Criteri di gruppo** selezionare l'unità organizzativa personalizzata, ad esempio *MyCustomOU*. Fare clic con il pulsante destro del mouse sull'unità organizzativa e **scegliere Crea un oggetto Criteri di gruppo in questo dominio e Collega qui...**:

    ![Creare un oggetto Criteri di gruppo personalizzato nella console Gestione Criteri di gruppo](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Specificare un nome per il nuovo oggetto Criteri di gruppo, ad esempio Oggetto Criteri di *gruppo personalizzato*, quindi scegliere **OK**. Facoltativamente, è possibile basare questo oggetto Criteri di gruppo personalizzato su un oggetto Criteri di gruppo esistente e su un set di opzioni dei criteri.

    ![Specificare un nome per il nuovo oggetto Criteri di gruppo personalizzato](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. L'oggetto Criteri di gruppo personalizzato viene creato e collegato all'unità organizzativa personalizzata. Per configurare le impostazioni dei criteri, selezionare con il pulsante destro del mouse l'oggetto Criteri di gruppo personalizzato e scegliere **Modifica...**:

    ![Scegliere l'opzione "Modifica" l'oggetto Criteri di gruppo personalizzato](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Verrà aperto **l'Editor Gestione Criteri di gruppo** che consente di personalizzare l'oggetto Criteri di gruppo:

    ![Personalizzare l'oggetto Criteri di gruppo per configurare le impostazioni in base alle esigenze](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Al termine, scegliere **File > Salva** per salvare il criterio. Computer aggiornano Criteri di gruppo per impostazione predefinita ogni 90 minuti e applicano le modifiche apportate.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle impostazioni di Criteri di gruppo disponibili che è possibile configurare utilizzando la Console Gestione Criteri di gruppo, vedere [Utilizzare gli elementi preferenza Criteri di gruppo][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
