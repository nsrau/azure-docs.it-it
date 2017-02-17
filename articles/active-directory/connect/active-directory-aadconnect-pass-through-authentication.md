---
title: 'Azure AD Connect: autenticazione pass-through | Documentazione Microsoft'
description: Questo argomento riporta le informazioni necessarie sul funzionamento dell&quot;autenticazione pass-through di Azure AD con Active Directory (AD) locale per garantire l&quot;accesso ad Azure Active Directory (Azure AD) e ai servizi connessi.
services: active-directory
keywords: "che cos&quot;è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 67b832253619afe789a4dfdb95893e8c0ae62bee
ms.openlocfilehash: 17890fddf948ddc0e89a9107ac5fe65223cd05e1

---

# <a name="what-is-azure-ad-pass-through-authentication"></a>Che cos'è l'autenticazione pass-through di Azure AD
L'impiego delle stesse credenziali (nome utente e password) per accedere alle risorse aziendali e ai servizi basati sul cloud permette agli utenti di non dover ricordare credenziali diverse, riducendo le probabilità che dimentichino come eseguire l'accesso e che ricorrano all'help desk per chiedere la reimpostazione della password.

Se da un lato molte organizzazioni optano tranquillamente per la [sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md) di Azure AD allo scopo di fornire agli utenti una sola credenziale di accesso ai servizi locali e cloud, per altre organizzazioni è importante che le password, anche con hash, non vengano divulgate al di fuori dell'ambiente aziendale interno.

L'autenticazione pass-through di Azure AD rappresenta una soluzione semplice per questi clienti. Assicura che la convalida della password per i servizi di Azure AD venga eseguita in Active Directory in locale. Le password possono essere convalidate senza la necessità di un'infrastruttura di rete complessa o che le password locali siano memorizzate nel cloud in qualsiasi forma.

Se si usa questa soluzione in combinazione con l'opzione di accesso[Single Sign-On](active-directory-aadconnect-sso.md), non è necessario che gli utenti digitino la password per accedere ad Azure AD o ad altri servizi cloud. Questa funzionalità offre a questi clienti un'esperienza completamente integrata nei computer aziendali.

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

L'autenticazione pass-through può essere configurata tramite Azure AD Connect e usa un semplice agente locale che rimane in ascolto delle richieste di convalida delle password. L'agente può essere facilmente distribuito su più computer per garantire un'elevata disponibilità e bilanciamento del carico. Dal momento che tutte le comunicazioni avvengono solo in uscita, non esiste alcun requisito per una rete perimetrale o per l'installazione del connettore in una rete perimetrale. Di seguito sono riportati i requisiti della macchina per il connettore:

- Windows Server 2012 R2 o versione successiva
- Aggiunta a un dominio nella foresta in cui gli utenti siano convalidati

>[!NOTE]
>Gli ambienti a più foreste sono supportati se sono presenti relazioni di trust tra le foreste e se il routing nel suffisso del nome è configurato correttamente.

## <a name="supported-clients-in-the-preview"></a>Client supportati nell'anteprima
L'autenticazione pass-through è supportata tramite i client basati su Web browser e i client di Office che supportano l'[autenticazione moderna](https://aka.ms/modernauthga). Per i client che non sono supportati, quali i client legacy di Office, Exchange Active Sync (client di posta elettronica nativi su dispositivi mobili), si consiglia di usare l'autenticazione moderna equivalente. Ciò non solo consente l'autenticazione pass-through, ma anche l'applicazione dell'accesso condizionale, ad esempio l'autenticazione a più fattori.

Non è attualmente supportata l'autenticazione pass-through per i clienti che usano Windows 10 aggiunti ad Azure AD. Tuttavia, possono usare la sincronizzazione delle password come fallback automatico per Windows 10, oltre ai client legacy.

>[!NOTE]
>Durante l'anteprima, la sincronizzazione delle password è abilitata per impostazione predefinita quando si seleziona l'autenticazione pass-through come opzione di accesso in Azure AD Connect. Tale impostazione può essere disabilitata nella pagina Opzioni di Azure AD Connect.

## <a name="how-azure-ad-pass-through-authentication-works"></a>Come funziona l'autenticazione pass-through di Azure AD
Quando un utente immette nome utente e password nella pagina di accesso di Azure AD, questo servizio li inserisce nella coda connettori locali appropriata per la convalida. Uno dei connettori locali disponibili recupera quindi queste credenziali e ne esegue la convalida in Active Directory. La convalida viene eseguita tramite le API Windows standard secondo una modalità di convalida simile a quella delle password in Active Directory Federation Services.

Il controller di dominio locale valuta la richiesta e restituisce una risposta al connettore, che a sua volta la restituisce ad Azure AD. Azure AD quindi valuta la risposta e risponde all'utente come appropriato, ad esempio rilasciando un token o richiedendo l'autenticazione a più fattori. Il diagramma seguente illustra i diversi passaggi.

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Prerequisiti pass-through di Azure AD
Prima di poter abilitare e usare l'autenticazione pass-through di Azure AD, è necessario disporre di:

- Azure AD Connect
- Un tenant di Azure AD di cui l'utente è un amministratore globale.

>[!NOTE]
>È consigliabile usare un account amministratore di tipo solo cloud in modo da poter gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali.

- Un server che esegue Windows Server 2012 R2 o versione successiva in cui eseguire Azure AD Connect. Questo computer deve essere un membro della stessa foresta degli utenti che vengono convalidati.
- Se si dispone di più di una foresta contenente utenti da convalidare con Azure AD, le foreste devono avere relazioni di trust reciproche.
- L'elemento UserPrincipalName locale deve essere utilizzato come nome utente Azure AD.
- Un secondo server che esegue Windows Server 2012 R2 o versione successiva in cui eseguire un altro connettore per la disponibilità elevata e il bilanciamento del carico. Di seguito sono indicate le istruzioni sulla distribuzione del connettore.
- Se è presente un firewall tra il connettore e Azure AD, assicurarsi che:
    - Se il filtro degli URL è abilitato, verificare che il connettore possa comunicare con i seguenti URL:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net.  
        -  Il connettore esegue anche la connessione tramite le connessioni IP dirette agli [intervalli IP del data center di Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Assicurarsi che il firewall non esegua l'ispezione SSL poiché il connettore usa certificati client per comunicare con Azure AD.
    - Verificare che il connettore possa inviare richieste HTTPS (TCP) ad Azure AD sulle porte seguenti.

|Numero della porta|Descrizione
| --- | ---
|80|Abilita il traffico HTTP in uscita per la convalida di sicurezza quale gli elenchi di revoche dei certificati SSL.
|443|    Abilita l'autenticazione utente con Azure AD.
|8080/443|    Abilita la sequenza di bootstrap del connettore e l'aggiornamento automatico del connettore.
|9090|    Abilita la registrazione del connettore (necessaria solo per il processo di registrazione del connettore).
|9091|    Abilita il rinnovo automatico dei certificati di attendibilità del connettore.
|9352, 5671|    Abilita la comunicazione tra il connettore e il servizio di Azure AD per le richieste in ingresso.
|9350|    Facoltativo, consente prestazioni migliori per le richieste in ingresso.
|10100–10120|    Abilita le risposte dal connettore ad Azure AD.

Se il firewall impone il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete. Assicurarsi anche di abilitare la porta 8080 per NT Authority\System.

## <a name="enabling-pass-through-authentication"></a>Abilitazione dell'autenticazione pass-through
L'autenticazione pass-through di Azure AD viene abilitata tramite Azure AD Connect. L'uso dell'autenticazione pass-through determina la distribuzione del primo connettore nello stesso server di Azure AD Connect. Quando si installa Azure AD Connect, scegliere un'installazione personalizzata e selezionare l'autenticazione pass-through nella pagina delle opzioni di accesso. Per altre informazioni, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Single sign-on](./media/active-directory-aadconnect-sso/sso3.png)

Per impostazione predefinita, il primo connettore dell'autenticazione pass-through viene installato nel server di Azure AD Connect. È necessario distribuire un secondo connettore in un altro computer per garantire disponibilità elevata e bilanciamento del carico delle richieste di autenticazione.

Per distribuire il secondo connettore, seguire le istruzioni seguenti:

### <a name="step-1-install-the-connector-without-registration"></a>Passaggio 1: Installare il connettore senza registrazione

1.    Scaricare il [connettore](https://go.microsoft.com/fwlink/?linkid=837580) più recente.
2.    Aprire un prompt dei comandi come amministratore.
3.    Eseguire il comando riportato di seguito in cui il parametro /q significa installazione non interattiva, ovvero durante l'installazione non viene richiesto di accettare il contratto di licenza con l'utente finale.

```
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
```

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Passaggio 2: Registrare il connettore con Azure AD per l'autenticazione pass-through

1.    Aprire una finestra di PowerShell come amministratore
2.    Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script.  
`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Quando richiesto, immettere le credenziali del proprio account amministratore del tenant Azure AD.

## <a name="troubleshooting-pass-through-authentication"></a>Risoluzione dei problemi relativi all'autenticazione pass-through
Nella risoluzione dei problemi relativi all'autenticazione pass-through sono interessate alcune categorie diverse di problemi. A seconda del tipo di problema potrebbe essere necessario verificare in posizioni diverse.

Per gli errori correlati al connettore è possibile controllare il log eventi del computer connettore in **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**. Se necessario, è possibile ottenere log più dettagliati visualizzando i log di analisi e debug e attivando il log della sessione del connettore. L'esecuzione con questo log attivato per impostazione predefinita non è consigliata e i contenuti sono visibili solo dopo che il log è stato disattivato.

Informazioni aggiuntive sono disponibili anche nei log di traccia per il connettore in **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Questi log indicano anche il motivo della mancata riuscita dell'autenticazione pass-through per un singolo utente, come la voce seguente che include il codice di errore 1328:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

È possibile ottenere i dettagli dell'errore avviando un prompt dei comandi ed eseguendo il comando seguente (sostituire '1328' con il numero di errore nella richiesta):

`Net helpmsg 1328`

Il risultato sarà simile al seguente.

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

Se è abilitata la registrazione di controllo, informazioni aggiuntive sono disponibili anche nei log di sicurezza dei controller di dominio. Un esempio di query semplice per le richieste di autenticazione dal connettore è il seguente:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

Altri errori segnalati nella schermata di accesso di Azure AD sono descritti di seguito con la procedura di risoluzione appropriata.

|Errore|Description|Risoluzione
| --- | --- | ---
|AADSTS80001|Impossibile connettersi ad Active Directory|Verificare che il computer connettore sia aggiunto a un dominio e in grado di connettersi ad Active Directory.  
|AADSTS8002|Si è verificato un timeout di connessione ad Active Directory|Verificare che Active Directory sia disponibile e risponda alle richieste dal connettore.
|AADSTS80004|Il nome utente trasmesso al connettore non era valido|Assicurarsi che l'utente stia tentando di accedere con il nome utente corretto.
|AADSTS80005|La convalida ha rilevato un errore WebException imprevedibile|Si tratta di un problema temporaneo. ripetere la richiesta. Se il problema persiste, contattare il supporto Microsoft.
|AADSTS80007|Errore durante la comunicazione con Active Directory|Controllare i log del connettore per ulteriori informazioni e verificare che Active Directory funzioni come previsto.



<!--HONumber=Jan17_HO5-->


