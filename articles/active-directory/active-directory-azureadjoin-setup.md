<properties 
	pageTitle="Configurazione di Aggiunta ad Azure AD per gli utenti | Microsoft Azure" 
	description="Viene illustrato come gli amministratori possono configurare Azure AD Join per la directory locale e la registrazione del dispositivo."
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Configurazione di Aggiunta ad Azure AD nell'organizzazione

Prima di procedere alla configurazione di Aggiunta ad Azure AD, è necessario sincronizzare la directory locale degli utenti nel cloud oppure creare manualmente account gestiti in Azure AD.

Le istruzioni dettagliate per sincronizzare gli utenti locali con Azure AD sono disponibili in [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).


Per creare e gestire manualmente gli utenti in Azure AD, vedere [Gestire gli utenti in Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Configurare la registrazione dei dispositivi 
1. Accedere al portale di Azure come Amministratore.
2. Nel riquadro sinistro selezionare Active Directory.
3. Selezionare la propria directory nella scheda **Directory**.
4. Selezionare la scheda **Configura**.
5. Scorrere fino alla sezione **Dispositivi**.
6. Nella scheda **Dispositivi** impostare le opzioni seguenti:  
   * **Numero massimo di dispositivi aggiunti per utente**: selezionare il numero massimo di dispositivi che un utente può aggiungere ad Azure AD. Se un utente raggiunge la quota specificata, non potrà aggiungere altri dispositivi fino a quando non verranno rimossi uno o più dispositivi esistenti.
   * **Richiedi autenticazione a più fattori per aggiungere i dispositivi**: abilitare questa opzione per richiedere agli utenti un secondo fattore di autenticazione per aggiungere i dispositivi ad Azure AD. Per altre informazioni sull'autenticazione a più fattori, vedere [Introduzione ad Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-get-started-cloud/).
   * **Gli utenti possono aggiungere dispositivi ad Azure AD**: selezionare gli utenti e i gruppi autorizzati ad aggiungere dispositivi ad Azure AD.
   * **Amministratori aggiuntivi su dispositivi aggiunti ad Azure AD**: con Azure AD Premium o Enterprise Mobility Suite (EMS), è possibile scegliere gli utenti a cui concedere diritti di amministratore locale per il dispositivo. Per impostazione predefinita, agli amministratori globali e ai proprietari dei dispositivi vengono sempre concessi i diritti di amministratore locale.

<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
Dopo aver configurato Aggiunta di Azure AD, gli utenti potranno connettersi ad Azure AD tramite i propri dispositivi aziendali o personali.

Di seguito sono descritti i tre scenari per abilitare gli utenti a configurare Aggiunta di Azure AD:

- Gli utenti aggiungono un dispositivo di proprietà della società direttamente ad Azure AD
- Gli utenti eseguono l'aggiunta a un dominio per un dispositivo di proprietà della società in Active Directory locale e quindi l'estensione ad Azure AD
- Gli utenti aggiungono gli account aziendali a Windows in un dispositivo personale 

## Informazioni aggiuntive

* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->