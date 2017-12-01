---
title: Progetto per l'elaborazione dei pagamenti di Azure - Requisiti per le password
description: Requisito 2 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 4ae9fc7d5b53d33f9feb98c450970e0560afa2af
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Requisiti per le password per gli ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-2"></a>Requisito 2 di PCI DSS

**Non usare i valori predefiniti del fornitore per le password del sistema e altri parametri di sicurezza**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Gli utenti malintenzionati (esterni e interni a un'entità) spesso usano le password predefinite del fornitore e altre impostazioni predefinite del fornitore per compromettere i sistemi. Queste password e impostazioni sono ben note all'interno delle community di pirati informatici e possono essere facilmente determinate tramite informazioni pubbliche.

## <a name="pci-dss-requirement-21"></a>Requisito 2.1 di PCI DSS
 
**2.1** Modificare sempre i valori predefiniti dei fornitori e rimuovere o disabilitare gli account predefiniti non necessari **prima** di installare un sistema in rete.
Ciò vale per TUTTE le password predefinite, incluse, solo a titolo di esempio, quelle usate dai sistemi operativi, da software che fornisce servizi di sicurezza, dagli account di sistema e applicazioni, dai terminali POS, dalle stringhe community SNMP (Simple Network Management Protocol) e così via.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Per le nuove password fornite dai clienti all'interno del portale AADUX vengono applicati i requisiti dei criteri password di Microsoft Azure Active Directory. Per le modifiche delle password self-service eseguite dai clienti è richiesta la convalida della password precedente. Le password per la reimpostazione dell'amministratore devono essere modificate dopo l'accesso successivo. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore richiede a tutti gli utenti di impostare password complesse. Nella demo non sono abilitati account di esempio o guest.<br /><br />Le funzionalità SNMP e wireless non sono implementate nella soluzione.|



### <a name="pci-dss-requirement-211"></a>Requisito 2.1.1 di PCI DSS

**2.1.1** Per gli ambienti wireless connessi all'ambiente dei dati di titolari di carte o che trasmettono dati di titolari di carte, modificare TUTTE le impostazioni predefinite del fornitore wireless, incluse a titolo esemplificativo, le chiavi di crittografia wireless predefinite, le password e le stringhe community SNMP.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Le funzionalità SNMP e wireless non sono implementate nella soluzione.|



## <a name="pci-dss-requirement-22"></a>Requisito 2.2 di PCI DSS

**2.2** Elaborare standard di configurazione per tutti i componenti di sistema. Assicurarsi che questi standard tengano conto di tutte le vulnerabilità di sicurezza note e siano coerenti con gli standard di protezione avanzata dei sistemi accettati dal settore.
Alcuni esempi di fonti degli standard di protezione avanzata dei sistemi accettati dal settore sono i seguenti:
- CIS (Center for Internet Security)
- ISO (International Organization for Standardization)
- Istituto SANS (SysAdmin Audit Network Security)
- NIST (National Institute of Standards Technology)

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Per Microsoft Azure, il team dei servizi di sicurezza tecnici OSSC sviluppa standard di configurazione della sicurezza per i sistemi nell'ambiente di Microsoft Azure, coerenti con gli standard di protezione avanzata accettati dal settore. Queste configurazioni sono documentante nelle linee di base di sistema e le modifiche di configurazione rilevanti vengono comunicate ai gruppi interessati (ad esempio, il team IPAK). Le procedure vengono implementate per monitorare la conformità con gli standard di configurazione della sicurezza. Gli standard di configurazione della sicurezza per i sistemi nell'ambiente Microsoft Azure sono coerenti con agli standard di protezione avanzata accettati dal settore e vengono verificati almeno ogni anno. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore fornisce protezione avanzata per tutti i servizi nell'ambito per l'ambiente dei dati di titolari di carte. <br /><br />Contoso Webstore implementa anche il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che offre una vista centralizzata dello stato di sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente, così come identificare rapidamente le risorse che richiedono attenzione.<br /><br />Contoso Webstore usa Operations Management Suite per registrare tutte le modifiche di sistema. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornisce una registrazione completa delle modifiche. Le modifiche possono essere controllate e verificate per stabilirne l'accuratezza. Per istruzioni più dettagliate, vedere [PCI Guidance - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Operations Management Suite).|



### <a name="pci-dss-requirement-221"></a>Requisito 2.2.1 di PCI DSS

**2.2.1** Implementare una sola funzione principale per server per evitare la coesistenza nello stesso server di funzioni che richiedono livelli di sicurezza diversi. Ad esempio, i server Web, i server di database e DNS devono essere implementati in server separati. 

> [!NOTE]
> Se si usano tecnologie di virtualizzazione, implementare una sola funzione principale per ogni componente del sistema virtuale.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I servizi di Contoso Webstore sono distribuiti come servizi PaaS. Tutti i servizi sono isolati e segmentati usando la segmentazione della rete.<br /><br />Contoso Webstore usa anche un [ambiente del servizio app](/azure/app-service-web/app-service-app-service-environment-intro) per applicare le procedure chiave. Per altre informazioni, vedere [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Linee guida per PCI - Ambiente del servizio app).|



### <a name="pci-dss-requirement-222"></a>Requisito 2.2.2 di PCI DSS

**2.2.2** Abilitare solo i servizi, protocolli, daemon e così via necessari per il funzionamento del sistema.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Le configurazioni software e hardware di Microsoft Azure vengono verificate almeno ogni trimestre per identificare ed eliminare le funzioni, le porte, i protocolli e i servizi non necessari. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I servizi di Contoso Webstore sono distribuiti come servizi PaaS. Tutti i servizi sono isolati e segmentati usando la segmentazione della rete.<br /><br />Contoso Webstore usa anche un [ambiente del servizio app](/azure/app-service-web/app-service-app-service-environment-intro) per applicare le procedure chiave. Per altre informazioni, vedere [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Linee guida per PCI - Ambiente del servizio app).|



### <a name="pci-dss-requirement-223"></a>Requisito 2.2.3 di PCI DSS

**2.2.3** Implementare funzionalità di sicurezza aggiuntive per eventuali servizi, protocolli o daemon necessari, considerati non sicuri. 

> [!NOTE]
> Quando si usa SSL/Early TLS, è necessario soddisfare i requisiti nell'appendice A2.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I servizi di Contoso Webstore sono distribuiti come servizi PaaS. Tutti i servizi sono isolati e segmentati usando la segmentazione della rete. La distribuzione offre inoltre protezione avanzata di tutti i servizi nell'ambito dell'ambiente dei dati di titolari di carte. <br /><br />Contoso Webstore implementa anche il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che offre una vista centralizzata dello stato di sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente, così come identificare rapidamente le risorse che richiedono attenzione.<br /><br />Contoso Webstore usa anche un [ambiente del servizio app](/azure/app-service-web/app-service-app-service-environment-intro) per applicare le procedure chiave. Per altre informazioni, vedere [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Linee guida per PCI - Ambiente del servizio app).|



### <a name="pci-dss-requirement-224"></a>Requisito 2.2.4 di PCI DSS

**2.2.4** Configurare i parametri di sicurezza del sistema per evitare usi impropri.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure garantisce che solo il personale autorizzato possa configurare i controlli di sicurezza della piattaforma Azure, usando controlli di accesso a più fattori e in base a esigenze aziendali documentate. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa AAD e il controllo degli accessi in base al ruolo di AD per gestire la corretta distribuzione dei parametri di sicurezza. Per altre informazioni, vedere [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).|



### <a name="pci-dss-requirement-225"></a>Requisito 2.2.5 di PCI DSS

**2.2.5** Rimuovere tutte le funzionalità non necessarie, ad esempio script, driver, funzionalità, sottosistemi, file system e server Web.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include la documentazione di come vengono stabiliti i limiti. Il modello di rischio e il diagramma di flusso dei dati di Contoso illustrano tutti i servizi usati e i controlli abilitati.|



## <a name="pci-dss-requirement-23"></a>Requisito 2.3 di PCI DSS

**2.3** Crittografare tutti gli accessi amministrativi non eseguiti tramite la console con la crittografia avanzata. 

> [!NOTE]
> Quando si usa SSL/Early TLS, è necessario soddisfare i requisiti nell'appendice A2.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure garantisce l'applicazione della crittografia avanzata per l'accesso all'infrastruttura dell'hypervisor. Microsoft Azure garantisce anche che i clienti che usano il portale di gestione di Microsoft Azure siano in grado di accedere alle console dei servizi/IaaS con crittografia avanzata. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore illustra come si possono implementare le password complesse in una soluzione. Inoltre, si possono eseguire tutti i test per verificare che la crittografia venga implementata tramite la soluzione.<br /><br />Contoso Webstore usa anche un [ambiente del servizio app](/azure/app-service-web/app-service-app-service-environment-intro) per applicare le procedure chiave. Per altre informazioni, vedere [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Linee guida per PCI - Ambiente del servizio app).|



## <a name="pci-dss-requirement-24"></a>Requisito 2.4 di PCI DSS

**2.4** Creare e mantenere aggiornato un inventario dei componenti di sistema che rientrano nell'ambito per PCI DSS.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | L'inventario di soluzioni PaaS demo di Contoso Webstore può essere esaminato nella documentazione fornita. Per altre informazioni, vedere [PCI Guidance - Pre-Installed OMS Solutions](payment-processing-blueprint.md#oms-solutions) (Linee guida per PCI - Soluzioni OMS preinstallate).|



## <a name="pci-dss-requirement-25"></a>Requisito 2.5 di PCI DSS

**2.5** Assicurarsi che i criteri di sicurezza e le procedure operative per la gestione dei valori predefiniti dei fornitori e gli altri parametri di sicurezza siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include documentazione con informazioni dettagliate sui parametri di sicurezza e gli elementi di servizio. |



## <a name="pci-dss-requirement-26"></a>Requisito 2.6 di PCI DSS

**2.6** I provider di hosting condivisi devono proteggere l'ambiente ospitato e i dati di titolari di carte di ogni entità. Questi provider devono soddisfare requisiti specifici, come descritto in dettaglio in *Appendix A: Additional PCI DSS Requirements for Shared Hosting Providers* (Appendice A: Requisiti PCI DSS aggiuntivi per i provider di hosting condivisi).

**Responsabilità:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile Microsoft Azure non è un provider di hosting condiviso. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile Microsoft Azure non è un provider di hosting condiviso.|




