<properties
	pageTitle="Anteprima di Servizi di dominio Azure Active Directory: Guida all'amministrazione | Microsoft Azure"
	description="Creare un'unità organizzativa (OU) nei domini gestiti di Servizi di dominio Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Creare un'unità organizzativa (OU) in un dominio gestito di Servizi di dominio Azure AD
I domini gestiti di Servizi di dominio Azure AD includono due contenitori predefiniti denominati rispettivamente "AADDC Computers" e "AADDC Users". Il contenitore "AADDC Computers" include oggetti computer per tutti i computer aggiunti al dominio gestito. Il contenitore "AADDC Users" include utenti e gruppi nel tenant Azure AD. In alcuni casi, può essere necessario creare account del servizio nel dominio gestito per distribuire i carichi di lavoro. A questo scopo, è possibile creare un'unità organizzativa (OU) personalizzata nel dominio gestito e creare gli account del servizio nell'unità organizzativa. Questo articolo descrive come creare una OU nel dominio gestito.


## Installare gli strumenti di amministrazione di AD in una macchina virtuale aggiunta al dominio per l'amministrazione remota
I domini gestiti di Servizi di dominio Azure AD possono essere gestiti in remoto con i familiari strumenti di amministrazione di Active Directory, ad esempio il Centro di amministrazione di Active Directory o AD PowerShell. Gli amministratori tenant non hanno i privilegi necessari per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. Per amministrare il dominio gestito, installare la funzionalità Strumenti di amministrazione di AD in una macchina virtuale aggiunta al dominio gestito. Per istruzioni, vedere l'articolo [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md).

## Creare un'unità organizzativa nel dominio gestito
Dopo aver installato gli strumenti di amministrazione di AD nella macchina virtuale aggiunta al dominio, sarà possibile usarli per creare un'unità organizzativa nel dominio gestito. Eseguire i passaggi seguenti.

> [AZURE.NOTE] Solo i membri del gruppo "AAD DC Administrators" hanno i privilegi necessari per creare una nuova OU. Assicurarsi di eseguire la procedura seguente come utente appartenente a questo gruppo.

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Gli strumenti di amministrazione di AD risulteranno installati nella macchina virtuale.

	![Strumenti di amministrazione installati nel server](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Fare clic su **Centro di amministrazione di Active Directory**.

	![Centro di amministrazione di Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Fare clic sul nome di dominio nel riquadro a sinistra, ad esempio "contoso100.com", per visualizzare il dominio.

    ![Centro di amministrazione di Active Directory - Visualizza dominio](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. Nel riquadro **Attività** sul lato destro fare clic su **Nuovo** sotto il nodo del nome di dominio. In questo esempio fare clic su **Nuovo** sotto il nodo "contoso100(local)" nel riquadro**Attività** sul lato destro, come illustrato di seguito.

    ![Centro di amministrazione di Active Directory - Nuova OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Verrà visualizzata l'opzione per creare un'unità organizzativa. Fare clic su **Unità organizzativa** per visualizzare la finestra di dialogo **Crea unità organizzativa**.

6. Nella finestra di dialogo **Crea unità organizzativa** specificare un **Nome** per la nuova OU. Fornire una breve descrizione per la OU. È anche possibile impostare il campo **Gestita da** per la OU. Fare clic su **OK** per creare la nuova OU.

    ![Centro di amministrazione di Active Directory - finestra di dialogo Crea OU](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. La OU appena creato verrà visualizzata nel Centro di amministrazione di Active Directory.

    ![Centro di amministrazione di Active Directory - OU creata](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## Autorizzazioni/sicurezza per le OU appena create
Per impostazione predefinita, all'utente, membro del gruppo "AAD DC Administrators", che ha creato la nuova OU verranno concessi privilegi amministrativi con controllo completo per la OU. L'utente può quindi procedere alla concessione di autorizzazioni ad altri utenti o al gruppo "AAD DC Administrators" secondo le esigenze. Come illustrato nello screenshot seguente, all'utente "bob@domainservicespreview.onmicrosoft.com" che ha creato la nuova unità organizzativa "MyCustomOU" è concesso il controllo completo della OU.

 ![Centro di amministrazione di Active Directory - Sicurezza della nuova OU](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## Note sull'amministrazione delle OU personalizzate
Dopo avere creato una OU personalizzata, è possibile procedere alla creazione di utenti, gruppi, computer e account del servizio in questa OU. Non sarà possibile spostare utenti o gruppi dalla OU '"AADDC Users" dell'utente alle OU personalizzate.

> [AZURE.WARNING] Account utente, gruppi, account del servizio e oggetti computer creati in OU personalizzate non saranno disponibili nel tenant Azure AD. In altre parole, questi oggetti non saranno visualizzati con l'API Graph di Azure AD o nell'interfaccia utente di Azure AD. Questi oggetti saranno disponibili solo nel dominio gestito di Servizi di dominio Azure AD.


## Contenuti correlati

- [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Centro di amministrazione di Active Directory: Introduzione](https://technet.microsoft.com/library/dd560651.aspx)

- [Guida dettagliata agli account del servizio gestiti](https://technet.microsoft.com/library/dd548356.aspx)

<!---HONumber=AcomDC_0706_2016-->