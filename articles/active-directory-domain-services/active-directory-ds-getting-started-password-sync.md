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
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Passaggio successivo *(anteprima)*: Abilitare la sincronizzazione password in Servizi di dominio Azure AD.

## Attività 5: Abilitare la sincronizzazione password in Servizi di dominio Azure AD per una directory di Azure AD solo cloud
Dopo aver abilitato Servizi di dominio Azure AD per il tenant di Azure AD, l'attività successiva consiste nell'abilitare la sincronizzazione delle credenziali in Servizi di dominio Azure AD. Ciò consente agli utenti di accedere al dominio gestito con le credenziali aziendali.

I passaggi sono diversi a seconda che l'organizzazione abbia una directory di Azure AD solo cloud o sia impostata per la sincronizzazione con la directory locale tramite Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Directory di Azure AD solo cloud](active-directory-ds-getting-started-password-sync.md)
- [Directory di Azure AD sincronizzata](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Abilitare la generazione di hash di credenziali NTLM e Kerberos per una directory di Azure AD solo cloud
Se l'organizzazione ha una directory di Azure AD solo cloud, gli utenti che devono usare Servizi di dominio Azure AD dovranno modificare le proprie password. Questo processo di modifica delle password attiva la generazione in Azure AD degli hash delle credenziali richiesti da Servizi di dominio Azure AD per l'autenticazione Kerberos e NTLM. È possibile impostare come scadute le password per tutti gli utenti nel tenant che dovranno usare Servizi di dominio Azure AD oppure richiedere a tali utenti di cambiare le loro password.

Ecco le istruzioni che è necessario fornire agli utenti finali per modificare le password:

1. Passare alla pagina del pannello di accesso di Azure AD per l'organizzazione. In genere è disponibile all'indirizzo [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Selezionare la scheda **Profilo** in questa pagina.

3. Fare clic sul riquadro **Cambia password** in questa pagina per avviare una modifica della password.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Verrà visualizzata la pagina **Cambia password**. Gli utenti possono immettere la password esistente (vecchia) e continuare con la modifica della password.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Dopo la modifica, la nuova password sarà utilizzabile in Servizi di dominio Azure AD dopo breve tempo. Dopo alcuni minuti, gli utenti possono accedere ai computer aggiunti al dominio gestito mediante la propria password appena modificata.


<br>

## Contenuti correlati

- [Abilitare la sincronizzazione password in Servizi di dominio Azure AD per una directory di Azure AD sincronizzata](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Aggiungere una macchina virtuale Windows a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0427_2016-->