---
title: Contratti Azure EA e modifiche
description: Questo articolo illustra l'impatto dei contratti Azure EA e delle relative modifiche su Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 4af0d92b724b3919cf6535910644cac20ce2a7e7
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901084"
---
# <a name="azure-ea-agreements-and-amendments"></a>Contratti Azure EA e modifiche

Questo articolo descrive l'impatto dei contratti Azure EA e delle relative modifiche influire sulle modalità di accesso, utilizzo e pagamento dei servizi di Azure.

## <a name="amendment-status"></a>Stato delle modifiche

Eventuali modifiche per i clienti di Azure devono essere gestite tramite il partner o il team degli account Microsoft ed essere elaborate tramite il centro operativo locale. Se si ritiene che una modifica non sia stata elaborata, rivolgersi al partner, al consulente software o al team degli account Microsoft.

## <a name="enrollment-provisioning-status"></a>Stato di provisioning della registrazione

La data di inizio di un nuovo impegno monetario è definita dalla data in cui è stato elaborato dal centro operativo locale. La data di inizio della copertura nell'ordine di acquisto in https://www.explore.ms indica l'inizio dell'impegno monetario. La data di inizio della copertura corrisponde a quella in cui l'impegno monetario viene visualizzato in Azure EA Portal.

## <a name="special-pricing-and-acd-amendments"></a>Prezzi speciali e modifiche dello Sconto per impegno in Azure

Per prezzi speciali e modifiche dello Sconto per impegno in Azure, è necessario contattare il team dell'account. Il team di Azure Ops può applicare lo Sconto per impegno in Azure dopo che una modifica è stata elaborata dal business desk e richiesta al team di Azure Ops. Lo Sconto per impegno in Azure viene applicato solo al mese corrente. Tutti i mesi precedenti vengono compensati con credito retrodatato. 

## <a name="commitment-not-added-to-enrollment"></a>Impegno non aggiunto alla registrazione

Quando un ordine di acquisto per un impegno monetario viene inviato dal partner ed elaborato dal centro operativo locale, dovrebbe comparire nella registrazione. Se non compare nella registrazione in Azure EA Portal, contattare il supporto tecnico di Azure per determinare il motivo.

Un impegno elaborato dopo il secondo giorno del mese viene visualizzato in Azure EA Portal il primo giorno del mese successivo.

## <a name="support-offer-not-provisioned"></a>Offerta di supporto senza provisioning

È possibile ordinare il supporto Standard o Pro-Direct acquistando SKU specifici del supporto tecnico. L'ordine è analogo a un ordine di acquisto di SKU monetari di Azure con contratto Enterprise Agreement. Ad esempio, 6QK-00001. I numeri degli SKU di supporto sono W6T-00002 (Pro-Direct) e W6T-00003 (Standard). Verificare le offerte di supporto correnti prima di acquistare SKU di supporto.

Per eseguire il provisioning dell'offerta di supporto, la registrazione deve includere almeno un account attivo.

 L'[offerta del piano di supporto tecnico per i contratti Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/) di Azure è disponibile per alcuni clienti.

## <a name="enrollment-status"></a>Stato della registrazione

A una registrazione possono essere associati uno dei valori di stato seguenti. Ogni valore determina la modalità di utilizzo e accesso a una registrazione. Lo stato della registrazione determina la fase in cui si trova la registrazione. Indica se è necessario attivare la registrazione prima di poterla usare oppure se il periodo iniziale è scaduto e viene addebitato l'eccedenza di utilizzo.

**Pending** (In sospeso): l'amministratore della registrazione deve accedere ad Azure EA Portal. Dopo l'accesso la registrazione passa allo stato **Active** (Attiva).

**Active** (Attiva): la registrazione è accessibile e utilizzabile. È possibile creare account e sottoscrizioni in Azure EA Portal. La registrazione rimane attiva fino alla data di fine del contratto Enterprise Agreement.

**Indefinite Extended Term** (Periodo di validità esteso a tempo indeterminato): questo stato si verifica al raggiungimento della data di fine del contratto Enterprise Agreement. Prima che la registrazione EA raggiunga la data di fine del contratto Enterprise Agreement, l'amministratore della registrazione deve decidere se:

- Rinnovare la registrazione aggiungendo impegno monetario aggiuntivo
- Trasferire la registrazione esistente a una nuova
- Eseguire la migrazione al programma Microsoft Online Subscription Program
- Confermare la disabilitazione di tutti i servizi associati alla registrazione

**Expired** (Scaduta): quando raggiunge la data di fine del contratto Enterprise Agreement, la registrazione EA scade. Il cliente EA è escluso dal periodo di validità esteso e tutti i relativi servizi vengono disabilitati.

A partire dal 1° agosto 2019 i nuovi moduli di rifiuto esplicito non sono accettati per i clienti commerciali di Azure. Tutte le registrazioni passano invece allo stato Indefinite Extended Term (Periodo di validità esteso a tempo indeterminato). Se si vuole interrompere l'uso dei servizi di Azure, chiudere la sottoscrizione nel [portale di Azure](https://portal.azure.com). In alternativa, il partner può inviare una richiesta di terminazione all'indirizzo https://www.explore.ms. Non sono previste modifiche per i clienti con tipi di contratto per enti pubblici.

**Transferred** (Trasferita): questo stato viene applicato alle registrazioni i cui account e servizi associati sono trasferiti a una nuova registrazione. Le registrazioni non vengono trasferite automaticamente se viene generato un nuovo numero di registrazione durante il rinnovo. Per il trasferimento automatico il numero di registrazione precedente deve essere incluso nella richiesta di rinnovo del cliente.

## <a name="price-protection-and-increasing-cost"></a>Protezione dei prezzi e aumento dei costi

I prezzi iniziali di un cliente corrispondono al prezzo bloccato garantito. Tale prezzo è protetto dai prezzi di vendita al dettaglio di Azure per le organizzazioni pubblicati al momento della completa esecuzione del contratto. Dopo la scadenza del periodo di registrazione iniziale, vengono applicati nuovamente i prezzi con pagamento in base al consumo.

Per visualizzare l'elenco prezzi negoziato in Azure EA Portal, passare a **Report** > fare clic su **Elenco prezzi** > **Scarica**.

I clienti indiretti devono contattare il partner per ottenere l'elenco prezzi e informazioni dettagliate sui prezzi.

## <a name="partner-markup"></a>Ricarico del partner

In Azure EA Portal la funzione Partner Price Markup (Ricarico prezzi partner) consente di ottimizzare la creazione di report sui costi per i clienti. Azure EA Portal mostra i prezzi e l'utilizzo configurati dai partner per i clienti.

Il ricarico consente agli amministratori dei partner di aggiungere una percentuale di ricarico ai contratti Enterprise Agreement indiretti. La percentuale di ricarico si applica a tutte le informazioni su servizi gestiti da Microsoft in Azure EA Portal, ad esempio tariffe dei contatori, impegni monetari e ordini. Dopo la pubblicazione del ricarico da parte del partner, il cliente potrà visualizzare i costi di Azure nel portale di Azure, ad esempio riepilogo dell'utilizzo, elenchi prezzi e report sull'utilizzo scaricati.

A partire da settembre 2019 i partner possono applicare il ricarico in qualsiasi momento durante un periodo di validità senza attendere la scadenza successiva del periodo di validità.

## <a name="enrollment-not-created"></a>Registrazione non creata

Il provisioning di una registrazione Azure EA viene effettuato dopo l'inserimento di un impegno monetario. Dopo che un ordine di acquisto di impegno monetario viene effettuato tramite il partner ed elaborato tramite il centro operativo, viene visualizzato in Azure EA Portal.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure EA Portal, vedere [Introduzione ad Azure EA Portal](billing-ea-portal-get-started.md).
- È consigliabile che gli amministratori di Azure EA Portal leggano [Amministrazione di Azure EA Portal](billing-ea-portal-administration.md) per informazioni sulle comuni attività amministrative.
