---
title: Che cos'è un test drive? Marketplace commerciale Microsoft
description: Spiegazione della funzionalità test drive del Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: 4f78aa110ed42e17db873b9593de62602aa4193f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911138"
---
# <a name="what-is-a-test-drive"></a>Che cos'è un test drive?

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di provare prima di acquistare, generando lead altamente qualificati e ottenendo una maggiore conversione. Un test drive porta il prodotto a vivere in uno scenario di implementazione reale. I clienti che provano il prodotto dimostrano un chiaro tentativo di acquistare una soluzione simile. Per sfruttare i vantaggi, è possibile usare lead più avanzati.

I clienti traggono vantaggio anche da un test drive. Consentendo loro di provare il prodotto per primo, si riduce la frizione del processo di acquisto. Inoltre, è stato eseguito il pre-provisioning di test drive, ovvero i clienti non devono scaricare, configurare o configurare il prodotto.

## <a name="how-does-it-work"></a>Come funziona?

Le unità di test sono istanze gestite che avviano la soluzione o l'applicazione su richiesta per i clienti che lo richiedono. Una volta assegnata un'istanza di test drive, è disponibile per l'utilizzo da parte del cliente per un periodo di tempo stabilito. Una volta terminato il periodo, viene eliminato per creare spazio per un altro cliente.

Come server di pubblicazione, è possibile gestire e configurare le impostazioni di test drive nel centro per i partner. I dettagli relativi alla configurazione tecnica variano a seconda del tipo di offerta. Per istruzioni dettagliate, vedere la pagina relativa alla [configurazione tecnica di test drive](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration).

I clienti potenziali possono individuare i test drive come CTA nell'offerta in [AppSource](https://appsource.microsoft.com/en-US/). Forniscono le informazioni di contatto e accettano le condizioni dell'offerta e l'informativa sulla privacy, quindi ottengono l'accesso all'ambiente preconfigurato per provarlo per un periodo di tempo fisso. I clienti ricevono una versione di valutazione pratica e autonoma delle funzionalità e dei vantaggi principali del prodotto e si riceve un vantaggio prezioso.

## <a name="types-of-test-drives"></a>Tipi di unità di test

Nel Marketplace commerciale sono disponibili diverse unità di test per le offerte Select a seconda del tipo di prodotto, scenario e Marketplace in uso:

- Azure Resource Manager
    - Applicazioni Azure
    - SaaS
    - Macchine virtuali
- test drive ospitata
    - Dynamics 365 for business Central (attualmente non supportato)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- App per la logica (solo in modalità di supporto)
- Power BI

Per informazioni dettagliate sulla configurazione di una di queste unità di test, vedere [configurazione tecnica di test drive](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration). 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Questo modello di distribuzione contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che rientrano in questo scenario usano solo risorse di Azure. Il test drive Azure Resource Manager è disponibile per i tipi di offerta seguenti: 

- Applicazioni di Azure
- SaaS
- Macchine virtuali

>[!NOTE]
>Questa è l'unica opzione test drive per le offerte di macchine virtuali e applicazioni Azure.

### <a name="hosted-test-drive-recommended"></a>Test drive ospitata (scelta consigliata)

Una test drive ospitata Elimina la complessità dell'installazione consentendo a Microsoft di ospitare e gestire il servizio che esegue il provisioning e il deprovisioning dell'utente test drive. Se si dispone di un'offerta in Microsoft AppSource, compilare il test drive per connettersi a un'istanza di Dynamics AX/CRM. È possibile usare i tipi di offerte AppSource seguenti:

- Usare [Dynamics 365 per il coinvolgimento dei clienti](partner-center-portal/create-new-customer-engagement-offer.md) per un sistema di engagement dei clienti, ad esempio vendite, servizi, servizi di progetto e servizi di campo.
- Usare [Dynamics 365 per le operazioni](partner-center-portal/create-new-operations-offer.md) per un sistema di pianificazione delle risorse aziendali finanziarie e operative, ad esempio finanza, operazioni e produzione, supply chain.

### <a name="logic-app-test-drive"></a>test drive app per la logica

Questo tipo di test drive non è ospitato da Microsoft e usa i modelli di Azure Resource Manager (ARM) per i tipi di offerta Dynamics AX/CRM. Sarà necessario eseguire il modello ARM per creare le risorse necessarie nella sottoscrizione di Azure. L'unità di test per l'app per la logica è attualmente in modalità di supporto e non è consigliata da Microsoft per informazioni dettagliate sulla configurazione di un'unità di test per la logica. vedere [configurazione tecnica di test drive](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration).

### <a name="power-bi-test-drive"></a>Test drive di Power BI

Si tratta semplicemente di un collegamento incorporato a un dashboard personalizzato. Tutti i prodotti che dimostrano solo un oggetto visivo Power BI interattivo devono usare questo tipo di test drive.

## <a name="transforming-examples"></a>Esempi di trasformazione

Il processo di trasformazione di un'architettura di risorse in un test drive può essere scoraggiante. Vedere questi esempi di come trasformare meglio le architetture correnti.

[Trasformare un modello di sito Web in un test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Trasformare un modello di macchina virtuale in un test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Trasformare un modello di Gestione risorse esistente in una test drive](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generare lead dal test drive

Un Marketplace commerciale test drive è un ottimo strumento per i marketers. È consigliabile incorporarla nelle attività di go-to-Market quando si avvia per generare più lead per l'azienda. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Se si chiude un'operazione con una test drive lead, assicurarsi di registrarla presso [Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Inoltre, saremmo lieti di ricevere informazioni sulle vittorie del cliente nel caso in cui un test drive abbia svolto un ruolo.

## <a name="other-resources"></a>Altre risorse

Risorse aggiuntive per i test drive:

- [Procedure consigliate per test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Panoramica](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (file con estensione pdf, assicurarsi che il blocco popup sia disattivato)

## <a name="next-step"></a>Passaggio successivo

- [Configurazione tecnica del test drive](test-drive-technical-configuration.md)
