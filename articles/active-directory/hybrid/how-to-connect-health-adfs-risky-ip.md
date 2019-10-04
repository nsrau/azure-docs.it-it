---
title: Azure AD Connect Health con report sugli indirizzi IP rischiosi AD FS | Microsoft Docs
description: Descrive i report sugli indirizzi IP a rischio di Azure AD Connect Health per AD FS.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b93cb7852692e4dad65fcbd72cd749db1b16fb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60350560"
---
# <a name="risky-ip-report-public-preview"></a>Report sugli indirizzi IP rischiosi (anteprima pubblica)
I clienti con AD FS possono esporre endpoint di autenticazione delle password in Internet per offrire servizi di autenticazione per l'accesso degli utenti finali ad applicazioni SaaS come Office 365. In questo caso, un attore malintenzionato potrebbe eseguire tentativi di accesso nel sistema AD FS per indovinare la password di un utente finale e ottenere l'accesso alle risorse dell'applicazione. A partire da AD FS in Windows Server 2012 R2, AD FS offre la funzionalità di blocco account Extranet per impedire questi tipi di attacchi. Se si usa una versione precedente, è consigliabile eseguire l'aggiornamento del sistema AD FS a Windows Server 2016. <br />

È anche possibile che un singolo indirizzo IP esegua più tentativi di accesso per più utenti. In questi casi, il numero di tentativi per utente potrebbe essere al di sotto della soglia della protezione del blocco account in AD FS. Azure AD Connect Health offre ora un report sugli indirizzi IP rischiosi che rileva questa condizione e invia una notifica agli amministratori quando si verifica. Di seguito sono riportati i vantaggi principali di questo report: 
- Rilevamento degli indirizzi IP che superano una soglia di accessi basati su password non riusciti
- Supporto per accessi non riusciti a causa di password errata o dello stato di blocco Extranet
- Notifica tramite posta elettronica per avvisare gli amministratori non appena questo si verifica, con impostazioni di posta elettronica personalizzabili
- Impostazioni di soglia personalizzabili corrispondenti ai criteri di sicurezza di un'organizzazione
- Report scaricabili per l'analisi offline e l'integrazione con altri sistemi tramite automazione

> [!NOTE]
> Per usare questo report, è necessario verificare che il servizio di controllo AD FS sia abilitato. Per altre informazioni, vedere [Abilitare il controllo per AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Per accedere all'anteprima, è necessaria un'autorizzazione di amministratore globale o di [Ruolo con autorizzazioni di lettura per la sicurezza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader).  
> 

## <a name="what-is-in-the-report"></a>Che cos'è il report?
L'accesso non riusciti agli indirizzi IP del client attività viene aggregato tramite Server Proxy applicazione Web. Ogni elemento nel report sugli indirizzi IP rischiosi mostra informazioni aggregate sulle attività di accesso ad AD FS non riuscite che superano la soglia designata. Fornisce le informazioni seguenti: ![Portale di Azure AD Connect Health](./media/how-to-connect-health-adfs/report4a.png)

| Elemento del report | Descrizione |
| ------- | ----------- |
| Timestamp | Mostra il timestamp corrispondente all'inizio dell'intervallo di tempo di rilevamento, in base all'ora locale del portale di Azure.<br /> Tutti gli eventi giornalieri vengono generati a mezzanotte nel fuso orario UTC. <br />Il timestamp degli eventi orari viene arrotondato all'inizio dell'ora. L'ora di inizio della prima attività è riportata in "firstAuditTimestamp" nel file esportato. |
| Tipo di trigger | Mostra il tipo di intervallo di tempo di rilevamento. I tipi di trigger di aggregazione sono su base oraria o giornaliera. Questo è utile per il rilevamento rispetto a un attacco di forza bruta con frequenza elevata e rispetto a un attacco lento in cui il numero di tentativi è distribuito durante il giorno. |
| Indirizzo IP | Il singolo indirizzo IP rischioso con attività di accesso con password errata o blocco Extranet. Potrebbe trattarsi di IPv4 o IPv6. |
| Numero errori di password errata | Il numero di errori di password errata provenienti dall'indirizzo IP durante l'intervallo di tempo di rilevamento. Gli errori di password errata possono verificarsi più volte per determinati utenti. Si noti che non sono inclusi i tentativi non riusciti a causa di password scadute. |
| Numero errori di blocco Extranet | Il numero di errori di blocco Extranet provenienti dall'indirizzo IP durante l'intervallo di tempo di rilevamento. Gli errori di blocco Extranet possono verificarsi più volte per determinati utenti. Questo verrà rilevato solo se il blocco Extranet è configurato in AD FS (versione 2012R2 o successiva). <b>Nota</b>: se si consentono accessi Extranet con password, è consigliabile attivare questa funzionalità. |
| Tentativi con utenti univoci | Il numero di tentativi riguardanti utenti univoci provenienti dall'indirizzo IP durante l'intervallo di tempo di rilevamento. Questo offre un meccanismo per distinguere uno schema di attacco a utente singolo da uno schema di attacco multiutente.  |

L'elemento del report seguente, ad esempio, indica che nell'intervallo orario dalle 18 alle 19 del 28 febbraio 2018 l'indirizzo IP <i>104.2XX.2XX.9</i> ha generato zero errori di password errata e 284 errori di blocco Extranet. In base ai criteri sono stati coinvolti 14 utenti univoci. L'evento dell'attività ha superato la soglia oraria designata del report. 

![portale di Azure AD Connect Health](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Nell'elenco del report verranno visualizzate solo le attività che superano la soglia designata. 
> - Questo report può risalire indietro al massimo di 30 giorni.
> - Questo report di avviso non mostra gli indirizzi IP di Exchange o quelli privati, che sono però inclusi nell'elenco esportato. 
>

![portale di Azure AD Connect Health](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Servizio di bilanciamento indirizzi IP nell'elenco del carico
Attività di accesso aggregate non riuscite del servizio di bilanciamento del carico, con raggiungimento della soglia di avviso. Se vengono visualizzati indirizzi IP del servizio di bilanciamento del carico, è molto probabile che il servizio di bilanciamento del carico esterno non invii l'indirizzo IP del client quando passa la richiesta al server proxy applicazione Web. Configurare il servizio di bilanciamento del carico correttamente in modo da passare l'indirizzo IP del client di inoltro. 

## <a name="download-risky-ip-report"></a>Scaricare report sugli indirizzi IP rischiosi 
Usando la funzionalità **Scarica** è possibile esportare l'intero elenco degli indirizzi IP rischiosi degli ultimi 30 giorni dal portale di Connect Health. Il risultato dell'esportazione includerà tutte le attività di accesso ad AD FS non riuscite in ogni intervallo di tempo di rilevamento, consentendo così di personalizzare i filtri dopo l'esportazione. Oltre alle aggregazioni evidenziate nel portale, il risultato dell'esportazione mostra anche altri dettagli sulle attività di accesso non riuscite in base all'indirizzo IP:

|  Elemento del report  |  Descrizione  | 
| ------- | ----------- | 
| firstAuditTimestamp | Mostra il primo timestamp corrispondente all'inizio delle attività non riuscite durante l'intervallo di tempo di rilevamento.  | 
| lastAuditTimestamp | Mostra l'ultimo timestamp corrispondente alla fine delle attività non riuscite durante l'intervallo di tempo di rilevamento.  | 
| attemptCountThresholdIsExceeded | Flag che indica se le attività correnti stanno superando la soglia di avviso.  | 
| isWhitelistedIpAddress | Flag che indica se l'indirizzo IP è escluso da avvisi e report. Gli indirizzi IP privati (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) e quelli di Exchange vengono filtrati e contrassegnati come True. Se vengono visualizzati intervalli di indirizzi IP privati, è molto probabile che il servizio di bilanciamento del carico esterno non invii l'indirizzo IP del client quando passa la richiesta al server proxy applicazione Web.  | 

## <a name="configure-notification-settings"></a>Configurare le impostazioni di notifica
I contatti degli amministratori del report possono essere aggiornati tramite **Impostazioni di notifica**. Per impostazione predefinita, la notifica di avviso tramite posta elettronica relativa agli indirizzi IP rischiosi è disattivata. È possibile abilitare la notifica facendo clic sul pulsante sotto "Ricevi notifiche tramite posta elettronica per gli indirizzi IP che superano la soglia di attività non riuscite nel report". Come per le impostazioni delle notifiche di avviso generiche di Connect Health, da qui è possibile personalizzare l'elenco dei destinatari designati della notifica per il report sugli indirizzi IP rischiosi. Si può anche inviare una notifica a tutti gli amministratori globali mentre si apporta la modifica. 

## <a name="configure-threshold-settings"></a>Configurare le impostazioni di soglia
La soglia di avviso può essere aggiornata tramite le impostazioni di soglia. Per iniziare, il sistema prevede un'impostazione predefinita della soglia. Le impostazioni di soglia per il report sugli indirizzi IP rischiosi includono quattro categorie:

![portale di Azure AD Connect Health](./media/how-to-connect-health-adfs/report4d.png)

| Elemento soglia | Descrizione |
| --- | --- |
| Nome utente/password non validi e blocco Extranet - Giorno  | Impostazione di soglia per segnalare l'attività e attivare la notifica di avviso quando il numero di errori di password errata e il numero di errori di blocco Extranet superano tale valore in un **giorno**. |
| Nome utente/password non validi e blocco Extranet - Ora | Impostazione di soglia per segnalare l'attività e attivare la notifica di avviso quando il numero di errori di password errata e il numero di errori di blocco Extranet superano tale valore in un'**ora**. |
| Blocco Extranet - Giorno | Impostazione di soglia per segnalare l'attività e attivare la notifica di avviso quando il numero di errori di blocco Extranet supera tale valore in un **giorno**. |
| Blocco Extranet - Ora| Impostazione di soglia per segnalare l'attività e attivare la notifica di avviso quando il numero di errori di blocco Extranet supera tale valore in un'**ora**. |

> [!NOTE]
> - La modifica della soglia per il report verrà applicata dopo un'ora dall'impostazione. 
> - La modifica della soglia non influirà sugli elementi segnalati esistenti. 
> - È consigliabile analizzare il numero di eventi rilevati nell'ambiente e modificare la soglia in modo appropriato. 
>
>

## <a name="faq"></a>Domande frequenti
**Il motivo per cui vengono visualizzati intervalli di indirizzi IP privati nel report?**  <br />
Gli indirizzi IP privati (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) e quelli di Exchange vengono filtrati e contrassegnati come True nell'elenco di IP consentiti. Se vengono visualizzati intervalli di indirizzi IP privati, è molto probabile che il servizio di bilanciamento del carico esterno non invii l'indirizzo IP del client quando passa la richiesta al server proxy applicazione Web.

**Il motivo per cui vengono visualizzati indirizzi IP del servizio di bilanciamento nel report di carico?**  <br />
Se vengono visualizzati indirizzi IP del servizio di bilanciamento del carico, è molto probabile che il servizio di bilanciamento del carico esterno non invii l'indirizzo IP del client quando passa la richiesta al server proxy applicazione Web. Configurare il servizio di bilanciamento del carico correttamente in modo da passare l'indirizzo IP del client di inoltro. 

**Cosa fare in modo da bloccare l'indirizzo IP?**  <br />
È consigliabile aggiungere l'indirizzo IP dannoso identificato al firewall o al blocco in Exchange.   <br />

**Il motivo per cui non vengono visualizzati tutti gli elementi in questo report?** <br />
- Le attività di accesso non riuscite non superano le impostazioni di soglia.
- Verificare che nell'elenco di server AD FS non sia attivo alcun avviso "Health service is not up to date" (Servizio integrità non aggiornato).  Vedere altre informazioni su [come risolvere i problemi relativi a questo avviso](how-to-connect-health-data-freshness.md).
- Il servizio di controllo non è abilitato nelle farm AD FS.

**Perché viene non visualizzato alcun accesso al report?**  <br />
È necessaria l'autorizzazione di amministratore globale o di [Ruolo con autorizzazioni di lettura per la sicurezza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader). Contattare l'amministratore globale per ottenere l'accesso.


## <a name="next-steps"></a>Passaggi successivi
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installazione dell'agente di Azure AD Connect Health](how-to-connect-health-agent-install.md)
