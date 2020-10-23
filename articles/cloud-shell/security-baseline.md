---
title: Baseline della sicurezza di Azure per Cloud Shell
description: La linea di base di sicurezza Cloud Shell fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 463bbe637eee26ab098d1531976a18999497d12f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210240"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Baseline della sicurezza di Azure per Cloud Shell

Questa linea di base di sicurezza applica le indicazioni della [versione 1,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview-v1.md) a cloud Shell. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a cloud Shell. I **controlli** non applicabili ai cloud Shell sono stati esclusi.

 
Per informazioni su come Cloud Shell viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base cloud shell sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Indicazioni**: i clienti possono distribuire Azure cloud Shell in una rete virtuale di proprietà del cliente.

Quando si distribuisce Azure Cloud Shell in una rete virtuale di proprietà del cliente, è necessario creare o usare una rete virtuale esistente. Assicurarsi che la rete virtuale scelta disponga di un gruppo di sicurezza di rete applicato alle subnet e ai controlli di accesso alla rete configurati in base alle porte e alle origini attendibili dell'applicazione.

- [Distribuire Cloud Shell in una rete virtuale di Azure](private-vnet.md)

- [Come creare un gruppo di sicurezza di rete con le regole di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: Azure cloud Shell è un'esperienza della riga di comando basata su browser che usa la stessa autorizzazione usata per accedere al portale di Azure, in questo caso un SSO nel portale di Azure eseguirà anche l'autenticazione con Cloud Shell. 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: Azure cloud Shell è un'esperienza della riga di comando basata su browser che usa la stessa autorizzazione usata per accedere al portale di Azure, in questo caso è necessaria anche l'autenticazione a più fattori necessaria per la connessione al portale di Azure per cloud Shell. 

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: Azure cloud Shell è un'esperienza da riga di comando basata su browser usata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini del contenitore vengono monitorate e aggiornate dal team di Cloud Shell.

Azure Cloud Shell consente ai clienti di installare i propri strumenti o software nella propria immagine in base alle esigenze dell'organizzazione.

I clienti hanno la responsabilità di eseguire strumenti automatici per l'analisi delle vulnerabilità rispetto al software in esecuzione nell'ambiente.  

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire una soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida**: non applicabile; Azure Cloud Shell è un'esperienza da riga di comando basata su browser utilizzata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini del contenitore vengono monitorate e aggiornate dal team di Cloud Shell.

Azure Cloud Shell consente ai clienti di installare i propri strumenti o software nella propria immagine in base alle esigenze dell'organizzazione.

I clienti sono responsabili della gestione delle patch software in esecuzione nel proprio ambiente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: Azure cloud Shell è un'esperienza da riga di comando basata su browser usata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini del contenitore vengono monitorate e aggiornate dal team di Cloud Shell.

Azure Cloud Shell consente ai clienti di installare i propri strumenti o software nella propria immagine in base alle esigenze dell'organizzazione.

I clienti sono tenuti a correggere le vulnerabilità individuate tramite le analisi delle vulnerabilità del software. Esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati con le analisi precedenti per verificare che le vulnerabilità siano state corrette. Quando si usano le raccomandazioni sulla gestione delle vulnerabilità suggerite dal centro sicurezza di Azure, è possibile passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: Azure cloud Shell è un'esperienza da riga di comando basata su browser usata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini del contenitore vengono monitorate e aggiornate dal team di Cloud Shell.

Azure Cloud Shell consente ai clienti di installare i propri strumenti o software nella propria immagine in base alle esigenze dell'organizzazione.

I clienti sono tenuti a correggere le vulnerabilità individuate tramite le analisi delle vulnerabilità del software.  Usare un programma comune di assegnazione dei punteggi dei rischi, ad esempio un sistema di assegnazione dei punteggi di vulnerabilità comune, o le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti. 

- [Pubblicazione NIST: sistema di valutazione delle vulnerabilità comune](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Indicazioni**: Azure cloud Shell è un'esperienza da riga di comando basata su browser usata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini e gli strumenti del contenitore vengono monitorati e aggiornati dal team Cloud Shell.  Il cliente è in grado di installare i propri strumenti nella propria immagine in base alle esigenze dell'organizzazione e gli strumenti non richiedono le `sudo` autorizzazioni durante l'installazione.

Si consiglia ai clienti di creare un inventario del software approvato installato tramite Azure Cloud Shell in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: Azure cloud Shell è un servizio gratuito senza asset di proprietà del cliente.  Le immagini e gli strumenti del contenitore vengono monitorati e aggiornati dal team Cloud Shell. 

Azure Cloud Shell consente ai clienti di installare i propri strumenti o software nella propria immagine in base alle esigenze dell'organizzazione.

I clienti sono tenuti a monitorare le applicazioni software in esecuzione nell'ambiente per assicurarsi che siano approvate in base ai criteri dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: Azure cloud Shell è un servizio gratuito senza asset di proprietà del cliente.  Le immagini e gli strumenti del contenitore vengono monitorati e aggiornati dal team Cloud Shell. 

Azure Cloud Shell consente ai clienti di installare i propri strumenti o software nella propria immagine in base alle esigenze dell'organizzazione.

I clienti sono responsabili del monitoraggio delle applicazioni software in esecuzione nell'ambiente per garantire che il software non approvato venga gestito in base ai criteri dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: Azure cloud Shell è un servizio gratuito senza asset di proprietà del cliente.  Le immagini e gli strumenti del contenitore vengono monitorati e aggiornati dal team Cloud Shell.  Gli strumenti specifici potrebbero non essere rimossi dal cliente.

Azure Cloud Shell consente ai clienti di installare i propri strumenti o applicazioni in una propria immagine in base alle esigenze dell'organizzazione.

I clienti sono tenuti a monitorare le applicazioni in esecuzione nell'ambiente per assicurarsi che siano approvate in base ai criteri dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: non applicabile; Azure Cloud Shell è un'esperienza da riga di comando basata su browser utilizzata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini del contenitore vengono monitorate e aggiornate dal team di Cloud Shell.

Azure Cloud Shell consente ai clienti di installare i propri strumenti o software nella propria immagine in base alle esigenze dell'organizzazione.

I clienti hanno la responsabilità di mantenere un inventario del software approvato in esecuzione nell'ambiente per assicurarsi che siano software approvati in base ai criteri dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: Azure cloud Shell è un'esperienza da riga di comando basata su browser usata per la gestione interattiva delle risorse cloud.  Le azioni eseguite all'interno di Cloud Shell funzionano allo stesso modo delle azioni eseguite dagli stessi strumenti o linguaggi eseguiti in un ambiente locale.  Le azioni da singoli strumenti e linguaggi devono essere limitate, i clienti non possono limitare l'accesso ai Cloud Shell o limitare ciò che è disponibile per un utente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: Azure cloud Shell possono essere isolate in una rete virtuale del cliente.

- [Distribuire Cloud Shell in una rete virtuale di Azure](private-vnet.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida**: cloud Shell consente l'esecuzione degli script in, la creazione e il caricamento nell'ambiente cloud Shell.  È consigliabile trasferire le credenziali in Azure Key Vault.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: Azure cloud Shell è un'esperienza da riga di comando basata su browser usata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini e gli strumenti del contenitore vengono monitorati e aggiornati dal team Cloud Shell.  Il cliente è in grado di installare i propri strumenti nella propria immagine in base alle esigenze dell'organizzazione e gli strumenti non richiedono le `sudo` autorizzazioni durante l'installazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme antimalware siano aggiornati

**Indicazioni**: Azure cloud Shell è un'esperienza da riga di comando basata su browser usata per la gestione interattiva delle risorse cloud.  Ogni contenitore del cliente è temporaneo. viene usato un nuovo contenitore per ogni sessione.  Le immagini e gli strumenti del contenitore vengono monitorati e aggiornati dal team Cloud Shell.  Il cliente è in grado di installare i propri strumenti nella propria immagine in base alle esigenze dell'organizzazione e gli strumenti non richiedono le `sudo` autorizzazioni durante l'installazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.
- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md) 
- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'utilizzo analitico per emettere l'avviso, oltre al livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.
Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.
- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 
- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.
- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.
- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.
- [Come configurare l'esportazione continua](../security-center/continuous-export.md) 
- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.
- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.
- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
