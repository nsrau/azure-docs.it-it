---
title: Supporto del firewall di DocumentDB | Documentazione Microsoft
description: Informazioni su come usare criteri di controllo di accesso IP per il supporto del firewall in account del database di Azure DocumentDB.
keywords: controllo di accesso IP, supporto del firewall
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb3c5c2adbaedc4bfb1e68f26b88079aeabe50f5


---
# <a name="documentdb-firewall-support"></a>Supporto del firewall di DocumentDB
Per proteggere i dati archiviati in un account del database di Azure DocumentDB, DocumentDB ha reso disponibile il supporto per un [modello di autorizzazione](https://msdn.microsoft.com/library/azure/dn783368.aspx) basato su segreto che utilizza un codice di autenticazione messaggi basato su hash (HMAC, Hash-based Message Authentication Code). Oltre al modello di autenticazione basato su segreto, DocumentDB supporta ora controlli di accesso IP basati su criteri per il supporto del firewall in ingresso. Questo modello è molto simile alle regole del firewall di un sistema di database tradizionale e offre un livello aggiuntivo di sicurezza per l'account del database di DocumentDB. Con questo modello è ora possibile configurare un account del database di DocumentDB perché sia accessibile solo da un set di computer e/o servizi cloud approvato. Per l'accesso alle risorse di DocumentDB da questi set di computer e servizi approvati, è comunque necessario che il chiamante presenti un token di autorizzazione valido.

## <a name="ip-access-control-overview"></a>Panoramica del controllo di accesso IP
Per impostazione predefinita, un account del database di DocumentDB è accessibile dalla rete Internet pubblica purché la richiesta sia accompagnata da un token di autorizzazione valido. Per configurare il controllo di accesso IP basato su criteri, l'utente deve fornire il set di indirizzi IP o di intervalli di indirizzi IP in formato CIDR perché venga incluso come elenco di IP client consentiti per un account del database specificato. Dopo aver applicato questa configurazione, tutte le richieste provenienti dai computer non inclusi in questo elenco di client IP consentiti verranno bloccati dal server.  Il flusso di elaborazione della connessione per il controllo di accesso basato su IP viene descritto nel diagramma seguente.

![Diagramma che mostra il processo di connessione per il controllo di accesso basato su IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Connessioni da servizi cloud
In Azure i servizi cloud sono uno strumento molto comune per ospitare la logica del servizio di livello intermedio con DocumentDB. Per consentire l'accesso a un account del database di DocumentDB da un servizio cloud, l'indirizzo IP pubblico del servizio cloud deve essere aggiunto all'elenco di indirizzi IP consentiti associato all'account del database di DocumentDB [contattando il supporto di Azure](#configure-ip-policy).  In questo modo, tutte le istanze del ruolo dei servizi cloud hanno accesso all'account del database di DocumentDB. È possibile recuperare gli indirizzi IP per i servizi cloud nel portale di Azure, come mostrato nello screenshot seguente. 

![Screenshot che mostra l'indirizzo IP pubblico per un servizio cloud visualizzato nel portale di Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Quando si scala orizzontalmente il servizio cloud aggiungendo altre istanze del ruolo, le nuove istanze hanno automaticamente accesso all'account del database di DocumentDB perché fanno parte dello stesso servizio cloud.

## <a name="connections-from-virtual-machines"></a>Connessioni da macchine virtuali
Per ospitare servizi di livello intermedio con DocumentDB, è possibile usare anche [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) o [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).  Per configurare l'account del database di DocumentDB in modo da consentire l'accesso da macchine virtuali, gli indirizzi IP pubblici della macchina virtuale e/o dei set di scalabilità di macchine virtuali devono essere configurati come uno degli indirizzi IP consentiti per l'account del database di DocumentDB [contattando il supporto di Azure](#configure-ip-policy). È possibile recuperare gli indirizzi IP per le macchine virtuali nel portale di Azure, come mostrato nello screenshot seguente.

![Screenshot che mostra un indirizzo IP pubblico per una macchina virtuale visualizzata nel portale di Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Quando si aggiungono istanze di macchina virtuale al gruppo, a queste viene automaticamente fornito l'accesso all'account del database di DocumentDB.

## <a name="connections-from-the-internet"></a>Connessioni da Internet
Quando si accede a un account del database di DocumentDB da un computer in Internet, l'indirizzo IP o l'intervallo di indirizzi IP client del computer deve essere aggiunto all'elenco di indirizzi IP consentiti per l'account del database di DocumentDB. 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> Configurazione dei criteri di controllo di accesso IP
Usare il portale di Azure per inviare al [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) una richiesta per l'abilitazione dei criteri di controllo di accesso IP nell'account del database.

1. Nel pannello [Guida e supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) selezionare **Nuova richiesta di supporto**.
2. Nel pannello **Nuova richiesta di supporto** selezionare **Informazioni di base**.
3. Nel pannello **Informazioni di base** selezionare le opzioni seguenti:
   * **Tipo di problema**: Quota
   * **Sottoscrizione**: sottoscrizione associata all'account in cui aggiungere i criteri di controllo di accesso IP.
   * **Tipo di quota**: DocumentDB
   * **Piano di supporto**: Supporto per la quota - Incluso.
4. Nel pannello **Problema** eseguire le operazioni seguenti:
   * **Gravità**: selezionare C - Impatto minimo
   * **Dettagli**: copiare il testo seguente nella casella e includere i nomi degli account e gli indirizzi IP: "Vorrei abilitare il supporto del firewall per l'account del database di DocumentDB. Account database: *includere i nomi degli account*. Indirizzi IP consentiti: *includere gli indirizzi IP o l'intervallo di indirizzi IP in formato CIDR, ad esempio 13.91.6.132, 13.91.6.1/24*."
   * Fare clic su **Next**. 
5. Nel pannello **Informazioni contatto** immettere i dettagli di contatto e fare clic su **Crea**. 

Dopo la ricezione della richiesta, il controllo di accesso IP dovrebbe essere abilitato entro 24 ore. Dopo aver completato la richiesta, si riceve una notifica.

![Screenshot dei pannelli Guida e supporto](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![Screenshot del pannello Problema](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>Risoluzione dei problemi relativi ai criteri di controllo di accesso IP
### <a name="portal-operations"></a>Operazioni nel portale
Abilitando criteri di controllo di accesso IP per l'account del database di DocumentDB, qualsiasi accesso all'account del database di DocumentDB da computer non inclusi nell'elenco degli intervalli di indirizzi IP consentiti viene bloccato. Grazie a questo modello, viene bloccata anche l'esplorazione dell'operazione del piano dati dal portale per garantire l'integrità del controllo di accesso. 

### <a name="sdk--rest-api"></a>SDK e API REST
Per motivi di sicurezza, l'accesso tramite SDK o API REST da computer non inclusi nell'elenco degli indirizzi IP consentiti restituisce una risposta generica 404 Non trovato senza altri dettagli. Controllare l'elenco degli indirizzi IP consentiti per l'account del database di DocumentDB per verificare che all'account sia applicata la configurazione dei criteri corretta.

## <a name="next-steps"></a>Passaggi successivi
Per suggerimenti relativi alle prestazioni di rete, vedere [Suggerimenti sulle prestazioni per DocumentDB](documentdb-performance-tips.md).




<!--HONumber=Nov16_HO3-->


