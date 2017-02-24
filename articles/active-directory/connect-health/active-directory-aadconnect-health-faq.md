---
title: Domande frequenti su Azure Active Directory Connect Healtht | Microsoft Docs
description: "Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull&quot;uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7c320a043322fefea1f58301492d4c5a0567320c
ms.openlocfilehash: aff2ced283de8282860d0aa356b12478f647d09c

---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Domande frequenti su Azure AD Connect Health
Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## <a name="general-questions"></a>Domande generali
**D: Quando si gestiscono più directory di Azure AD, come passo da una directory all'altra nel Portale di Azure?**

È possibile spostarsi tra tenant di Azure AD diversi selezionando il nome dell'utente connesso nell'angolo in alto a destra e scegliendo l'account appropriato. Se l'account non è elencato qui, selezionare Sign out (Esci) e quindi usare le credenziali appropriate della Directory per accedere.

**D: Quali versioni dei ruoli di identità sono supportate da Azure AD Connect Health?**

Nella tabella seguente sono elencati i ruoli e le versioni del sistema operativo supportate.

|Ruolo| Versione/Sistema operativo|
|--|--|
|AD FS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 o superiore.|
|Servizi di dominio Active Directory| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Si noti che le funzionalità offerte dal servizio possono variare in base al ruolo e al sistema operativo. In altre parole, è possibile che non tutte le funzionalità siano disponibili per tutte le versioni del sistema operativo. Vedere le descrizioni delle funzionalità per i dettagli.

**D: Quante licenze è necessario avere per monitorare l'infrastruttura?**

* Per il primo agente di Connect Health è necessaria almeno una licenza Premium di Azure AD.
* Ogni agente registrato successivamente richiede altre 25 licenze AADP. 
* Il numero di agenti è uguale al numero totale di agenti registrati in tutti i ruoli monitorati (AD FS, Azure AD Connect e/o Servizi di dominio Active Directory.)

È inoltre possibile trovare le informazioni sulle licenze sulla [pagina dei prezzi di Azure AD](https://aka.ms/aadpricing).

Esempio:

| Agenti registrati | Licenze necessarie | Esempio di configurazione di monitoraggio |
| ------ | --------------- | --- |
| 1 | 1 | 1 Server di connessione AAD |
| 2 | 26| 1 Server di connessione AAD Connect Server e 1 controller di dominio |
| 3 | 51 | 1 Server AD FS, 1 proxy AD FS e 1 controller di dominio |
| 4 | 76 | 1 Server AD FS, 1 proxy AD FS e 2 controller di dominio |
| 5 | 101 | 1 Server di connessione AAD, 1 server AD FS, 1 proxy AD FS e 2 controller di dominio |
## <a name="installation-questions"></a>Domande sull'installazione
**D: Qual è l'impatto dell'installazione dell'agente di Azure AD Connect Health in singoli server?**

L'impatto dell'installazione degli agenti di Microsoft Azure AD Connect Health AD FS, dei server proxy applicazione Web, dei server Azure AD Connect (sincronizzazione) e dei controller di dominio è minimo per quanto riguarda CPU, utilizzo della memoria, larghezza di banda della rete e archiviazione.

I numeri indicati di seguito sono approssimativi.

    * Utilizzo della CPU: ~1-5% di aumento
    * Utilizzo della memoria: fino a al 10% della memoria di sistema totale

> [!NOTE]
> Se l'agente per l'integrità perde la connettività al servizio Connect Health, i dati vengono archiviati localmente, fino a un limite massimo definito. L'agente sovrascrive i dati "memorizzati nella cache" secondo un criterio di tipo "intervento di manutenzione meno recente".
>
>

* Archiviazione buffer locale per gli agenti di Azure AD Connect Health: circa&20; MB
* Per i server AD FS è consigliabile eseguire il provisioning di 1024 MB (1 GB) di spazio su disco per il canale di controllo di AD FS per consentire agli agenti di Azure AD Connect Health di elaborare tutti i dati di controllo prima che vengano sovrascritti.

**D: È necessario riavviare i server durante l'installazione degli agenti di Azure AD Connect Health?**

No. Per l'installazione degli agenti non è necessario il riavvio del server.

L'installazione di alcuni passaggi preliminari necessari può richiedere un riavvio del server. Ad esempio, in Windows Server 2008 R2 l'installazione di .Net 4.5 Framework richiede un riavvio del server.

**Q: Il servizio Azure AD Connect Health funziona tramite un proxy HTTP pass-through?**

Sì.  Per le operazioni in corso è possibile configurare l'agente di Health per l'inoltro delle richieste HTTP in uscita tramite un proxy HTTP. Altre informazioni sulla [configurazione del proxy HTTP per gli agenti di integrità](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)

Se è necessario configurare un proxy durante la registrazione dell'agente, modificare prima le impostazioni del proxy di Internet Explorer.

1. Aprire Internet Explorer -> Impostazioni -> Opzioni Internet -> Connessioni -> Impostazioni LAN.
2. Selezionare Usa un server di proxy per la rete LAN.
3. Selezionare Avanzate se sono presenti porte proxy diverse per HTTP e HTTPS/Protetto.

**D: I servizi di Azure AD Connect Health supportano l'autenticazione di base durante la connessione a proxy HTTP?**

No. Non è attualmente supportato alcun meccanismo per specificare in modo arbitrario un nome utente o una password per l'autenticazione di base.

**D: Quali porte del firewall è necessario aprire per garantire il funzionamento dell'agente di Azure AD Connect Health?**

Consultare la [sezione sui requisiti](active-directory-aadconnect-health-agent-install.md#requirements) per l'elenco delle porte del firewall e altri requisiti di connettività.


**D: Perché vengono visualizzati due server con lo stesso nome nel portale di Azure AD Connect Health?**

Quando si rimuove un agente da un server, tale server non viene automaticamente rimosso dal portale di Azure AD Connect.  Se è stato rimosso manualmente un agente da un server o se è stato rimosso il server stesso, è necessario eliminare manualmente la voce relativa al server dal portale di Azure AD Connect Health.

Se è stata creata una nuova immagine di un server o se è stato creato un nuovo server con gli stessi dettagli, ad esempio il nome del computer, e il server già registrato non è stato rimosso dal portale di Azure AD Connect Health, una volta installato l'agente nel nuovo server è possibile che vengano visualizzate due voci con lo stesso nome.  
In questo caso è consigliabile eliminare manualmente la voce appartenente al server precedente. I dati per questo server non dovrebbero essere aggiornati.

## <a name="health-agent-registration-and-data-freshness"></a>Registrazione dell'agente per l'integrità e aggiornamento dati

**D: Quali sono le cause più comuni che generano errori nella registrazione dell'agente per l'integrità e come risolverle?**

Le possibili cause per cui un agente per l'integrità non riesce a eseguire la registrazione sono elencate di seguito
    * L'agente non è in grado di comunicare con gli endpoint necessari a causa del blocco del traffico da parte del firewall. Questa situazione è molto comune nei server proxy delle applicazioni Web. Assicurarsi di aver abilitato la comunicazione in uscita per le porte e gli endpoint obbligatori. Per informazioni dettagliate, vedere la sezione [Requisiti](active-directory-aadconnect-health-agent-install.md#requirements).
    * La comunicazione in uscita viene sottoposta a un'ispezione SSL dal livello di rete. In questo modo il certificato usato dall'agente viene sostituito dall'entità o dal server per l'ispezione e non è possibile eseguire i passaggi necessari per completare la registrazione dell'agente.
    * L'utente non dispone dell'accesso per eseguire la registrazione dell'agente. Per impostazione predefinita, agli amministratori globali l'accesso è consentito. È possibile usare il [Controllo degli accessi in base al ruolo](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) per delegare l'accesso ad altri utenti.

**D: Ricevo degli avvisi che mi dicono che "I dati del Servizio integrità non sono aggiornati". Come risolvere questo problema?**

Questo avviso viene generato dal Servizio integrità quando non riceve tutti i punti dati dal server nelle ultime 2 ore. I motivi per cui questo avviso viene generato possono essere diversi.
    * L'agente non è in grado di comunicare con gli endpoint necessari a causa del blocco del traffico da parte del firewall. Questa situazione è molto comune nei server proxy delle applicazioni Web. Assicurarsi di aver abilitato la comunicazione in uscita per le porte e gli endpoint obbligatori. Per informazioni dettagliate, vedere la sezione [Requisiti](active-directory-aadconnect-health-agent-install.md#requirements).
    * La comunicazione in uscita viene sottoposta a un'ispezione SSL dal livello di rete. In questo modo il certificato usato dall'agente viene sostituito dall'entità o dal server per l'ispezione e non è possibile caricare i dati nel servizio Connect Health.
    * È possibile usare il comando di connettività predefinito nell'agente. [Altre informazioni](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
    * Gli agenti supportano anche la connettività in uscita tramite un proxy HTTP non autenticato. [Altre informazioni](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).


## <a name="operations-questions"></a>Domande sulle operazioni
**D: è necessario abilitare il controllo nei server proxy delle applicazioni Web?**

No, il controllo non deve essere abilitato nei server proxy applicazione Web.

**D: In che modo vengono risolti gli avvisi di Azure AD Connect Health?**

Gli avvisi di Azure AD Connect Health vengono risolti se si verifica una condizione di esito positivo. Gli agenti di Azure AD Connect Health rilevano e segnalano periodicamente al servizio le condizioni di esito positivo. Per alcuni avvisi, l'eliminazione è basata sul tempo. In altri termini, se entro 72 ore dalla generazione dell'avviso non viene osservata la stessa condizione di errore, l'avviso viene automaticamente risolto.


## <a name="migration-questions"></a>Domande sulla migrazione

Questa sezione riguarda solo i clienti che hanno ricevuto una notifica su una prossima migrazione dei dati del servizio Azure AD Connect Health.

**D: Sarà necessario registrare di nuovo gli agenti o riconfigurare le impostazioni di notifica, dopo la migrazione?**

No, le informazioni di registrazione degli agenti e le impostazioni di notifica verranno spostate durante la migrazione.

**D: Quanto tempo dopo la migrazione, i dati saranno visibili nel portale?**

I dati saranno disponibili nel portale entro un'ora dalla migrazione.

**D: Che cosa accade agli avvisi attivi esistenti?**

Gli avvisi applicabili verranno riattivati entro un'ora dalla migrazione.


## <a name="related-links"></a>Collegamenti correlati
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Feb17_HO2-->


