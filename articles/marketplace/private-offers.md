---
title: Offerte private | Azure Marketplace
description: Offerte private in Azure Marketplace per gli editori di app e servizi.
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 11/1/2018
ms.author: pabutler
ms.openlocfilehash: dcba2b40bdcf4558f7a06f7e14d0ce654a9c1ec1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876070"
---
# <a name="private-offers"></a>Offerte private

Le offerte private in [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) consentono agli editori di creare SKU visibili solo a clienti specifici.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Incrementare le opportunità aziendali con le offerte private

I clienti aziendali usano sempre di più i marketplace online per trovare, provare e acquistare le soluzioni cloud. Con le offerte private, gli editori ora possono usare il marketplace per condividere privatamente con clienti specifici soluzioni personalizzate con le funzionalità richieste dalle aziende:

- I *prezzi concordati* consentono agli editori di estendere gli sconti e i prezzi fuori listino delle offerte disponibili pubblicamente.
- I *termini e condizioni privati* consentono agli editori di personalizzare termini e condizioni per un cliente specifico.
- Le *configurazioni specializzate* consentono agli editori di personalizzare l'offerta per macchine virtuali, applicazioni Azure e app SaaS in base alle esigenze di un singolo cliente. Questa opzione consente anche agli editori di fornire l'accesso in anteprima alle nuove funzionalità di un prodotto, prima di estendere il lancio a tutti i clienti.

Le offerte private consentono agli editori di sfruttare la scalabilità e la disponibilità globale di un marketplace pubblico, con la flessibilità e il controllo necessari per concordare e distribuire configurazioni e offerte personalizzate. Tutte queste funzionalità aprono la strada all'adozione in massa dei marketplace cloud da parte delle aziende.  Le aziende possono ora effettuare acquisti e vendite in base alle specifiche esigenze.

Le offerte private sono ora disponibili per le offerte di macchine virtuali, applicazioni Azure (implementate come modelli di soluzione o applicazioni gestite) e app SaaS. Come le offerte pubbliche, le offerte private possono essere create e gestite tramite il [portale Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  L'accesso alle offerte private può essere concesso o revocato ai clienti in pochi minuti.

## <a name="creating-private-offers-using-skus-and-plans"></a>Creazione di offerte private con piani e SKU

Per le *offerte nuove o esistenti con SKU o piani pubblici*, gli editori possono facilmente creare nuove varianti private creando nuovi SKU o piani e contrassegnandoli come privati.  I piani e gli [SKU privati](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) sono componenti di un'offerta e sono visibili e acquistabili solo per clienti specifici. I piani e gli SKU privati possono riutilizzare le immagini di base e/o i metadati delle offerte già pubblicati per un piano o uno SKU pubblico. Questa opzione consente agli editori di creare più varianti private di un'offerta pubblica senza dover pubblicare più versioni della stessa immagine di base e degli stessi metadati dell'offerta. Solo per le offerte di macchine virtuali e applicazioni Azure, quando uno SKU privato condivide un'immagine di base con uno SKU pubblico, tutte le modifiche apportate all'immagine di base dell'offerta verranno propagate in tutti gli SKU pubblici e privati che usano tale immagine di base.

Per le *nuove offerte che includono solo piani o SKU privati*, gli editori possono creare le offerte come qualsiasi altra offerta e quindi contrassegnare gli SKU o i piani come privati. Le offerte che includono solo piani o SKU privati non saranno individuabili o accessibili tramite [Azure Marketplace](https://azuremarketplace.microsoft.com) o il [portale di Azure](https://azure.microsoft.com/features/azure-portal/) per i clienti non associati all'offerta.

## <a name="targeting-customers-with-private-offers"></a>Selezione dei clienti per le offerte private
Per le offerte private sia nuove che esistenti, gli editori possono rivolgersi a determinati clienti usando gli identificatori di sottoscrizioni. Gli editori che usano un'offerta di macchine virtuali o applicazioni Azure possono limitare la disponibilità di uno SKU privato a un singolo ID sottoscrizione di Azure o caricare un file CSV contenente fino 20.000 ID sottoscrizione di Azure. Quando usano un'offerta privata di app SaaS, gli editori possono associare un ID sottoscrizione di Azure o un ID tenant per limitare la disponibilità di un piano privato, usando l'approccio manuale o il caricamento del file CSV.

Dopo che un'offerta è stata certificata e pubblicata, i clienti possono essere aggiornati o rimossi dallo SKU o dal piano in pochi minuti usando la funzionalità di sincronizzazione delle sottoscrizioni private. Questa funzionalità consente agli editori di aggiornare rapidamente e facilmente l'elenco di clienti a cui viene presentato il piano o lo SKU privato senza dover nuovamente certificare o pubblicare l'offerta.

## <a name="deploying-private-offers"></a>Distribuzione delle offerte private

Le offerte private possono essere individuate solo tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/) e non vengono presentate tramite [Azure Marketplace](https://azuremarketplace.microsoft.com). Dopo aver effettuato l'accesso al portale di Azure, i clienti possono selezionare l'elemento di navigazione nel Marketplace per accedere alle offerte private. Le offerte private verranno anche visualizzate nei risultati della ricerca e possono essere distribuite tramite la riga di comando e i modelli di Azure Resource Manager, come qualsiasi altra offerta.

![[Offerte private]](./media/marketplace-publishers-guide/private-offer.png)

Le offerte private verranno visualizzate anche nei risultati della ricerca. È sufficiente individuare la notifica "Private".

> [!Note]
> Le offerte private non sono supportate con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

## <a name="next-steps"></a>Passaggi successivi

Per sfruttare queste nuove funzionalità, è possibile iniziare a vendere in [Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
