---
title: Fornitori di soluzioni cloud Azure Marketplace
description: Gli editori possono ora vendere le loro offerte tramite il canale partner Di Microsoft Cloud Solution Provider (CSP).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: c4c8f21234cfbf040d57c0e1c8dbecbb698d7d50
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685916"
---
# <a name="cloud-solution-providers"></a>Cloud Solution Provider

Le offerte di software possono raggiungere milioni di clienti Microsoft qualificati serviti da partner del programma Cloud Solution Provider (CSP), oltre alla disponibilità pubblica delle offerte tramite [Microsoft web storefronts](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace).

Gli editori configurano le offerte per la disponibilità nel programma CSP su base opt-in, per una nuova offerta o una esistente, consentendo ai partner di vendere i tuoi prodotti e creare soluzioni in bundle per i clienti.

Gli editori sono responsabili della fornitura del supporto break-fix ai clienti finali e della fornitura di un meccanismo per i partner del programma CSP e/o dei clienti per contattarti per il supporto. È consigliabile fornire ai partner del programma CSP la documentazione utente, la formazione e le notifiche di integrità/interruzione del servizio (se applicabile) in modo che i partner del programma CSP siano attrezzati per gestire le richieste di supporto di livello 1 da parte dei clienti.  

Le seguenti offerte possono essere rifiutate di essere rifiutate per essere vendute dai partner nel programma CSP:

- Offerte di transazioni Software-as-a-Service (SaaS)
- Macchine virtuali (VM)
- Modelli di soluzioni
- Applicazioni gestite

> [!NOTE]
> I contenitori e gli SKU per vm (BYOL) sono optati per essere venduti dai partner nel programma CSP per impostazione predefinita.

## <a name="how-to-configure-an-offering"></a>Come configurare un'offerta

L'impostazione di consenso esplicito del programma CSP è configurata nel Centro per i partner o nel portale Cloud Partner offre un'esperienza di creazione. [Ulteriori informazioni sulla modifica dell'esperienza dell'editore](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Opt-in per il Centro per i partner

Nel Centro per i partner, troverai l'esperienza di consenso esplicito nel modulo Pubblico rivenditore CSP.

![Destinatari del rivenditore CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Nel modulo Pubblico rivenditore CSP sono disponibili tre opzioni tra cui scegliere:

- Opzione uno: Qualsiasi partner nel programma CSP
- Opzione due: partner specifici del programma CSP che seleziono
- Opzione tre: Nessun partner nel programma CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opzione uno: Qualsiasi partner nel programma CSP

![Qualsiasi partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Scegliendo questa opzione, tutti i partner del programma CSP sono idonei a rivendere la tua offerta ai loro clienti.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opzione due: partner specifici del programma CSP che seleziono

![Partner specifici del programma CSP che seleziono](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Scegliendo questa opzione, autorizzi i partner del programma CSP idonei a rivendere la tua offerta.

Per autorizzare i partner, fare clic su **Seleziona partner CSP** e viene visualizzato un menu che consente di eseguire la ricerca in base al nome del partner o all'ID tenant di Azure Active Directory (AAD).

![Selezionare il menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

È possibile applicare filtri di ricerca, ad esempio **Paese,** Competenza o **Competenza.** **Skill**

![Filtri per paese, competenza e competenze per la ricerca di partner](media/marketplace-publishers-guide/csp-add-resellers.png)

Dopo aver scelto l'elenco dei partner, selezionare **Aggiungi**.

![Elenco di esempio di partner autorizzati nel programma CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Nella pagina Pubblico del rivenditore CSP viene visualizzata una tabella che mostra l'elenco dei partner selezionati.

![Tabella con l'elenco dei partner nella pagina del gruppo di destinatari CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selezionare **Salva bozza** per registrare le modifiche.

Se questa offerta non è pubblicata, dovrai pubblicare l'offerta per renderla disponibile ai partner selezionati.

>[!NOTE]
>Se autorizzi un partner nel programma CSP in una determinata regione, questi può vendere l'offerta a qualsiasi cliente appartenente a quella particolare regione. Per ulteriori informazioni su come le offerte CSP sono classificate nelle aree geografiche, vedere [Cloud Solution Provider](https://docs.microsoft.com/partner-center/regional-authorization-overview) program.

Se si sta aggiornando l'elenco CSP di un'offerta già pubblicata, aggiungere i partner aggiuntivi e **selezionare Sincronizza gruppo**di destinatari CSP .

Se hai un'offerta che ha già un elenco di partner autorizzati e desideri utilizzare lo stesso elenco per un'altra offerta, usa **Importa/Esporta**. Passare all'offerta contenente l'elenco CSP e selezionare **Esporta CSP**. La funzione sviluppa un file CSV che può essere importato in un'altra offerta.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opzione tre: Nessun partner nel programma CSP

![Nessun partner nel programma CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Scegliendo questa opzione, stai scegliendo la tua offerta dal programma CSP. È possibile modificare questa selezione in qualsiasi momento.

### <a name="cloud-partner-portal-opt-in"></a>Consenso esplicito per il portale per i partner cloud

Nel portale Cloud Partner, l'opzione di consenso esplicito è impostata nella scheda Marketplace o Storefront. La possibilità di scegliere partner specifici nel programma CSP è disponibile solo nel Centro per i partner.

![Esperienza di consenso esplicito CSP in CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Rimuovere l'autorizzazione dei partner nel programma CSP

Se hai autorizzato un partner nel programma CSP e tale partner ha già rivenduto il prodotto ai loro clienti, non ti sarà consentito rimuovere l'autorizzazione di tale partner.

Se un partner del programma CSP non ha venduto il prodotto ai propri clienti e desideri rimuovere il CSP dopo la pubblicazione dell'offerta, segui le istruzioni seguenti:

1. Passare alla [pagina Richiesta supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa precompilate.

2. In **Selezionare la versione del prodotto**selezionare Gestione offerte **dinamiche**.
3. Per **Selezionare una categoria che meglio descrive il problema,** scegliere la categoria che fa riferimento all'offerta.
4. Per **Selezionare un problema che meglio descrive il problema,** selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per essere indirizzati alla **pagina Dettagli problema** per immettere ulteriori dettagli sul problema.
6. Utilizzare **Deauthorize CSP** come titolo del problema e compilare il resto delle sezioni richieste.




## <a name="navigate-between-options"></a>Spostarsi tra le opzioni

Utilizzare questa sezione per passare tra le tre opzioni del rivenditore CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Passare dall'opzione uno: Qualsiasi partner nel programma CSP

Se la tua offerta è attualmente **L'opzione 1: qualsiasi partner nel programma CSP** e desideri passare a una delle altre due opzioni, segui le istruzioni seguenti per creare una richiesta:

1. Passare alla [pagina Richiesta supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa precompilate.

2. In **Selezionare la versione del prodotto**selezionare Gestione offerte **dinamiche**.
3. Per **Selezionare una categoria che meglio descrive il problema,** scegliere la categoria che fa riferimento all'offerta.
4. Per **Selezionare un problema che meglio descrive il problema,** selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per essere indirizzati alla **pagina Dettagli problema** per immettere ulteriori dettagli sul problema.
6. Utilizzare **Deauthorize CSP** come titolo del problema e compilare il resto delle sezioni richieste.

> [!NOTE]
> Se stai cercando di passare all'opzione due e un partner nel programma CSP ha già rivenduto l'offerta ai loro clienti, per impostazione predefinita, tale partner è già presente nell'elenco dei partner autorizzati.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Passare dall'opzione due: partner specifici nel programma CSP selezionato

Se la tua offerta è attualmente **Opzione 2: Partner specifici del programma CSP che seleziona** e desideri passare all'Opzione **uno: Qualsiasi partner nel programma CSP**, utilizza le seguenti istruzioni per creare una richiesta:

1. Passare alla [pagina Richiesta supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa precompilate.

2. In **Selezionare la versione del prodotto**selezionare Gestione offerte **dinamiche**.
3. Per **Selezionare una categoria che meglio descrive il problema,** scegliere la categoria che fa riferimento all'offerta.
4. Per **Selezionare un problema che meglio descrive il problema,** selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per essere indirizzati alla **pagina Dettagli problema** per immettere ulteriori dettagli sul problema.
6. Utilizzare **Deauthorize CSP** come titolo del problema e compilare il resto delle sezioni richieste.

 Se la tua offerta è attualmente **Opzione 2: Partner specifici del programma CSP che seleziona** e desideri passare all'opzione **3: Nessun partner nel programma CSP**, potrai passare a tale opzione solo se i partner del programma CSP che precedentemente avresti autorizzato non hanno rivenduto l'offerta ai clienti finali. Si prega di utilizzare le seguenti istruzioni per creare una richiesta:

1. Passare alla [pagina Richiesta supporto](https://aka.ms/marketplacepublishersupport). I primi menu a discesa vengono compilati automaticamente.

   > [!NOTE]
   > Non modificare le selezioni del menu a discesa precompilate.

2. In **Selezionare la versione del prodotto**selezionare Gestione offerte **dinamiche**.
3. Per **Selezionare una categoria che meglio descrive il problema,** scegliere la categoria che fa riferimento all'offerta.
4. Per **Selezionare un problema che meglio descrive il problema,** selezionare **Aggiorna offerta esistente**.
5. Selezionare **Avanti** per essere indirizzati alla **pagina Dettagli problema** per immettere ulteriori dettagli sul problema.
6. Utilizzare **Deauthorize CSP** come titolo del problema e compilare il resto delle sezioni richieste.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Spostarsi dall'opzione 3: Nessun partner nel programma CSP

Se la tua offerta è attualmente **L'opzione 3: Nessun partner nel programma CSP**, puoi passare a una delle altre due opzioni in qualsiasi momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Condivisione di materiali di vendita e supporto con i partner del programma CSP

Per aiutare i partner del programma Cloud Solution Provider a rappresentare in modo più efficace la tua offerta e interagire con la tua organizzazione, devi inviare i materiali di vendita e supporto che saranno disponibili per i rivenditori. Queste risorse non saranno esposte ai clienti nelle vetrine del marketplace.

### <a name="partner-center-csp-channel"></a>Canale CSP del Centro per i partner

Se hai scelto il canale CSP nel Centro per i partner, i publisher devono immettere un URL che condivida i materiali di marketing pertinenti e le informazioni di contatto del canale sul canale CSP nel modulo di presentazione dell'offerta:

![Informazioni collaterali CSP del Centro per i partner](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Canale CSP del portale per i partner cloud

Se hai scelto il canale CSP nel portale Cloud Partner, gli editori devono immettere un URL che convoglia i materiali di marketing pertinenti e le informazioni di contatto del canale sul canale CSP:

![Informazioni collaterali CSP del portale per i partner cloud](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Passaggi successivi

Visita la Guida di [Azure Marketplace e AppSource Publisher](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Per ulteriori informazioni sui servizi GTM del marketplace, vedere [Go-to-market services](https://partner.microsoft.com/reach-customers/gtm).

Accedi al [Centro per i partner partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) o al portale per i partner [cloud](https://cloudpartner.azure.com/) per creare e configurare la tua offerta.
