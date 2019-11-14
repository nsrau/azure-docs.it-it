---
title: Provider di soluzioni cloud | Azure Marketplace
description: Gli editori possono ora vendere le proprie offerte tramite il canale del partner Microsoft Cloud Solution Provider (CSP).
services: Azure, Marketplace, Compute, Storage, Networking, Partner Center
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/12/2019
ms.author: v-chjen
ms.openlocfilehash: 546702af671cfe9506a4fc0448f40b7b8353960c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038726"
---
# <a name="cloud-solution-providers"></a>Cloud Solution Provider

Le offerte software possono raggiungere milioni di clienti Microsoft qualificati serviti da partner nel programma Cloud Solution Provider (CSP), oltre alla disponibilità pubblica delle offerte tramite le [Web StoreFront Microsoft](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace).

Gli editori configurano le offerte per la disponibilità nel programma CSP per un consenso esplicito, per una nuova offerta o per uno esistente, consentendo ai partner di vendere i prodotti e creare soluzioni in bundle per i clienti.

Gli editori sono responsabili del supporto delle interruzioni per i clienti finali e della fornitura di un meccanismo che consente ai partner del programma CSP e/o ai clienti di contattare l'utente per ottenere supporto. È consigliabile fornire ai partner del programma CSP le notifiche relative a documentazione, formazione e integrità dei servizi (come applicabile), in modo che i partner del programma CSP siano in grado di gestire le richieste di supporto di livello 1 dai clienti.  

Le offerte seguenti sono idonee per la vendita da parte dei partner del programma CSP:

- Offerte di transazione SaaS (software-as-a-Service)
- Macchine virtuali (VM)
- Modelli di soluzioni
- Applicazioni gestite

> [!NOTE]
> Per impostazione predefinita, i contenitori e gli SKU di VM Bring your own License (BYOL) vengono scelti per essere venduti dai partner del programma CSP.

## <a name="how-to-configure-an-offering"></a>Come configurare un'offerta

L'impostazione di consenso esplicito del programma CSP è configurata nel centro per i partner o nell'esperienza di creazione dell'offerta portale Cloud Partner. [Altre informazioni sulla modifica dell'esperienza di pubblicazione](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Consenso esplicito al centro per i partner

Nel centro per i partner troverai l'esperienza di consenso esplicito nel modulo del pubblico del rivenditore CSP.

![Destinatari del rivenditore CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Nel modulo del destinatario del rivenditore CSP sono disponibili tre opzioni:

- Opzione uno: qualsiasi partner nel programma CSP
- Opzione due: partner specifici nel programma CSP selezionato
- Opzione 3: nessun partner nel programma CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opzione uno: qualsiasi partner nel programma CSP

![Qualsiasi partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Se si sceglie questa opzione, tutti i partner del programma CSP saranno idonei per la rivendita dell'offerta ai clienti.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opzione due: partner specifici nel programma CSP selezionato

![Partner specifici nel programma CSP selezionato](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Se si sceglie questa opzione, si autorizzano i partner del programma CSP idonei a rivendere l'offerta.

Per autorizzare i partner, fare clic su **Seleziona partner CSP** e viene visualizzato un menu che consente di eseguire la ricerca in base al nome del partner o all'ID tenant del Azure Active Directory CSP (AAD).

![Selezionare il menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

È possibile applicare filtri di ricerca, ad esempio **paese**, **competenza**o **competenza**.

![Filtri per paesi, competenze e competenze per la ricerca di partner](media/marketplace-publishers-guide/csp-add-resellers.png)

Dopo aver scelto l'elenco dei partner, selezionare **Aggiungi**.

![Elenco di esempio di partner autorizzati nel programma CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Una tabella che mostra l'elenco dei partner selezionati viene visualizzata nella pagina dei destinatari del rivenditore CSP.

![Tabella con elenco di partner nella pagina del pubblico del rivenditore CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selezionare **Salva bozza** per registrare le modifiche.

Se questa offerta non è pubblicata, sarà necessario pubblicare l'offerta per renderla disponibile ai partner selezionati.

>[!NOTE]
>Se si autorizza un partner nel programma CSP in una determinata area, è possibile vendere l'offerta a qualsiasi cliente che appartiene a tale area specifica. Per ulteriori informazioni sul modo in cui le offerte CSP sono classificate in aree, vedere la pagina relativa ai [mercati e alla valuta del programma Cloud Solution Provider](https://docs.microsoft.com/partner-center/regional-authorization-overview) .

Se si sta aggiornando l'elenco CSP di un'offerta già pubblicata, aggiungere i partner aggiuntivi e selezionare **Sincronizza destinatari CSP**.

Se si dispone di un'offerta che dispone già di un elenco di partner autorizzati e si desidera utilizzare lo stesso elenco per un'altra offerta, utilizzare **importazione/esportazione**. Passare all'offerta con l'elenco CSP e selezionare **Esporta CSP**. La funzione sviluppa un file con estensione CSV che può essere importato in un'altra offerta.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opzione 3: nessun partner nel programma CSP

![Nessun partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Scegliendo questa opzione, l'offerta viene scelta al di fuori del programma CSP. È possibile modificare questa selezione in qualsiasi momento.

### <a name="cloud-partner-portal-opt-in"></a>Consenso esplicito portale Cloud Partner

In portale Cloud Partner, il consenso esplicito viene impostato nella scheda Marketplace o storefront. La possibilità di scegliere partner specifici nel programma CSP è disponibile solo nel centro per i partner.

![Esperienza di consenso esplicito CSP in CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Annullare l'autorizzazione dei partner nel programma CSP

Se è stato autorizzato un partner nel programma CSP e tale partner ha già rivenduto il prodotto ai clienti, non sarà consentito deautorizzare il partner.

Se un partner del programma CSP non ha venduto il prodotto ai clienti e si vuole rimuovere il CSP dopo la pubblicazione dell'offerta, usare le istruzioni seguenti:

1. Passare alla [pagina richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa pre-popolate.

2. Per **selezionare la versione del prodotto**, selezionare **gestione offerta dinamica**.
3. Per **selezionare una categoria che descriva meglio il problema**, scegliere la categoria che fa riferimento all'offerta.
4. Per **selezionare un problema che descrive meglio il problema**, selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per indirizzare alla **pagina dei dettagli del problema** per immettere ulteriori dettagli sul problema.
6. Usare **DEAUTORIZZAZIONE CSP** come titolo del problema e compilare il resto delle sezioni obbligatorie.




## <a name="navigate-between-options"></a>Spostarsi tra le opzioni

Utilizzare questa sezione per spostarsi tra le tre opzioni del rivenditore CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Passare da un'opzione all'altra: qualsiasi partner nel programma CSP

Se l'offerta è attualmente l' **opzione 1: qualsiasi partner nel programma CSP** e si vuole passare a una delle altre due opzioni, usare le istruzioni seguenti per creare una richiesta:

1. Passare alla [pagina richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa pre-popolate.

2. Per **selezionare la versione del prodotto**, selezionare **gestione offerta dinamica**.
3. Per **selezionare una categoria che descriva meglio il problema**, scegliere la categoria che fa riferimento all'offerta.
4. Per **selezionare un problema che descrive meglio il problema**, selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per indirizzare alla **pagina dei dettagli del problema** per immettere ulteriori dettagli sul problema.
6. Usare **DEAUTORIZZAZIONE CSP** come titolo del problema e compilare il resto delle sezioni obbligatorie.

> [!NOTE]
> Se si sta tentando di passare all'opzione due e un partner nel programma CSP ha già rivenduto l'offerta ai clienti, il partner è già per impostazione predefinita nell'elenco dei partner autorizzati.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Spostarsi tra due opzioni: partner specifici nel programma CSP selezionato

Se l'offerta è attualmente l' **opzione 2: partner specifici nel programma CSP selezionato** e si vuole passare all' **opzione uno: qualsiasi partner nel programma CSP**, usare le istruzioni seguenti per creare una richiesta:

1. Passare alla [pagina richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa pre-popolate.

2. Per **selezionare la versione del prodotto**, selezionare **gestione offerta dinamica**.
3. Per **selezionare una categoria che descriva meglio il problema**, scegliere la categoria che fa riferimento all'offerta.
4. Per **selezionare un problema che descrive meglio il problema**, selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per indirizzare alla **pagina dei dettagli del problema** per immettere ulteriori dettagli sul problema.
6. Usare **DEAUTORIZZAZIONE CSP** come titolo del problema e compilare il resto delle sezioni obbligatorie.

 Se l'offerta è attualmente l' **opzione 2: partner specifici nel programma CSP selezionato** e si vuole passare all' **opzione 3: nessun partner nel programma CSP**, sarà possibile passare a tale opzione solo se i partner del programma CSP precedentemente autorizzati non hanno rivenduto l'offerta ai clienti finali. Usare le istruzioni seguenti per creare una richiesta:

1. Passare alla [pagina richiesta di supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa pre-popolate.

2. Per **selezionare la versione del prodotto**, selezionare **gestione offerta dinamica**.
3. Per **selezionare una categoria che descriva meglio il problema**, scegliere la categoria che fa riferimento all'offerta.
4. Per **selezionare un problema che descrive meglio il problema**, selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per indirizzare alla **pagina dei dettagli del problema** per immettere ulteriori dettagli sul problema.
6. Usare **DEAUTORIZZAZIONE CSP** come titolo del problema e compilare il resto delle sezioni obbligatorie.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Spostarsi dall'opzione 3: nessun partner nel programma CSP

Se l'offerta è attualmente l' **opzione 3: nessun partner nel programma CSP**, è possibile passare a una delle altre due opzioni in qualsiasi momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Condivisione di materiali di vendita e supporto con partner nel programma CSP

Per consentire ai partner del programma Cloud Solution Provider di rappresentare in modo più efficace l'offerta e di interagire con l'organizzazione, è necessario inviare i materiali di vendita e supporto che saranno disponibili per i rivenditori. Queste risorse non verranno esposte ai clienti nelle vetrine del Marketplace.

### <a name="partner-center-csp-channel"></a>Canale CSP del centro per i partner

Se si è scelto il canale CSP nel centro per i partner, gli editori devono immettere un URL che ospita i materiali di marketing pertinenti e le informazioni di contatto del canale nel canale CSP sotto il modulo di elenco delle offerte:

![Informazioni collaterali CSP del centro per i partner](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Canale portale Cloud Partner CSP

Se è stato scelto il canale CSP in portale Cloud Partner, gli editori devono immettere un URL che ospita i materiali di marketing pertinenti e le informazioni di contatto del canale nel canale CSP:

![Informazioni sulle garanzie di portale Cloud Partner CSP](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Passaggi successivi

Visitare la [Guida dell'editore di Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Per altre informazioni sui servizi GTM del Marketplace, vedere [servizi di immissione sul mercato](https://partner.microsoft.com/reach-customers/gtm).

Accedere al centro per i [partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) o [portale cloud partner](https://cloudpartner.azure.com/) per creare e configurare l'offerta.
