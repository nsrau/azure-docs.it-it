<properties 
	pageTitle="Autenticazione LDAP e server Azure Multi-Factor Authentication" 
	description="Questa è la pagina di Azure Multi-Factor Authentication contenente le informazioni utili per distribuire l'autenticazione LDAP e il server Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Autenticazione LDAP e server Azure Multi-Factor Authentication 


Per impostazione predefinita, il server Azure Multi-Factor Authentication è configurato per importare o sincronizzare utenti da Active Directory, ma può essere configurato anche per il binding con altre directory LDAP, ad esempio con una directory ADAM o un controller di dominio di Active Directory specifico. Una volta configurato per la connessione a una directory tramite LDAP, il server Azure Multi-Factor Authentication può essere configurato come proxy LDAP per l'esecuzione di autenticazioni. Consente inoltre di usare il binding LDAP come una destinazione RADIUS per la preautenticazione degli utenti durante l'uso dell'autenticazione IIS o per l'autenticazione primaria nel portale utenti Azure Multi-Factor Authentication.

Quando Azure Multi-Factor Authentication viene usato come proxy LDAP, il server Azure Multi-Factor Authentication viene inserito tra il client LDAP (ad esempio, il dispositivo e l'applicazione VPN) e il server della directory LDAP per l'aggiunta di Multi-Factor Authentication. Per il funzionamento di Azure Multi-Factor Authentication, è necessario che il server Azure Multi-Factor Authentication sia configurato in modo da comunicare sia con i server client sia con la directory LDAP. In questo tipo di configurazione le richieste LDAP provenienti dalle applicazioni e dai server client vengono accettate dal server Azure Multi-Factor Authentication e inoltrate alla directory LDAP di destinazione per convalidare le credenziali primarie. Se nella risposta proveniente dalla directory LDAP è indicato che le credenziali primarie sono valide, Azure Multi-Factor Authentication esegue l'autenticazione basata sul secondo fattore e restituisce una risposta al client LDAP. L'intera autenticazione verrà completata solo se avranno esito positivo sia l'autenticazione al server LDAP sia quella a più fattori.





## Configurazione dell'autenticazione LDAP


Per configurare l'autenticazione LDAP, è necessario installare il server Azure Multi-Factor Authentication in un server Windows. Utilizzare la procedura seguente:

1. Sul server Azure Multi-Factor Authentication fare clic sull'icona Autenticazione LDAP nel menu a sinistra.
2. Selezionare la casella di controllo Abilita autenticazione LDAP.![Autenticazione LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png) 
3. Nella scheda Client modificare le porte TCP e SSL se il servizio LDAP di Azure Multi-Factor Authentication deve essere associato a porte non standard per ricevere richieste LDAP dai client che verranno configurati.
4. Se si prevede di usare LDAPS dal client al server Azure Multi-Factor Authentication, è necessario installare un certificato SSL nel server su cui è in esecuzione il server Windows. Fare clic su Sfoglia accanto alla casella Certificato SSL e selezionare il certificato installato che verrà usato per la connessione protetta. 
5. Fare clic sul pulsante Aggiungi.
6. Nella finestra di dialogo Aggiungi client LDAP immettere l'indirizzo IP del dispositivo, del server o dell'applicazione che eseguirà l'autenticazione al server e il nome di un'applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
7. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti all'autenticazione a più fattori, selezionare la casella di controllo Richiedi corrispondenza utente di Multi-Factor Authentication. Se invece rimangono ancora molti utenti da importare nel server e/o molti utenti esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità. 
8. È possibile ripetere i passaggi dal 5 al 7 per aggiungere altri client LDAP.
9. Quando Azure Multi-Factor Authentication è configurato per ricevere autenticazioni LDAP, deve usare un proxy per le autenticazioni alla directory LDAP. Di conseguenza, nella scheda Destinazione viene visualizzata un'unica opzione non selezionabile per l'uso di una destinazione LDAP. Per configurare la connessione della directory LDAP, fare clic sull'icona relativa all'integrazione della directory. 
10. Nella scheda Impostazioni selezionare il pulsante di opzione Usa specifico configurazione LDAP.
11. Fare clic su Edit.
12. Nella finestra di dialogo Modifica configurazione LDAP compilare i campi con le informazioni necessarie per connettersi alla directory LDAP. Nella tabella seguente sono incluse le descrizioni dei campi. Nota: queste informazioni sono incluse anche nel file della Guida del server Azure Multi-Factor Authentication.![Integrazione di directory](./media/multi-factor-authentication-get-started-server-ldap/ldap.png) 
13. Verificare la connessione LDAP facendo clic sul pulsante Test.
14. Se il test della connessione LDAP ha esito positivo, fare clic sul pulsante OK. 
15. Fare clic sulla scheda Filtri. Il server è preconfigurato per il caricamento di contenitori, gruppi di sicurezza e utenti da Active Directory. Se viene eseguito il binding a un'altra directory LDAP, è probabilmente necessario modificare i filtri visualizzati. Fare clic sul collegamento Guida per altre informazioni sui filtri.
16. Fare clic sulla scheda Attributi. Il server è preconfigurato per il mapping degli attributi da Active Directory.
17. Se viene eseguito il binding a un'altra directory LDAP o vengono modificati i mapping degli attributi preconfigurati, fare clic sul pulsante Modifica.
18. Nella finestra di dialogo Modifica attributi apportare modifiche ai mapping degli attributi LDAP per la directory. I nomi degli attributi possono essere digitati o selezionati facendo clic sul pulsante con i puntini di sospensione (...) accanto a ogni campo.
19. Fare clic sul collegamento Guida per altre informazioni sugli attributi.
20. Fare clic sul pulsante OK.
21. Fare clic sull'icona Impostazioni società e selezionare la scheda Risoluzione nome utente. 22. Se si esegue la connessione ad Active Directory da un server di dominio, dovrebbe essere possibile lasciare selezionato il pulsante di opzione Usa identificatori di sicurezza (SID) Windows per la corrispondenza dei nomi utente. In caso contrario, selezionare il pulsante di opzione Usa attributo dell'identificatore univoco LDAP per la corrispondenza dei nomi utente. Quando questa opzione è selezionata, il server Azure Multi-Factor Authentication proverà a risolvere ogni nome utente con un identificatore univoco della directory LDAP. Verrà eseguita una ricerca LDAP negli attributi dei nomi utente definiti nella scheda Integrazione directory -> Attributi. Quando un utente esegue l'autenticazione, il nome utente viene risolto nell'identificatore univoco nella directory LDAP, che viene usato per stabilire la corrispondenza dell'utente con il file di dati di Azure Multi-Factor Authentication. In questo modo è possibile eseguire confronti senza distinzione tra maiuscole e minuscole e usare formati lunghi e corti per i nomi utente. A questo punto la configurazione del server Azure Multi-Factor Authentication risulta completata. Il server è ora in ascolto sulle porte configurate per le richieste di accesso LDAP provenienti dai client configurati ed è impostato per l'uso di un proxy per le richieste di autenticazione alla directory LDAP.


## Configurazione del client LDAP

Per configurare il client LDAP, seguire queste linee guida:

- Configurare il dispositivo, il server o l'applicazione per l'autenticazione tramite LDAP al server Azure Multi-Factor Authentication come per la directory LDAP. Si consiglia di usare le stesse impostazioni normalmente definite per connettersi direttamente alla directory LDAP, fatta eccezione per il nome o l'indirizzo IP del server, che deve corrispondere a quello del server Azure Multi-Factor Authentication. 
- Impostare il timeout di LDAP su un valore compreso tra 30 e 60 secondi in modo da rendere disponibile una quantità di tempo sufficiente per convalidare le credenziali dell'utente con la directory LDAP, eseguire l'autenticazione basata su un secondo fattore, ricevere la risposta e quindi rispondere alla richiesta di accesso LDAP. 
- Se si usa LDAPS, il dispositivo o il server che esegue le query LDAP deve considerare attendibile il certificato SSL installato nel server Azure Multi-Factor Authentication.

<!---HONumber=AcomDC_0224_2016-->