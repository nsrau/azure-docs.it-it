<properties 
	pageTitle="Autenticazione RADIUS e Azure Server Multi-Factor Authentication"
	description="Questa è la pagina di Azure Multi-Factor Authentication contenente le informazioni utili per distribuire l'autenticazione RADIUS e il server Azure Multi-Factor Authentication."
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
	ms.date="08/15/2016"
	ms.author="kgremban"/> 



# Autenticazione RADIUS e Azure Server Multi-Factor Authentication

La sezione autenticazione RADIUS consente di abilitare e configurare l'autenticazione RADIUS per il Server Azure Multi-Factor Authentication. RADIUS è un protocollo standard per accettare le richieste di autenticazione e per elaborare le richieste. Il Server Azure Multi-Factor Authentication funge da server RADIUS e viene inserito tra il client RADIUS (ad esempio un accessorio VPN) e la destinazione di autenticazione, che potrebbe essere Active Directory (AD), una directory LDAP o un altro server RADIUS, allo scopo di aggiungere Azure Multi-Factor Authentication. Per far funzionare Azure Multi-Factor Authentication, è necessario configurare il Server Azure Multi-Factor Authentication in modo che possa comunicare con i server client e la destinazione di autenticazione. Il Server Azure Multi-Factor Authentication accetta richieste da un client RADIUS, convalida le credenziali rispetto alla destinazione di autenticazione, aggiunge Azure Multi-Factor Authentication e invia una risposta al client RADIUS. L'intera autenticazione avrà esito positivo solo se l'autenticazione principale e Azure Multi-Factor Authentication hanno esito positivo.

>[AZURE.NOTE]
Il server MFA supporta solo i protocolli RADIUS PAP (Password Authentication Protocol) e MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol) quando agisce da server RADIUS. Quando il server MFA agisce come proxy RADIUS per un altro server RADIUS che supporta tale protocollo, ad esempio Microsoft NPS, è possibile usare altri protocolli, ad esempio EAP (Extensible Authentication Protocol). </br> Quando si usano altri protocolli in questa configurazione, i token OATH e SMS unidirezionali non funzioneranno perché il server MFA non è in grado di avviare una risposta RADIUS Challenge corretta tramite tale protocollo.


![Autenticazione RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png) 

## Configurazione dell'autenticazione RADIUS

Per configurare l'autenticazione RADIUS, installare il Server Azure Multi-Factor Authentication su un server Windows. Se si dispone di un ambiente Active Directory, il server deve appartenere al dominio all'interno della rete. Utilizzare la procedura seguente per configurare il Server Azure Multi-Factor Authentication:

1. Nel Server Azure Multi-Factor Authentication fare clic sull'icona autenticazione IIS nel menu a sinistra.
2. Selezionare la casella di controllo di autenticazione Abilitare RADIUS.
3. Nella scheda Client modificare le porte di autenticazione e di account se il servizio RADIUS di Azure Multi-Factor Authentication deve essere associato a porte non standard per ricevere richieste RADIUS dai client che verranno configurati.
4. Fare clic sul pulsante Aggiungi...
5. Nella finestra di dialogo Aggiungi client RADIUS immettere l'indirizzo IP del dispositivo/server che eseguirà l'autenticazione al server Azure Multi-Factor Authentication, il nome di un'applicazione (facoltativo), e un segreto condiviso. Il segreto condiviso dovrà essere lo stesso per il Server Azure multi-Factor Authentication e il dispositivo/server. Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
6. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti all'autenticazione a più fattori, selezionare la casella di controllo Richiedi Azure Multi-Factor Authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità.
7. Controllare la casella Attiva fallback OATH token se gli utenti utilizzeranno l'autenticazione dell'app mobile Azure Multi-Factor Authentication e si desidera utilizzare i passcode OATH come autenticazione di fallback per le chiamate fuori banda, gli SMS o le notifiche push.
8. Fare clic sul pulsante OK.
9. È possibile ripetere i passaggi da 4 a 8 per aggiungere altri client RADIUS.
10. Fare clic sulla scheda destinazione.
11. Se il Server Azure Multi-Factor Authentication è installato in un server di dominio in un ambiente Active Directory, selezionare il dominio di Windows.
12. Se gli utenti devono essere autenticati in una directory LDAP, selezionare binding LDAP. Quando si utilizza il binding LDAP, è necessario fare clic sull'icona integrazione Directory e modificare la configurazione LDAP nella scheda impostazioni in modo da associare il Server alla directory. Le istruzioni per la configurazione LDAP sono disponibili nella Guida alla configurazione del Proxy LDAP.
13. Se gli utenti devono essere autenticati in un altro server RADIUS, selezionare il/i server RADIUS.
14. Configurare il server a cui verranno indirizzate le richieste RADIUS facendo clic sul pulsante Aggiungi.
15. Nella finestra di dialogo Aggiungi Server RADIUS immettere l'indirizzo IP del server RADIUS e un segreto condiviso. Il segreto condiviso dovrà essere lo stesso per il server RADIUS e il Server Azure Multi-Factor Authentication. Se il server RADIUS utilizza porte diverse, modificare le porte di autenticazione e Accounting.
16. Fare clic sul pulsante OK.
17. È necessario aggiungere il Server Azure Multi-Factor Authentication come client RADIUS in altri server RADIUS in modo che questo elabori le richieste di accesso ricevute dal Server Azure Multi-Factor Authentication. È necessario utilizzare lo stesso segreto condiviso configurato nel Server Azure Multi-Factor Authentication.
18. È possibile ripetere questo passaggio per aggiungere altri server RADIUS e configurare l'ordine in cui il Server deve chiamarli con i pulsanti Sposta su e Sposta giù. In questo modo è stata completata la configurazione del Server Azure Multi-Factor Authentication. Il Server è in attesa sulle porte configurate per le richieste di accesso RADIUS dai client configurati.


## Configurazione dei Client RADIUS

Per configurare il client RADIUS, utilizzare le linee guida:

- Configurare il dispositivo/server per l'autenticazione RADIUS per l'indirizzo IP del Server di Azure Multi-Factor Authentication, che fungerà da server RADIUS.
- Utilizzare lo stesso segreto condiviso configurato in precedenza.
- Impostare il timeout di RADIUS su un valore compreso tra 30 e 60 secondi in modo da rendere disponibile una quantità di tempo sufficiente per convalidare le credenziali dell'utente, eseguire l'autenticazione a più fattori, ricevere la risposta e quindi rispondere alla richiesta di accesso RADIUS.

<!---HONumber=AcomDC_0921_2016-->