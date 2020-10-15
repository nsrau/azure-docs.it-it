---
title: Domande frequenti sulla connettività di rete Analysis Services | Microsoft Docs
description: Questo articolo fornisce le risposte ad alcune delle domande più comuni su Analysis Services la connettività di rete.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82838502"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Domande frequenti sulla connettività di rete di Analysis Services

Questo articolo fornisce le risposte alle domande comuni sulla connessione a account di archiviazione, origini dati, firewall e indirizzi IP.

## <a name="backup-and-restore"></a>Backup e ripristino

**Domanda** : come è possibile eseguire il backup e il ripristino usando un account di archiviazione che si trova dietro un firewall?   
**Answer** -Azure Analysis Services non usa indirizzi IP fissi o tag di servizio. L'intervallo di indirizzi IP usati dai server Analysis Services può essere qualsiasi elemento nell'intervallo di indirizzi IP per l' *area di Azure*. Poiché gli indirizzi IP del server sono variabili e possono cambiare nel tempo, le regole del firewall devono consentire l'intero intervallo di indirizzi IP dell'area di Azure in cui si trova il server.

**Domanda** : l'account di archiviazione di Azure si trova in un'area diversa rispetto al server Analysis Services. Ricerca per categorie configurare le impostazioni del firewall dell'account di archiviazione?   
**Risposta** : se l'account di archiviazione si trova in un'area diversa, configurare le impostazioni del firewall dell'account di archiviazione per consentire l'accesso dalle **reti selezionate**. In **intervallo di indirizzi**del firewall specificare l'intervallo di indirizzi IP per l'area in cui si trova il server Analysis Services. Per ottenere gli intervalli IP per le aree di Azure, vedere [Azure IP Ranges and Service Tags-Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). La configurazione delle impostazioni del firewall dell'account di archiviazione per consentire l'accesso da tutte le reti è supportata, tuttavia è preferibile scegliere le reti selezionate e specificare un intervallo di indirizzi IP. 

**Domanda** : l'account di archiviazione di Azure si trova nella stessa area del server Analysis Services. Come è possibile configurare le impostazioni del firewall dell'account di archiviazione?   
**Risposta** : poiché il server di Analysis Services e l'account di archiviazione si trovano nella stessa area, le comunicazioni tra di essi usano intervalli di indirizzi IP interni, quindi la configurazione di un firewall per l'uso di reti selezionate e la specifica di un intervallo di indirizzi IP non sono supportate. Se i criteri dell'organizzazione richiedono un firewall, è necessario configurarlo per consentire l'accesso da tutte le reti.


## <a name="data-source-connections"></a>Connessioni alle origini dati

**Domanda** : ho un VNET per il mio sistema di origine dati. Come è possibile consentire ai server di Analysis Services di accedere al database dal VNET?   
**Risposta** : Azure Analysis Services non è in grado di partecipare a un vnet. La soluzione migliore consiste nell'installare e configurare un gateway dati locale in VNET, quindi configurare i server di Analysis Services con la proprietà del server **AlwaysUseGateway** . Per altre informazioni, vedere [usare il gateway per le origini dati in una rete virtuale di Azure (VNet)](analysis-services-vnet-gateway.md).

**Domanda** : ho un database di origine dietro a un firewall. Come è possibile configurare il firewall per consentire al server Analysis Services di accedervi?   
**Answer** -Azure Analysis Services non usa indirizzi IP fissi o tag di servizio. L'intervallo di indirizzi IP usati dai server Analysis Services può essere qualsiasi elemento nell'intervallo di indirizzi IP per l' *area di Azure*. È necessario fornire la *gamma completa* di indirizzi IP per l'area di Azure del server nelle regole del firewall del database di origine. Un'altra soluzione e possibilmente più sicura è la configurazione di un gateway dati locale. È quindi possibile configurare i server di Analysis Services con la [proprietà del server AlwaysUseGateway](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)e quindi assicurarsi che il gateway dati locale disponga di un indirizzo IP consentito dalle regole del firewall dell'origine dati.

## <a name="azure-apps-with-ip-address"></a>App di Azure con indirizzo IP

**Domanda** : si usa un'applicazione basata su Azure (ad esempio, funzioni di azure, Azure Data Factory) con un indirizzo IP che viene modificato in tempo reale. Come è possibile gestire le regole del firewall Azure Analysis Services per consentire dinamicamente l'indirizzo IP in cui è in esecuzione l'app?   
**Answer** -Azure Analysis Services non supporta collegamenti privati, reti virtuali o tag di servizio. Sono disponibili alcune soluzioni open source, ad esempio, https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) che rilevano l'indirizzo IP dell'applicazione client e aggiornano automaticamente e temporaneamente le regole del firewall.


## <a name="next-steps"></a>Passaggi successivi

[Installare e configurare un gateway dati locale](analysis-services-gateway-install.md)   
[Connessione a origini dati locali con gateway dati locale](analysis-services-gateway.md)   
[Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure (VNet)](analysis-services-vnet-gateway.md)
