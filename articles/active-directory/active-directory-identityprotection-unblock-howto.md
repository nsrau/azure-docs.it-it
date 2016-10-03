<properties
	pageTitle="Azure Active Directory Identity Protection: come sbloccare gli utenti | Microsoft Azure"
	description="Informazioni su come sbloccare gli utenti bloccati da un criterio di Azure Active Directory Identity Protection."
	services="active-directory"
	keywords="Azure Active Directory Identity Protection, sbloccare gli utenti"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="markvi"/>  

#Azure Active Directory Identity Protection: come sbloccare gli utenti

Azure Active Directory Identity Protection consente di configurare criteri per bloccare gli utenti se vengono rispettate le condizioni configurate. Un utente bloccato contatta in genere l'help desk per essere sbloccato. Questo argomento illustra la procedura da eseguire per sbloccare un utente bloccato.


## Determinare il motivo del blocco

Il primo passaggio per sbloccare un utente consiste nel determinare il tipo di criterio che ha bloccato l'utente, perché i passaggi successivi dipendono da questa informazione. Azure Active Directory Identity Protection consente di bloccare un utente mediante un criterio di rischio di accesso o un criterio di rischio.

È possibile ottenere il tipo di criterio che ha bloccato un utente basandosi sull'intestazione della finestra di dialogo visualizzata all'utente durante il tentativo di accesso:

|Criterio | Finestra di dialogo utente|
|--- | --- |
|Rischio di accesso | ![Accesso bloccato](./media/active-directory-identityprotection-unblock-howto/02.png)   |
|Rischio utente | ![Account bloccato](./media/active-directory-identityprotection-unblock-howto/104.png)   |


Un utente bloccato da:

- Un criterio di rischio di accesso è noto anche come accesso sospetto
- Un criterio di rischio utente è noto anche come account a rischio

 
## Sblocco di accessi sospetti

Per sbloccare un accesso sospetto, sono disponibili le opzioni seguenti:

1. **Accesso da una località o un dispositivo familiare**: un motivo comune per gli accessi sospetti bloccati è costituito da tentativi di accesso da località o dispositivi non familiari. Gli utenti possono determinare rapidamente se questo è il motivo del blocco provando ad accedere da una località o un dispositivo familiare.


3. **Esclusione dal criterio**: se si ritiene che la configurazione corrente del criterio di accesso provochi problemi agli utenti, è possibile escludere gli utenti dal criterio. Per informazioni dettagliate, vedere [Criteri di sicurezza per il rischio di accesso](active-directory-identityprotection.md#sign-in-risk-policy).
 
4. **Disabilitazione del criterio**: se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per informazioni dettagliate, vedere [Criteri di sicurezza per il rischio di accesso](active-directory-identityprotection.md#sign-in-risk-policy).


## Sblocco di account a rischio

Per sbloccare un account a rischio, sono disponibili le opzioni seguenti:

1. **Reimpostazione della password**: è possibile reimpostare la password dell'utente. Per informazioni dettagliate, vedere [Reimpostazione manuale della password di protezione](active-directory-identityprotection.md#manual-secure-password-reset).

2. **Eliminazione di tutti gli eventi di rischio**: il criterio di rischio utente blocca un utente se è stato raggiunto il livello di rischio utente configurato per il blocco dell'accesso. È possibile ridurre il livello di rischio di un utente chiudendo manualmente gli eventi di rischio segnalati. Per informazioni dettagliate, vedere [Chiusura manuale degli eventi di rischio](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Esclusione dal criterio**: se si ritiene che la configurazione corrente del criterio di accesso provochi problemi agli utenti, è possibile escludere gli utenti dal criterio. Per informazioni dettagliate, vedere [Criteri di sicurezza per il rischio utente](active-directory-identityprotection.md#user-risk-policy).
 
4. **Disabilitazione del criterio**: se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per informazioni dettagliate, vedere [Criteri di sicurezza per il rischio utente](active-directory-identityprotection.md#user-risk-policy).




## Passaggi successivi

 Per altre informazioni su Azure AD Identity Protection, vedere [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 

<!---HONumber=AcomDC_0921_2016-->