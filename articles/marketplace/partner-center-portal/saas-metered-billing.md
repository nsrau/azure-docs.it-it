---
title: Fatturazione a consumo tramite il servizio di misurazione del marketplace Azure Marketplace
description: Questa documentazione è una guida per gli ISV che pubblicano le offerte SaaS con modelli di fatturazione flessibili.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281324"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Fatturazione a consumo tramite il servizio di misurazione del marketplace

Con il servizio di misurazione Marketplace, è possibile creare offerte software-as-a-service (SaaS) nel programma del mercato commerciale che vengono addebitate in base a unità non standard.  Prima di pubblicare questa offerta, è necessario definire le dimensioni di fatturazione, ad esempio larghezza di banda, ticket o messaggi di posta elettronica elaborati.  I clienti pagano quindi in base al consumo di queste dimensioni, con il sistema che informa Microsoft tramite l'API del servizio di misurazione Marketplace degli eventi fatturabili man mano che si verificano.  

## <a name="prerequisites-for-metered-billing"></a>Prerequisiti per la fatturazione a consumoPrerequisites for metered billing

Affinché un'offerta SaaS utilizzi la fatturazione a consumo, deve:

* Soddisfare tutti i requisiti dell'offerta per [un'offerta Vendere tramite Microsoft](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) come descritto in [Creare un'offerta SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integrati con le [API SaaS Fulfillment](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) per i clienti per il provisioning e la connessione alla tua offerta.  
* Essere configurato per il modello di determinazione **forfettaria** per la ricarica dei clienti per il servizio.  Le dimensioni sono un'estensione facoltativa del modello di determinazione tariffaria forfettaria. 
* Integrazione con le API del [servizio di misurazione Marketplace](./marketplace-metering-service-apis.md) per informare Microsoft degli eventi fatturabili.

>[!Note]
>Il servizio di misurazione Marketplace è disponibile solo per il modello di fatturazione forfettario e non si applica al modello di fatturazione per utente.

## <a name="how-metered-billing-fits-in-with-pricing"></a>In che modo la fatturazione a consumo si adatta ai prezzi

Quando si tratta di definire l'offerta insieme ai suoi modelli di prezzo, è importante comprendere la gerarchia dell'offerta.

* Ogni offerta SaaS è configurata per la vendita tramite Microsoft o meno.  Questa impostazione non può essere modificata dopo la pubblicazione di un'offerta.
* Ogni offerta SaaS, configurata per la vendita tramite Microsoft, può avere uno o più piani. Un utente sottoscrive l'offerta SaaS, ma viene acquistato tramite Microsoft nel contesto di un piano.
* A ogni piano è associato un modello di determinazione dei prezzi: **tariffa forfettaria** o **per utente.** Tutti i piani in un'offerta devono essere associati allo stesso modello di determinazione dei prezzi. Ad esempio, non può essere presente un'offerta in cui uno dei suoi piani è il modello di determinazione dei prezzi a tariffa fissa e un altro è per modello di determinazione dei prezzi utente.
* All'interno di ogni piano configurato per un modello di fatturazione forfettario, è inclusa almeno una commissione ricorrente (che può essere pari a 0 USD):
    * Tariffa **mensile** ricorrente: tariffa mensile fissa prepagata con ricorrenza mensile quando l'utente acquista il piano.
    * Tassa **annuale** ricorrente: tariffa annuale forfettaria che viene prepagata in caso di ricorrenza annuale quando l'utente acquista il piano.
* Oltre alle commissioni ricorrenti, il piano può includere anche dimensioni facoltative utilizzate per addebitare ai clienti l'utilizzo non incluso nella tariffa forfettaria.   Ogni dimensione rappresenta un'unità fatturabile che il servizio comunicherà a Microsoft tramite l'API del [servizio di misurazione Marketplace.](./marketplace-metering-service-apis.md)

## <a name="sample-offer"></a>Esempio di offerta

Ad esempio, Contoso è un editore con un servizio SaaS denominato Contoso Notification Services (CNS). CNS consente ai clienti di inviare notifiche via e-mail o SMS. Contoso è registrato come editore nel Centro per i partner per il programma del marketplace commerciale per pubblicare offerte ai clienti di Azure.Contoso is registered as a publisher in Partner Center for the commercial marketplace program to publish offers to Azure customers.  Ci sono due piani associati al CNS, descritti di seguito:

* Piano di base
    * Invia 10000 e-mail e 1000 messaggi per 0 USD/mese
    * Al di là delle 10000 e-mail, paga 1 USD ogni 100 e-mail
    * Oltre i 1000 testi, paga 0,02 USD per ogni testo
* Piano Premium
    * Invia 50000 e-mail e 10000 testi per 350 dollari al mese
    * Al di là delle 50000 e-mail, paga 0,5 dollari ogni 100 e-mail
    * Oltre i 10000 testi, paga 0,01 USD per ogni testo

Un cliente di Azure che sottoscrive il servizio CNS sarà in grado di inviare la quantità di testo e messaggi di posta elettronica inclusi al mese in base al piano selezionato.  Contoso misura l'utilizzo fino alla quantità inclusa senza inviare eventi di uso a Microsoft.  Quando i clienti consumano più della quantità inclusa, non devono modificare i piani o fare nulla di diverso.  Contoso misurerà l'esadio oltre la quantità inclusa e inizierà a emettere eventi di uso a Microsoft per un utilizzo aggiuntivo tramite l'API del servizio di [misurazione Marketplace.](./marketplace-metering-service-apis.md)  Microsoft a sua volta addebiterà al cliente l'utilizzo aggiuntivo specificato dall'editore.

## <a name="billing-dimensions"></a>Dimensioni di fatturazione

Le dimensioni di fatturazione vengono utilizzate per comunicare al cliente come verranno fatturate utilizzando il software e anche per comunicare gli eventi di utilizzo a Microsoft. Essi sono definiti come segue:

* **Identificatore di dimensione:** l'identificatore non modificabile a cui viene fatto riferimento durante l'emissione di eventi di uso.
* **Nome dimensione**: il nome visualizzato associato alla dimensione, ad esempio "messaggi di testo inviati".
* **Unità di misura**: la descrizione dell'unità di fatturazione, ad esempio "per messaggio di testo" o "per 100 e-mail".
* **Prezzo per unità:** il prezzo di un'unità della dimensione.  
* **Quantità inclusa per il termine mensile:** quantità di dimensione inclusa al mese per i clienti che pagano il canone mensile ricorrente, deve essere un numero intero.
* **Quantità inclusa per il periodo annuale**: quantità di dimensione inclusa al mese per i clienti che pagano la tariffa annuale ricorrente, deve essere un numero intero.

Le dimensioni di fatturazione vengono condivise in tutti i piani per un'offerta.  Alcuni attributi si applicano alla dimensione in tutti i piani e altri attributi sono specifici del piano.

Gli attributi che definiscono la dimensione stessa vengono condivisi tra tutti i piani per un'offerta.  Prima di pubblicare l'offerta, una modifica apportata a questi attributi dal contesto di qualsiasi piano influirà sulla definizione della dimensione in tutti i piani.  Una volta pubblicata l'offerta, questi attributi non saranno più modificabili.  Gli attributi sono:

* Identificatore
* Nome
* Unità di misura

Gli altri attributi di una dimensione sono specifici per ogni piano e possono avere valori diversi da piano a piano.  Prima di pubblicare il piano è possibile modificare questi valori e solo questo piano sarà interessato.  Una volta pubblicato il piano, questi attributi non saranno più modificabili.  Gli attributi sono:

* Prezzo unitario
* Quantità inclusa per i clienti mensili 
* Quantità inclusa per i clienti annuali 

Le dimensioni hanno anche due concetti speciali, "abilitati" e "infiniti":

* **Abilitato** indica che il piano partecipa a questa dimensione.  È possibile lasciare questa opzione deselezionata se si sta creando un nuovo piano che non invia eventi di uso in base a questa dimensione.  Inoltre, tutte le nuove dimensioni aggiunte dopo la prima pubblicazione di un piano verranno visualizzate come "non abilitate" nel piano già pubblicato.  Una dimensione disabilitata non verrà visualizzata in alcun elenco di dimensioni per un piano visualizzato dai clienti.
* **Infinito**, rappresentato dal simbolo di infinito "-", indica che questo piano partecipa a questa dimensione, ma non misura l'utilizzo rispetto a questa dimensione.  Se si desidera indicare ai clienti che la funzionalità rappresentata da questa dimensione è inclusa nel piano, ma senza limiti di utilizzo.  Una dimensione con utilizzo infinito verrà visualizzata negli elenchi di dimensioni per un piano visualizzato dai clienti, con l'indicazione che non verrà mai addebitato alcun costo per questo piano.

>[!Note] 
>Gli scenari seguenti sono supportati in modo esplicito:The following scenarios are explicitly supported: <br> - È possibile aggiungere una nuova dimensione a un nuovo piano.  La nuova dimensione non verrà abilitata per i piani già pubblicati. <br> - È possibile pubblicare un piano **forfettario** senza dimensioni, quindi aggiungere un nuovo piano e configurare una nuova dimensione per tale piano. La nuova dimensione non sarà abilitata per i piani già pubblicati.

## <a name="constraints"></a>Vincoli

### <a name="trial-behavior"></a>Comportamento di prova

La fatturazione a consumo tramite il servizio di misurazione del marketplace non è compatibile con l'offerta di una versione di valutazione gratuita.  Non è possibile configurare un piano per l'utilizzo sia della fatturazione a consumo che di una versione di valutazione gratuita.

### <a name="locking-behavior"></a>Comportamento di blocco

Poiché una dimensione utilizzata con il servizio di misurazione Marketplace rappresenta una comprensione del modo in cui un cliente pagherà per il servizio, tutti i dettagli per una dimensione non sono più modificabili dopo la pubblicazione.  È importante che le dimensioni siano completamente definite per un piano prima della pubblicazione.
  
Una volta pubblicata un'offerta con una dimensione, i dettagli a livello di offerta per tale dimensione non possono più essere modificati:

* Identificatore
* Nome
* Unità di misura

Una volta pubblicato un piano, i dettagli a livello di piano non possono più essere modificati:Once a plan is published, the plan-level details can no longer be changed:

* Prezzo unitario
* Quantità inclusa per la durata mensile
* Quantità inclusa per il periodo annuale
* Se la dimensione è abilitata per il piano

### <a name="upper-limits"></a>Limiti superiori

Il numero massimo di dimensioni configurabili per una singola offerta è 18 dimensioni univoche.

## <a name="get-support"></a>Supporto

Se si dispone di uno dei seguenti, è possibile aprire un ticket di supporto.

* Problemi tecnici con l'API del servizio di misurazione del marketplace.
* Un problema che deve essere riassegnato a causa di un errore o di un bug sul lato (ad es. evento di utilizzo errato).
* Eventuali altri problemi relativi alla fatturazione a consumo. 

Per inviare il ticket di supporto, procedi nel seguente modo:

1. Andare alla [pagina del supporto](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). I primi menu a discesa vengono compilati automaticamente. Per il supporto di Marketplace, identificare la famiglia di prodotti come **Cloud e servizi online**, il prodotto come Marketplace **Publisher**.  Non modificare le selezioni precompilate del menu a discesa.
2. In "Seleziona la versione del prodotto", seleziona **Gestione offerte dal**vivo .
3. In "Selezionare una categoria che meglio descrive il problema", scegli **App SaaS.**
4. In "Selezionare un problema che meglio descrive il problema", selezionare **fatturazione a consumo**.
5. Selezionando il pulsante **Avanti,** si verrà indirizzati alla pagina **Dettagli problema,** in cui è possibile immettere ulteriori dettagli sul problema.

Per altre opzioni di supporto per gli editori, vedere [Supporto per il programma del marketplace commerciale nel Centro per i partner.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere API del [servizio di controllo Marketplace.See Marketplace metering service APIs](./marketplace-metering-service-apis.md) for more information.
