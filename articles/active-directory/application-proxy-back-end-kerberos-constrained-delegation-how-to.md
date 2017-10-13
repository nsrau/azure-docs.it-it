---
title: Come configurare un'applicazione proxy dell'applicazione per l'uso della delega vincolata Kerberos | Microsoft Docs
description: Come configurare la delega vincolata Kerberos per un'applicazione proxy dell'applicazione Azure AD.
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3a768c30cb874d42d7b4fbd2eeaa6c0e23904e10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-an-application-proxy-application-to-use-kerberos-constrained-delegation"></a>Come configurare un'applicazione proxy dell'applicazione per l'uso della delega vincolata Kerberos

I metodi disponibili per ottenere l'accesso SSO alle applicazioni pubblicate possono in qualche modo variare da un'applicazione all'altra e una delle opzioni predefinite del proxy dell'applicazione Azure è la delega vincolata Kerberos (KCD). È qui che un host del connettore viene configurato per eseguire l'autenticazione Kerberos vincolata nelle applicazioni back-end per conto degli utenti.

La procedura effettiva per l'abilitazione della delega vincolata Kerberos è relativamente semplice e in genere richiede semplicemente una conoscenza generale dei vari componenti e del flusso di autenticazione che facilita l'accesso SSO. Trovare fonti di informazioni valide per risolvere i problemi degli scenari in cui l'accesso SSO della delega vincolata Kerberos non funziona come previsto può essere complicato.

A tale proposito, questo articolo tenta di fornire un singolo punto di riferimento per risolvere e correggere in modo autonomo alcuni tra i problemi più comuni riscontrati. L'articolo offre al contempo indicazioni aggiuntive per la diagnosi delle implementazioni più complesse e problematiche.

Si noti che questo articolo presuppone quanto segue:

-   Il proxy dell'applicazione Azure è stato distribuito in base alla nostra [documentazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) e l'accesso generale alle applicazioni non di delega vincolata Kerberos funziona come previsto.

-   L'applicazione di destinazione pubblicata è basata sull'implementazione di Kerberos da parte di IIS e Microsoft.

-   Gli host del server e dell'applicazione si trovano in un unico dominio di Active Directory. Informazioni dettagliate sugli scenari tra domini e foreste sono disponibili nel nostro [white paper sulla delega vincolata Kerberos](http://aka.ms/KCDPaper).

-   L'applicazione in questione viene pubblicata in un tenant Azure con preautenticazione abilitata e gli utenti dovrebbero eseguire l'autenticazione basata su moduli in Azure. Gli scenari di autenticazione dei rich client non sono trattati in questo articolo, ma verranno aggiunti in futuro.

## <a name="prerequisites"></a>Prerequisiti

Il proxy dell'applicazione Azure può essere distribuito praticamente in qualsiasi tipo di infrastruttura o ambiente e indubbiamente le architetture variano in base all'organizzazione. Una delle cause più comuni dei problemi correlati alla delega vincolata Kerberos non è rappresentata dagli ambienti stessi, ma piuttosto da semplici errori di configurazione o disattenzioni.

Per questo motivo, è consigliabile sempre verificare di aver soddisfatto tutti i prerequisiti elencati nel nostro articolo principale [Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) prima di procedere con la risoluzione dei problemi.

Si noti in particolare la sezione sulla configurazione della delega vincolata Kerberos in 2012 R2, in quanto adotta un approccio radicalmente diverso alla configurazione della delega vincolata Kerberos nelle versioni precedenti di Windows, ma è opportuno tenere presente anche altre considerazioni:

-   Spesso può accadere che un server membro del dominio apra una finestra di dialogo con canale sicuro con un controller di dominio specifico. Passare a un'altra finestra di dialogo in qualsiasi momento, in modo da non limitare gli host del connettore alla sola comunicazione con specifici controller di dominino di siti locali.

-   In modo analogo al punto precedente, gli scenari tra domini si affidano ai riferimenti che indirizzano un host del connettore ai controller di dominio che possono trovarsi all'esterno del perimetro della rete locale. In questo scenario è ugualmente importante verificare anche di consentire il traffico verso i controller di dominio che rappresentano altri rispettivi domini; in caso contrario, la delega avrà esito negativo.

-   Laddove possibile, è consigliabile evitare di posizionare dispositivi IPS/IDS attivi tra gli host del connettore e i controller di dominio, in quanto sono spesso altamente intrusivi e interferiscono con il traffico RPC di base

Per quanto sarebbe auspicabile risolvere i problemi in modo rapido ed efficiente, questa attività può richiedere tempo. Pertanto, laddove possibile, è opportuno testare la delega negli scenari più semplici. Il numero di variabili introdotte è direttamente proporzionale ai problemi da risolvere. Ad esempio, limitare i test a un singolo connettore permette di risparmiare tempo prezioso e, dopo aver risolto i problemi, sarà possibile aggiungere altri connettori.

Alcuni fattori ambientali possono contribuire a un problema. Anche in questo caso, se possibile, ridurre l'architettura al minimo ai fini di test. Spesso, ad esempio, si verificano errori di configurazione negli elenchi di controllo di accesso (ACL) del firewall interno. Se possibile, quindi, consentire tutto il traffico di un connettore direttamente verso i controller di dominio e l'applicazione back-end. 

La situazione ideale richiede di posizionare i connettori quanto più vicini alle destinazioni. La presenza di un firewall inline durante i test aggiunge inutili complessità e potrebbe prolungare le indagini.

In sostanza, che cosa costituisce un problema di delega vincolata Kerberos? Esistono diverse indicazioni tipiche di problemi con l'accesso SSO della delega vincolata Kerberos: i primi segnali si manifestano in genere nel browser.

   ![Errore di configurazione della delega vincolata Kerberos](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Autorizzazione non riuscita a causa di autorizzazioni mancanti](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Questi messaggi segnalano tutti l'impossibilità di eseguire l'accesso SSO e, di conseguenza, impediscono all'utente l'accesso all'applicazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

La modalità di risoluzione dipende quindi dal problema e dai sintomi osservati. Prima di procedere, è consigliabile visitare i collegamenti seguenti, che contengono informazioni utili probabilmente non ancora fornite:

-   [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Errori di Kerberos](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Utilizzo dell'accesso Single Sign-On quando le identità cloud e locali non sono identiche](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Se si è giunti a questo punto, è senza dubbio presente un problema importante. È necessario andare più a fondo: per iniziare, separare il flusso in tre fasi distinte di cui è possibile risolvere i problemi.

**Preautenticazione del client**: l'utente esterno che esegue l'autenticazione in Azure tramite un browser.

Per garantire il funzionamento dell'accesso SSO della delega vincolata Kerberos, è essenziale poter eseguire la preautenticazione in Azure. Questa fase deve essere verificata e gestita in prima istanza, in presenza di errori. Si noti che la fase di preautenticazione non è in alcun modo correlata alla delega vincolata Kerberos o all'applicazione pubblicata. Dovrebbe essere abbastanza semplice risolvere eventuali discrepanze eseguendo un test di integrità per verificare che l'account sia presente in Azure e non sia disabilitato o bloccato. La risposta di errore nel browser è in genere sufficientemente descrittiva per comprendere la causa. In caso di dubbi, è anche possibile controllare i nostri documenti aggiuntivi sulla risoluzione dei problemi.

**Servizio di delega**: il connettore del proxy Azure che ottiene un ticket di servizio Kerberos da un centro di distribuzione chiavi Kerberos (KDC) per conto degli utenti.

Le comunicazioni esterne tra il client e il front-end di Azure non dovrebbero avere comunque alcuna rilevanza sulla delega vincolata Kerberos, se non per garantirne il funzionamento. In questo modo il servizio proxy di Azure può disporre di un ID utente valido che può essere usato per ottenere un ticket Kerberos. Senza questa delega vincolata Kerberos non sarebbe possibile e l'operazione avrebbe esito negativo.

Come accennato in precedenza, i messaggi di errore del browser offrono in genere alcune indicazioni valide sul motivo per cui si verificano errori. Accertarsi di indicare l'ID attività e il timestamp nella risposta, poiché consentiranno di associare il comportamento agli eventi effettivi nel log eventi del proxy Azure.

   ![Errore di configurazione della delega vincolata Kerberos](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

E le voci corrispondenti visualizzate nel log eventi verrebbero interpretate come eventi 13019 o 12027. I log eventi dei connettori sono disponibili in **Registri applicazioni e servizi** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connettore**&gt;**Admin (Amministratore)**.

   ![Evento 13019 del log eventi del proxy dell'applicazione](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Evento 12027 del log eventi del proxy dell'applicazione](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Usare un record A nel DNS interno per l'indirizzo dell'applicazione e non un record CNAME

-   Verificare nuovamente che all'host del connettore siano stati concessi i diritti di delega al nome dell'entità servizio (SPN) dell'account di destinazione designato e che sia selezionata l'opzione **Usa un qualsiasi protocollo di autenticazione**. Questo argomento è trattato nel nostro articolo principale sulla [configurazione di SSO](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   Verificare che in AD sia presente una sola istanza del nome SPN eseguendo un comando **setspn -x** da un prompt dei comandi in un host membro di dominio qualsiasi

-   Verificare se sono stati applicati criteri di dominio per limitare la [dimensione massima dei token Kerberos emessi](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), in quanto, se eccessiva, impedisce al connettore di ottenere un token

Una traccia di rete che acquisisca gli scambi tra l'host del connettore e una delega vincolata Kerberos del dominio rappresenta quindi il successivo passaggio ideale per ottenere un livello maggiore di dettaglio sui problemi. Queste informazioni sono disponibili nel [white paper di approfondimento sulla risoluzione dei problemi](https://aka.ms/proxytshootpaper).

Se la creazione di ticket non presenta problemi, verrà visualizzato un evento nei log a indicare che l'autenticazione ha avuto esito negativo a causa di un codice 401 restituito dall'applicazione. Ciò indica in genere che l'applicazione di destinazione ha rifiutato il ticket. Procedere alla fase successiva.

**Applicazione di destinazione**: il consumer del ticket Kerberos fornito dal connettore

In questa fase il connettore dovrebbe aver inviato un ticket di servizio Kerberos al back-end, sotto forma di intestazione all'interno della prima richiesta dell'applicazione.

-   Tramite l'URL interno dell'applicazione definito nel portale, confermare che l'applicazione sia accessibile direttamente dal browser nell'host del connettore. A questo punto sarà possibile eseguire l'accesso. I dettagli a questo proposito sono disponibili nella pagina di risoluzione dei problemi del connettore.

-   Nell'host del connettore confermare che l'autenticazione tra il browser e l'applicazione usi Kerberos, effettuando una delle operazioni seguenti:

1.  Eseguire Strumenti di sviluppo (**F12**) in Internet Explorer oppure usare [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) dall'host del connettore. Passare all'applicazione mediante l'URL interno ed esaminare le intestazioni dell'autorizzazione WWW restituite nella risposta dell'applicazione, per garantire che sia presente Negotiate o Kerberos. Un BLOB Kerberos restituito successivamente nella risposta del browser all'applicazione inizia in genere con **YII**, pertanto si tratta di un'indicazione valida del fatto che Kerberos è in uso. NTLM, d'altro canto, inizia sempre con **TlRMTVNTUAAB** (NTLMSSP in caso di decodifica da Base64). Se **TlRMTVNTUAAB** è presente all'inizio del BLOB, Kerberos **non** è disponibile. In caso contrario, Kerberos dovrebbe essere disponibile.

  * Si noti che, se si usa Fiddler, questo metodo richiedere la disabilitazione temporanea della protezione estesa sulla configurazione dell'applicazione in IIS.

     ![Finestra di controllo di rete del browser](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Figura:* poiché non inizia con TIRMTVNTUAAB, questo esempio indica che Kerberos è disponibile. Si tratta di un esempio di un BLOB Kerberos che non inizia con YII.

2.  Rimuovere temporaneamente NTLM dall'elenco dei provider nel sito IIS e accedere all'app direttamente da Internet Explorer nell'host del connettore. Dopo aver rimosso NTLM dall'elenco dei provider, sarà possibile accedere all'applicazione solo mediante Kerberos. In caso di errore, si è verificato un problema con la configurazione dell'applicazione e l'autenticazione Kerberos non funziona.

Se Kerberos non è disponibile, controllare le impostazioni di autenticazione dell'applicazione in IIS per verificare che Negotiate si trovi in cima all'elenco e NTLM al di sotto di esso (Not Negotiate:kerberos o Negotiate:PKU2U). Procedere solo se Kerberos funziona.

   ![Provider di autenticazione di Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Con Kerberos e NTLM disponibili, disabilitare temporaneamente la preautenticazione per l'applicazione nel portale. Verificare se è possibile accedervi da Internet tramite l'URL esterno. Verrà richiesto di eseguire l'autenticazione, per la quale sarà possibile usare lo stesso account del passaggio precedente. In caso contrario, si è verificato un problema con l'applicazione back-end e non con la delega vincolata Kerberos.

-   Riabilitare la preautenticazione nel portale ed eseguire l'autenticazione tramite Azure tentando di connettersi all'applicazione tramite l'URL esterno. Se l'accesso SSO ha esito negativo, verranno visualizzati un messaggio di errore non consentito nel browser e l'evento 13022 nel log:

    *Microsoft AAD Application Proxy Connector non riesce ad autenticare l'utente perché il server back-end risponde ai tentativi di autenticazione Kerberos con un errore HTTP 401.*

    ![Errore HTTP 401 non consentito](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Controllare l'applicazione IIS per verificare che il pool di applicazioni sia configurato per l'uso dello stesso account con cui è stato configurato il nome SPN in Active Directory navigando in IIS come illustrato di seguito

    ![Finestra di configurazione dell'applicazione IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Quando si conosce l'identità, eseguire il comando seguente da un prompt dei comandi per verificare che questo account sia configurato con il nome SPN in questione. Ad esempio, **setspn – q http/spn.wacketywack.com**

    ![Finestra di comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Verificare che il nome SPN definito in base alle impostazioni dell'applicazione nel portale sia lo stesso nome SPN configurato in base all'account Active Directory di destinazione usato dal pool di applicazioni

   ![Configurazione del nome SPN nel Portale di Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Passare a IIS e selezionare l'opzione **Editor di configurazione** per l'applicazione, quindi navigare a **system.webServer/security/authentication/windowsAuthentication** per verificare che **UseAppPoolCredentials** sia impostato su true

   ![Opzione delle credenziali dei pool di applicazioni della configurazione IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Oltre a risultare utile per migliorare le prestazioni delle operazioni Kerberos, l'abilitazione della modalità Kernel determina la decrittografia del ticket per il servizio richiesto con l'account del computer. Questo è anche noto come sistema locale, pertanto la sua impostazione su true interrompe la delega vincolata Kerberos quando l'applicazione è ospitata in più server in una farm.

-   Come verifica aggiuntiva, è possibile disabilitare anche la protezione **estesa**. In alcuni scenari è emerso che questa impostazione interrompe la delega vincolata Kerberos se abilitata in configurazioni particolarmente specifiche, in cui un'applicazione viene pubblicata come sottocartella del sito Web predefinito. Questa stessa impostazione è configurata solo per l'autenticazione anonima, lasciando le finestre di dialogo disattivate a indicare che gli oggetti figlio non erediteranno impostazioni attive. Laddove possibile è comunque consigliabile mantenere abilitata questa impostazione. Eseguire i test, ma non dimenticare di riabilitarla.

Questi controlli aggiuntivi dovrebbero consentire di iniziare a usare correttamente l'applicazione pubblicata. È possibile proseguire ed eseguire lo spin up di altri connettori configurati per la delega ma, se l'operazione ha esito negativo, è consigliabile leggere la nostra procedura tecnica dettagliata nella [Guida completa alla risoluzione dei problemi del proxy dell'applicazione di Azure AD](https://aka.ms/proxytshootpaper)

Se il problema persiste, il supporto è a disposizione per fornire assistenza e procedere da questo punto. Creare un ticket di supporto direttamente all'interno del portale per essere contattati dai nostri ingegneri.

## <a name="other-scenarios"></a>Altri scenari

-   Il proxy dell'applicazione Azure richiede un ticket Kerberos prima dell'invio della richiesta a un'applicazione. Alcune applicazioni di terze parti, ad esempio Tableau Server, non implementano questo metodo di autenticazione, preferendo l'approccio più tradizionale delle negoziazioni. La prima richiesta è anonima, consentendo all'applicazione di rispondere con i tipi di autenticazione supportati tramite un codice 401.

-   Autenticazione a doppio hop: generalmente usata negli scenari con applicazioni a livelli, con un back-end e un front-end che richiedono l'autenticazione, ad esempio SQL Reporting Services.

## <a name="next-steps"></a>Passaggi successivi
[Configurare la delega vincolata Kerberos (KCD) in un dominio gestito](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
