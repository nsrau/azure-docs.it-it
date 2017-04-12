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
ms.date: 04/04/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e22a1ccb958942cfa3c67194430af6bc74fdba64
ms.openlocfilehash: 233691d19aa2553744f92af17f7ecf9fda2290e0
ms.lasthandoff: 04/05/2017

---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Domande frequenti su Azure AD Connect Health
Questo articolo include risposte alle domande frequenti su Azure Active Directory (Azure AD) Connect Health. Le domande sono relative all'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## <a name="general-questions"></a>Domande generali
**D: Quando si gestiscono più directory di Azure AD, come è possibile passare a quella con Azure Active Directory Premium?**

Per spostarsi tra tenant di Azure AD diversi, selezionare il **Nome utente** attualmente connesso nell'angolo in alto a destra e scegliere l'account appropriato. Se l'account non è incluso nell'elenco, selezionare **Disconnetti** e quindi usare le credenziali di amministratore globale della directory con Azure Active Directory Premium abilitato per l'accesso.

**D: Quali versioni dei ruoli di identità sono supportate da Azure AD Connect Health?**

La tabella seguente elenca i ruoli e le versioni del sistema operativo supportate.

|Ruolo| Sistema operativo/Versione|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versione 1.0.9125 o successiva|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Si noti che le funzionalità offerte dal servizio possono variare in base al ruolo e al sistema operativo. In altre parole, è possibile che non tutte le funzionalità siano disponibili per tutte le versioni del sistema operativo. Vedere le descrizioni delle funzionalità per i dettagli.

**D: Quante licenze è necessario avere per monitorare l'infrastruttura?**

* Per il primo agente di Connect Health è necessaria almeno una licenza Premium di Azure AD.
* Ogni agente registrato successivamente richiede altre 25 licenze Azure AD Premium.
* Il numero di agenti è uguale al numero totale di agenti registrati in tutti i ruoli monitorati (AD FS, Azure AD Connect e/o AD DS).

È inoltre possibile trovare le informazioni sulle licenze sulla [pagina dei prezzi di Azure AD](https://aka.ms/aadpricing).

Esempio:

| Agenti registrati | Licenze necessarie | Esempio di configurazione di monitoraggio |
| ------ | --------------- | --- |
| 1 | 1 | 1 server di Azure AD Connect |
| 2 | 26| 1 server di Azure AD Connect e 1 controller di dominio |
| 3 | 51 | 1 server di Active Directory Federation Services (AD FS), 1 proxy di AD FS e 1 controller di dominio |
| 4 | 76 | 1 server di AD FS, 1 proxy di AD FS e 2 controller di dominio |
| 5 | 101 | 1 server di Azure AD Connect, 1 server di AD FS, 1 proxy di AD FS e 2 controller di dominio |


## <a name="installation-questions"></a>Domande sull'installazione

**D: Qual è l'impatto dell'installazione dell'agente di Azure AD Connect Health in singoli server?**

L'impatto dell'installazione dell'agente di Microsoft Azure AD Connect Health, di AD FS, dei server proxy applicazione Web, dei server di Azure AD Connect (sincronizzazione) e dei controller di dominio è minimo per quanto riguarda CPU, utilizzo della memoria, larghezza di banda della rete e archiviazione.

I numeri seguenti sono approssimativi:

* Utilizzo della CPU: ~1-5% di incremento.
* Utilizzo della memoria: fino a al 10% della memoria di sistema totale.

> [!NOTE]
> Se l'agente non può comunicare con Azure, archivia i dati localmente per un limite massimo definito. L'agente sovrascrive i dati "memorizzati nella cache" secondo un criterio di tipo "intervento di manutenzione meno recente".
>
>

* Archiviazione buffer locale per gli agenti di Azure AD Connect Health: ~20 MB.
* Per i server di AD FS, è consigliabile effettuare il provisioning di 1.024 MB (1 GB) di spazio su disco per il canale di controllo di AD FS per consentire agli agenti di Azure AD Connect Health di elaborare tutti i dati di controllo prima che vengano sovrascritti.

**D: È necessario riavviare i server durante l'installazione degli agenti di Azure AD Connect Health?**

No. Per l'installazione degli agenti non è necessario il riavvio del server. L'installazione di alcuni passaggi preliminare può richiedere tuttavia un riavvio del server.

In Windows Server 2008 R2, ad esempio, l'installazione di .NET 4.5 Framework richiede un riavvio del server.

**D: Il servizio Azure AD Connect Health usa un proxy HTTP pass-through?**

Sì. Per le operazioni in corso, è possibile configurare l'agente per l'integrità in modo che usi un proxy HTTP per inoltrare le richieste HTTP in uscita.
Per altre informazioni, vedere la [configurazione del proxy HTTP per gli agenti per l'integrità](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Se è necessario configurare un proxy durante la registrazione dell'agente, modificare prima di tutto le impostazioni del proxy di Internet Explorer.

1. Aprire Internet Explorer > **Impostazioni** > **Opzioni Internet** > **Connessioni** > **Impostazioni LAN**.
2. Selezionare **Usa un server di proxy per la rete LAN**.
3. Selezionare **Avanzate** se sono presenti porte proxy diverse per HTTP e HTTPS/Protetto.

**D: Il servizio Azure AD Connect Health supporta l'autenticazione di base per la connessione ai proxy HTTP?**

No. Non è attualmente supportato alcun meccanismo per specificare in modo arbitrario un nome utente o una password per l'autenticazione di base.

**D: Quali porte del firewall è necessario aprire per garantire il funzionamento dell'agente di Azure AD Connect Health?**

Vedere la [sezione sui requisiti](active-directory-aadconnect-health-agent-install.md#requirements) per l'elenco delle porte del firewall e altri requisiti di connettività.

**D: Perché vengono visualizzati due server con lo stesso nome nel portale di Azure AD Connect Health?**

Quando si rimuove un agente da un server, tale server non viene automaticamente rimosso dal portale di Azure AD Connect Health. Se si rimuove manualmente un agente da un server o se si rimuove il server stesso, è necessario eliminare manualmente la voce relativa al server dal portale di Azure AD Connect Health.

È possibile ricreare l'immagine di un server o creare un nuovo server con gli stessi dettagli, ad esempio il nome del computer. Se il server già registrato non è stato rimosso dal portale di Azure AD Connect Health e l'agente è stato installato nel nuovo server, è possibile che vengano visualizzate due voci con lo stesso nome.

In questo caso, eliminare manualmente la voce appartenente al server meno recente. I dati per questo server non dovrebbero essere aggiornati.

## <a name="health-agent-registration-and-data-freshness"></a>Registrazione dell'agente per l'integrità e aggiornamento dati

**D: Quali sono le cause più comuni che generano errori nella registrazione dell'agente per l'integrità e come risolverle?**

Le possibili cause per cui un agente per l'integrità non riesce a eseguire la registrazione sono elencate di seguito:

* L'agente non può comunicare con gli endpoint necessari perché un firewall blocca il traffico. Questa situazione è molto comune nei server proxy applicazione Web. Assicurarsi di aver abilitato la comunicazione in uscita per le porte e gli endpoint obbligatori. Per informazioni dettagliate, vedere la sezione [Requisiti](active-directory-aadconnect-health-agent-install.md#requirements).
* La comunicazione in uscita viene sottoposta a un'ispezione SSL dal livello di rete. In questo modo il certificato usato dall'agente viene sostituito dall'entità o dal server per l'ispezione e non è possibile eseguire i passaggi necessari per completare la registrazione dell'agente.
* L'utente non dispone dell'accesso per eseguire la registrazione dell'agente. Per impostazione predefinita, agli amministratori globali l'accesso è consentito. È possibile usare il [Controllo degli accessi in base al ruolo](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) per delegare l'accesso ad altri utenti.

**D: Si ricevono avvisi che indicano che "I dati del Servizio integrità non sono aggiornati". Come si risolve il problema?**

Azure AD Connect Health genera l'avviso quando non riceve tutti i punti dati dal server nelle ultime due ore. I motivi per cui questo avviso viene generato possono essere diversi.

* L'agente non può comunicare con gli endpoint necessari perché un firewall blocca il traffico. Questa situazione è molto comune nei server proxy applicazione Web. Assicurarsi di aver abilitato la comunicazione in uscita per le porte e gli endpoint obbligatori. Per informazioni dettagliate, vedere la sezione [Requisiti](active-directory-aadconnect-health-agent-install.md#requirements).
* La comunicazione in uscita viene sottoposta a un'ispezione SSL dal livello di rete. In questo modo il certificato usato dall'agente viene sostituito dall'entità o dal server per l'ispezione e il processo non riesce a caricare i dati nel servizio Azure AD Connect Health.
* È possibile usare il comando di connettività predefinito nell'agente. [Altre informazioni](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
* Gli agenti supportano anche la connettività in uscita tramite un proxy HTTP non autenticato. [Altre informazioni](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).

## <a name="operations-questions"></a>Domande sulle operazioni
**D: è necessario abilitare il controllo nei server proxy applicazione Web?**

No, il controllo non deve essere abilitato nei server proxy applicazione Web.

**D: In che modo vengono risolti gli avvisi di Azure AD Connect Health?**

Gli avvisi di Azure AD Connect Health vengono risolti se si verifica una condizione di esito positivo. Gli agenti di Azure AD Connect Health rilevano e segnalano periodicamente al servizio le condizioni di esito positivo. Per alcuni avvisi, l'eliminazione è basata sul tempo. In altri termini, se entro 72 ore dalla generazione dell'avviso non viene osservata la stessa condizione di errore, l'avviso viene automaticamente risolto.

## <a name="related-links"></a>Collegamenti correlati
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

