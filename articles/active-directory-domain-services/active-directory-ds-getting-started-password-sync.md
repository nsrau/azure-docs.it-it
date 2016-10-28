<properties
	pageTitle="Servizi di dominio Azure AD: Abilitare la sincronizzazione password | Microsoft Azure"
	description="Introduzione a Servizi di dominio Azure Active Directory"
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
	ms.topic="get-started-article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Abilitare la sincronizzazione password in Servizi di dominio Azure AD
Nelle attività precedenti si è abilitato Servizi di dominio Azure AD per il tenant di Azure AD. L'attività successiva prevede l'abilitazione degli hash delle credenziali necessari per l'autenticazione NTLM e Kerberos per la sincronizzazione con Servizi di dominio Azure AD. Dopo avere configurato la sincronizzazione delle credenziali, gli utenti possono accedere al dominio gestito usando le credenziali aziendali.

I passaggi sono diversi a seconda che l'organizzazione abbia un tenant di Azure AD basato solo sul cloud o sia impostata per la sincronizzazione con la directory locale tramite Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Tenant di Azure AD solo cloud](active-directory-ds-getting-started-password-sync.md)
- [Tenant di Azure AD sincronizzato](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## Attività 5: Abilitare la sincronizzazione password in Servizi di dominio Azure AD per un tenant di Azure AD solo cloud
Servizi di dominio Azure AD richiede che gli hash delle credenziali siano in un formato adatto all'autenticazione NTLM e Kerberos, per autenticare gli utenti nel dominio gestito. A meno che non si abiliti Servizi di dominio AAD per il tenant, Azure AD non genera né archivia gli hash delle credenziali nel formato necessario per l'autenticazione NTLM o Kerberos. Per ovvi motivi di sicurezza, Azure AD non archivia nemmeno le credenziali in un formato non crittografato. Azure AD quindi non può generare questi hash delle credenziali NTLM o Kerberos in base alle credenziali esistenti degli utenti.

> [AZURE.NOTE] Se l'organizzazione ha un tenant di Azure AD solo cloud, gli utenti che devono usare Servizi di dominio Azure AD devono modificare le proprie password.

Questo processo di modifica delle password attiva la generazione in Azure AD degli hash delle credenziali richiesti da Servizi di dominio Azure AD per l'autenticazione Kerberos e NTLM. È possibile impostare come scadute le password per tutti gli utenti nel tenant che dovranno usare Servizi di dominio Azure AD oppure richiedere a tali utenti di cambiare le loro password.


### Abilitare la generazione di hash di credenziali NTLM e Kerberos per un tenant di Azure AD solo cloud
Ecco le istruzioni che è necessario fornire agli utenti finali in modo che possano modificare le password:

1. Passare alla pagina del pannello di accesso di Azure AD per l'organizzazione all'indirizzo [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Selezionare la scheda **Profilo** in questa pagina.

3. Fare clic sul riquadro **Cambia password** in questa pagina.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Se l'opzione **Cambia password** non è visualizzata nella pagina del pannello di accesso, assicurarsi che l'organizzazione abbia configurato la [gestione delle password in Azure AD](../active-directory/active-directory-passwords-getting-started.md).

4. Nella pagina **cambia password** digitare la password esistente (precedente), quindi digitare una nuova password e confermarla. Fare clic su **invia**.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Dopo la modifica, la nuova password sarà utilizzabile in Servizi di dominio Azure AD dopo breve tempo. Dopo alcuni minuti (in genere circa 20 minuti), è possibile accedere ai computer aggiunti al dominio gestito usando la password appena modificata.

<br>

## Contenuti correlati

- [Come aggiornare la password](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Introduzione alla gestione delle password](../active-directory/active-directory-passwords-getting-started.md)

- [Abilitare la sincronizzazione password in Servizi di dominio AAD per un tenant di Azure AD sincronizzato](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Aggiungere una macchina virtuale Windows a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!----HONumber=AcomDC_0921_2016-->