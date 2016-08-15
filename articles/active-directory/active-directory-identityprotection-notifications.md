<properties
	pageTitle="Notifiche di Azure Active Directory Identity Protection| Microsoft Azure"
	description="Informazioni su come le notifiche agevolano le attività di analisi."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
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
	ms.date="08/02/2016"
	ms.author="markvi"/>

#Notifiche di Azure Active Directory Identity Protection 


Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatica per la gestione del rischio utente e degli eventi di rischio:

- Messaggio di posta elettronica di avviso utente compromesso

- Messaggio di posta elettronica di riepilogo settimanale

## Messaggio di posta elettronica di avviso utente compromesso

Quando Azure AD Identity Protection identifica un account come compromesso, viene generato un avviso di posta elettronica utente compromesso. Il messaggio include un collegamento al report relativo agli utenti contrassegnati per il rischio nel dashboard di Identity Protection. È consigliabile analizzare immediatamente le notifiche relative alle compromissioni.


## Messaggio di posta elettronica di riepilogo settimanale

Il messaggio di riepilogo settimanale contiene un riepilogo dei nuovi eventi di rischio.<br> Sono inclusi:

- Utenti a rischio.
- Attività sospette
- Vulnerabilità rilevate.
- Collegamenti ai report correlati in Identity Protection.


<br> ![Correzione](./media/active-directory-identityprotection-notifications/400.png "Correzione") <br>

È possibile disattivare l’invio del messaggio di posta elettronica settimanale contenente il riepilogo. <br><br> ![Rischi utente](./media/active-directory-identityprotection-notifications/62.png "Rischi utente") <br>
 

**Per aprire la relativa finestra di dialogo di configurazione**:

1. Nel pannello **Azure AD Identity Protection** fare clic su **Impostazioni**. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection-notifications/401.png "Criteri di rischio utente") <br>

2. Nella sezione **Generale** fare clic su **Notifiche**. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection-notifications/405.png "Criteri di rischio utente") <br>




## Vedere anche

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0803_2016-->