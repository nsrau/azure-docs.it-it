---
title: Supporto del firewall e controllo dell'accesso agli indirizzi IP per Azure Cosmos DB | Microsoft Docs
description: Informazioni su come usare criteri di controllo di accesso agli indirizzi IP per il supporto del firewall in account del database di Azure Cosmos DB.
keywords: controllo di accesso IP, supporto del firewall
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: ankshah
ms.openlocfilehash: 85f5e0e076f92afc79ed8f4ce652bb0f31923113
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="azure-cosmos-db-firewall-support"></a>Supporto del firewall di Azure Cosmos DB
Per proteggere i dati archiviati in un account del database di Azure Cosmos DB, Azure Cosmos DB ha reso disponibile il supporto per un [modello di autorizzazione](https://msdn.microsoft.com/library/azure/dn783368.aspx) basato su segreto che usa un codice di autenticazione messaggi basato su hash (HMAC, Hash-based Message Authentication Code). Oltre al modello di autenticazione basato su segreto, Azure Cosmos DB supporta ora controlli dell'accesso agli indirizzi IP basati su criteri per il supporto del firewall in ingresso. Questo modello è simile alle regole del firewall di un sistema di database tradizionale e offre un livello aggiuntivo di sicurezza per l'account del database di Azure Cosmos DB. Con questo modello è ora possibile configurare un account del database di Azure Cosmos DB perché sia accessibile solo da un set di computer e/o servizi cloud approvato. Per l'accesso alle risorse di Azure Cosmos DB da questi set di computer e servizi approvati, è comunque necessario che il chiamante presenti un token di autorizzazione valido.

## <a name="ip-access-control-overview"></a>Panoramica del controllo di accesso IP
Per impostazione predefinita, un account del database di Azure Cosmos DB è accessibile dalla rete Internet pubblica purché la richiesta sia accompagnata da un token di autorizzazione valido. Per configurare il controllo di accesso IP basato su criteri, l'utente deve fornire il set di indirizzi IP o di intervalli di indirizzi IP in formato CIDR perché venga incluso come elenco di IP client consentiti per un account del database specificato. Una volta applicata questa configurazione, vengono bloccate tutte le richieste provenienti da computer di fuori di questo elenco consentito dal server.  Il flusso di elaborazione della connessione per il controllo di accesso basato su IP è descritto nel diagramma seguente:

![Diagramma che mostra il processo di connessione per il controllo di accesso basato su IP](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Configurazione dei criteri di controllo di accesso IP
Impostare i criteri di controllo di accesso IP nel portale di Azure oppure a livello di programmazione tramite [CLI di Azure](cli-samples.md), [Azure Powershell](powershell-samples.md), o [API REST](/rest/api/documentdb/) aggiornando il **ipRangeFilter** proprietà. 

Per impostare i criteri di controllo di accesso IP nel portale di Azure, passare alla pagina account di Azure Cosmos DB, fare clic su **Firewall** nel menu di navigazione, quindi modificare il **Abilita controllo di accesso IP** valore **ON**. 

![Screenshot che illustra come aprire la pagina di Firewall nel portale di Azure](./media/firewall-support/azure-portal-firewall.png)

Una volta nel controllo di accesso IP, il portale fornisce opzioni per abilitare l'accesso al portale di Azure, altri servizi di Azure e l'IP corrente. Informazioni aggiuntive su tali opzioni vengono fornite nelle sezioni seguenti.

![Screenshot che mostra come configurare le impostazioni del firewall nel portale di Azure](./media/firewall-support/azure-portal-firewall-configure.png)

> [!NOTE]
> Abilitando criteri di controllo dell'accesso agli indirizzi IP per l'account del database di Azure Cosmos DB, qualsiasi accesso all'account del database di Azure Cosmos DB da computer non inclusi nell'elenco degli intervalli di indirizzi IP consentiti viene bloccato. Grazie a questo modello, viene bloccata anche l'esplorazione dell'operazione del piano dati dal portale per garantire l'integrità del controllo di accesso.

## <a name="connections-from-the-azure-portal"></a>Connessioni dal portale di Azure 

Quando si abilita un criterio di controllo di accesso IP a livello di codice, è necessario aggiungere l'indirizzo IP per il portale di Azure per il **ipRangeFilter** proprietà per mantenere l'accesso. Gli indirizzi IP del portale sono i seguenti:

|Region|Indirizzo IP|
|------|----------|
|Tutte le aree a eccezione di quelle specificate di seguito|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Germania|51.4.229.218|
|Cina|139.217.8.252|
|US Gov|52.244.48.71|

Per abilitare l'accesso al portale di Azure, tramite il portale di Azure, impostare il **consentire l'accesso al portale di Azure** valore **ON** nel portale di Azure (il **Abilita controllo di accesso IP** valore deve essere impostato su **ON** per visualizzare e modificare il **consentire l'accesso al portale di Azure** valore).

![Screenshot che illustra come abilitare l'accesso al portale Azure](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-other-azure-paas-services"></a>Connessioni da altri servizi PaaS di Azure 
In Azure, servizi PaaS come flusso di Azure analitica, funzioni di Azure e Azure App Service utilizzati in combinazione con Azure Cosmos DB. Per abilitare l'accesso al database di Azure Cosmos aggiungere l'indirizzo IP 0.0.0.0 all'elenco di indirizzi IP associati all'account di database di Azure Cosmos DB a livello di codice consentito da questi servizi, i cui indirizzi IP non sono disponibili account di database o impostare il **Consentire l'accesso ai servizi di Azure** valore su ON nel portale di Azure (il **Abilita controllo di accesso IP** valore deve essere impostato su **ON** per visualizzare e modificare il **Consenti Accesso ai servizi di Azure** valore). In questo modo si garantisce che i servizi PaaS di Azure possono accedere all'account di Azure Cosmos DB. 

![Screenshot che illustra come aprire la pagina di Firewall nel portale di Azure](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Connessioni dall'IP corrente

Per semplificare lo sviluppo, il portale di Azure consente di identificare e aggiungere l'indirizzo IP del computer client all'elenco di indirizzi consentiti, in modo che le app in esecuzione nella macchina virtuale possano accedere all'account Azure Cosmos DB. L'indirizzo IP del client viene rilevato in base a quanto visualizzato dal portale. Potrebbe trattarsi dell'indirizzo IP del client della macchina virtuale oppure dell'indirizzo IP del gateway di rete. Non dimenticare di rimuoverlo prima di passare all'ambiente di produzione.

![Screenshot che illustra come configurare le impostazioni di firewall per l'IP corrente](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Connessioni da servizi cloud
In Azure, i servizi cloud sono uno strumento comune per ospitare la logica del servizio di livello intermedio con Azure Cosmos DB. Per consentire l'accesso a un account del database di Azure Cosmos DB da un servizio cloud, l'indirizzo IP pubblico del servizio cloud deve essere aggiunto all'elenco di indirizzi IP consentiti associato all'account del database di Azure Cosmos DB [configurando il criterio di controllo dell'accesso agli indirizzi IP](#configure-ip-policy).  In questo modo, tutte le istanze del ruolo dei servizi cloud hanno accesso all'account del database di Azure Cosmos DB. È possibile recuperare gli indirizzi IP per i servizi cloud nel portale di Azure, come mostrato nello screenshot seguente:

![Screenshot che mostra l'indirizzo IP pubblico per un servizio cloud visualizzato nel portale di Azure](./media/firewall-support/public-ip-addresses.png)

Quando si aumenta il numero di istanze del servizio cloud aggiungendo altre istanze del ruolo, le nuove istanze hanno automaticamente accesso all'account del database di Azure Cosmos DB perché fanno parte dello stesso servizio cloud.

## <a name="connections-from-virtual-machines"></a>Connessioni da macchine virtuali
Per ospitare servizi di livello intermedio con Azure Cosmos DB è possibile usare anche [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) o [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).  Per configurare l'account del database di Azure Cosmos DB in modo da consentire l'accesso da macchine virtuali, gli indirizzi IP pubblici della macchina virtuale e/o del set di scalabilità di macchine virtuali devono essere configurati come uno degli indirizzi IP consentiti per l'account del database di Azure Cosmos DB [configurando il criterio di controllo dell'accesso agli indirizzi IP](#configure-ip-policy). È possibile recuperare gli indirizzi IP per le macchine virtuali nel portale di Azure, come mostrato nello screenshot seguente.

![Screenshot che mostra un indirizzo IP pubblico per una macchina virtuale visualizzata nel portale di Azure](./media/firewall-support/public-ip-addresses-dns.png)

Quando si aggiungono istanze di macchina virtuale al gruppo, a queste viene automaticamente fornito l'accesso all'account del database di Azure Cosmos DB.

## <a name="connections-from-the-internet"></a>Connessioni da Internet
Quando si accede a un account del database di Azure Cosmos DB da un computer in Internet, l'indirizzo IP o l'intervallo di indirizzi IP client del computer deve essere aggiunto all'elenco di indirizzi IP consentiti per l'account del database di Azure Cosmos DB. 

## <a name="troubleshooting-the-ip-access-control-policy"></a>Risoluzione dei problemi relativi ai criteri di controllo di accesso IP
### <a name="portal-operations"></a>Operazioni nel portale
Abilitando criteri di controllo dell'accesso agli indirizzi IP per l'account del database di Azure Cosmos DB, qualsiasi accesso all'account del database di Azure Cosmos DB da computer non inclusi nell'elenco degli intervalli di indirizzi IP consentiti viene bloccato. Pertanto se si desidera abilitare le operazioni di piano dati portale come raccolte e query nei documenti di esplorazione, è necessario consentire esplicitamente l'accesso al portale Azure utilizzando il **Firewall** pagina del portale. 

![Screenshot che mostra come abilitare l'accesso al portale di Azure](./media/firewall-support/enable-azure-portal.png)

### <a name="sdk--rest-api"></a>SDK e API REST
Per motivi di sicurezza, l'accesso tramite SDK o API REST da computer non inclusi nell'elenco degli indirizzi IP consentiti restituisce una risposta generica 404 Non trovato senza altri dettagli. Controllare l'elenco degli indirizzi IP consentiti per l'account del database di Azure Cosmos DB per verificare che all'account sia applicata la configurazione dei criteri corretta.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui suggerimenti sulle prestazioni relative alla rete, vedere [suggerimenti sulle prestazioni](performance-tips.md).

