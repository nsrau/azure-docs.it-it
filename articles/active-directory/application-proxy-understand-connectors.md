---
title: Comprendere i connettori del proxy applicazione Azure AD | Documentazione Microsoft
description: Tratta i fondamenti dei connettori del proxy applicazione Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 36e737ebc3451a190e99dc2bc91ef4242d2f573e
ms.openlocfilehash: e9dfe8ad62dfa0eec810ecdeeddadbecc25b9163


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Comprendere i connettori del proxy applicazione Azure AD

Questo articolo illustra i connettori, che sono l'ingrediente segreto del proxy applicazione Azure Active Directory. Sono semplici, estremamente potenti e facile da distribuire e gestire.

> [!NOTE]
> Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> 
> 

## <a name="what-are-azure-ad-application-proxy-connectors"></a>Cosa sono i connettori del proxy applicazione Azure AD?
Il proxy applicazione funziona dopo che è stato installato il servizio Windows Server, chiamato connettore, nella rete. È possibile installare i connettori in base alla esigenze di disponibilità elevata e scalabilità. Iniziare con uno e aggiungerne altri in base alle esigenze. Ogni volta che viene installato un connettore, questo viene aggiunto al pool di connettori che serve il tenant.

Si consiglia di non installare i connettori nei server applicazione stessi, anche se è possibile farlo, specialmente per distribuzioni di piccole dimensioni.

Non è necessario eliminare i connettori che non si usano più. Quando un connettore è in esecuzione, rimane attivo quando si connette al servizio. I connettori inutilizzati vengono contrassegnati come _inattivi_ e vengono rimossi dopo 10 giorni di inattività. 

Per informazioni sulla risoluzione dei problemi di connettività di Azure AD, vedere [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Come risolvere i problemi di connettività del proxy applicazione Azure AD). 

## <a name="what-are-the-cloud-rules-for-connectors"></a>Quali sono le regole cloud per i connettori?
I connettori e il servizio si occupano di tutte le attività che richiedono disponibilità elevata. Possono essere aggiunti o rimossi in modo dinamico. Ogni volta che arriva una nuova richiesta, viene indirizzata a uno dei connettori attualmente disponibili. Se un connettore è temporaneamente non disponibile, non risponderà a questo traffico.

I connettori sono senza stato e non hanno dati di configurazione del computer, a parte le impostazioni di connettività al servizio e il certificato che autenticano il connettore. Quando si connettono al servizio, eseguono il pull di tutti i dati di configurazione necessari e li aggiornano ogni due minuti.
Eseguono inoltre il polling del server per verificare se è disponibile una versione più recente del connettore. Se ne viene individuata una, i connettori vengono aggiornati.

È possibile monitorare i connettori dal computer in cui sono eseguiti usando il registro eventi e i contatori delle prestazioni o dal cloud usando la pagina dello stato del connettore, come illustrato di seguito.

 ![Connettori del proxy applicazione AzureAD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>Tutta l'attività di rete è in uscita
I connettori inviano solo richieste in uscita, pertanto la connessione viene sempre avviata dai connettori. Non c'è la necessità di aprire porte in ingresso perché, una volta stabilita una sessione, il traffico scorre in entrambe le direzioni.

Il traffico in uscita viene inviato al servizio proxy applicazione e alle applicazioni pubblicate. Il traffico verso il servizio viene inviato ai data center di Azure su diversi numeri di porta. Per altre informazioni, vedere [Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md).

Siccome il traffico è solo in uscita, non è necessario configurare il bilanciamento del carico tra i connettori o configurare l'accesso in ingresso attraverso il firewall.

Per informazioni sulla configurazione delle regole del firewall in uscita, vedere [Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md).

## <a name="network-security"></a>Sicurezza di rete

I connettori possono essere installati in qualsiasi punto della rete che consenta loro di inviare richieste sia al servizio che alle applicazioni back-end. Funzioneranno correttamente se sono installati all'interno della rete aziendale, in una rete perimetrale (DMZ) o anche in una macchina virtuale eseguita nel cloud che dispone dell'accesso alle applicazioni.

Le distribuzioni su reti perimetrali sono in genere più complesse. Tuttavia, uno dei motivi per distribuire i connettori nella rete perimetrale è usare un'altra infrastruttura che sia disponibile per i componenti in esecuzione, ad esempio servizi di bilanciamento del carico dell'applicazione back-end e/o controlli di sicurezza come sistemi di rilevamento delle intrusioni.

## <a name="domain-joining"></a>Aggiunta al dominio

I connettori possono essere eseguiti in un computer che non fa parte del dominio. Tuttavia è necessario un computer appartenente al dominio se si sceglie di usare l'accesso Single Sign-On (SSO) per le applicazioni che impiegano l'autenticazione integrata di Windows (IWA). 

In questo caso i computer di connessione devono appartenere a un dominio che può eseguire la delega vincolata [Kerberos](https://web.mit.edu/kerberos) per conto degli utenti rilevanti per le applicazioni pubblicate.

I connettori possono anche essere aggiunti a domini o foreste con attendibilità parziale o a controller di dominio di sola lettura (RODC).

## <a name="connectors-on-hardened-environments"></a>Connettori in ambienti protetti

Nella maggior parte dei casi la distribuzione dei connettori è molto semplice e non richiede alcuna configurazione speciale. Esistono tuttavia alcune condizioni specifiche che devono essere prese in considerazione:

* Le organizzazioni che limitano il traffico in uscita devono seguire le istruzioni indicate qui per aprire le porte necessarie.
* Per i computer compatibili FIPS potrebbe essere necessario modificare la configurazione della macchina per consentire al servizio connettore, al servizio di aggiornamento del connettore e al suo programma di installazione di generare e archiviare un certificato sul computer.
* Le organizzazioni che bloccano il proprio ambiente in base ai processi che inviano le richieste di rete devono assicurarsi che siano abilitati entrambi i servizi connettore per accedere a tutte le porte e gli indirizzi IP necessari.
* In alcuni casi il proxy di inoltro in uscita può interrompere l'autenticazione bidirezionale con certificato e non consentire la comunicazione.

## <a name="all-connectors-are-created-almost-equal"></a>Tutti i connettori vengono creati quasi uguali

Tutti i connettori sono considerati identici tra loro e ogni richiesta in ingresso può arrivare a ciascuno dei connettori. Ciò significa che tutti devono avere la stessa connettività di rete e le stesse impostazioni [Kerberos](https://web.mit.edu/kerberos).

Tutte le comunicazioni dal connettore al servizio sono protette da un certificato client emesso e quindi installato nel computer di connessione. Per informazioni sul rinnovo dei certificati del connettore, vedere [Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md).

## <a name="connector-authentication"></a>Autenticazione del connettore

Per fornire un servizio sicuro, i connettori devono eseguire l'autenticazione verso il servizio e il servizio deve eseguire l'autenticazione verso il connettore. Questa operazione viene eseguita usando i certificati client e server quando i connettori avviano la connessione. In questo modo il nome utente e la password dell'amministratore non sono archiviati sul computer di connessione.

I certificati utilizzati sono specifici per il servizio proxy applicazione. Essi vengono creati durante la registrazione iniziale e rinnovati automaticamente dai connettori ogni due mesi. 

Se un connettore non viene connesso al servizio per un periodo di diversi mesi, potrebbe avere certificati obsoleti. In questo caso è necessaria la registrazione, perciò occorre disinstallare e reinstallare il connettore per attivare la registrazione. È possibile eseguire i seguenti comandi di PowerShell:

```
* Import-module AppProxyPSModule
* Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Prestazioni e scalabilità

Anche se le dimensioni per il servizio in linea sono trasparenti, sono un fattore da considerare quando si tratta di connettori. È necessario disporre di connettori sufficienti per gestire il traffico di picco. Poiché i connettori sono senza stato, non dipendono dal numero di utenti o sessioni. Dipendono invece dal numero di richieste e dalle dimensioni del payload. Per il traffico Web standard abbiamo visto computer medi gestire duemila richieste al secondo. Dipende dalle esatte caratteristiche del computer.

Le prestazioni del connettore sono legate alla CPU e alla rete. Le prestazioni della CPU sono necessarie per la crittografia SSL e la decrittografia, mentre la rete è fondamentale per una connettività veloce alle applicazioni e al servizio online in Azure. La memoria, al contrario, ha meno importanza per i connettori.

Per il servizio connettore si cerca di eseguire il massimo offload possibile dei connettori. Il servizio online si occupa di gran parte dell'elaborazione e di tutto il traffico non autenticato. Tutto ciò che può essere fatto nel cloud viene fatto nel cloud.

Un altro fattore relativo alle prestazioni è la qualità della connessione di rete tra i connettori, tra cui:

* _Il servizio online:_ se la connessione è lenta o presenta una latenza elevata interferisce con il livello di servizio. È preferibile che l'organizzazione sia connessa ad Azure tramite Express Route. In caso contrario assicurarsi che il team di rete garantisca che le connessioni ad Azure siano gestite in modo efficiente.

* _Le applicazioni back-end:_ in alcuni casi sono presenti altri proxy tra il connettore e le applicazioni back-end. È facile risolvere questo problema aprendo un browser dal computer di connessione e accedendo a queste applicazioni. Se si eseguono i connettori in Azure e le applicazioni sono locali, l'esperienza potrebbe essere diversa da come gli utenti si aspettano.
* _I controller di dominio:_ se i connettori eseguono l'accesso SSO mediante la delega vincolata Kerberos (KCD), essi contattano i controller di dominio prima di inviare la richiesta al back-end. I connettori hanno una cache dei ticket Kerberos ma in ambienti affollati la velocità di risposta dei controller di dominio può rallentare l'esperienza. Questa situazione è più comune per i connettori in esecuzione in Azure, mentre i controller di dominio sono in locale.

##<a name="automatic-updates-to-the-connector"></a>Aggiornamenti automatici del connettore

Con il servizio di aggiornamento del connettore offriamo un metodo di aggiornamento automatizzato. In questo modo si ottiene il vantaggio continuo di tutte le nuove funzionalità oltre a miglioramenti alla sicurezza e le prestazioni.

Azure AD supporta gli aggiornamenti automatici per tutti i connettori da distribuire. Fino a quando il servizio di aggiornamento del connettore del proxy applicazione è in esecuzione, i connettori vengono aggiornati automaticamente, senza tempi di inattività e senza richiedere passaggi manuali. Se non viene visualizzato il servizio di aggiornamento del connettore sul server, è necessario reinstallare il connettore per ottenere gli aggiornamenti. Per altre informazioni sull'installazione dei connettori, vedere la [documentazione di installazione](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-enable.md).

### <a name="updater-impact"></a>Impatto dell'aggiornamento

_Tenant con un solo connettore:_ se si dispone di un solo connettore, tale connettore verrà aggiornato come parte del gruppo più recente. Poiché non esiste un altro connettore a cui reindirizzare il traffico, il servizio non sarà disponibile durante l'aggiornamento. Per evitare questo periodo di inattività e assicurare più facilmente la disponibilità elevata, si consiglia di installare un altro connettore e creare un gruppo di connettori. Per informazioni dettagliate su come eseguire questa operazione, vedere la [documentazione sui gruppi di connettori](https://azure.microsoft.com/en-us/documentation/articlesactive-directory-application-proxy-connectors.md).

_Altri tenant:_ durante l'aggiornamento del connettore il traffico viene reindirizzato agli altri connettori per ridurre al minimo l'interruzione. Tuttavia le transazioni che sono in corso quando viene avviato l'aggiornamento potrebbero essere interrotte. Il browser deve ripetere automaticamente l'operazione, rendendo questa potenziale riduzione delle prestazioni trasparente all'utente. In caso contrario potrebbe essere necessario aggiornare la pagina per risolvere il problema.

Sappiamo che anche un solo minuto di inattività può creare problemi, ma questi aggiornamenti consentono di fornire un connettore ancora migliore con numerosi miglioramenti, perciò riteniamo che ne valga la pena.

Per altre informazioni sulle modifiche apportate dal nostro recente aggiornamento del connettore, vedere l'[aggiornamento più recente](https://azure.microsoft.com/en-us/updates/app-proxy-connector-12sept2016). La pagina viene rivista con ogni aggiornamento.

## <a name="under-the-hood"></a>Dietro le quinte

Offriamo numerosi strumenti comodi per l'utente in Azure. I connettori, in particolare, dispongono di numerose funzionalità utili. Poiché i connettori sono basati sul proxy applicazione Web di Windows Server, hanno la maggior parte degli stessi strumenti di gestione, tra cui il set completo di contatori delle prestazioni di Windows e i registri eventi di Windows, come illustrato di seguito nel Visualizzatore eventi:

 ![Visualizzatore eventi di Azure AD](./media/application-proxy-understand-connectors/event-view-window.png)

E Performance Monitor:

 ![Performance Monitor di Azure AD](./media/application-proxy-understand-connectors/performance-monitor.png)

I connettori hanno sia log di amministrazione che log di sessione. I log di amministrazione includono gli eventi principali e i relativi errori. I log di sessione includono tutte le transazioni e i relativi dettagli di elaborazione. 

Per visualizzarli è necessario attivare l'opzione "Visualizza registri analitici e di debug" nel menu "Visualizza" del Visualizzatore eventi. Quindi è necessario abilitarli per avviare la raccolta degli eventi. Questi log non appaiono nel proxy applicazione Web in Windows Server 2012 R2, in quanto i connettori sono basati su una versione più recente.

 ![Visualizzatore eventi di sessione di Azure AD](./media/application-proxy-understand-connectors/event-view-window-session.png)

È possibile esaminare lo stato del servizio nella finestra Servizi. Il connettore è costituito da due servizi Windows: uno è il connettore vero e proprio e l'altro esegue l'aggiornamento. Entrambi devono essere eseguiti ininterrottamente.

 ![Servizi Azure AD locali](./media/application-proxy-understand-connectors/aad-connector-services.png)

Per informazioni sulla risoluzione degli errori con il connettore del proxy applicazione, vedere [Risolvere i problemi del proxy applicazione](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

##<a name="next-steps"></a>Passaggi successivi
[Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md)<br>
[Come eseguire un'installazione invisibile all'utente del connettore del proxy applicazione Azure AD](active-directory-application-proxy-silent-installation.md)




<!--HONumber=Feb17_HO1-->


