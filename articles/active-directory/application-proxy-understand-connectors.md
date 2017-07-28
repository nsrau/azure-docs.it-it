---
title: Comprendere i connettori del proxy applicazione Azure AD | Microsoft Docs
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
ms.date: 06/02/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: cd02855717c7de00e8b12aaa6d7578a0ab6079bf
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Comprendere i connettori del proxy applicazione Azure AD

I connettori sono ciò che rende possibile il proxy di applicazione di Azure AD. Sono semplici, estremamente potenti e facile da distribuire e gestire. Questo articolo illustra i connettori, il loro funzionamento e alcune procedure consigliate per sfruttare al meglio la distribuzione. 

## <a name="deployment"></a>Distribuzione

Il proxy applicazione funziona dopo che è stato installato il servizio Windows Server, chiamato connettore, nella rete. È possibile installare più connettori in base alla esigenze di disponibilità elevata e scalabilità. Iniziare con uno e aggiungerne altri in base alle esigenze. Ogni volta che viene installato un connettore, questo viene aggiunto al pool di connettori che serve il tenant.

È consigliabile non installare i connettori negli stessi server che ospitano le applicazioni. È necessario tuttavia essere in grado di accedere all'applicazione dal server in cui si installa il connettore.


## <a name="maintenance"></a>Manutenzione
I connettori e il servizio si occupano di tutte le attività che richiedono disponibilità elevata. Possono essere aggiunti o rimossi in modo dinamico. Ogni volta che arriva una nuova richiesta, questa viene indirizzata a uno dei connettori attualmente disponibili. Se un connettore non è temporaneamente disponibile, non risponderà a questo traffico.

I connettori sono senza stato e non hanno dati di configurazione nel computer, a parte le impostazioni di connettività al servizio e il certificato che autentica il connettore. Quando si connettono al servizio, eseguono il pull di tutti i dati di configurazione necessari e li aggiornano ogni due minuti.
Eseguono inoltre il polling del server per verificare se è disponibile una versione più recente del connettore. Se ne viene individuata una, i connettori vengono aggiornati.

È possibile monitorare i connettori dal computer in cui vengono eseguiti usando il registro eventi e i contatori delle prestazioni o dalla pagina del proxy di applicazione del Portale di Azure.

 ![Connettori del proxy applicazione AzureAD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

Non è necessario eliminare manualmente i connettori che non vengono usati. Quando un connettore è in esecuzione, rimane attivo quando si connette al servizio. I connettori inutilizzati vengono contrassegnati come _inattivi_ e vengono rimossi dopo 10 giorni di inattività. 

## <a name="automatic-updates"></a>Aggiornamenti automatici

Azure AD supporta gli aggiornamenti automatici per tutti i connettori da distribuire. Fino a quando il servizio di aggiornamento del connettore proxy di applicazione è in esecuzione, i connettori vengono aggiornati automaticamente. Se non viene visualizzato il servizio di aggiornamento del connettore nel server, è necessario [reinstallare il connettore](active-directory-application-proxy-enable.md) per ottenere gli aggiornamenti. In caso di tenant con più connettori, gli aggiornamenti automatici vengono applicati a un connettore per volta in ogni gruppo, al fine di evitare tempo di inattività nell'ambiente in uso. 

Se non si vuole attendere l'aggiornamento automatico del connettore, è possibile eseguire un aggiornamento manuale. Passare alla [pagina di download del connettore](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) nel server in cui si trova il connettore e selezionare **Download**. In questo modo viene avviato un aggiornamento del connettore locale. 

Potrebbero verificarsi tempi di inattività quando viene aggiornato il connettore se:  
- Si ha un solo connettore. Per evitare questo periodo di inattività e migliorare la disponibilità elevata, è consigliabile installare un altro connettore e [creare un gruppo di connettori](active-directory-application-proxy-connectors-azure-portal.md).  
- Un connettore si trovava nel mezzo di una transazione quando è iniziato l'aggiornamento. Il browser dovrebbe ripetere automaticamente l'operazione. In caso contrario, è possibile aggiornare la pagina. Quando la richiesta viene inviata di nuovo, il traffico viene indirizzato a un connettore di backup.

## <a name="outbound-only-networking"></a>Rete solo in uscita
I connettori inviano solo richieste in uscita, pertanto la connessione viene sempre avviata dal connettore. Non è necessario aprire porte in ingresso perché il traffico scorre in entrambe le direzioni, dopo aver stabilito una sessione.

Il traffico in uscita viene inviato al servizio proxy applicazione e alle applicazioni pubblicate. Il traffico verso il servizio viene inviato ai data center di Azure su diversi numeri di porta. Per altre informazioni sulle porte usate, vedere [Enable Application Proxy in the Azure portal](active-directory-application-proxy-enable.md) (Abilitare il proxy di applicazione nel Portale di Azure).

Dato che il traffico è solo in uscita, non è necessario configurare il bilanciamento del carico tra i connettori o configurare l'accesso in ingresso attraverso i firewall.

Per informazioni sulla configurazione delle regole del firewall in uscita, vedere [Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md).

Usare lo [strumento per il test delle porte del connettore Proxy di applicazione Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) per verificare che il connettore possa raggiungere il servizio Proxy di applicazione. Assicurarsi almeno che l'area Stati Uniti centrali e l'area più vicina all'utente abbiano segni di spunta verdi. Tuttavia, la presenza di più segni di spunta verde indica una maggiore resilienza. 

## <a name="network-security"></a>Sicurezza di rete

I connettori possono essere installati in qualsiasi punto della rete che consenta loro di inviare richieste sia al servizio proxy dell'applicazione che alle applicazioni back-end. Funzionano correttamente se si installano all'interno di una rete aziendale, con rete perimetrale, o anche in una macchina virtuale in esecuzione nel cloud. È importante che il computer che esegue il connettore abbia anche accesso alle app.

Le distribuzioni su reti perimetrali sono più complesse. Uno dei motivi per i quali è consigliabile distribuire i connettori in una rete perimetrale consiste nell'usare un'altra infrastruttura, ad esempio i servizi di bilanciamento del carico dell'applicazione back-end o sistemi di rilevamento delle intrusioni.

## <a name="domain-joining"></a>Aggiunta al dominio

I connettori possono essere eseguiti in un computer che non fa parte del dominio. È necessario tuttavia un computer appartenente al dominio se si sceglie di implementare un accesso Single Sign-On (SSO) per le applicazioni che usano l'autenticazione integrata di Windows (IWA). In questo caso i computer di connessione devono appartenere a un dominio che può eseguire la delega vincolata [Kerberos](https://web.mit.edu/kerberos) per conto degli utenti rilevanti per le applicazioni pubblicate.

I connettori possono anche essere aggiunti a domini o foreste con attendibilità parziale o a controller di dominio di sola lettura.

## <a name="connectors-on-hardened-environments"></a>Connettori in ambienti protetti

Nella maggior parte dei casi la distribuzione dei connettori è molto semplice e non richiede una configurazione speciale. Esistono tuttavia alcune condizioni specifiche che devono essere considerate:

* Le organizzazioni che limitano il traffico in uscita devono [aprire le porte necessarie](active-directory-application-proxy-enable.md#open-your-ports).
* Per i computer conformi FIPS potrebbe essere necessario modificare la configurazione per consentire al servizio connettore, al servizio di aggiornamento del connettore e al programma di installazione di generare e archiviare un certificato.
* Le organizzazioni, che bloccano il proprio ambiente in base ai processi che inviano le richieste di rete, devono assicurarsi che siano abilitati entrambi i servizi connettore per accedere a tutte le porte e agli indirizzi IP necessari.
* In alcuni casi il proxy di inoltro in uscita può interrompere l'autenticazione bidirezionale con certificato e non consentire la comunicazione.

## <a name="connector-authentication"></a>Autenticazione del connettore

Per garantire un servizio sicuro, i connettori devono eseguire l'autenticazione verso il servizio e il servizio deve eseguire l'autenticazione verso il connettore. Questa operazione viene eseguita usando i certificati client e server quando i connettori avviano la connessione. In questo modo il nome utente e la password dell'amministratore non sono archiviati sul computer di connessione.

I certificati utilizzati sono specifici per il servizio proxy applicazione. Vengono creati durante la registrazione iniziale e rinnovati automaticamente dai connettori ogni due mesi. 

Se un connettore non viene connesso al servizio per molti mesi, i relativi certificati potrebbero non essere più aggiornati. In questo caso, disinstallare e reinstallare il connettore per attivare la registrazione. È possibile eseguire i seguenti comandi di PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Prestazioni e scalabilità

Anche se la scalabilità per il servizio online è trasparente, è comunque un fattore per i connettori. È necessario disporre di connettori sufficienti per gestire il traffico di picco. Poiché i connettori sono senza stato, non vengono influenzati dal numero di utenti o sessioni. Dipendono invece dal numero di richieste e dalle dimensioni del payload. In un traffico Web standard, un computer medio può gestire circa duemila richieste al secondo. La capacità specifica dipende dalle esatte caratteristiche del computer.

Le prestazioni del connettore sono legate alla CPU e alla rete. Le prestazioni della CPU sono necessarie per la crittografia SSL e la decrittografia, mentre la rete è fondamentale per una connettività veloce alle applicazioni e al servizio online in Azure. 

La memoria, al contrario, ha meno importanza per i connettori. Il servizio online si occupa di gran parte dell'elaborazione e di tutto il traffico non autenticato. Tutto ciò che può essere fatto nel cloud viene fatto nel cloud.

Un altro fattore che influenza le prestazioni è la qualità della connessione di rete tra i connettori, inclusi:

* **Servizio online:** le connessioni lente o a latenza elevata influenzano il servizio del connettore. È preferibile che l'organizzazione sia connessa ad Azure tramite Express Route. In caso contrario, assicurarsi che il team di rete garantisca una gestione efficiente delle connessioni ad Azure.  
* **Applicazioni back-end:** in alcuni casi ci sono altri proxy tra il connettore e le applicazioni back-end. Per risolvere questo problema, aprire un browser dal computer di connessione e accedere a queste applicazioni. Se si eseguono i connettori in Azure e le applicazioni sono locali, l'esperienza potrebbe essere diversa da quella prevista.
* **I controller di dominio:** se i connettori eseguono l'accesso SSO mediante la delega vincolata Kerberos (KCD), essi contattano i controller di dominio prima di inviare la richiesta al back-end. I connettori hanno una cache dei ticket Kerberos, ma in ambienti affollati la velocità di risposta dei controller di dominio può influenzare l'esperienza. Questa situazione è più comune per i connettori eseguiti in Azure, se i controller di dominio sono locali.

## <a name="under-the-hood"></a>Dietro le quinte

I connettori sono basati su proxy di applicazione Web di Windows Server, per cui condividono la maggior parte degli strumenti di gestione, inclusi i registri eventi di Windows

 ![Gestire i registri eventi con il Visualizzatore eventi](./media/application-proxy-understand-connectors/event-view-window.png)

e con i contatori delle prestazioni di Windows. 

 ![Aggiungere contatori al connettore con Performance Monitor](./media/application-proxy-understand-connectors/performance-monitor.png)

I connettori hanno sia log di amministrazione che log di sessione. I log di amministrazione includono gli eventi principali e i relativi errori. I log di sessione includono tutte le transazioni e i relativi dettagli di elaborazione. 

Per visualizzare i registri, passare al Visualizzatore eventi, aprire il menu **Visualizza** e abilitare **Visualizza registri analitici e di debug**. È necessario abilitarli per avviare la raccolta degli eventi. Questi log non appaiono nel proxy applicazione Web in Windows Server 2012 R2, in quanto i connettori sono basati su una versione più recente.

È possibile esaminare lo stato del servizio nella finestra Servizi. Il connettore è costituito da due servizi di Windows, ovvero il connettore stesso e il programma di aggiornamento. Entrambi devono essere eseguiti ininterrottamente.

 ![Servizi Azure AD locali](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passaggi successivi
[Risolvere i problemi di errore del proxy di applicazione e del connettore](active-directory-application-proxy-troubleshoot.md)

[Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md)

[Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](active-directory-application-proxy-silent-installation.md)


