---
title: Risolvere i problemi di configurazione della delega vincolata Kerberos per il proxy applicazione | Microsoft Docs
description: Risolvere i problemi di configurazione della delega vincolata Kerberos per Application Proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca50cfb8697fdbb8c71054c5a6b4d5e23792eb5
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381532"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Risolvere i problemi di configurazione della delega vincolata Kerberos per Application Proxy

I metodi disponibili per ottenere funzionalità di punto di accesso singolo (Single Sign-On, SSO) per le applicazioni pubblicate possono variare da un'applicazione a un'altra. Un'opzione offerta per impostazione predefinita da Azure Active Directory (Azure AD) Application Proxy è la delega vincolata Kerberos (KCD, Kerberos Constrained Delegation). È possibile configurare un connettore che esegue l'autenticazione Kerberos vincolata nelle applicazioni back-end per conto degli utenti.

La procedura per abilitare la delega vincolata Kerberos è semplice e normalmente è sufficiente avere una conoscenza generica dei vari componenti e del flusso di autenticazione che supportano l'accesso SSO. Non sempre però l'accesso SSO della delega vincolata Kerberos funziona come previsto e per risolvere questo tipo di situazioni sono necessarie fonti di informazione valide.

Questo articolo offre un punto di riferimento unico per risolvere e correggere in modo autonomo alcuni tra i problemi più comuni. Spiega anche come diagnosticare problemi relativi a implementazioni più complesse.

L'articolo presuppone quanto segue:

- Azure Active Directory Application Proxy è stato distribuito come indicato nell'[introduzione ad Application Proxy](application-proxy-add-on-premises-application.md) e l'accesso generale alle applicazioni non KCD funziona come previsto.
- L'applicazione di destinazione pubblicata è basata su Internet Information Services (IIS) e sull'implementazione Microsoft di Kerberos.
- Gli host del server e dell'applicazione si trovano in un unico dominio di Azure Active Directory. Informazioni dettagliate sugli scenari tra diversi domini e foreste sono disponibili nel [white paper sulla delega vincolata Kerberos](https://aka.ms/KCDPaper).
- L'applicazione oggetto è pubblicata in un tenant di Azure con preautenticazione abilitata. Gli utenti eseguono l'autenticazione in Azure tramite l'autenticazione basata su form. Gli scenari di autenticazione dei rich client non sono trattati in questo articolo, ma verranno aggiunti in futuro.

## <a name="prerequisites"></a>prerequisiti

Azure Active Directory Application Proxy può essere distribuito in numerosi tipi di infrastrutture o ambienti e le architetture variano da organizzazione a organizzazione. Nella maggior parte dei casi, i problemi relativi alla delega vincolata Kerberos non sono dovuti agli ambienti, ma a semplici errori di configurazione o a errori generici.

Per questo motivo è consigliabile verificare che siano soddisfatti tutti i prerequisiti descritti in [Delega vincolata Kerberos per l'accesso Single Sign-On alle app con il proxy dell'applicazione](application-proxy-configure-single-sign-on-with-kcd.md) prima di procedere con la risoluzione dei problemi. Si noti in particolare la sezione sulla configurazione della delega vincolata Kerberos in 2012 R2, in quanto adotta un approccio radicalmente diverso alla configurazione della delega vincolata Kerberos nelle versioni precedenti di Windows. È opportuno tenere presente anche altre considerazioni:

- Spesso può accadere che un server membro del dominio apra una finestra di dialogo con canale sicuro con un controller di dominio specifico. Quindi il server potrebbe passare a un'altra finestra di dialogo in qualsiasi momento. Gli host del connettore non sono quindi limitati alla comunicazione con specifici controller di dominio di siti locali.
- Gli scenari tra domini si affidano ai riferimenti che indirizzano un host del connettore ai controller di dominio che possono trovarsi all'esterno del perimetro della rete locale. In questi casi, è altrettanto importante consentire il traffico verso i controller di dominio che rappresentano altri rispettivi domini. In caso contrario, la delega avrà esito negativo.
- Dove possibile, evitare di inserire eventuali dispositivi attivi IPS o IDS tra gli host del connettore e i controller di dominio, Questi dispositivi sono talvolta troppo intrusivi e interferiscono con il traffico RPC di base.

È consigliabile testare la delega negli scenari più semplici. Il numero di variabili introdotte è infatti direttamente proporzionale ai problemi da risolvere. Ad esempio, limitare i test a un singolo connettore permette di risparmiare tempo prezioso e, dopo aver risolto i problemi, sarà possibile aggiungere altri connettori.

I problemi possono essere legati anche ad alcuni fattori ambientali. Per evitare questi fattori, ridurre al minimo l'architettura durante i test. Spesso, ad esempio, si verificano errori di configurazione negli elenchi di controllo di accesso (ACL) del firewall interno. Se possibile, quindi, consentire tutto il traffico di un connettore direttamente verso i controller di dominio e l'applicazione back-end.

È consigliabile posizionare i connettori il più vicino possibile alle destinazioni. La presenza di un firewall inline durante il test aggiunge complessità superflua e può prolungare l'analisi.

Che cosa costituisce un problema di delega vincolata Kerberos? Ci sono diverse indicazioni tipiche di problemi con l'accesso SSO della delega vincolata Kerberos. I primi segnali si manifestano in genere nel browser.

![Esempio: Errore di configurazione della delega vincolata Kerberos](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Esempio: Autorizzazione non riuscita a causa di autorizzazioni mancanti](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Entrambe queste immagini mostrano lo stesso sintomo: un errore di accesso SSO. L'accesso dell'utente all'applicazione viene negato.

## <a name="troubleshooting"></a>risoluzione dei problemi

La modalità di risoluzione dipende dal problema e dai sintomi osservati. Prima di proseguire, leggere gli articoli seguenti, che contengono informazioni utili sulla risoluzione dei problemi:

- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)
- [Errori di Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Uso dell'accesso Single Sign-On quando le identità cloud e locali non sono identiche](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Se si è giunti a questo punto, è presente un problema importante. Per iniziare, separare il flusso nelle tre seguenti fasi distinte per le quali è possibile risolvere i problemi.

### <a name="client-pre-authentication"></a>Preautenticazione del client

L'utente esterno che esegue l'autenticazione in Azure tramite un browser. Per garantire il funzionamento dell'accesso SSO della delega vincolata Kerberos, è essenziale poter eseguire la preautenticazione in Azure. È consigliabile eseguire test e risolvere gli eventuali problemi. La fase di preautenticazione non è in alcun modo correlata alla delega vincolata Kerberos o all'applicazione pubblicata. È semplice risolvere eventuali discrepanze eseguendo un test di integrità per verificare che l'account sia presente in Azure e non sia disabilitato o bloccato. La risposta di errore nel browser è in genere sufficientemente descrittiva per comprendere la causa. In caso di dubbi, consultare altri articoli Microsoft sulla risoluzione dei problemi.

### <a name="delegation-service"></a>Servizio di delega

Il connettore Proxy di Azure che ottiene un ticket di servizio Kerberos da un Centro distribuzione chiavi (KDC) Kerberos per gli utenti.

Le comunicazioni esterne tra il client e il front-end di Azure non dovrebbero avere comunque alcuna rilevanza sulla delega vincolata Kerberos, se non per garantirne il funzionamento. In questo modo il servizio proxy di Azure può ricevere un ID utente valido usato per ottenere un ticket Kerberos. Senza di questo, la delega vincolata Kerberos non sarebbe possibile e l'operazione avrebbe esito negativo.

Come accennato in precedenza, i messaggi di errore del browser offrono in genere alcune indicazioni valide sul motivo per cui si verificano errori. Assicurarsi di annotare l'ID attività e il timestamp nella risposta. Queste informazioni consentono di associare il comportamento a eventi correnti nel registro eventi del servizio proxy di Azure.

![Esempio: Errore di configurazione della delega vincolata Kerberos](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Le voci corrispondenti visualizzate nel log eventi vengono mostrate come eventi 13019 o 12027. I log eventi dei connettori sono disponibili in **Registri applicazioni e servizi** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connettore**&gt;**Amministratore**.

![Evento 13019 del log eventi del proxy dell'applicazione](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Evento 12027 del log eventi del proxy dell'applicazione](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Usare un record **A** nel DNS interno per l'indirizzo dell'applicazione e non un record **CName**.
1. Verificare nuovamente che all'host del connettore siano stati concessi i diritti di delega al nome dell'entità servizio (SPN) dell'account di destinazione designato e che l'opzione **Usa un qualsiasi protocollo di autenticazione** sia selezionata. Per altre informazioni, vedere l'articolo sulla [configurazione dell'accesso SSO](application-proxy-configure-single-sign-on-with-kcd.md).
1. Verificare che in Azure AD sia presente solo un'istanza del nome dell'entità servizio, eseguendo il comando `setspn -x` da un prompt dei comandi di qualsiasi host membro di dominio.
1. Verificare che sia applicato un criterio di dominio che limita la [dimensione massima dei token Kerberos pubblicati](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Questo criterio impedisce che il connettore riceva un token se il valore della dimensione è eccessivo.

Una traccia di rete che acquisisca gli scambi tra l'host del connettore e una delega vincolata Kerberos del dominio rappresenta quindi il successivo passaggio ideale per ottenere un livello maggiore di dettaglio sui problemi. Per altre informazioni, vedere il [white paper di approfondimento sulla risoluzione dei problemi](https://aka.ms/proxytshootpaper).

Se la creazione di ticket non presenta problemi, verrà visualizzato un evento nei log a indicare che l'autenticazione ha avuto esito negativo a causa di un codice 401 restituito dall'applicazione. Questo evento indica che l'applicazione di destinazione ha rifiutato il ticket. Passare alla fase successiva.

### <a name="target-application"></a>Applicazione di destinazione

Il consumer del ticket Kerberos fornito dal connettore. In questa fase si prevede che il connettore abbia inviato un ticket di servizio Kerberos al back-end, sotto forma di intestazione nella prima richiesta dell'applicazione.

1. Tramite l'URL interno dell'applicazione definito nel portale, confermare che l'applicazione sia accessibile direttamente dal browser nell'host del connettore. A questo punto sarà possibile eseguire l'accesso. I dettagli a questo proposito sono disponibili nella pagina di **risoluzione dei problemi** del connettore.
1. Sempre nell'host del connettore verificare che l'autenticazione tra il browser e l'applicazione usi Kerberos, eseguendo una delle operazioni seguenti:
1. Eseguire Strumenti di sviluppo (**F12**) in Internet Explorer oppure usare [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) dall'host del connettore. Passare all'applicazione usando l'URL interno. Esaminare le intestazioni dell'autorizzazione WWW restituite nella risposta dell'applicazione, per assicurarsi che sia presente Negotiate o Kerberos.

   - Il successivo BLOB Kerberos restituito nella risposta dal browser all'applicazione inizia con **YII**, e questa è una buona indicazione del fatto che Kerberos è in esecuzione. Microsoft NT LAN Manager (NTLM), d'altro canto, inizia sempre con **TlRMTVNTUAAB**, ovvero NTLM Security Support Provider (NTLMSSP) in caso di decodifica da Base64. Se **TlRMTVNTUAAB** è presente all'inizio del BLOB, Kerberos non è disponibile. Se **TlRMTVNTUAAB**non è visibile, Kerberos dovrebbe essere disponibile.

      > [!NOTE]
      > Se si usa Fiddler, questo metodo richiede la disabilitazione temporanea della protezione estesa sulla configurazione dell'applicazione in IIS.

      ![Finestra di controllo di rete del browser](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Il BLOB nell'immagine non inizia con **TIRMTVNTUAAB**. In questo esempio pertanto Kerberos è disponibile, e il BLOB Kerberos non inizia con **YII**.

1. Rimuovere temporaneamente NTLM dall'elenco di provider nel sito di IIS. Accedere all'app direttamente da Internet Explorer nell'host del connettore. NTLM non è più presente nell'elenco dei provider, ed è possibile accedere all'applicazione solo tramite Kerberos. Se l'accesso non riesce, potrebbe esserci un problema con la configurazione dell'applicazione. L'autenticazione Kerberos non funziona.

   - Se Kerberos non è disponibile, controllare le impostazioni di autenticazione dell'applicazione in IIS. Verificare che l'opzione **Negotiate** sia elencata nella parte superiore, con NTLM immediatamente sotto. Se viene visualizzata l'opzione **Not Negotiate**, **Kerberos o Negotiate**, o **PKU2U**, procedere solo se Kerberos funziona.

     ![Provider di autenticazione di Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Con Kerberos e NTLM disponibili, disabilitare temporaneamente la preautenticazione per l'applicazione nel portale. Verificare che sia possibile accedervi da Internet tramite l'URL esterno. Viene richiesto di eseguire l'autenticazione. A tal fine, usare lo stesso account usato nel passaggio precedente. In caso contrario, si è verificato un problema con l'applicazione back-end e non con la delega vincolata Kerberos.
   - Riabilitare la preautenticazione nel portale, ed eseguire l'autenticazione tramite Azure cercando di connettersi all'applicazione tramite l'URL esterno. Se l'accesso SSO ha esito negativo, viene visualizzato un messaggio di errore non consentito nel browser e l'evento 13022 nel log:

     *Microsoft AAD Application Proxy Connector non riesce ad autenticare l'utente perché il server back-end risponde ai tentativi di autenticazione Kerberos con un errore HTTP 401.*

      ![Mostra l'errore HTTTP 401 Forbidden](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Controllare l'applicazione IIS. Verificare che il pool di applicazioni sia configurato per l'uso dello stesso account con cui è stato configurato il nome dell'entità servizio in Azure AD. Passare a IIS come mostrato nella figura seguente.

      ![Finestra di configurazione dell'applicazione IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Dopo aver stabilito l'identità, verificare che questo account venga configurato con il nome dell'entità servizio in questione. Un esempio è `setspn –q http/spn.wacketywack.com`. Nel prompt dei comandi immettere il testo seguente.

      ![Mostra la finestra di comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Controllare il nome dell'entità servizio definito in base alle impostazioni dell'applicazione nel portale. Verificare che lo stesso nome dell'entità servizio configurato nell'account Azure AD di destinazione sia usato dal pool di applicazioni dell'applicazione.

      ![Configurazione del nome dell'entità servizio nel portale di Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Passare a IIS e selezionare l'opzione **Editor di configurazione** per l'applicazione. Passare a **system.webServer/security/authentication/windowsAuthentication**. Verificare che il valore **UseAppPoolCredentials** sia **True**.

      ![Opzione delle credenziali dei pool di applicazioni della configurazione IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Modificare questo valore in **True**. Rimuovere tutti i ticket Kerberos memorizzati nella cache dal server back-end usando il comando seguente:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Per altre informazioni, vedere [Purge the Kerberos client ticket cache for all sessions](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf) (Ripulire la cache dei ticket client Kerberos per tutte le sessioni).

Oltre a risultare utile per migliorare le prestazioni delle operazioni Kerberos, l'abilitazione della modalità Kernel determina la decrittografia del ticket per il servizio richiesto con l'account del computer. Questo è anche noto come sistema locale, pertanto la sua impostazione su **True** interrompe la delega vincolata Kerberos quando l'applicazione è ospitata in più server in una farm.

- Come verifica aggiuntiva, disabilitare anche la protezione **estesa**. In alcune situazioni, la protezione **estesa** interrompe la delega vincolata Kerberos se abilitata in configurazioni specifiche, in cui un'applicazione viene pubblicata come sottocartella del sito Web predefinito. Tale applicazione è configurata soltanto per l'autenticazione anonima, lasciando le finestre di dialogo disattivate a indicare che gli oggetti figlio non erediteranno impostazioni attive. È consigliabile eseguire il test e quindi ripristinare questo valore su **abilitata**, laddove possibile.

  Questi controlli aggiuntivi dovrebbero consentire di iniziare a usare correttamente l'applicazione pubblicata. È possibile avviare i connettori aggiuntivi che sono anch'essi configurati per la delega. Per altre informazioni, leggere la procedura tecnica dettagliata relativa nella [Guida completa alla risoluzione dei problemi di Azure AD Application Proxy](https://aka.ms/proxytshootpaper).

Se il problema persiste, contattare il supporto tecnico Microsoft creando un ticket direttamente nel portale per essere contattati da un tecnico.

## <a name="other-scenarios"></a>Altri scenari

- Il proxy dell'applicazione Azure richiede un ticket Kerberos prima dell'invio della richiesta a un'applicazione. Alcune applicazioni di terze parti non sono simili a questo metodo di autenticazione. preferendo l'approccio più tradizionale delle negoziazioni. La prima richiesta è anonima, consentendo all'applicazione di rispondere con i tipi di autenticazione supportati tramite un codice 401.
- L'autenticazione multihop viene generalmente usata negli scenari con applicazioni a livelli, con un back-end e un front-end che richiedono l'autenticazione, ad esempio SQL Server Reporting Services. Per configurare lo scenario a più hop, vedere l'articolo del supporto per la [delega vincolata Kerberos può richiedere la transizione del protocollo negli scenari con più hop](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Passaggi successivi

[Configurare la delega vincolata Kerberos in un dominio gestito](../../active-directory-domain-services/deploy-kcd.md).
