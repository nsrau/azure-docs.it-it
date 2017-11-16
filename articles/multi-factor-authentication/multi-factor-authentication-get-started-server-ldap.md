---
title: Autenticazione LDAP e Azure MFA Server | Documentazione Microsoft
description: "Questa è la pagina di Azure Multi-Factor Authentication contenente le informazioni utili per distribuire l'autenticazione LDAP e il server Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.openlocfilehash: 60b96fcfcbbc33a101d06fc59425a4ea8907bce0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Autenticazione LDAP e server Azure Multi-Factor Authentication
Per impostazione predefinita, il server Azure Multi-Factor Authentication è configurato per importare o sincronizzare utenti da Active Directory, ma può essere configurato anche per il binding con altre directory LDAP, ad esempio con una directory ADAM o un controller di dominio di Active Directory specifico. Quando è connesso a una directory tramite LDAP, il server Azure Multi-Factor Authentication può agire come proxy LDAP per l'esecuzione di autenticazioni. Permette anche di usare l'associazione LDAP come destinazione RADIUS, la preautenticazione degli utenti con l'autenticazione IIS oppure l'autenticazione primaria nel portale utenti di Azure MFA.

Per usare Azure Multi-Factor Authentication come proxy LDAP, inserire il server Azure Multi-Factor Authentication tra il client LDAP (ad esempio, l'applicazione o il dispositivo VPN) e il server della directory LDAP. Il server Azure Multi-Factor Authentication deve essere configurato in modo da comunicare sia con i server client che con la directory LDAP. In questo tipo di configurazione le richieste LDAP provenienti dalle applicazioni e dai server client vengono accettate dal server Azure Multi-Factor Authentication e inoltrate alla directory LDAP di destinazione per convalidare le credenziali primarie. Se la directory LDAP indica che le credenziali primarie sono valide, Azure Multi-Factor Authentication esegue una seconda verifica dell'identità e invia una risposta al client LDAP. L'intero processo di autenticazione riesce solo se sia l'autenticazione nel server LDAP che il secondo passaggio di verifica hanno esito positivo.

## <a name="configure-ldap-authentication"></a>Configurare l'autenticazione LDAP
Per configurare l'autenticazione LDAP, è necessario installare il server Azure Multi-Factor Authentication in un server Windows. Usare la procedura seguente:

### <a name="add-an-ldap-client"></a>Aggiungere un client LDAP

1. Nel server Azure Multi-Factor Authentication selezionare l'icona Autenticazione LDAP nel menu a sinistra.
2. Selezionare la casella di controllo **Abilita autenticazione LDAP**.

   ![Autenticazione LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Nella scheda Client modificare le porte TCP e SSL se il servizio LDAP di Azure Multi-Factor Authentication deve essere associato a porte non standard per rimanere in ascolto di richieste LDAP.
4. Se si prevede di usare LDAPS dal client al server Azure Multi-Factor Authentication, è necessario installare un certificato SSL nello stesso server come server MFA. Fare clic su **Sfoglia** accanto alla casella del Certificato SSL e selezionare un certificato da usare per la connessione protetta.
5. Fare clic su **Aggiungi**.
6. Nella finestra di dialogo Aggiungi client LDAP immettere l'indirizzo IP del dispositivo, del server o dell'applicazione che esegue l'autenticazione al server e il nome di un'applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
7. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti alla verifica in due passaggi, selezionare la casella di controllo **Richiedi corrispondenza utente di Multi-Factor Authentication**. Se un numero significativo di utenti non è ancora stato importato nel server e/o non è soggetto alla verifica in due passaggi, lasciare deselezionata la casella. Vedere il file della Guida del server di MFA per altre informazioni su questa funzionalità.

Ripetere questa procedura per aggiungere altri client LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurare la connessione alla directory LDAP

Quando Azure Multi-Factor Authentication è configurato per ricevere autenticazioni LDAP, deve usare un proxy per le autenticazioni alla directory LDAP. Di conseguenza, nella scheda Destinazione viene visualizzata un'unica opzione non selezionabile per l'uso di una destinazione LDAP.

1. Per configurare la connessione alla directory LDAP, fare clic sull'icona **Integrazione directory**.
2. Nella scheda Impostazioni selezionare il pulsante di opzione **Usa configurazione LDAP specifica**.
3. Selezionare **Modifica...**
4. Nella finestra di dialogo Modifica configurazione LDAP compilare i campi con le informazioni necessarie per connettersi alla directory LDAP. Le descrizioni dei campi sono incluse nel file della Guida del server Azure Multi-Factor Authentication.

    ![Integrazione di directory](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Verificare la connessione LDAP facendo clic sul pulsante **Test**.
6. Se il test della connessione LDAP ha esito positivo, fare clic sul pulsante **OK**.
7. Fare clic sulla scheda **Filtri**. Il server è preconfigurato per il caricamento di contenitori, gruppi di sicurezza e utenti da Active Directory. Se viene eseguita l'associazione a un'altra directory LDAP, è probabilmente necessario modificare i filtri visualizzati. Per altre informazioni sui filtri, fare clic sul collegamento **Guida**.
8. Fare clic sulla scheda **Attributes** . Il server è preconfigurato per il mapping degli attributi da Active Directory.
9. Se viene eseguita l'associazione a un'altra directory LDAP o vengono modificati i mapping degli attributi preconfigurati, fare clic su **Modifica...**
10. Nella finestra di dialogo Modifica attributi apportare modifiche ai mapping degli attributi LDAP per la directory. I nomi degli attributi possono essere digitati o selezionati facendo clic sul pulsante **...** accanto a ogni campo. Per altre informazioni sugli attributi, fare clic sul collegamento **Guida**.
11. Fare clic sul pulsante **OK**.
12. Fare clic sull'icona **Impostazioni società** e selezionare la scheda **Risoluzione nome utente**.
13. Se ci si connette ad Active Directory da un server aggiunto a un dominio, lasciare selezionato il pulsante di opzione **Usa identificatori di sicurezza (SID) Windows per la corrispondenza dei nomi utente**. In caso contrario, selezionare il pulsante di opzione **Usa attributo dell'identificatore univoco LDAP per la corrispondenza dei nomi utente**. 

Quando il pulsante di opzione **Usa attributo dell'identificatore univoco LDAP per la corrispondenza dei nomi utente** è selezionato, il server Azure Multi-Factor Authentication prova a risolvere ogni nome utente in un identificatore univoco nella directory LDAP. Viene eseguita una ricerca LDAP negli attributi dei nomi utente definiti nella scheda Integrazione directory -> Attributi. Se un utente è autenticato, il nome utente viene risolto nell'identificatore univoco nella directory LDAP. L'identificatore univoco è usato per abbinare l'utente nel file di dati di Azure Multi-Factor Authentication. Sono ammessi confronti senza distinzione tra maiuscole e minuscole e formati dei nomi utente lunghi e corti.

Dopo aver completato questa procedura, il server MFA ascolta le porte configurate per le richieste di accesso LDAP dai client configurati ed agisce come un proxy per le richieste di autenticazione alla directory LDAP.

## <a name="configure-ldap-client"></a>Configurare il client LDAP
Per configurare il client LDAP, seguire queste linee guida:

* Configurare il dispositivo, il server o l'applicazione per l'autenticazione tramite LDAP al server Azure Multi-Factor Authentication come per la directory LDAP. È consigliabile usare le stesse impostazioni specificate normalmente per connettersi direttamente alla directory LDAP, fatta eccezione per il nome o l'indirizzo IP del server, che deve corrispondere a quello del server Azure Multi-Factor Authentication.
* Impostare il timeout di LDAP su un valore compreso tra 30 e 60 secondi, sufficiente per convalidare le credenziali dell'utente con la directory LDAP, eseguire la verifica in due passaggi, ricevere la risposta e rispondere alla richiesta di accesso LDAP.
* Se si usa LDAPS, il dispositivo o il server che esegue le query LDAP deve considerare attendibile il certificato SSL installato nel server Azure Multi-Factor Authentication.

