<properties
	pageTitle="Azure AD Connect: introduzione alle impostazioni rapide | Microsoft Azure"
	description="Informazioni su come scaricare, installare e configurare la procedura guidata per Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="billmath;andkjell"/>

# Introduzione alle impostazioni rapide per Azure AD Connect
Questo argomento fornisce informazioni per iniziare a usare Azure Active Directory Connect. Questa documentazione illustra l'uso dell'installazione rapida di Azure AD Connect. Le impostazioni rapide vengono usate in una topologia a foresta singola con sincronizzazione password. Questa opzione richiede solo pochi clic per estendere la directory locale nel cloud.

## Documentazione correlata
Se non è stata letta la documentazione in [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md), la tabella seguente fornisce collegamenti ad argomenti correlati. È necessario consultare i primi due argomenti in grassetto prima di iniziare l'installazione.

| Argomento | |
| --------- | --------- |
| **Scaricare Azure AD Connect** | [Scaricare Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware e prerequisiti** | [Azure AD Connect: Hardware e prerequisiti](active-directory-aadconnect-prerequisites.md) |
| Eseguire l'installazione con le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Aggiornamento da DirSync | [Aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Dopo l'installazione | [Verificare l'installazione e assegnare le licenze ](active-directory-aadconnect-whats-next.md) |
| Account usati per l'installazione | [Altre informazioni sugli account e le autorizzazioni di Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |


## Installazione rapida di Azure AD Connect
**Impostazioni rapide** è l'opzione predefinita e viene usata in uno degli scenari di distribuzione più comuni. Quando si usano le impostazioni rapide, Azure AD Connect distribuisce il servizio di sincronizzazione per una topologia di foresta singola. La [sincronizzazione password](active-directory-aadconnectsync-implement-password-synchronization.md) è abilitata e consente agli utenti di usare la propria password locale per accedere al cloud. L'[aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) è abilitato e semplificherà la manutenzione. Se si usa Impostazioni rapide, viene avviata automaticamente una sincronizzazione al termine dell'installazione, anche se è possibile scegliere di non eseguire questo passaggio.

### Per installare Azure AD Connect utilizzando le impostazioni di rapide

1. Accedere come amministratore locale al server in cui si vuole installare Azure AD Connect. Questa operazione deve essere eseguita sul server da impostare come server di sincronizzazione.
2. Individuare e fare doppio clic su **AzureADConnect.msi**.
3. Nella schermata iniziale selezionare la casella per accettare le condizioni di licenza e fare clic su **Continua**. ![Avvio di Azure AD Connect](./media/active-directory-aadconnect-get-started-express/welcome.png)
4. Nella schermata Impostazioni rapide fare clic su **Usa impostazioni rapide**. ![Avvio di Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. Nella schermata Connessione ad Azure AD immettere il nome utente e la password di un amministratore globale per Azure AD. Fare clic su **Avanti**. ![Connessione ad Azure AD](./media/active-directory-aadconnect-get-started-express/connectaad.png) Se viene visualizzato un errore e si hanno problemi di connettività, vedere [Risolvere i problemi di connettività con Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Nella schermata Connessione a Servizi di dominio Active Directory immettere il nome utente e la password di un account amministratore dell'organizzazione. È possibile immettere la parte relativa al dominio in formato NetBios o FQDN, ad esempio FABRIKAM\\administrator o fabrikam.com\\administrator. Fare clic su **Avanti**. ![Connessione ad AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. Nella schermata Pronto per la configurazione fare clic su **Installa**.
	- Nella pagina Pronto per la configurazione è possibile deselezionare la casella di controllo **Avviare il processo di sincronizzazione non appena viene completata la configurazione iniziale**. È necessario deselezionare questa casella di controllo per apportare una configurazione aggiuntiva, ad esempio il [filtro](active-directory-aadconnectsync-configure-filtering.md). Se si deseleziona questa opzione, la procedura guidata configura la sincronizzazione ma lascia disabilitata l'utilità di pianificazione, che non verrà eseguita finché non la si abilita manualmente eseguendo di nuovo l'installazione guidata.
	- È anche possibile scegliere di configurare i servizi di sincronizzazione per la **distribuzione ibrida di Exchange** selezionando la casella di controllo corrispondente. Abilitare questa opzione se si prevede di avere cassette postali di Exchange contemporaneamente nel cloud e in locale. ![Pronto per la configurazione di Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
8. Una volta completata l'installazione, fare clic su **Esci**.
9. Al termine dell'installazione, disconnettersi e accedere nuovamente prima di usare Synchronization Service Manager o Synchronization Rules Editor.

Per un video sull'uso dell'installazione rapida, vedere:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Passaggi successivi
Dopo aver installato Azure AD Connect è possibile [verificare l'installazione e assegnare le licenze](active-directory-aadconnect-whats-next.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0427_2016-->