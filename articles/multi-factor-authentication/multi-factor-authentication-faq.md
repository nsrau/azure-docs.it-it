---
title: Domande frequenti su Azure Multi-Factor Authentication
description: "Fornisce un elenco di domande e risposte frequenti correlate ad Azure Multi-Factor Authentication. Multi-Factor Authentication è un metodo di verifica dell&quot;identità dell&quot;utente che richiede l&quot;uso di più fattori, oltre a un nome utente e una password. Fornisce un livello di sicurezza aggiuntivo agli accessi e alle transazioni degli utenti."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c83a7216d9763994fd5006dd9cd40883337ba4a


---
# <a name="azure-multi-factor-authentication-faq"></a>Domande frequenti su Azure Multi-Factor Authentication
Queste domande frequenti offrono risposte su Azure Multi-Factor Authentication e sull'uso del servizio Multi-Factor Authentication, incluse domande relative al modello di fatturazione e all'usabilità.

## <a name="general"></a>Generale
**D: In che modo il server Azure Multi-Factor Authentication gestisce i dati utente?**

Con il server Multi-Factor Authentication, i dati utente vengono archiviati solo nei server locali. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue l'autenticazione a due fattori, il server Multi-Factor Authentication invia dati al servizio cloud Azure Multi-Factor Authentication per l'autenticazione. La comunicazione tra il server Multi-Factor Authentication e il servizio cloud Multi-Factor Authentication avviene tramite Secure Sockets Layer (SSL) o Transport Layer Security (TLS) sulla porta 443 in uscita.

Quando le richieste di autenticazione vengono inviate al servizio cloud, vengono raccolti dati per i report di autenticazione e uso. Di seguito sono riportati i campi di dati inclusi nei log di verifica in due passaggi:

* **ID univoco:** nome utente o ID del server Multi-Factor Authentication locale
* **Nome e cognome:** facoltativo
* **Indirizzo di posta elettronica:** facoltativo
* **Numero di telefono:** quando si esegue una chiamata vocale o l'autenticazione tramite SMS
* **Token del dispositivo:** quando si esegue l'autenticazione con l'app per dispositivi mobili
* **Modalità di autenticazione**
* **Risultato dell'autenticazione**
* **Nome del server Multi-Factor Authentication**
* **IP del server Multi-Factor Authentication**
* **IP client:** se disponibile

I campi facoltativi possono essere configurati nel server Multi-Factor Authentication.

Il risultato della verifica (esito positivo o rifiuto), e il motivo di eventuali rifiuti, vengono archiviati insieme con i dati di autenticazione e sono disponibili nei report di autenticazione e uso.

## <a name="billing"></a>Fatturazione
È possibile rispondere alla maggior parte delle domande sulla fatturazione facendo riferimento alla pagina [Multi-Factor Authentication Prezzi](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**D: All’azienda verranno addebitati i costi per le chiamate telefoniche o gli SMS usati per autenticare gli utenti?**

Alle aziende non vengono addebitati costi per singole chiamate telefoniche o SMS inviati agli utenti tramite Azure Multi-Factor Authentication. Potrebbero essere addebitati ai proprietari del telefono le chiamate telefoniche o gli SMS che ricevono, in base al loro servizio telefonico personale.

**D: Il modello di fatturazione per utente comporta l'addebito dei costi in base al numero di utenti configurati per l'uso di Multi-Factor Authentication o in base al numero di utenti che eseguono le verifiche?**

La fatturazione si basa sul numero di utenti configurati per l'uso di Multi-Factor Authentication.

**D: Come funziona la fatturazione per Multi-Factor Authentication?**

Quando si usa il modello "per utente" o "per autenticazione", Azure MFA è una risorsa in base al consumo. Tutti i costi vengono addebitati alla sottoscrizione di Azure dell'organizzazione, proprio come per le macchine virtuali, i siti Web e così via.

Quando si usa il modello di licenza, le licenze di Azure Multi-Factor Authentication vengono acquistate e quindi assegnate agli utenti, proprio come accade per Office 365 e altri prodotti con sottoscrizione.

**D: È disponibile una versione gratuita di Azure Multi-Factor Authentication per gli amministratori?**

In alcuni casi, sì. Multi-Factor Authentication per amministratori di Azure offre un sottoinsieme delle funzionalità di Azure MFA senza costi aggiuntivi. Questa offerta è valida per i membri del gruppo di amministratori globali di Azure nelle istanze di Azure Active Directory che non sono collegate a un provider di Azure Multi-Factor Authentication in base al consumo. L'uso del provider di Multi-Factor Authentication consente di aggiornare tutti gli amministratori e gli utenti nella directory configurati per l'uso di Multi-Factor Authentication alla versione completa di Azure Multi-Factor Authentication.

**D: È disponibile una versione gratuita di Azure Multi-Factor Authentication per gli utenti di Office 365?**

In alcuni casi, sì. Multi-Factor Authentication per Office 365 offre un sottoinsieme delle funzionalità di Azure MFA senza costi aggiuntivi. Questa offerta è valida per gli utenti ai quali è stata assegnata una licenza di Office 365, se non è stato collegato un provider di Azure Multi-Factor Authentication basato sul consumo all'istanza di Azure Active Directory corrispondente. L'uso del provider di Multi-Factor Authentication consente di aggiornare tutti gli amministratori e gli utenti nella directory configurati per l'uso di Multi-Factor Authentication alla versione completa di Azure Multi-Factor Authentication.

**D: L'organizzazione può passare dai modelli di fatturazione in base all'utilizzo per utente a quelli per autenticazione in qualsiasi momento?**

Quando crea una risorsa, l'organizzazione sceglie un modello di fatturazione. Non è possibile modificare il modello di fatturazione dopo il provisioning della risorsa. È tuttavia possibile creare un'altra risorsa di Multi-Factor Authentication per sostituire l'originale. Le impostazioni degli utenti e le opzioni di configurazione non possono essere trasferite alla nuova risorsa.

**D: L'organizzazione può passare dai modelli di fatturazione in base all'utilizzo ai modelli di licenza in qualsiasi momento?**

Quando le licenze vengono aggiunte a una directory in cui è già presente un provider di Azure Multi-Factor Authentication per utente, la fatturazione in base all'utilizzo viene decrementata del numero di licenze di proprietà. Se tutti gli utenti configurati per l'uso di Multi-Factor Authentication dispongono di licenze assegnate, l'amministratore può eliminare il provider di Azure Multi-Factor Authentication.

Non è possibile combinare la fatturazione in base all'uso per autenticazione con un modello di licenza. Quando un provider di Multi-Factor Authentication per autenticazione è collegato a una directory, verranno fatturate all'organizzazione tutte le richieste di verifica di Multi-Factor Authentication, indipendentemente dalle eventuali licenze di proprietà.

**D: È necessario che l'organizzazione usi e sincronizzi le identità per usare Azure Multi-Factor Authentication?**

Quando si usano i modelli di fatturazione in base all'utilizzo, non è obbligatorio usare Azure Active Directory. Il collegamento di un provider di Multi-Factor Authentication a una directory è facoltativo. Se l'organizzazione non è collegata a una directory, è possibile distribuire il server Azure Multi-Factor Authentication o l'SDK di Azure Multi-Factor Authentication in locale.

Quando si usa il modello di licenza, è necessario usare Azure Active Directory in quanto le licenze vengono aggiunte alla directory quando vengono acquistate e assegnate agli utenti nella directory.

## <a name="usability"></a>Usabilità
**D: Cosa fare nel caso in cui un utente non riceve risposta sul telefono, o se il telefono non è disponibile all'utente?**

Se l'utente ha configurato un telefono di backup, riprovare selezionando tale telefono quando richiesto nella pagina di accesso. Se non è stato configurato alcun altro metodo, l'amministratore dell'organizzazione può aggiornare il numero assegnato al telefono principale dell'utente.

**D: Cosa fare se un amministratore se viene contattato da un utente per un account al quale l'utente non riesce più ad accedere?**

È possibile reimpostare l'account utente chiedendo all'utente stesso di ripetere il processo di registrazione. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**: Cosa fare se il telefono di un utente che usa password di app viene smarrito o rubato?**

L'amministratore può eliminare tutte le password dell'app dell'utente per impedire eventuali accessi non autorizzati. Dopo aver sostituito il dispositivo, l'utente può ricrearle. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**D: Cosa accade se l'utente non può accedere alle applicazioni non basate su browser?**

A un utente configurato per l'uso di Multi-Factor Authentication è necessaria una password di app per accedere ad alcune app non basate su browser. L'utente deve eliminare le informazioni di accesso, riavviare l'app e accedere con il proprio nome utente e la password di app.

Altre informazioni sulla creazione di password di app e altro nella [Guida alle password per le app](multi-factor-authentication-end-user-app-passwords.md).

> [!NOTE]
> Autenticazione moderna per i client di Office 2013
> 
> I client di Office 2013, tra cui Outlook, supportano nuovi protocolli di autenticazione. È possibile configurare Office 2013 affinché supporti l'autenticazione a più fattori. Dopo aver configurato Office 2013, le password di app non sono necessarie per i client di Office 2013. Per altre informazioni, vedere l' [annuncio dell'anteprima pubblica dell'autenticazione moderna di Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
> 
> 

**D: Cosa fare se un utente non riceve un SMS o se risponde a un SMS bidirezionale, ma la verifica scade?**

Il recapito degli SMS e la ricezione di risposte SMS bidirezionale non sono garantiti in quanto sussistono fattori non controllabili che possono influire sull'affidabilità del servizio. Questi fattori includono il paese di destinazione, il gestore di telefonia mobile e la qualità del segnale.

Gli utenti che hanno difficoltà nella ricezione degli SMS in modo affidabile, sono invitati a selezionare il metodo dell'app per dispositivi mobili o della telefonata. L'app per dispositivi mobili può ricevere notifiche sia su rete cellulare che Wi-Fi. L'app per dispositivi mobili può generare codici di verifica anche in caso di totale assenza di segnale. L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Se è necessario usare gli SMS, è consigliabile usare SMS unidirezionali anziché bidirezionali, poiché sono più affidabili ed evitano all'utente costi globali per l'SMS, causati dalla risposta a un messaggio di testo inviato da un altro paese.

**D: È possibile usare i token hardware con il server Azure multi-Factor Authentication?**

Se si usa il server Azure Multi-Factor Authentication, è possibile importare token OATH TOPT di terze parti e quindi usarli per la verifica in due passaggi.

È possibile usare token ActiveIdentity che sono token OATH TOTP qualora si inserisca il file di chiave privata in un file CSV che è possibile importare nel server Azure Multi-Factor Authentication. È possibile usare i token OATH con Active Directory Federation Services (ADFS), con Remote Authentication Dial-In User Service (RADIUS), quando il sistema client riesce a elaborare le risposte di richiesta di accesso e con l'autenticazione basata su moduli di IIS (Internet Information Server).

È possibile importare token OATH TOTP di terze parti con i formati seguenti:  

* Portable Symmetric Key Container (PSKC)  
* CSV se il file contiene un numero di serie, una chiave privata in formato Base 32 e un intervallo di tempo  

**D: È possibile usare il server di Azure Multi-Factor Authentication per proteggere Servizi Terminal?**

Sì, ma se si usa Windows Server 2012 R2 o versioni successive, è possibile farlo solo tramite Gateway Desktop remoto.

Le modifiche della sicurezza in Windows Server 2012 R2 hanno variato le modalità di connessione del server di Azure Multi-Factor Authentication al pacchetto di sicurezza dell'autorità di protezione locale in Windows Server 2012 e versioni precedenti. Per le versioni di Servizi terminal in Windows Server 2012 o versioni precedenti è possibile [proteggere un'applicazione con l'autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se si usa Windows Server 2012 R2, è necessario un Gateway Desktop remoto.

**D: Perché un utente potrebbe ricevere una chiamata Multi-Factor Authentication da un chiamante anonimo dopo aver impostato l'ID chiamante?**

Quando vengono effettuate chiamate MFA tramite la rete telefonica pubblica, queste vengono a volte indirizzate su un gestore che non supporta l'ID chiamante. Per questo motivo, l'ID chiamante non è garantito, anche se il sistema di autenticazione a più fattori lo invia sempre.

## <a name="errors"></a>Errori
**D: Cosa fare quando viene visualizzato l'errore "Authentication request is not for an activated account" (La richiesta di autenticazione non si riferisce a un account attivato) quando si usano le notifiche delle app per dispositivi mobili?**

Seguire questa procedura per rimuovere il proprio account dall'app per dispositivi mobili e quindi aggiungerlo di nuovo:

1. Passare al [profilo nel portale di Azure](https://account.activedirectory.windowsazure.com/profile/) e accedere con l'account dell'organizzazione.
2. Selezionare **Verifica aggiuntiva di sicurezza**.
3. Rimuovere l'account esistente dall'app per dispositivi mobili.
4. Fare clic su **Configura**e seguire le istruzioni per riconfigurare l'app per dispositivi mobili.

**D: Cosa è necessario fare se viene visualizzato un messaggio di errore 0x800434D4L durante l'accesso a un'applicazione non basata su browser?**

Attualmente è possibile usare la verifica aggiuntiva di sicurezza solo con applicazioni e servizi ai quali è possibile accedere tramite browser. Le applicazioni non basate su browser, denominate anche *applicazioni rich client*, che vengono installate sul computer locale, come ad esempio Windows PowerShell, non funzionano con gli account che richiedono la verifica aggiuntiva di sicurezza. In questo caso, l'utente potrebbe visualizzare l'errore 0x800434D4L generato dall'applicazione.

Una soluzione alternativa consiste nel disporre di account utente separati per le operazioni correlate all'amministratore e per quelle non correlate all'amministratore. In un secondo momento è possibile collegare le cassette postali tra l'account amministratore e l'account non amministratore in modo da poter accedere a Outlook usando l'account non amministratore. Per altre informazioni, vedere [Consentire a un amministratore di aprire e visualizzare il contenuto della cassetta postale di un utente](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passaggi successivi
Se la domanda non ha ricevuto risposta qui, riproporla nei commenti in fondo alla pagina. In alternativa, di seguito vengono elencate alcune opzioni aggiuntive per ottenere assistenza:

**D: Come è possibile ottenere assistenza per Azure Multi-Factor Authentication?**

* Cercare nella [Knowledge Base del supporto tecnico Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) le soluzioni ai problemi tecnici comuni.
* È possibile cercare e sfogliare le domande e risposte tecniche dalla community o porre le proprie domande nei [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* I clienti legacy di PhoneFactor che hanno domande o necessitano di assistenza per reimpostare una password possono usare il collegamento per la [reimpostazione della password](mailto:phonefactorsupport@microsoft.com) per aprire una richiesta di assistenza.
* Contattare un tecnico del supporto tramite [Azure Multi-Factor Authentication - PhoneFactor](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando si contatta Microsoft, è utile includere il maggior numero possibile di informazioni relative al problema. Tali informazioni includono la pagina in cui viene visualizzato l'errore, il codice dell'errore specifico, l'ID della sessione specifico e l'ID dell'utente che visualizza l'errore.




<!--HONumber=Nov16_HO3-->


