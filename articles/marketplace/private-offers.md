---
title: Offerte Private Azure Marketplace
description: Offerte private in Azure Marketplace per gli editori di app e servizi.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681438"
---
# <a name="private-offers"></a>Offerte private

Le offerte private in [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) consentono agli editori di creare SKU visibili solo a clienti specifici.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Sblocca le offerte aziendali con le offerte private

I clienti aziendali usano sempre di più i marketplace online per trovare, provare e acquistare le soluzioni cloud. Con le offerte private, gli editori ora possono usare il marketplace per condividere privatamente con clienti specifici soluzioni personalizzate con le funzionalità richieste dalle aziende:

- I *prezzi concordati* consentono agli editori di estendere gli sconti e i prezzi fuori listino delle offerte disponibili pubblicamente.
- I *termini e condizioni privati* consentono agli editori di personalizzare termini e condizioni per un cliente specifico.
- Le *configurazioni specializzate* consentono agli editori di personalizzare l'offerta per macchine virtuali, applicazioni Azure e app SaaS in base alle esigenze di un singolo cliente. Questa opzione consente anche agli editori di fornire l'accesso in anteprima alle nuove funzionalità di un prodotto, prima di estendere il lancio a tutti i clienti.

Le offerte private consentono agli editori di sfruttare la scalabilità e la disponibilità globale di un marketplace pubblico, con la flessibilità e il controllo necessari per concordare e distribuire configurazioni e offerte personalizzate. Tutte queste funzionalità aprono la strada all'adozione in massa dei marketplace cloud da parte delle aziende. Le aziende possono ora effettuare acquisti e vendite in base alle specifiche esigenze.

Le offerte private sono ora disponibili per le offerte di macchine virtuali, applicazioni Azure (implementate come modelli di soluzione o applicazioni gestite) e app SaaS. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Creazione di offerte private utilizzando SKU e piani

Per le *offerte nuove o esistenti con SKU o piani pubblici*, gli editori possono facilmente creare nuove varianti private creando nuovi SKU o piani e contrassegnandoli come privati.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

I piani e gli SKU privati sono componenti di un'offerta e sono visibili e acquistabili solo per clienti specifici. I piani e gli SKU privati possono riutilizzare le immagini di base e/o i metadati delle offerte già pubblicati per un piano o uno SKU pubblico. Questa opzione consente agli editori di creare più varianti private di un'offerta pubblica senza dover pubblicare più versioni della stessa immagine di base e degli stessi metadati dell'offerta. Per la macchina virtuale e l'applicazione Azure offre solo, quando uno SKU privato condivide un'immagine di base con uno SKU pubblico, qualsiasi modifica all'immagine di base dell'offerta verrà propagata in tutti gli SKU pubblici e privati usando tale immagine di base.

Per le *nuove offerte che includono solo piani o SKU privati*, gli editori possono creare le offerte come qualsiasi altra offerta e quindi contrassegnare gli SKU o i piani come privati. Le offerte che dispongono solo di SKU o piani privati non saranno individuabili o accessibili tramite il marketplace commerciale Microsoft o il portale di [Azure](https://azure.microsoft.com/features/azure-portal/) da parte dei clienti non associati all'offerta.

## <a name="targeting-customers-with-private-offers"></a>Targeting dei clienti con offerte private

Per le offerte private sia nuove che esistenti, gli editori possono rivolgersi a determinati clienti usando gli identificatori di sottoscrizioni. Gli editori che usano un'offerta di macchine virtuali o applicazioni Azure possono limitare la disponibilità di uno SKU privato a un singolo ID sottoscrizione di Azure o caricare un file CSV contenente fino 20.000 ID sottoscrizione di Azure. Durante l'uso di un'offerta privata dell'app SaaS, gli editori possono associare un ID tenant per vincolare la disponibilità di un piano privato, usando l'approccio di caricamento manuale o CSV.

Dopo che un'offerta è stata certificata e pubblicata, i clienti possono essere aggiornati o rimossi dallo SKU o dal piano in pochi minuti usando la funzionalità di sincronizzazione delle sottoscrizioni private. Questa funzionalità consente ai publisher di aggiornare rapidamente e facilmente l'elenco dei clienti a cui viene presentato lo SKU privato senza certificare o pubblicare nuovamente l'offerta.

## <a name="deploying-private-offers"></a>Distribuzione di offerte private

Le offerte private sono individuabili solo tramite il portale di [Azure](https://azure.microsoft.com/features/azure-portal/) e non vengono visualizzate in [Microsoft AppSource](https://appsource.microsoft.com/) o [Azure Marketplace.](https://azuremarketplace.microsoft.com) Per ulteriori informazioni sulla pubblicazione nelle diverse vetrine del marketplace commerciale, vedere [Determinare l'opzione](./determine-your-listing-type.md)di pubblicazione .

Dopo aver effettuato l'accesso al portale di Azure, i clienti possono selezionare l'elemento di navigazione nel Marketplace per accedere alle offerte private. Le offerte private verranno visualizzate anche nei risultati della ricerca e possono essere distribuite tramite la riga di comando e i modelli di Azure Resource Manager come qualsiasi altra offerta.

![[Offerte private]](./media/marketplace-publishers-guide/private-offer.png)

Le offerte private verranno visualizzate anche nei risultati della ricerca. Basta cercare il distintivo "Privato".

>[!Note]
>Le offerte private non sono supportate con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->