<properties 
	pageTitle="Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS" 
	description="Questa è la pagina su Multi-Factor Authentication di Azure in cui viene descritto come iniziare a utilizzare questa tipologia di autenticazione di Azure nel cloud." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>

# Protezione delle risorse cloud con Azure Multi-Factor Authentication e AD FS

Se l'organizzazione è federata con Azure Active Directory e si dispone di risorse che sono accessibili da Azure AD, è possibile utilizzare Azure Multi-Factor Authentication o Active Directory Federation Services per proteggere tali risorse. Utilizzare le procedure seguenti per proteggere le risorse di Azure Active Directory con Azure Multi-Factor Authentication o Active Directory Federation Services.

## Per la protezione delle risorse Azure AD con ADFS procedere come segue: 



1. Usare la procedura descritta in [Attivare Multi-Factor Authentication](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) per consentire agli utenti di abilitare un account.
2. Utilizzare la procedura seguente per impostare una regola attestazioni:

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

- 	Avviare la console di gestione di ADFS.
- 	Passare alla Relying Party Trusts e fare clic destro sul Trust della Relying Party. Selezionare Modifica regole attestazione...
- 	Fare nuovamente clic su Add Rule.
- 	Selezionare Send Claims Using a Custom Rule e fare clic su Next.
- 	Immettere un nome per la regola attestazione.
- 	In Regola personalizzata: aggiungere quanto segue:


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Attestazione corrispondente:

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Fare clic su OK. Fare clic su Finish. Chiudere la console di gestione di ADFS.

Gli utenti possono quindi completare l'accesso utilizzando il metodo locale (ad esempio smart card).

## Indirizzi IP attendibili per utenti federati
Gli indirizzi IP attendibili consentono agli amministratori di ignorare Multi-Factor Authentication per specifici indirizzi IP o utenti federati con richieste provenienti dalla propria rete Intranet. Le sezioni seguenti descrivono come configurare gli indirizzi IP attendibili di Azure Multi-Factor Authentication con utenti federati e ignorare Multi-Factor Authentication, quando una richiesta proviene da una Intranet di utenti federati. Questo avviene tramite la configurazione di ADFS in modo da applicare la funzione di pass-through o di filtro a un modello di attestazione in ingresso con il tipo di attestazione All'interno della rete aziendale. Questo esempio usa Office 365 per l'attendibilità del componente.

### Configurare le regole attestazioni di ADFS

Per prima cosa è necessario configurare le attestazioni ADFS. Si creeranno due regole attestazioni, una per il tipo di attestazione All'interno della rete aziendale e un'altra per mantenere gli utenti connessi.

1. Aprire il componente di gestione di ADFS.
2. A sinistra selezionare Attendibilità componente.
3. Al centro fare clic con il pulsante destro del mouse sulla piattaforma delle identità di Microsoft Office 365 e scegliere **Modifica regole attestazione**![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. In Regole di trasformazione rilascio fare clic su **Aggiungi regola.** ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare Applicare la funzione di pass-through o di filtro a un'attestazione in ingresso dall'elenco a discesa e fare clic su Avanti. ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Nella casella Nome regola attestazione assegnare un nome alla regola. Ad esempio: InternoReteAziend.
7. Nell'elenco a discesa accanto a Tipo di attestazione in ingresso selezionare All'interno della rete aziendale. ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Fare clic su Finish.
9. In Regole di trasformazione rilascio fare clic su **Aggiungi regola**.
10. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare Inviare attestazioni mediante una regola personalizzata dall'elenco a discesa e fare clic su Avanti.
11. Nella casella Nome regola attestazione specificare di mantenere gli utenti gli utenti connessi.
12. Nella casella Regola personalizzata immettere:
	    
		c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
			=> issue(claim = c);
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Fare clic su **Finish**.
14. Fare clic su **Apply**.
15. Fare clic su **OK**.
16. Chiudere Gestione ADFS.



### Configurare gli indirizzi IP attendibili di Azure Multi-Factor Authentication con utenti federati
Ora che le attestazioni sono configurate, è possibile procedere alla configurazione degli indirizzi IP attendibili.

1. Accedere al portale di gestione di Azure.
2. A sinistra fare clic su Active Directory.
3. In Directory fare clic sulla directory per cui impostare gli indirizzi IP attendibili.
4. Per la directory selezionata fare clic su Configura.
5. Nella sezione Multi-Factor Authentication fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni servizio selezionare **Per le richieste degli utenti federati originate dalla Intranet dell'utente** in Indirizzi IP attendibili. ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Fare clic su Salva.
8. Una volta che gli aggiornamenti sono stati applicati, fare clic su Chiudi.


La procedura è terminata. A questo punto, gli utenti federati di Office 365 dovrebbero usare solo MFA quando una richiesta proviene dall'esterno della Intranet aziendale.

<!---HONumber=AcomDC_1125_2015-->