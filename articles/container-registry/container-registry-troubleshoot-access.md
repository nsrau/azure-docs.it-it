---
title: Risolvere i problemi di rete con il registro di sistema
description: Sintomi, cause e risoluzione dei problemi comuni durante l'accesso a un registro contenitori di Azure in una rete virtuale o dietro a un firewall
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 227eeeadb2aef4b4d3feb7923a198b129a6267d3
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227467"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Risolvere i problemi di rete con il registro di sistema

Questo articolo consente di risolvere i problemi che possono verificarsi durante l'accesso a un registro contenitori di Azure in una rete virtuale o dietro a un firewall. 

## <a name="symptoms"></a>Sintomi

Può includere uno o più degli elementi seguenti:

* Non è possibile eseguire il push o il pull delle immagini e si riceve un errore `dial tcp: lookup myregistry.azurecr.io`
* Non è possibile eseguire il push o il pull delle immagini e si riceve un errore dell'interfaccia della `Could not connect to the registry login server`
* Non è possibile estrarre le immagini dal registro di sistema al servizio Azure Kubernetes o a un altro servizio di Azure
* Non è possibile accedere a un registro di sistema dietro un proxy HTTPS e si riceve un errore `Error response from daemon: login attempt failed with status: 403 Forbidden`
* Non è possibile accedere alle impostazioni del registro di sistema o visualizzarle in portale di Azure o gestire il registro di sistema con l'interfaccia
* Non è possibile aggiungere o modificare le impostazioni della rete virtuale o le regole di accesso pubblico
* Le attività ACR non sono in grado di eseguire il push o il pull delle immagini
* Il Centro sicurezza di Azure non può analizzare le immagini nel registro di sistema o i risultati dell'analisi non vengono visualizzati nel centro sicurezza di Azure

## <a name="causes"></a>Cause

* Un firewall o un proxy client impedisce l'accesso- [soluzione](#configure-client-firewall-access)
* Le regole di accesso alla rete pubblica nel registro di sistema impediscono l'accesso- [soluzione](#configure-public-access-to-registry)
* La configurazione della rete virtuale impedisce l'accesso- [soluzione](#configure-vnet-access)
* Si tenta di integrare il Centro sicurezza di Azure con un registro con un endpoint privato o un endpoint servizio- [soluzione](#configure-image-scanning-solution)

## <a name="further-diagnosis"></a>Ulteriore diagnosi 

Eseguire il comando [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) per ottenere altre informazioni sull'integrità dell'ambiente del registro di sistema e, facoltativamente, l'accesso a un registro di sistema di destinazione. Ad esempio, diagnosticare determinati problemi di configurazione o connettività di rete. 

Vedere [verificare l'integrità di un registro contenitori di Azure](container-registry-check-health.md) per gli esempi di comando. Se vengono segnalati errori, esaminare il [riferimento all'errore](container-registry-health-error-reference.md) e le sezioni seguenti per le soluzioni consigliate.

> [!NOTE]
> Alcuni sintomi della connettività di rete possono verificarsi anche in caso di problemi con l'autenticazione o l'autorizzazione del registro di sistema. Vedere [risolvere i problemi di accesso al registro di sistema](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Possibili soluzioni

### <a name="configure-client-firewall-access"></a>Configurare l'accesso al firewall client

Per accedere a un registro da dietro un firewall client o un server proxy, configurare le regole del firewall per accedere agli endpoint di dati e REST del registro di sistema. Se gli [endpoint dati dedicati](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) sono abilitati, è necessario disporre di regole per accedere a:

* Endpoint REST: `<registryname>.azurecr.io`
* Endpoint dati: `<registry-name>.<region>.data.azurecr.io`

Per un registro con replica geografica, configurare l'accesso all'endpoint dati per ogni replica regionale.

Dietro un proxy HTTPS, assicurarsi che il client Docker e il daemon Docker siano configurati per il comportamento del proxy.

I log delle risorse del registro di sistema nella tabella ContainerRegistryLoginEvents possono aiutare a diagnosticare una connessione tentata bloccata.

Collegamenti correlati:

* [Configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](container-registry-firewall-access-rules.md)
* [Configurazione proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Replica geografica in Azure Container Registry](container-registry-geo-replication.md)
* [Log di Azure Container Registry per la valutazione diagnostica e il controllo](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Configurare l'accesso pubblico al registro di sistema

Se si accede a un registro di sistema tramite Internet, verificare che il registro di sistema consenta l'accesso alla rete pubblica dal client. Per impostazione predefinita, un registro contenitori di Azure consente l'accesso agli endpoint del registro di sistema pubblici da tutte le reti. Un registro può limitare l'accesso alle reti selezionate o agli indirizzi IP selezionati. 

Se il registro di sistema è configurato per una rete virtuale con un endpoint del servizio, disabilitando l'accesso alla rete pubblica viene disabilitato anche l'accesso tramite l'endpoint del servizio. Se il registro di sistema è configurato per una rete virtuale con collegamento privato, le regole di rete IP non si applicano agli endpoint privati del registro di sistema. 

Collegamenti correlati:

* [Configurare le regole di rete per IP pubblico](container-registry-access-selected-networks.md)
* [Connettersi privatamente a un registro contenitori di Azure usando il collegamento privato di Azure](container-registry-private-link.md)
* [Limitare l'accesso a un registro contenitori usando un endpoint di servizio in una rete virtuale di Azure](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Configurare l'accesso a VNet

Verificare che la rete virtuale sia configurata con un endpoint privato per il collegamento privato o un endpoint servizio (anteprima). Attualmente un endpoint di Azure Bastion non è supportato.

Esaminare le regole e i tag del servizio NSG usati per limitare il traffico da altre risorse della rete al registro di sistema. 

Se è configurato un endpoint di servizio per il registro di sistema, verificare che venga aggiunta una regola di rete al registro di sistema che consenta l'accesso da tale subnet di rete. L'endpoint servizio supporta solo l'accesso da macchine virtuali e cluster AKS nella rete.

Se nella rete è configurato un firewall di Azure o una soluzione simile, verificare che il traffico in uscita da altre risorse, ad esempio un cluster AKS, sia abilitato per raggiungere gli endpoint del registro di sistema.

Collegamenti correlati:

* [Connettersi privatamente a un registro contenitori di Azure usando il collegamento privato di Azure](container-registry-private-link.md)
* [Limitare l'accesso a un registro contenitori usando un endpoint di servizio in una rete virtuale di Azure](container-registry-vnet.md)
* [Regole di rete in uscita obbligatorie e FQDN per i cluster AKS](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: debug della risoluzione DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

### <a name="configure-image-scanning-solution"></a>Configurare la soluzione per l'analisi delle immagini

Se il registro di sistema è configurato con un endpoint privato o un endpoint del servizio, attualmente non è possibile eseguire l'integrazione con il Centro sicurezza di Azure per l'analisi delle immagini. Facoltativamente, è possibile configurare altre soluzioni di analisi immagini disponibili in Azure Marketplace, tra cui:

* [Piattaforma di sicurezza nativa di Aqua cloud](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security)
* [Twistlock Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock)

Collegamenti correlati:

* [Analisi delle immagini del Container Registry di Azure per Centro sicurezza](../security-center/azure-container-registry-integration.md)
* Invia [commenti e suggerimenti](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)


## <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Se nel registro di sistema è abilitata la [raccolta di log delle risorse](container-registry-diagnostics-audit-logs.md) , esaminare il log ContainterRegistryLoginEvents. Questo log archivia gli eventi e lo stato di autenticazione, inclusi l'identità in ingresso e l'indirizzo IP. Eseguire una query sul log per individuare gli [errori di autenticazione del registro](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Collegamenti correlati:

* [Log per la valutazione diagnostica e il controllo](container-registry-diagnostics-audit-logs.md)
* [Domande frequenti sul registro contenitori](container-registry-faq.md)
* [Baseline della sicurezza di Azure per Azure Container Registry](security-baseline.md)
* [Procedure consigliate per Registro Azure Container](container-registry-best-practices.md)

## <a name="next-steps"></a>Passaggi successivi

Se non si risolve il problema, vedere le opzioni seguenti.

* Altri argomenti sulla risoluzione dei problemi del registro di sistema includono:
  * [Risolvere i problemi di accesso al registro](container-registry-troubleshoot-login.md) 
  * [Risolvere i problemi relativi alle prestazioni del registro](container-registry-troubleshoot-performance.md)
* Opzioni di [supporto della community](https://azure.microsoft.com/support/community/)
* [Domane e risposte Microsoft](https://docs.microsoft.com/answers/products/)
* [Aprire un ticket di supporto](https://azure.microsoft.com/support/create-ticket/)


