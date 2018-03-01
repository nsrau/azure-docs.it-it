---
title: Risolvere i problemi delle configurazioni della delega vincolata Kerberos per il proxy di applicazione | Microsoft Docs
description: Risolvere i problemi delle configurazioni della delega vincolata Kerberos per il proxy di applicazione.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: harshja
ms.openlocfilehash: a580b0afbd34623986ea8a3f60147a937c423e5e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Risolvere i problemi delle configurazioni della delega vincolata Kerberos per il proxy di applicazione

I metodi disponibili per ottenere funzionalità Single Sign-On per le applicazioni pubblicate possono variare da un'applicazione a un'altra. Una delle opzioni offerte per impostazione predefinita dal proxy di applicazione Azure è la delega vincolata Kerberos (KCD, Kerberos Constrained Delegation). È possibile configurare un connettore per eseguire l'autenticazione Kerberos vincolata nelle applicazioni back-end per conto degli utenti.

La procedura effettiva per l'abilitazione della delega vincolata Kerberos è relativamente semplice e in genere richiede semplicemente una conoscenza generale dei vari componenti e del flusso di autenticazione che facilita l'accesso SSO. Trovare fonti di informazioni valide per risolvere i problemi degli scenari in cui l'accesso SSO della delega vincolata Kerberos non funziona come previsto può essere complicato.

A tale proposito, questo articolo mira a fornire un singolo punto di riferimento per risolvere e correggere in modo autonomo alcuni tra i problemi più comuni riscontrati. L'articolo offre al contempo indicazioni aggiuntive per la diagnosi delle implementazioni più complesse e problematiche.

L'articolo presuppone quanto segue:

-   Il proxy di applicazione Azure è stato distribuito in base alla [documentazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) e l'accesso generale alle applicazioni non di delega vincolata Kerberos funziona come previsto.

-   L'applicazione di destinazione pubblicata è basata su IIS e sull'implementazione Microsoft di Kerberos.

-   Gli host del server e dell'applicazione si trovano in un unico dominio di Active Directory. Informazioni dettagliate sugli scenari tra diversi domini e foreste sono disponibili nel [white paper sulla delega vincolata Kerberos](https://aka.ms/KCDPaper).

-   L'applicazione in questione viene pubblicata in un tenant di Azure con preautenticazione abilitata e gli utenti devono usare l'autenticazione basata su moduli per autenticarsi in Azure. Gli scenari di autenticazione dei rich client non sono trattati in questo articolo, ma verranno aggiunti in futuro.

## <a name="prerequisites"></a>prerequisiti

Il proxy di applicazione Azure può essere distribuito in numerosi tipi di infrastrutture o ambienti e indubbiamente le architetture variano in base all'organizzazione. Una delle cause più comuni dei problemi correlati alla delega vincolata Kerberos non è rappresentata dagli ambienti stessi, ma piuttosto da semplici errori di configurazione o disattenzioni.

Per questo motivo, è consigliabile sempre iniziare verificando di aver soddisfatto tutti i prerequisiti elencati nell'articolo [Delega vincolata Kerberos per l'accesso Single Sign-On alle app con il proxy di applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) prima di procedere con la risoluzione dei problemi.

Si noti in particolare la sezione sulla configurazione della delega vincolata Kerberos in 2012 R2, in quanto adotta un approccio radicalmente diverso alla configurazione della delega vincolata Kerberos nelle versioni precedenti di Windows, ma è opportuno tenere presente anche altre considerazioni:

-   Spesso può accadere che un server membro del dominio apra una finestra di dialogo con canale sicuro con un controller di dominio specifico. Passare a un'altra finestra di dialogo in qualsiasi momento, in modo da non limitare gli host del connettore alla sola comunicazione con specifici controller di dominino di siti locali.

-   In modo analogo al punto precedente, gli scenari tra domini diversi fanno affidamento su riferimenti che indirizzano un host del connettore ai controller di dominio che possono trovarsi all'esterno del perimetro della rete locale. In questo scenario è ugualmente importante assicurarsi anche di consentire il traffico verso i controller di dominio che rappresentano altri rispettivi domini. In caso contrario, la delega avrà esito negativo.

-   Laddove possibile, è consigliabile evitare di posizionare dispositivi IPS/IDS attivi tra gli host del connettore e i controller di dominio, in quanto sono spesso altamente intrusivi e interferiscono con il traffico RPC di base

È consigliabile testare la delega negli scenari più semplici. Il numero di variabili introdotte è direttamente proporzionale ai problemi da risolvere. Ad esempio, limitare i test a un singolo connettore permette di risparmiare tempo prezioso e, dopo aver risolto i problemi, sarà possibile aggiungere altri connettori.

I problemi possono essere legati anche ad alcuni fattori ambientali. Durante i test, ridurre al minimo l'architettura per evitare questi fattori ambientali. Spesso, ad esempio, si verificano errori di configurazione negli elenchi di controllo di accesso (ACL) del firewall interno. Se possibile, quindi, consentire tutto il traffico di un connettore direttamente verso i controller di dominio e l'applicazione back-end. 

La situazione ideale richiede di posizionare i connettori quanto più vicini alle destinazioni. La presenza di un firewall inline durante i test aggiunge inutili complessità e potrebbe prolungare le indagini.

In sostanza, che cosa costituisce un problema di delega vincolata Kerberos? Ci sono diverse indicazioni tipiche di problemi con l'accesso SSO della delega vincolata Kerberos e i primi segnali si manifestano in genere nel browser.

   ![Errore di configurazione della delega vincolata Kerberos](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Autorizzazione non riuscita a causa di autorizzazioni mancanti](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Questi messaggi segnalano tutti l'impossibilità di eseguire l'accesso SSO e, di conseguenza, impediscono all'utente l'accesso all'applicazione.

## <a name="troubleshooting"></a>risoluzione dei problemi

La modalità di risoluzione dipende quindi dal problema e dai sintomi osservati. Prima di procedere, è consigliabile visitare i collegamenti seguenti, che contengono informazioni utili probabilmente non ancora esaminate:

-   [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Errori di Kerberos](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Utilizzo dell'accesso Single Sign-On quando le identità cloud e locali non sono identiche](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Se si è giunti a questo punto, è senza dubbio presente un problema importante. Iniziare separando il flusso in tre fasi distinte per le quali è possibile risolvere i problemi.

**Preautenticazione del client**: l'utente esterno che esegue l'autenticazione in Azure tramite un browser.

Per garantire il funzionamento dell'accesso SSO della delega vincolata Kerberos, è essenziale poter eseguire la preautenticazione in Azure. È consigliabile eseguire test e risolvere gli eventuali problemi. La fase di preautenticazione non è in alcun modo correlata alla delega vincolata Kerberos o all'applicazione pubblicata. Dovrebbe essere abbastanza semplice risolvere eventuali discrepanze eseguendo un test di integrità per verificare che l'account sia presente in Azure e non sia disabilitato o bloccato. La risposta di errore nel browser è in genere sufficientemente descrittiva per comprendere la causa. In caso di dubbi, è anche possibile controllare i nostri documenti aggiuntivi sulla risoluzione dei problemi.

**Servizio di delega**: il connettore del proxy di Azure che ottiene un ticket di servizio Kerberos da un centro di distribuzione Kerberos (KDC, Kerberos Distribution Center) per conto degli utenti.

Le comunicazioni esterne tra il client e il front-end di Azure non dovrebbero avere comunque alcuna rilevanza sulla delega vincolata Kerberos, se non per garantirne il funzionamento. In questo modo il servizio proxy di Azure può ricevere un ID utente valido usato per ottenere un ticket Kerberos. Senza di questo, la delega vincolata Kerberos non sarebbe possibile e l'operazione avrebbe esito negativo.

Come accennato in precedenza, i messaggi di errore del browser offrono in genere alcune indicazioni valide sul motivo per cui si verificano errori. Assicurarsi di annotare l'ID attività e il timestamp nella risposta, poiché consentiranno di associare il comportamento agli eventi effettivi nel log eventi del proxy di Azure.

   ![Errore di configurazione della delega vincolata Kerberos](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

E le voci corrispondenti visualizzate nel log eventi verrebbero interpretate come eventi 13019 o 12027. I log eventi dei connettori sono disponibili in **Registri applicazioni e servizi** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connettore**&gt;**Admin (Amministratore)**.

   ![Evento 13019 del log eventi del proxy dell'applicazione](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Evento 12027 del log eventi del proxy dell'applicazione](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Usare un record A nel DNS interno per l'indirizzo dell'applicazione e non un record CNAME

-   Verificare nuovamente che all'host del connettore siano stati concessi i diritti di delega al nome dell'entità servizio (SPN) dell'account di destinazione designato e che l'opzione **Usa un qualsiasi protocollo di autenticazione** sia selezionata. Per altre informazioni su questo argomento, vedere l'articolo sulla [configurazione dell'accesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   Verificare che in AD sia presente una sola istanza del nome SPN eseguendo un comando `setspn -x` da un prompt dei comandi in qualsiasi host membro di dominio

-   Controllare se sono stati applicati criteri di dominio per limitare la [dimensione massima dei token Kerberos emessi](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), in quanto, se eccessiva, impedisce al connettore di ottenere un token

Una traccia di rete che acquisisca gli scambi tra l'host del connettore e una delega vincolata Kerberos del dominio rappresenta quindi il successivo passaggio ideale per ottenere un livello maggiore di dettaglio sui problemi. Per altre informazioni, vedere il [white paper di approfondimento sulla risoluzione dei problemi](https://aka.ms/proxytshootpaper).

Se la creazione di ticket non presenta problemi, verrà visualizzato un evento nei log a indicare che l'autenticazione ha avuto esito negativo a causa di un codice 401 restituito dall'applicazione. Questo indica in genere che l'applicazione di destinazione ha rifiutato il ticket. Passare quindi alla fase successiva seguente.

**Applicazione di destinazione**: il consumer del ticket Kerberos fornito dal connettore

In questa fase, il connettore dovrebbe aver inviato un ticket di servizio Kerberos al back-end, sotto forma di intestazione all'interno della prima richiesta dell'applicazione.

-   Tramite l'URL interno dell'applicazione definito nel portale, confermare che l'applicazione sia accessibile direttamente dal browser nell'host del connettore. A questo punto sarà possibile eseguire l'accesso. I dettagli a questo proposito sono disponibili nella pagina di risoluzione dei problemi del connettore.

-   Sempre nell'host del connettore verificare che l'autenticazione tra il browser e l'applicazione usi Kerberos, effettuando una delle operazioni seguenti:

1.  Eseguire Strumenti di sviluppo (**F12**) in Internet Explorer oppure usare [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) dall'host del connettore. Passare all'applicazione mediante l'URL interno ed esaminare le intestazioni dell'autorizzazione WWW restituite nella risposta dell'applicazione, per assicurarsi che sia presente Negotiate o Kerberos. Un BLOB Kerberos restituito successivamente nella risposta del browser all'applicazione inizia in genere con **YII**, quindi si tratta di una buona indicazione del fatto che Kerberos sia in uso. NTLM, d'altro canto, inizia sempre con **TlRMTVNTUAAB** (NTLMSSP in caso di decodifica da Base64). Se **TlRMTVNTUAAB** è presente all'inizio del BLOB, Kerberos **non** è disponibile. In caso contrario, Kerberos dovrebbe essere disponibile.
    > [!NOTE]
    > Se si usa Fiddler, questo metodo richiede la disabilitazione temporanea della protezione estesa sulla configurazione dell'applicazione in IIS.

     ![Finestra di controllo di rete del browser](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Figura:* poiché non inizia con TIRMTVNTUAAB, questo esempio indica che Kerberos è disponibile. Si tratta di un esempio di un BLOB Kerberos che non inizia con YII.

2.  Rimuovere temporaneamente NTLM dall'elenco dei provider nel sito IIS e accedere all'app direttamente da Internet Explorer nell'host del connettore. Dopo aver rimosso NTLM dall'elenco dei provider, sarà possibile accedere all'applicazione solo mediante Kerberos. In caso di errore, si è verificato un problema con la configurazione dell'applicazione e l'autenticazione Kerberos non funziona.

Se Kerberos non è disponibile, controllare le impostazioni di autenticazione dell'applicazione in IIS per verificare che Negotiate si trovi in cima all'elenco e NTLM al di sotto di esso (non Negotiate: Kerberos o Negotiate: PKU2U). Procedere solo se Kerberos funziona.

   ![Provider di autenticazione di Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Con Kerberos e NTLM disponibili, disabilitare temporaneamente la preautenticazione per l'applicazione nel portale. Verificare se è possibile accedervi da Internet tramite l'URL esterno. Verrà richiesto di eseguire l'autenticazione, per la quale sarà possibile usare lo stesso account del passaggio precedente. In caso contrario, si è verificato un problema con l'applicazione back-end e non con la delega vincolata Kerberos.

-   Riabilitare ora la preautenticazione nel portale ed eseguire l'autenticazione tramite Azure cercando di connettersi all'applicazione tramite l'URL esterno. Se l'accesso SSO ha esito negativo, verranno visualizzati un messaggio di errore non consentito nel browser e l'evento 13022 nel log:

    *Microsoft AAD Application Proxy Connector non riesce ad autenticare l'utente perché il server back-end risponde ai tentativi di autenticazione Kerberos con un errore HTTP 401.*

    ![Errore HTTP 401 non consentito](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Controllare l'applicazione IIS per verificare che il pool di applicazioni sia configurato per l'uso dello stesso account con cui è stato configurato il nome SPN in AD, spostandosi in IIS come illustrato di seguito

    ![Finestra di configurazione dell'applicazione IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Quando si conosce l'identità, eseguire il comando seguente da un prompt dei comandi per verificare che questo account sia configurato con il nome SPN in questione. Ad esempio, `setspn –q http/spn.wacketywack.com`

    ![Finestra di comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Controllare che il nome SPN definito in base alle impostazioni dell'applicazione nel portale corrisponda al nome SPN configurato in base all'account AD di destinazione usato dal pool di applicazioni

   ![Configurazione del nome dell'entità servizio nel portale di Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Passare a IIS e selezionare l'opzione **Editor di configurazione** per l'applicazione, quindi passare a **system.webServer/security/authentication/windowsAuthentication** per verificare che il valore di **UseAppPoolCredentials** sia **True**

   ![Opzione delle credenziali dei pool di applicazioni della configurazione IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Dopo la modifica di questo valore in **True**, è necessario rimuovere tutti i ticket Kerberos memorizzati nella cache dal server back-end. A questo scopo, è possibile eseguire questo comando:

```powershell
Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
``` 

Per altre informazioni, vedere [Purge the Kerberos client ticket cache for all sessions](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf) (Ripulire la cache dei ticket client Kerberos per tutte le sessioni).



Oltre a risultare utile per migliorare le prestazioni delle operazioni Kerberos, l'abilitazione della modalità Kernel determina la decrittografia del ticket per il servizio richiesto con l'account del computer. Questo è anche noto come sistema locale, pertanto la sua impostazione su true interrompe la delega vincolata Kerberos quando l'applicazione è ospitata in più server in una farm.

-   Come verifica aggiuntiva, è possibile disabilitare anche la protezione **estesa**. In alcuni scenari è emerso che questa impostazione interrompe la delega vincolata Kerberos se abilitata in configurazioni specifiche, in cui un'applicazione viene pubblicata come sottocartella del sito Web predefinito. Questa stessa impostazione è configurata solo per l'autenticazione anonima, lasciando le finestre di dialogo disattivate a indicare che gli oggetti figlio non erediteranno impostazioni attive. Laddove possibile è comunque consigliabile mantenere abilitata questa impostazione. Eseguire i test, ma non dimenticare di riabilitarla.

Questi controlli aggiuntivi dovrebbero consentire di iniziare a usare correttamente l'applicazione pubblicata. È possibile proseguire ed eseguire lo spin up di altri connettori configurati per la delega ma, se l'operazione ha esito negativo, è consigliabile leggere la nostra procedura tecnica dettagliata nella [Guida completa alla risoluzione dei problemi del proxy dell'applicazione di Azure AD](https://aka.ms/proxytshootpaper)

Se il problema persiste, il supporto è a disposizione per fornire assistenza e procedere da questo punto. Creare un ticket di supporto direttamente all'interno del portale per essere contattati da un tecnico.

## <a name="other-scenarios"></a>Altri scenari

-   Il proxy dell'applicazione Azure richiede un ticket Kerberos prima dell'invio della richiesta a un'applicazione. Alcune applicazioni di terze parti, ad esempio Tableau Server, non implementano questo metodo di autenticazione, preferendo l'approccio più tradizionale delle negoziazioni. La prima richiesta è anonima, consentendo all'applicazione di rispondere con i tipi di autenticazione supportati tramite un codice 401.

-   Autenticazione a doppio hop: generalmente usata negli scenari con applicazioni a livelli, con un back-end e un front-end che richiedono l'autenticazione, ad esempio SQL Reporting Services.

## <a name="next-steps"></a>Passaggi successivi
[Configurare la delega vincolata Kerberos (KCD) in un dominio gestito](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
