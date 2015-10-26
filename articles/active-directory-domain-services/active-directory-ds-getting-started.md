<properties
	pageTitle="Anteprima di Servizi di dominio Azure Active Directory: Introduzione | Microsoft Azure"
	description="Introduzione a Servizi di dominio Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Servizi di dominio Azure AD *(anteprima)* - Introduzione

In questo articolo vengono illustrati i passaggi di configurazione necessari per abilitare Servizi di dominio Azure AD per il tenant di Azure AD.

## Passaggio 1: Creare il gruppo 'AAD DC Administrators'
Il primo passaggio consiste nel creare un gruppo amministrativo nel tenant di Azure Active Directory. Questo gruppo amministrativo speciale è chiamato **AAD DC Administrators**. Ai membri di questo gruppo verranno concessi privilegi di amministratore per i computer aggiunti al dominio di Servizi di dominio Azure AD che verrà configurato. Dopo l'aggiunta al dominio, questo gruppo verrà aggiunto al gruppo 'Administrators' nei computer aggiunti al dominio. Inoltre, i membri di questo gruppo potranno usare Desktop remoto per connettersi ai computer del dominio da remoto.

> [AZURE.NOTE]Non sarà possibile esercitare i privilegi di amministratore di dominio o amministratore dell'organizzazione all'interno del dominio creato con Servizi di dominio Azure AD. Trattandosi di un dominio gestito, questi privilegi sono riservati dal servizio e non vengono resi disponibili agli utenti all'interno del tenant. Tuttavia, sarà possibile usare questo gruppo di amministratori speciale creato in questo passaggio di configurazione per eseguire alcune operazioni privilegiate, ad esempio aggiungere computer al dominio, gestire le appartenenze al gruppo Administrators nei computer del dominio, configurare Criteri di gruppo e così via.

In questo passaggio di configurazione verrà creato il gruppo e verranno aggiunti uno o più utenti nel tenant al gruppo. Eseguire i passaggi seguenti per creare il gruppo amministrativo per Servizi di dominio Azure AD:

1. Accedere al **portale di gestione di Azure** (ad esempio [https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selezionare il nodo **Active Directory** nel riquadro sinistro.
3. Selezionare il tenant di Azure AD (directory) per il quale si desidera abilitare Servizi di dominio Azure AD. Si noti che è possibile creare solo un dominio per ogni directory di Azure AD.

    ![Selezionare una directory di Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Fare clic sulla scheda **Gruppi**.
5. Fare clic su **Aggiungi gruppo** dal riquadro attività nella parte inferiore della pagina per aggiungere un gruppo alla directory.
6. Creare un gruppo denominato **AAD DC Administrators**.

    > [AZURE.WARNING]Per consentire l'accesso a Servizi di dominio Azure AD, è necessario creare un gruppo con questo nome esatto.

	![Creare un gruppo di amministratori](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. È possibile aggiungere una descrizione per il gruppo che consenta ad altri utenti all'interno del tenant di Azure AD di capire che questo gruppo verrà usato per concedere privilegi amministrativi all'interno di Servizi di dominio Azure AD.
8. Dopo aver creato il gruppo, fare clic sul nome del gruppo per visualizzarne le proprietà. Fare clic sul pulsante **Aggiungi membri** nel pannello inferiore, per aggiungere utenti come membri di questo gruppo.
9. Nella finestra di dialogo **Aggiungi membri** selezionare gli utenti da includere in questo gruppo e selezionare la casella di controllo al termine.

    ![Aggiungere utenti al gruppo di amministratori](./media/active-directory-domain-services-getting-started/add-group-members.png)

---
[**Passaggio successivo - Creare o selezionare una rete virtuale di Azure.**](active-directory-ds-getting-started-vnet.md)

<!---HONumber=Oct15_HO3-->