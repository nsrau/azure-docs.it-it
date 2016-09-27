<properties 
	pageTitle="L'autenticazione di Windows e Azure il Server Multi-Factor Authentication"
	description="Questa è la pagina di autenticazione a più fattori di Azure che sarà utile per distribuire l'autenticazione di Windows e Server Azure Multi-Factor Authentication."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>  

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>  

# L'autenticazione di Windows e Azure il Server Multi-Factor Authentication

La sezione autenticazione di Windows consente all'amministratore di abilitare e configurare l'autenticazione di Windows per una o più applicazioni. Di seguito è riportato un elenco di aspetti da tenere presenti prima di configurare l'autenticazione di Windows.

-  prima che Azure Multi-Factor Authentication per i servizi Terminal venga attivata, è necessario riavviare il computer.
-  Se "Richiedere corrispondenza utente Azure Multi-Factor Authentication" è selezionata e non si è nell'elenco degli utenti, non sarà possibile accedere alla macchina dopo il riavvio.
-  La funzione IP attendibili dipende da se l'applicazione può fornire l'IP del client con l'autenticazione. Attualmente solo la funzione Servizi Terminal è supportata.







>[AZURE.NOTE]Questa funzionalità non è supportata per proteggere Servizi Terminal in Windows Server 2012 R2.




## Per proteggere un'applicazione con l'autenticazione di Windows, utilizzare la procedura seguente.

1. Nel Server Azure Multi-Factor Authentication fare clic sull'icona Autenticazione di Windows.![Autenticazione di Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Selezionare la casella di controllo abilita l’autenticazione di Windows. Per impostazione predefinita, questa casella è deselezionata.
3. La scheda applicazioni consente all'amministratore di configurare uno o più applicazioni per l'autenticazione di Windows.
4. Selezionare un server o un'applicazione, specificare se il server/applicazione è abilitato. Fare clic su OK.
5. Fare clic sul pulsante Aggiungi...
6. La scheda di ID attendibili consente di ignorare Azure Multi-Factor Authentication per le sessioni Windows provenienti da IP specifici. Ad esempio, se i dipendenti usano l'applicazione dall'ufficio e da casa, è possibile decidere di non volere che i loro telefoni squillino per Azure Multi-Factor Authentication in ufficio. A tale scopo, specificare la subnet dell'ufficio come voce di ID attendibili.
7. Fare clic sul pulsante Aggiungi...
8. Selezionare IP singolo Se si desidera ignorare un singolo indirizzo IP.
9. Selezionare intervallo IP se si desidera ignorare un intero intervallo IP. Esempio: 10.63.193.1-10.63.193.100.
10. Selezionare Subnet se si desidera specificare un intervallo di indirizzi IP utilizzando la notazione di subnet. Immettere l’IP iniziale della subnet e scegliere la mask appropriata dall'elenco a discesa.
11. Fare clic sul pulsante OK.

<!---HONumber=AcomDC_0921_2016-->