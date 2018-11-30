---
title: Raccomandazioni delle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza che facilitano la protezione delle macchine virtuali, dei computer, delle app Web e degli ambienti di Servizio app di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: rkarlin
ms.openlocfilehash: 626b9199f3de6fe9aba03ba537aa6e6b6e28ba5c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310924"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Informazioni sulle raccomandazioni delle risorse per Centro sicurezza di Azure


## <a name="recommendations"></a>Consigli
Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili per Calcolo e Servizi app i relativi effetti in caso di applicazione.

### <a name="computers"></a>Computer
| Raccomandazione | DESCRIZIONE |
| --- | --- |
| [Abilita la raccolta di dati per le sottoscrizioni](security-center-enable-data-collection.md) |Consiglia di attivare la raccolta dati nei criteri di sicurezza per ogni sottoscrizione e per tutte le macchine virtuali nelle sottoscrizioni. |
| [Abilitare la crittografia per l'account di archiviazione di Azure](security-center-enable-encryption-for-storage-account.md) | Consiglia di abilitare la crittografia del servizio Archiviazione di Azure per i dati inattivi che applica la crittografia ai dati quando vengono scritti nell'archiviazione di Azure e li decrittografa prima del recupero. La crittografia del servizio Archiviazione di Azure è attualmente disponibile solo per il servizio BLOB di Azure e può essere usata per BLOB in blocchi, BLOB di pagine e BLOB di aggiunta. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).</br>La crittografia del servizio Archiviazione di Azure è supportata solo negli account di archiviazione di Resource Manager. Gli account di archiviazione classici non sono attualmente supportati. Per informazioni sui modelli di distribuzione classico e di Gestione risorse, vedere i [modelli di distribuzione di Azure](../azure-classic-rm.md). |
| [Correggere le configurazioni di sicurezza](security-center-remediate-os-vulnerabilities.md) |Consiglia di allineare le configurazioni dei sistemi operativi alle regole di configurazione di sicurezza raccomandate, ad esempio non consentire il salvataggio delle password. |
| [Applicare gli aggiornamenti di sistema](security-center-apply-system-updates.md) |Consiglia di distribuire gli aggiornamenti critici e della sicurezza di sistema mancanti nelle macchine virtuali. |
| [Applicare un controllo di accesso alla rete JIT](security-center-just-in-time.md) | Consiglia di applicare l'accesso Just-In-Time alla macchina virtuale. La funzionalità Just-In-Time è in anteprima e disponibile nel livello Standard di Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md). |
| [Riavvia dopo gli aggiornamenti del sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Consiglia di riavviare una macchina virtuale per completare il processo di applicazione degli aggiornamenti del sistema. |
| [Installa Endpoint Protection](security-center-install-endpoint-protection.md) |Suggerisce di effettuare il provisioning dei programmi antimalware nelle macchine virtuali (solo VM Windows). |
| [Abilita l'agente di macchine virtuali](security-center-enable-vm-agent.md) |Consente di identificare le macchine virtuali per le quali è necessario l'agente di macchine virtuali, che deve essere installato nelle macchine virtuali per poter effettuare il provisioning dei programmi di analisi delle patch, analisi della baseline e antimalware. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. L'articolo relativo all'[agente di macchine virtuali e relative estensioni, parte 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) offre informazioni su come installare l'agente di macchine virtuali. |
| [Applicare Crittografia dischi](security-center-apply-disk-encryption.md) |Suggerisce di crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure (VM Windows e Linux). La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale. |
| [Aggiornare la versione sistema operativo](security-center-update-os-version.md) |Suggerisce di aggiornare la versione del sistema operativo per il servizio cloud alla versione più recente disponibile per la famiglia di sistemi operativi.  Per altre informazioni sul servizio cloud, vedere [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md). |
| [La valutazione della vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md) |Consiglia di installare una soluzione di valutazione della vulnerabilità nella VM. |
| [Correggi le vulnerabilità](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Consente di visualizzare le vulnerabilità del sistema e delle applicazioni rilevate dalla soluzione di valutazione delle vulnerabilità installata nella VM. |

### <a name="app-services"></a>Servizi app
| Raccomandazione | DESCRIZIONE |
| --- | --- |
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


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-azure-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
