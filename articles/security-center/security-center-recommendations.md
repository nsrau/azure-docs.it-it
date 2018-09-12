---
title: Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2018
ms.author: rkarlin
ms.openlocfilehash: 6aba94627d0676917664dc654507dce286a4e685
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304561"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure
Questo documento illustra come usare le raccomandazioni presenti nel Centro sicurezza di Azure per la protezione delle risorse di Azure.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>
>

## <a name="what-are-security-recommendations"></a>Informazioni sulle raccomandazioni di sicurezza
Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni. Queste raccomandazioni illustrano in dettaglio il processo di configurazione dei controlli necessari.

## <a name="implementing-security-recommendations"></a>Implementare le raccomandazioni di sicurezza
### <a name="set-recommendations"></a>Impostare le raccomandazioni
In [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md)si apprende come:

* Configurare i criteri di sicurezza.
* Attivare la raccolta dei dati.
* Scegliere le raccomandazioni da visualizzare nell'ambito dei criteri di sicurezza.

Le raccomandazioni relative ai criteri di sicurezza si basano attualmente su aggiornamenti di sistema, regole di base, programmi antimalware, [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per subnet e interfacce di rete, controllo del database SQL, Transparent Data Encryption per il database SQL e web application firewall.  [Impostazione dei criteri di sicurezza](security-center-policies.md) fornisce una descrizione di ogni opzione relativa alle raccomandazioni.

### <a name="monitor-recommendations"></a>Monitorare le raccomandazioni
Una volta impostato un criterio di sicurezza, il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Il riquadro **Raccomandazioni** in **Panoramica** consente di conoscere il numero totale di raccomandazioni identificate dal Centro sicurezza.

![Riquadro Raccomandazioni][1]

Per visualizzare i dettagli di ogni raccomandazione, selezionare il **riquadro Raccomandazioni** in **Overview**. Si apre **Raccomandazioni**.

![Filtrare le raccomandazioni][2]

È possibile filtrare le raccomandazioni. Per filtrare le raccomandazioni, selezionare **Filtro** nel pannello **Raccomandazioni**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a gravità e stato da visualizzare.

Le raccomandazioni vengono visualizzate sotto forma di tabella, in cui ogni riga rappresenta una particolare raccomandazione. Le colonne della tabella sono:

* **DESCRIZIONE**: spiega la raccomandazione e cosa occorre fare per metterla in pratica.
* **RISORSA**: elenca le risorse a cui si applica questa raccomandazione.
* **STATO**: descrive lo stato attuale della raccomandazione:
  * **Aperta**: la raccomandazione non è ancora stata applicata.
  * **In corso**: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
  * **Risolta**: la raccomandazione è già stata completata. In questo caso, la riga viene visualizzata in grigio.
* **GRAVITÀ**: descrive il livello di gravità della raccomandazione:
  * **Alta**: una vulnerabilità associata a una risorsa significativa, ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete, richiede attenzione.
  * **Media**: per eliminare una vulnerabilità o per completare un processo sono necessari passaggi aggiuntivi o non critici.
  * **Bassa**: una vulnerabilità che è opportuno risolvere, non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente.

Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili e gli effetti che producono se si decide di metterle in pratica.

> [!NOTE]
> È necessario conoscere i [modelli di distribuzione di Resource Manager e classici](../azure-classic-rm.md) per le risorse di Azure.
>
>

| Raccomandazione | DESCRIZIONE |
| --- | --- |
| [Abilita la raccolta di dati per le sottoscrizioni](security-center-enable-data-collection.md) |Consiglia di attivare la raccolta dati nei criteri di sicurezza per ogni sottoscrizione e per tutte le macchine virtuali di Azure e i computer non Azure. |
| [Correggere le configurazioni di sicurezza](security-center-remediate-os-vulnerabilities.md) |Consiglia di allineare le configurazioni dei sistemi operativi alle regole di configurazione della sicurezza raccomandate, ad esempio impedendo il salvataggio delle password. |
| [Applicare gli aggiornamenti di sistema](security-center-apply-system-updates.md) |Consiglia di distribuire gli aggiornamenti critici e della sicurezza di sistema mancanti nelle macchine virtuali e nei computer Windows e Linux. |
| [Applicare un controllo di accesso alla rete JIT](security-center-just-in-time.md) | Consiglia di applicare l'accesso Just-In-Time alla macchina virtuale. La funzionalità Just-In-Time è disponibile nel livello Standard del Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md). |
| [Riavvia dopo gli aggiornamenti del sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Consiglia di riavviare una macchina virtuale per completare il processo di applicazione degli aggiornamenti del sistema. |
| [Aggiungere un Web Application Firewall](security-center-add-web-application-firewall.md) |Suggerisce di distribuire un Web application firewall (WAF) per gli endpoint Web. Viene visualizzata una raccomandazione WAF per qualsiasi IP pubblico (IP a livello di istanza o IP con carico bilanciato) cui è associato un gruppo di sicurezza di rete con porte Web in ingresso aperte (80, 443). </br>Centro sicurezza consiglia di effettuare il provisioning di un WAF per consentire la protezione da attacchi contro le applicazioni Web nelle macchine virtuali e nell'Ambiente del servizio app. Un Ambiente del servizio app è un'opzione del piano di servizio [Premium](https://azure.microsoft.com/pricing/details/app-service/) del Servizio app di Azure che offre un ambiente completamente isolato e dedicato per eseguire in modo sicuro tutte le app del servizio. Per altre informazioni sull'ambiente del servizio app, vedere [Documentazione relativa agli ambienti del servizio app](../app-service/environment/intro.md).</br>Per proteggere più applicazioni Web in Centro sicurezza, è possibile aggiungerle alle distribuzioni WAF esistenti. |
| [Finalizza la protezione dell'applicazione](security-center-add-web-application-firewall.md#finalize-application-protection) |Per completare la configurazione di un Web Application Firewall, il traffico deve essere reindirizzato al dispositivo WAF. Seguendo questa raccomandazione si completeranno le modifiche di configurazione necessarie. |
| [Aggiungi un firewall di nuova generazione](security-center-add-next-generation-firewall.md) |Il Centro sicurezza di Azure consiglia di aggiungere un firewall di nuova generazione di un partner Microsoft per aumentare i meccanismi di protezione per la sicurezza. |
| [Route traffic through NGFW only (Indirizza il traffico solo tramite il firewall di nuova generazione) (Indirizza il traffico solo tramite il firewall di nuova generazione)](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Consiglia di configurare le regole del gruppo di sicurezza di rete che forzano il traffico in ingresso alla VM tramite il firewall di nuova generazione. |
| [Installa Endpoint Protection](security-center-install-endpoint-protection.md) |Suggerisce di effettuare il provisioning dei programmi antimalware nelle macchine virtuali (solo VM Windows). |
| [Abilita i gruppi di sicurezza di rete sulle subnet o sulle macchine virtuali](security-center-enable-network-security-groups.md) |Consiglia di attivare i gruppo di sicurezza di rete sulle subnet o sulle macchine virtuali. |
| [Limita l'accesso tramite un endpoint con connessione Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Consiglia di configurare le regole del traffico in ingresso per i gruppi di sicurezza di rete. |
| [Abilitare il controllo e il rilevamento delle minacce nei server SQL](security-center-enable-auditing-on-sql-servers.md) |Consiglia di attivare il controllo e il rilevamento delle minacce per i server di Azure SQL. (Solo servizio Azure SQL. Non include istanze di SQL in esecuzione nelle macchine virtuali.) |
| [Abilitare il controllo e il rilevamento delle minacce nei database SQL](security-center-enable-auditing-on-sql-databases.md) |Consiglia di attivare il controllo e il rilevamento delle minacce per i database SQL di Azure. (Solo servizio Azure SQL. Non include istanze di SQL in esecuzione nelle macchine virtuali.) |
| [Abilitare Transparent Data Encryption sui database SQL](security-center-enable-transparent-data-encryption.md) |Raccomanda di abilitare la crittografia per i database SQL (Solo servizio Azure SQL.) |
| [Abilita l'agente di macchine virtuali](security-center-enable-vm-agent.md) |Consente di identificare le macchine virtuali per le quali è necessario l'agente di macchine virtuali, che deve essere installato nelle macchine virtuali per effettuare il provisioning dei programmi di analisi delle patch, analisi della baseline e antimalware. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. L'articolo relativo all'[agente di macchine virtuali e relative estensioni, parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) offre informazioni su come installare l'agente di macchine virtuali. |
| [Applicare Crittografia dischi](security-center-apply-disk-encryption.md) |Suggerisce di crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure (VM Windows e Linux). La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale. |
| [Specificare dettagli del contatto per la sicurezza](security-center-provide-security-contact-details.md) |Suggerisce di specificare le informazioni di contatto per la sicurezza per ogni sottoscrizione. Le informazioni di contatto sono un indirizzo di posta elettronica e un numero di telefono. Le informazioni vengono usate per contattare l'utente se il team di sicurezza rileva risorse compromesse. |
| [Aggiornare la versione sistema operativo](security-center-update-os-version.md) |Suggerisce di aggiornare la versione del sistema operativo per il servizio cloud alla versione più recente disponibile per la famiglia di sistemi operativi.  Per altre informazioni sul servizio cloud, vedere [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md). |
| [La valutazione della vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md) |Consiglia di installare una soluzione di valutazione della vulnerabilità nella VM. |
| [Correggi le vulnerabilità](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Consente di visualizzare le vulnerabilità del sistema e delle applicazioni rilevate dalla soluzione di valutazione delle vulnerabilità installata nella VM. |
| [Abilitare la crittografia per l'account di archiviazione di Azure](security-center-enable-encryption-for-storage-account.md) | Consiglia di abilitare la crittografia del servizio Archiviazione di Azure per i dati inattivi che applica la crittografia ai dati quando vengono scritti nell'archiviazione di Azure e li decrittografa prima del recupero. La crittografia del servizio Archiviazione di Azure è attualmente disponibile solo per il servizio BLOB di Azure e può essere usata per BLOB in blocchi, BLOB di pagine e BLOB di aggiunta. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).</br>La crittografia del servizio Archiviazione di Azure è supportata solo negli account di archiviazione di Resource Manager. |
| [Abilita i controlli applicazione adattivi](security-center-adaptive-application.md) | Suggerisce di applicare controlli dell'applicazione adattivi nelle macchine virtuali Windows. Questa funzionalità è disponibile nel livello Standard di Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md). |
| Il servizio app deve essere accessibile solo tramite HTTPS | Consiglia di limitare l'accesso del servizio app solo tramite HTTPS. |
| Web Socket deve essere disabilitato per l'applicazione Web| Consiglia di esaminare attentamente l'utilizzo di Web Socket all'interno delle applicazioni Web.  Il protocollo Web Socket è vulnerabile a diversi tipi di minacce alla sicurezza. |
| Usare i domini personalizzati per l'applicazione Web | Consiglia l'uso dei domini personalizzati per proteggere un'applicazione Web da attacchi comuni come il phishing e altri attacchi correlati a DNS. |
| Configurare le restrizioni IP per l'applicazione Web | Consiglia di definire un elenco di indirizzi IP che possono accedere all'applicazione.  L'uso di restrizioni IP protegge un'applicazione Web dagli attacchi comuni. |
| Do not allow all ('*') resources to access your application (Non consentire a tutte le risorse ('*') di accedere all'applicazione) | Consiglia di non impostare il parametro WEBSITE_LOAD_CERTIFICATES su '*'. L'impostazione del parametro su '*' comporta che tutti i certificati verranno caricati nell'archivio certificati personale delle applicazioni Web.  Ciò può causare un abuso del principio dei privilegi minimi poiché è improbabile che il sito necessiti dell'accesso a tutti i certificati in fase di esecuzione. |
| Condivisione risorse tra le origini non deve consentire a tutte le risorse di accedere all'applicazione | Consiglia di consentire solo ai domini richiesti di interagire con l'applicazione Web. Condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'applicazione Web. |
| Usare la versione supportata più recente di .NET Framework per l'applicazione Web | Consiglia di usare la versione più recente di .NET Framework per le classi di sicurezza più recenti. L'uso di classi e tipi meno recenti può rendere vulnerabile l'applicazione. |
| Usare la versione supportata più recente di Java per l'applicazione Web | Consiglia di usare la versione più recente di Java per le classi di sicurezza più recenti. L'uso di classi e tipi meno recenti può rendere vulnerabile l'applicazione. |
| Usare la versione supportata più recente di PHP per l'applicazione Web | Consiglia di usare la versione più recente di PHP per le classi di sicurezza più recenti. L'uso di classi e tipi meno recenti può rendere vulnerabile l'applicazione. |
| [Aggiungere un Web Application Firewall](security-center-add-web-application-firewall.md) |Suggerisce di distribuire un Web application firewall (WAF) per gli endpoint Web. Viene visualizzata una raccomandazione WAF per qualsiasi IP pubblico (IP a livello di istanza o IP con carico bilanciato) cui è associato un gruppo di sicurezza di rete con porte Web in ingresso aperte (80, 443).</br></br>Centro sicurezza consiglia di effettuare il provisioning di un WAF per consentire la protezione da attacchi contro le applicazioni Web nelle macchine virtuali e nell'Ambiente del servizio app. Un Ambiente del servizio app è un'opzione del piano di servizio [Premium](https://azure.microsoft.com/pricing/details/app-service/) del Servizio app di Azure che offre un ambiente completamente isolato e dedicato per eseguire in modo sicuro tutte le app del servizio. Per altre informazioni sull'ambiente del servizio app, vedere [Documentazione relativa agli ambienti del servizio app](../app-service/environment/intro.md).</br></br>Per proteggere più applicazioni Web in Centro sicurezza, è possibile aggiungerle alle distribuzioni WAF esistenti. |
| [Finalizza la protezione dell'applicazione](security-center-add-web-application-firewall.md#finalize-application-protection) |Per completare la configurazione di un Web Application Firewall, il traffico deve essere reindirizzato al dispositivo WAF. Seguendo questa raccomandazione si completeranno le modifiche di configurazione necessarie. |
| Usare la versione supportata più recente di Node.js per l'applicazione Web | Consiglia di usare la versione più recente di Node.js per le classi di sicurezza più recenti. L'uso di classi e tipi meno recenti può rendere vulnerabile l'applicazione. |
| Condivisione risorse tra le origini non deve consentire a tutte le risorse di accedere all'app per le funzioni dell'utente | Consiglia di consentire solo ai domini richiesti di interagire con l'applicazione Web. Condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'applicazione per le funzioni. |
| Usare i domini personalizzati per l'app per le funzioni | Consiglia l'uso dei domini personalizzati per proteggere un'app per le funzioni da attacchi comuni come il phishing e altri attacchi correlati a DNS. |
| Configurare le restrizioni IP per l'app per le funzioni | Consiglia di definire un elenco di indirizzi IP che possono accedere all'applicazione. L'uso di restrizioni IP protegge un'app per le funzioni dagli attacchi comuni. |
| L'app per le funzioni deve essere accessibile solo tramite HTTPS | Consiglia di limitare l'accesso delle app per le funzioni solo tramite HTTPS. |
| Il debug remoto deve essere disattivato per l'app per le funzioni | Consiglia di disattivare il debug per l'app per le funzioni se non è più necessario. Il debug remoto richiede che vengano aperte le porte in ingresso in un'app per le funzioni. |
| Web Socket deve essere disabilitato per l'app per le funzioni | Consiglia di esaminare attentamente l'utilizzo di Web Socket all'interno delle app per le funzioni. Il protocollo Web Socket è vulnerabile a diversi tipi di minacce alla sicurezza. |
| Designare più di un proprietario per la sottoscrizione | Consiglia di designare più di un proprietario di sottoscrizione per assicurare la ridondanza dell'accesso amministratore. |
| Designare fino a 3 proprietari per la sottoscrizione | Consiglia di designare meno di 3 proprietari di sottoscrizione in modo da ridurre la probabilità di violazione da parte di un proprietario compromesso. |
| Enable MFA for accounts with owner permissions on your subscription (Abilitare MFA per gli account con autorizzazioni di proprietario per la sottoscrizione) | Consiglia di abilitare l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per tutti gli account della sottoscrizione con privilegi di amministratore per impedire una violazione degli account o delle risorse. |
| Abilitare MFA per gli account con autorizzazioni di scrittura per la sottoscrizione | Consiglia di abilitare l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per tutti gli account della sottoscrizione con privilegi di scrittura per impedire una violazione degli account o delle risorse. |
| Abilitare MFA per gli account con autorizzazioni di lettura per la sottoscrizione | Consiglia di abilitare l'autenticazione a più fattori (MFA, Multi-Factor Authentication) per tutti gli account della sottoscrizione con privilegi di lettura per impedire una violazione degli account o delle risorse. |
| Rimuovere gli account esterni con autorizzazioni di lettura dalla sottoscrizione | Consiglia di rimuovere dalla sottoscrizione gli account esterni con privilegi di lettura in modo da evitare l'accesso non monitorato. |
| Rimuovere gli account esterni con autorizzazioni di scrittura dalla sottoscrizione | Consiglia di rimuovere dalla sottoscrizione gli account esterni con privilegi di scrittura in modo da evitare l'accesso non monitorato. |
| Remove external accounts with owner permissions from your subscription (Rimuovere gli account esterni con autorizzazioni di proprietario dalla sottoscrizione) | Consiglia di rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di proprietario in modo da evitare l'accesso non monitorato. |
| Rimuovere gli account deprecati dalla sottoscrizione | Consiglia di rimuovere gli account deprecati dalle sottoscrizioni. |
| Remove deprecated accounts with owner permissions from subscription (Rimuovere gli account deprecati con autorizzazioni di proprietario dalla sottoscrizione) | Consiglia di rimuovere gli account deprecati con autorizzazioni di proprietario dalle sottoscrizioni. |

### <a name="apply-recommendations"></a>Applicare le raccomandazioni
Dopo aver esaminato tutte le raccomandazioni, decidere quale applicare per prima. È consigliabile usare la classificazione di gravità come parametro principale per valutare quali raccomandazioni applicare per prime.

Nella tabella delle raccomandazioni precedente selezionare una raccomandazione e usarla come esempio di come applicare una raccomandazione.

## <a name="next-steps"></a>Passaggi successivi
Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
