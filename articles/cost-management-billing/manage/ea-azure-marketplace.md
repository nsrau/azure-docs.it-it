---
title: Azure Marketplace
description: Descrive in che modo i clienti EA possono utilizzare Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: banders
ms.openlocfilehash: 73c9e011359d2f86ec9f91d7e1e9b2869e68c794
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648643"
---
# <a name="azure-marketplace"></a>Azure Marketplace

Questo articolo illustra in che modo clienti e partner EA possono visualizzare gli addebiti di marketplace e abilitare gli acquisti in Azure Marketplace.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace per i clienti con Contratto Enterprise

Per i clienti diretti, gli addebiti per Azure Marketplace sono visibili in Azure Enterprise Portal. Tutti gli acquisti e l'utilizzo di Azure Marketplace vengono fatturati al di fuori dell'impegno monetario con cadenza trimestrale o mensile e in modo posticipato.

Per i clienti indiretti, le sottoscrizioni di Azure Marketplace sono disponibili nella pagina **Gestisci sottoscrizioni** di Azure Enterprise Portal, ma i prezzi sono nascosti. I clienti devono contattare il proprio Licensing Solutions Provider (LSP) per informazioni sugli addebiti per Azure Marketplace.

I nuovi acquisti periodici in Azure Marketplace mensili o annuali vengono fatturati a prezzo intero durante il periodo di acquisto degli elementi di Azure Marketplace. Per questi elementi verrà eseguito il rinnovo automatico nel periodo seguente nello stesso giorno dell'acquisto originario.

Gli addebiti periodici mensili esistenti continueranno a essere rinnovati il primo giorno di ogni mese di calendario. Gli addebiti annuali verranno rinnovati alla ricorrenza annuale della data di acquisto.

Alcuni servizi di rivenditori di terze parti disponibili in Azure Marketplace ora utilizzano il saldo dell'impegno monetario del Contratto Enterprise (EA). In precedenza questi servizi venivano addebitati al di fuori dell'impegno monetario EA e venivano fatturati separatamente. L'impegno monetario EA per questi servizi in Azure Marketplace semplifica la gestione degli acquisti e dei pagamenti dei clienti. Per un elenco completo dei servizi che ora utilizzano l'impegno monetario, vedere l'[aggiornamento del 6 marzo 2018 nel sito Web di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partner

I Licensing Solutions Provider (LSP) possono scaricare un listino prezzi di Azure Marketplace dalla pagina dell'elenco prezzi in Azure Enterprise Portal. Selezionare il collegamento per il **listino prezzi di Marketplace** in alto a destra. Il listino prezzi di Azure Marketplace elenca tutti i servizi disponibili e i relativi prezzi.

Per scaricare il listino prezzi:

1. In Azure Enterprise Portal scegliere **Report** > **Elenco prezzi**.
1. Nell'angolo superiore destro trovare il collegamento per il listino prezzi di Azure Marketplace sotto il proprio nome utente.
1. Fare clic con il pulsante destro del mouse sul collegamento e selezionare **Salva oggetto con nome**.
1. Nella finestra **Salva** modificare il titolo del documento in `AzureMarketplacePricelist.zip` in modo che il file XLSX venga cambiato in file ZIP.
1. Al termine del download, si avrà a disposizione un file ZIP con i listini prezzi specifici del paese.
1. Gli LSP devono fare riferimento al file del singolo paese per individuare i prezzi specifici del paese in questione. I Licensing Solutions Provider (LSP) possono usare la scheda **Notifiche** per visualizzare gli SKU nuovi o ritirati.
1. Le variazioni di prezzo si verificano raramente. I Licensing Solutions Provider (LSP) ricevono 30 giorni prima tramite posta elettronica le notifiche relative agli aumenti dei prezzi e alle modifiche del tasso di cambio.
1. I Licensing Solutions Provider (LSP) ricevono una fattura per registrazione, per ISV, per trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Abilitazione degli acquisti in Azure Marketplace

Gli amministratori aziendali hanno la possibilità di disabilitare o abilitare gli acquisti in Azure Marketplace per tutte le sottoscrizioni di Azure incluse nella registrazione. Se l'amministratore aziendale disabilita gli acquisti e sono presenti sottoscrizioni di Azure che hanno già sottoscrizioni di Azure Marketplace, queste sottoscrizioni di Azure Marketplace non verranno annullate o non saranno interessate.

Sebbene i clienti possano convertire le sottoscrizioni di Azure dirette al Contratto Enterprise di Azure associandole alla relativa registrazione in Azure Enterprise Portal, questa azione non converte automaticamente le sottoscrizioni figlio.

Per abilitare gli acquisti in Azure Marketplace:

1. Accedere ad Azure Enterprise Portal come amministratore aziendale.
1. Passare a **Gestisci**.
1. In **Dettagli registrazione** selezionare l'icona matita accanto alla voce **Azure Marketplace**.
1. Selezionare o deselezionare **Abilitato/Disabilitato** o **Solo SKU di tipo Gratuito/BYOL** in base alle esigenze.
1. Selezionare **Salva**.

> [!NOTE]
> L'opzione BYOL (Bring Your Own License) e Gratuito limita l'acquisto e l'acquisizione degli SKU di Azure Marketplace ai soli SKU di tipo BYOL e Gratuito.

### <a name="services-billed-hourly-for-azure-ea"></a>Servizi fatturati su base oraria per il Contratto Enterprise di Azure

I servizi seguenti vengono fatturati su base oraria secondo il Contratto Enterprise anziché in base alla tariffa mensile di MOSP:

- Application Delivery Network
- Web application firewall

### <a name="azure-remoteapp"></a>Azure RemoteApp

Se si ha un Contratto Enterprise, l'addebito per Azure RemoteApp è basato sul livello di prezzo del Contratto Enterprise. Non sono previsti addebiti aggiuntivi. Il prezzo standard include 40 ore iniziali. Il prezzo illimitato include 80 ore iniziali. RemoteApp interrompe il calcolo dell'utilizzo oltre le 80 ore.

## <a name="azure-marketplace-faq"></a>Domande frequenti su Azure Marketplace

Questa sezione descrive in che modo l'impegno monetario di Azure potrebbe essere applicato ad alcuni servizi di rivenditori di terze parti in Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Che cosa è cambiato nei servizi di Azure Marketplace e nell'impegno monetario del Contratto Enterprise?

A partire dal 1° marzo 2018 alcuni servizi di rivenditori di terze parti utilizzano l'impegno monetario (MC) del Contratto Enterprise. Ad eccezione delle istanze di macchine virtuali riservate (RI) di Azure, i servizi venivano conteggiati in precedenza al di fuori dell'impegno monetario del Contratto Enterprise e fatturati separatamente.

L'uso dell'impegno monetario (MC) è stato esteso ad alcuni servizi di Azure Marketplace pubblicati di terze parti che vengano acquistati con maggiore frequenza. L'impegno monetario del Contratto Enterprise per questi servizi in Azure Marketplace semplifica la gestione degli acquisti e dei pagamenti.

### <a name="why-did-we-make-this-change"></a>Perché è stata apportata questa modifica?

I clienti cercano sempre altri modi per utilizzare il pagamento anticipato dell'impegno monetario. Questa modifica è stata richiesta spesso dai clienti e interessa una gran parte dei clienti di Azure Marketplace.

### <a name="how-do-you-benefit"></a>Quali sono i vantaggi?

L'esperienza di fatturazione è più semplice ed è possibile utilizzare l'impegno monetario del Contratto Enterprise. Poiché questi servizi sono inclusi nell'impegno monetario prepagato, l'impegno monetario del Contratto Enterprise acquisisce maggior valore.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Quali servizi di Azure Marketplace utilizzano l'impegno monetario del Contratto Enterprise e come viene informato il cliente?

Quando si acquista un servizio che utilizza l'impegno monetario, Azure Marketplace visualizza una dichiarazione di non responsabilità. Sono supportati alcuni servizi pubblicati da Red Hat, SUSE, Autodesk e Oracle. Attualmente, i servizi con denominazione simile pubblicati da altre parti non vengono detratti dall'impegno monetario. Alla fine di queste domande frequenti è disponibile un elenco completo.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Cosa accade se si esaurisce l'impegno monetario del Contratto Enterprise?

Se è stato utilizzato tutto l'impegno monetario e si è in condizione di eccedenza, gli addebiti relativi a questi servizi saranno inclusi nella successiva fattura per eccedenza insieme a eventuali altri servizi a consumo. Prima della modifica del 1° marzo 2018, questi addebiti venivano fatturati con gli altri servizi di Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Perché l'utilizzo dell'impegno monetario del Contratto Enterprise non viene abilitato per tutti i servizi di Azure Marketplace?

Stiamo lavorando spesso per offrire la miglior esperienza cliente correlata all'impegno monetario del Contratto Enterprise. Questa modifica riguarderà un numero elevato di clienti e una parte significativa della spesa totale in Azure Marketplace. In futuro potrebbero venire aggiunti altri servizi.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Quali saranno le differenze per la registrazione e i partner indiretti?

Non ci sono variazioni per i partner o i clienti con registrazione indiretta. Questi servizi sono soggetti alle stesse possibilità di ricarico dei partner a cui sono sottoposti altri servizi a consumo. L'unico cambiamento è costituito dall'inserimento degli addebiti in una fattura diversa e dal pagamento degli addebiti attraverso l'impegno monetario del Contratto Enterprise del cliente.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>È disponibile un elenco dei servizi di Azure Marketplace che utilizzano l'impegno monetario del Contratto Enterprise?

Alcune offerte specifiche di Azure Marketplace possono usare i fondi dell'impegno monetario. Per un elenco completo dei prodotti che partecipano al programma, vedere i [servizi di terze parti che usano l'impegno monetario](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [prezzi](ea-pricing-overview.md).